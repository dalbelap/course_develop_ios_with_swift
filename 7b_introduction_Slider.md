## Slider
---------

Ejercicio "Seleccionar mes" con un widget _Slider_ que elige un mes del año (rango 1-12).

A través del panel de propiedades limitar el slider a 12 elementos con una etiqueta que varíe y se vaya poniendo el nombre del mes. Un botón "Vamos" permite mostrar para cada mes seleccionado una imagen asociada y utilizando los mecanismos de navegación se muestre la pantalla o permite volver.

1. En las opciones de "Attributes inspector" seleccionar en "slider" el valor mínimo 1 y el máximo 12. En current poner 6.
![Slider properties](captions/day6/xcode_slider_sample1.png)

Desactivar la opción del Slide Events: "Continuous Updates"

2. Añadir los widgets al controlador y añadir la acción al cambiar el slide:
```
class ViewController: UIViewController {

    let SEGUE : String = "SegueImage"
    @IBOutlet weak var lbMonth: UILabel!
    @IBOutlet weak var sldSelectMonth: UISlider!

    var months : [String] = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio", "Julio", "Agostor", "Septiembre", "Octubre", "Noviembre", "Diciembre"]

    override func viewDidLoad() {
        super.viewDidLoad()

        // Set actual month
        let date = NSDate()
        let calendar = NSCalendar.currentCalendar()
        let components = calendar.components(NSCalendarUnit.Month, fromDate: date)
        sldSelectMonth.value = Float(components.month)
    }

    @IBAction func setMonth(sender: AnyObject) {
        if let slider = sender as? UISlider {
            let index = Int(slider.value)
            lbMonth.text = months[index - 1]
        }
    }

    @IBAction func showImage(sender: AnyObject) {
        performSegueWithIdentifier(SEGUE, sender: nil)
    }
```

3. Descargar los recursos y añadirlos a una nueva ruta llamada img

4. Crear un nuevo "ViewController" en Main.StoryBoard al lado de la primera pantalla con nombre ImageViewController

5. Crear NavigationController
Seleccionar el ViewController principal y en Editor -> Embebed In -> seleccionar "Navigation Controller".

6. Crear el _Segue_ con Navigation Controller hacia la segunda pantalla _PlanetViewController_. Arrastrar desde vista del controllador de la primera pantalla hacia la segunda pulsando la tegla "Control" y seleccionar la opción _Push_

7. Poner nombre al Segue creado con nombre "SegueImage" en el panel derecho seleccionando la flecha del _Segue_ y en "Attributes inspector" en "Identifier".

8. Crear un nuevo Controllador llamdo ImageViewController y asociarlo a la escena nueva creada.
En el nombre del proyecto pulsando "Control" y clic del ratón: new -> New File -> Source -> Cocoa Touch Class.
```
class ImageViewController: UIViewController {

    var monthToShow : Int = 1
    @IBOutlet weak var imageView: UIImageView!

    override func viewDidLoad() {
        super.viewDidLoad()
    }
}
```

9. Coger el segundo controlador y darle a Editor -> Refator to StoryBoard
Esto creará un segundo StoryBoard con otro nombre, "Calendar", y estará vinculado con el principal.

10. Cuando se pulse el botón "Vamos!" llamar a la otra pantalla
```
    @IBAction func showImage(sender: AnyObject) {
        performSegueWithIdentifier(SEGUE, sender: nil)
    }
```

11. Cuando se cambie a la pantalla de mostrar imagen actualizar el índice del slice:
```
    // Ventana del tiempo. Ocurre antes de que el SEGUE sea lanzado
    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
          if segue.identifier == SEGUE {
              // obtener el controlador
              let imageController = segue.destinationViewController as! ImageViewController

              // actualizar el índice del mes
              imageController.monthToShow = (Int(sldSelectMonth.value))
          }
    }
```

12. En el controlador de la segunda pantalla mostrar la imagen
sobreescribir el método _viewWillAppear_. Este se ejecuta justo antes de mostrar la pantalla al usuario y es mejor que cargar la imagen desde didLoad
```
override func viewWillAppear(animated: Bool) {
        let imageName = String(format: "%02d.jpg", arguments: [monthToShow])
        imageView.image = UIImage(named: imageName)
}
```

- Resultado:

![Demo pantalla 1](captions/day6/demo_calendar1.png)
![Demo pantalla 2](captions/day6/demo_calendar2.png)
