# Gestos
--------

Crear un proyecto nuevo llamado "Gestos" e importar la imagen _rosa_vientos.png_ al proyecto.

1. Crear un ImageView en la escena con la imange importada.

2. Añadir la propiedad en el controlador con el nombre _gestureView_

```
@IBOutlet weak var gestureView: UIImageView!
```

3. Habilitar los gestos sobre la imagen
```
gestureView.userInteractionEnabled = true
```

4. Crear el gesto llamado _Gesture_
Hay varios gestos como swipe, rotate, etc. de tipo _UIRotationGestureRecognizer_
```
// Los ":" significa que el parámetro tiene un argumento en la función Selector
       let rotateGesture = UIRotationGestureRecognizer(target: self,
                                                       action: Selector("rotateView:"))
```

UIRotationGestureRecognizer recibe como parámetro el propio controlador y la acción del gesto con lo que tiene que hacer. En este caso el gesto es _Selector_ (la forma con que se va a interactuar) y la acción es el método _rotateView_ que se creará en el siguiente apartado. Los dos puntos significa que _rotateView_ recibe un parámetro de entrada, aunque no se defina el tipo.

En las nuevas versiones de Swift:
```
let rotateGesture = UIRotationGestureRecognizer(target: self,
                                                        action: #selector(ViewController.rotateView(_:)))
```

5. Crear la acción _rotateView_

Conectar la imagen con el evento _Referencing Outlets_
```

    func rotateView(recognizer: UIRotationGestureRecognizer){
        // Si hay una vista que ha ejecutado el elemento rotate
        if let view = recognizer.view {
            // transformar la imagen con la view de la imagen y el ángulo dado por recognizer en el atributo rotation
            view.transform = CGAffineTransformRotate(view.transform, recognizer.rotation)
            // reinicializar el rotation a 0
            recognizer.rotation = 0
        }
    }
```

6. Asignar el rotateGesture

A la imagen para que realize el gesto con _gestureView.gestureRecognizers_ es un array con los gestos
```
gestureView.gestureRecognizers = [rotateGesture]
```

7. Lanzar el simulador

Pulsado la tecla ALT y clic sobre la pantalla

## Gesto Tap (clic)

Recordar que en las propiedades del widget de imagen _ImageView_ del proyecto debe estar habilitada la interacción con gestos del usuario: "User Interaction Enable
d".

Que al pulsar el Tap la imagen haga que el color de fondo de la imagen varíe. Una ImageView es una vista especial que contiene una imagen.

1. Crear el gesto UITapGestureRecognizer
```
let tapGesture = UITapGestureRecognizer(target: self, action: #selector(ViewController.tapBar(_:)))
```

2. Añadir a los gestos que recibe la ImageView
```
gestureView.gestureRecognizers = [rotateGesture, tapGesture]
```

3. Añadir el método tapBar
```
unc tapBar(recognizer: UITapGestureRecognizer){    // TODO
 }
```

4. Cambiar el color de fondo de la imagen en la función
```
  // gestureView.backgroundColor = UIColor.greenColor()
  // random entre 0 y 1
  let red = CGFloat(drand48())
  let green = CGFloat(drand48())
  let blue = CGFloat(drand48())

  // alpha es la transparencia entre 0 y 1, siendo 0 transparente y 1 opaco
  gestureView.backgroundColor = UIColor(red: red, green: green, blue: blue, alpha: 1.0)
```

5. Añadir que haga a 3 clics
```
  tapGesture.numberOfTapsRequired = 3
```

## Gesto Pan (traslación)

Gesto de traslación de un objeto. Es un movimiento en un plano x,y. Primero es necesario conocer que trayectoria seguir y

1. Crear el gesto UIPanGestureRecognizer
```
let panGesture = UIPanGestureRecognizer(target: self, action: #selector(ViewController.moveView(_:)))
gestureView.gestureRecognizers = [tapGesture, panGesture]
```

2. Crear la acción del gesto
```
func moveView(recognizer: UIPanGestureRecognizer){

  }
```

3. Añadir acción de movimiento de traslación
```
    let translation = recognizer.translationInView(self.view)
    recognizer.view!.center = CGPoint(x: view.center.x + translation.x, y: view.center.y + translation.y)
```

## Pinch (pinzar)

Transforma la escala del tamaño de la imagen. Puede entrar en conflicto con la acción de Pan anterior.

1. Crear el gesto UI
```
let pinchGesture = UIPinchGestureRecognizer(target: self, action: #selector(ViewController.pinchView(_:)))
// quitar el gesto rotateGesture
gestureView.gestureRecognizers = [tapGesture, panGesture, pinchGesture]
```
2. Crear método
```
func pinchView(recognizer: UIPinchGestureRecognizer){
  // TODO
}
```
3. Crear la escala
```
if let view = recognizer.view {
    view.transform = CGAffineTransformScale(view.transform, recognizer.scale, recognizer.scale)
    recognizer.scale = 1
}
```

Para que salgan los dos dedos pulsar la tecla ATL en el Simulador.

## Programar fin de Gesto

https://stackoverflow.com/questions/12278798/how-do-i-programmatically-end-reset-a-uigesturerecognizer

Comprobando el estado actual del objeto _recognizer_ con el enumerado UIGestureRecognizerState:
```
if recognizer.state == UIGestureRecognizerState.Ended {

}
```

Por ejemplo, para el método moveView del gesto de translation:
```
func moveView(recognizer: UIPanGestureRecognizer){
    let initialX = view.center.x
    let initialY = view.center.y
    let translation = recognizer.translationInView(self.view)
    recognizer.view!.center = CGPoint(x: view.center.x + translation.x, y: view.center.y + translation.y)

    if recognizer.state == UIGestureRecognizerState.Ended {
        recognizer.view!.center = CGPoint(x: initialX, y: initialY)
    }
}
```
