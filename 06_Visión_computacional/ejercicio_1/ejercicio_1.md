# Ejercicio 1 — Cambiar la imagen de predicción en YOLO

## 1. Qué se hizo

Se trabajó sobre una copia de `13 YOLO ultralytics.ipynb`, sin modificar el
original. Se corrió primero tal cual (instalación de `ultralytics`,
predicción CLI sobre `zidane.jpg`, entrenamiento de 3 épocas en `coco128` y
predicción sobre `bus.jpg`), y después se agregaron dos celdas nuevas —una
CLI y una en Python, ambas apuntando a la **misma imagen propia**— sin tocar
el modelo (`yolov8n.pt`), las épocas (`epochs=3`) ni el dataset
(`coco128.yaml`).

Como imagen propia se usó una fotografía real distinta de `zidane.jpg` y
`bus.jpg`: el retrato oficial de la astronauta Eileen Collins (dominio
público, distribuido como imagen de muestra del paquete `scikit-image`), que
muestra una persona, una bandera y una maqueta de transbordador espacial —
sirve igual que una foto subida a mano porque cumple el único requisito real
del ejercicio: contener al menos un objeto que COCO sepa nombrar.

En la copia final, la celda que crea `mi_foto.jpg` no depende de subir un
archivo a mano: instala `scikit-image` y genera la imagen directamente
dentro de Colab, así que basta con subir la notebook y darle "Ejecutar
todo" para que las celdas de predicción encuentren el archivo.

## 2. Corrida original (sin cambios)

| Imagen | Clases detectadas | Cajas |
|---|---|---|
| `zidane.jpg` | `person` (0.84), `person` (0.82), `tie` (0.29) | 3 |
| `bus.jpg` | `bus` (0.88), `stop sign` (0.43), `person` (0.87), `person` (0.80), `person` (0.86), `person` (0.29) | 6 |

El entrenamiento de 3 épocas sobre `coco128` corrió completo (128 imágenes,
`Box(P)` subió de 0.639 a 0.684 y `mAP50` de 0.619 a 0.654 entre la época 1
y la 3), y la predicción final sobre `bus.jpg` usó ese modelo recién
afinado.

## 3. Predicción sobre mi imagen (mismo cambio en CLI y en Python)

| Vía | Clases detectadas |
|---|---|
| CLI (`!yolo predict ... source='mi_foto.jpg'`) | `person` (0.74), `person` (0.33) |
| Python (`model('mi_foto.jpg', save=True)`) | `person` (0.74), `person` (0.33) — idéntico |

## 4. Reporte

**¿Qué clases detectó YOLO en las fotos de Ultralytics y cuáles en la mía?**
En `zidane.jpg` detectó dos `person` y una `tie` (la corbata verde de
Zidane). En `bus.jpg` detectó un `bus`, un `stop sign` y cuatro `person`
(los peatones). En mi foto detectó dos `person`: una con confianza alta
(0.74, la astronauta) y otra con confianza baja (0.33) sobre el casco negro
en primer plano — una detección incorrecta, no un segundo objeto real.

**¿Algún objeto evidente de tu foto no salió etiquetado? ¿Por qué podría
pasar?**
Sí, dos objetos muy visibles en la foto no aparecen etiquetados: la
**bandera de Estados Unidos** y la **maqueta del transbordador espacial** al
fondo. La razón es la más simple posible: ninguna de esas dos cosas es una
de las 80 clases de COCO (no existe clase "bandera" ni "cohete"/"nave
espacial"), así que por más clara que se vea la maqueta, el modelo no tiene
ninguna etiqueta para asignarle — el modelo solo puede nombrar lo que
existe en su vocabulario de entrenamiento.

**¿La predicción de la celda CLI y la de `model(...)` coinciden sobre tu
misma imagen?**
Sí, exactamente: ambas devolvieron las mismas dos cajas `person` con las
mismas confianzas (0.74 y 0.33). Tiene sentido, porque las dos vías cargan
el mismo `yolov8n.pt` sin reentrenar y corren inferencia sobre el mismo
archivo — la CLI es solo un envoltorio de línea de comandos sobre la misma
llamada `model(...)` que se usa en Python.

## 5. Reto opcional

### a) Umbral más estricto (`conf=0.7`)

| `conf` | Cajas |
|---|---|
| 0.25 (default) | `person` (0.74), `person` (0.33) |
| 0.70 | `person` (0.74) |

Con `conf=0.7` desaparece la caja de menor confianza (0.33, la del casco),
quedando solo la detección correcta de la persona (0.74). Esto confirma que
la segunda caja no era un objeto real adicional, sino una detección
incorrecta con confianza baja: subir el umbral es exactamente la forma de
filtrarla sin tocar el modelo.

### b) Modelo más grande (`yolov8s.pt`, sin reentrenar)

| Modelo | Clases detectadas |
|---|---|
| `yolov8n.pt` (nano) | `person` (0.74), `person` (0.33) |
| `yolov8s.pt` (small) | `person` (0.89), `traffic light` (0.49), `person` (0.33) |

El modelo más grande subió bastante la confianza de la detección correcta
(0.74 → 0.89), pero **no eliminó el error** del casco: en vez de marcarlo
como `person`, lo reclasificó como `traffic light` (0.49) y además agregó
una tercera caja `person` (0.33) recortada en el borde de la imagen. Es
decir, un modelo más grande no es automáticamente "más confiable" en
general — aquí mejoró la confianza del objeto real, pero cambió la forma
del error en lugar de corregirlo.