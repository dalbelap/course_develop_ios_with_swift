# [Core Graphics](https://developer.apple.com/library/tvos/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html)

Abrir el proyecto samples/CoreGraphics que contiene una vista como una especia de _canvas_ donde se pueden dibujar líneas y figuras de diferentes colores.

Abrir el controllador _QuartzFunView_. Este hereda de UIView con varios métodos:
- touchesBegan: cuando comienzas a deslizar
- touchesMoved: cuando mueves el dedo sobre la pantalla
- touchesEnded: cuanto terminas de deslizar
- drawRect: ejecución y las formas que se deben pintar

En la vista de ViewController en Main.StoryBoard, el widget "Quartz Fun View" está asociado con el controllador _QuartzFunView_, ver panel derecho en ficha del identity inspector.

1. En touchesBegan añadir

```
setNeedsDisplay()
```

2. En touchesMoved
```
setNeedsDisplayInRect(redrawRect)
```

3. En touchesEnded

```
setNeedsDisplayInRect(redrawRect)
```

4. Antes de iniciar el pintado en _drawRect_

```
// Crear el context de Graphics
let context = UIGraphicsGetCurrentContext()

// Anchura de la línea
CGContextSetLineWidth(context, 2.0)
// Color de línea
CGContextSetStrokeColorWithColor(context, currentColor.CGColor)
// Color de relleno
CGContextSetFillColorWithColor(context, currentColor.CGColor)
```

5. Pintar sobre el lienzo

```
case .Rect:
          // Pintar con relleno y borde
          CGContextDrawPath(context, .FillStroke)

case .Ellipse:
          // igual
CGContextDrawPath(context, .FillStroke)

case .Image:
        //TODO: pintar
        // Pintar una imagen en el punto escogido por el usuario
        image!.drawAtPoint(drawPoint)

```
