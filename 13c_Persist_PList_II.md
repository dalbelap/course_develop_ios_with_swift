# Persistencia (continuación)
-----------------------------

## Properties List
------------------

Abrir el proyecto de ejemplo _PersistencePlist_. Volver al controllador y obtener el recurso del fichero Books.plist. Este recurso será de solo lectura porque no se encuentra en _Documents_. Lo que se puede hacer es guardar el fichero PList en _Documents_ y guardar y persistir los datos ahí. Por ejemplo, es habitual crear un fichero de configuración para ello en _Documents_.

1. Leer datos de un Plist

```
// Manipulación de PList
let plistURL = NSBundle.mainBundle().URLForResource("Books", withExtension: "plist")!

let fileManager = NSFileManager.defaultManager()

if let plistData = NSData(contentsOfURL: plistURL){
    var format = NSPropertyListFormat.XMLFormat_v1_0
    do {
        // Recuperar los objetos libros de tipo Array
        // format se pasa por referencia usando "&"
        let books = try NSPropertyListSerialization.propertyListWithData(plistData, options: .Immutable, format: &format)

        let book = books[0]

        if let name = book["name"]{
            print(name)
        }

        if let pageCount = book["Page Count"] {
            print(pageCount)
        }

        if let publicationDate = book["Publication Date"]{
            print(publicationDate)
        }

        if let thumbnail = book["Thumbnail"] {
            let data = thumbnail as! NSData
            let thumbnail = UIImage(data: data)
        }

    } catch {
        print("ERROR: \(error)")
    }
}
```

2. Almacenar en un PList

Arrastrar una imagen al proyecto.

```
// Persistencia en Documents
let bookImage = UIImage(named: "earth.png")!
// obtener el dato binario de la imagen
let bookData = UIImagePNGRepresentation(bookImage)

let newBook = [
    "Name" : "Padre rico, padre pobre",
    "Publication Date" : NSDate(),
    "Page Count" : 178,
    "Thunmbnail": bookData!
]

let saveBooks = [book, newBook]

// serializar para almacenar en un formato concreto
let serializedData = try NSPropertyListSerialization.dataWithPropertyList(saveBooks, format: NSPropertyListFormat.XMLFormat_v1_0, options: 0)

// almacenar en el Document data del usuario
let documentDirectory = try fileManager.URLForDirectory(.DocumentDirectory, inDomain: .UserDomainMask, appropriateForURL: nil, create: false)

// crear el path completo con la URL
let fileUrl = documentDirectory.URLByAppendingPathComponent("books.plist")

// almacenar
serializedData.writeToURL(fileUrl, atomically: true)

// ver la ruta
print(fileUrl)

```

![Output with path](xcode_plist_sample_1.png)

![books.plist data](xcode_plist_sample_2.png)

## NUserDefaults

1. Abrir el proyecto _samples/AppSettingsDemo_

2. Asegurarse que los outlets están conectados con el controllador

3. Añadir al método saveData los datos del usuario que se desean guardar:

```
  @IBAction func saveData(sender: AnyObject) {

    let userDefaults = NSUserDefaults.standardUserDefaults()

    // almacenar los objetos
    userDefaults.setObject(firstName.text, forKey: "FirstName")
    userDefaults.setObject(lastName.text, forKey: "LastName")

    // almacenar enteros del widget botón segmentado SegmentedControl
    userDefaults.setInteger(mood.selectedSegmentIndex, forKey: "Mood")

    // almacenar imagen en base64
    if let pickedImage = imageView.image {
        let imageData = UIImagePNGRepresentation(pickedImage)
        userDefaults.setObject(imageData, forKey: "Picture")
    }

    // ocultar el botón de guardar imagen
    pickImageButton.hidden = true

  }
```

4. Ejecutar y guardar datos con una imagen

5. Recuperar información

Desde el método _viewDidLoad_ cargar la información que se ha guardado

```
override func viewDidLoad() {
   super.viewDidLoad()

   let userDefaults = NSUserDefaults.standardUserDefaults()
   firstName.text = userDefaults.objectForKey("FirstName") as? String
   lastName.text = userDefaults.objectForKey("LastName") as? String

   mood.selectedSegmentIndex = userDefaults.integerForKey("Mood")

   let imageData = userDefaults.objectForKey("Picture") as? NSData

   if let imageData = imageData {
       let picture = UIImage(data: imageData)
       imageView.image = picture
   }
}
```

## Application Settings

1. Para ello es necesario crear un PList especial. Botón derecho pulsando Control, new file -> iOS -> Resource -> _Settings Bundle_
![Settings Bundle](xcode_plist_sample_3.png)


2. Eliminar los hijos de "Preference Items"
![Settings Bundle](xcode_plist_sample_4.png)

3. Pulsar en más sobre "Preferencia Items" y añadir un tipo "Multi Value". Aparecerán 4 hijos. Poner a cada uno los valores.

Para el ítem 0 se incluirán los valores del SegmentedControl. Title es el nombre, Identifier la clave a la que se accederá y default value (tipo Number) la opción por defecto. Añadir un quinto elemento hijo llamado "Titles" de tipo array. Si se pulsa en su flecha, esta apuntará hacia abajo. Añadir elementos de tipos String con los valores: Good, Bad, Meh. Repetir el proceso con el array "Values" añadiendo 0, 1 y 2. Para ello, pulsar de nuevo en la flecha de "Values" (haciendo que apunte hacia la derecha) y pulsar en el más.

Realizar lo mismo para los elemento TextField de _First Name_ y _Last Name_.

Lanzar el simulador y desde Hardware -> Home ir a Settings y en la lista buscar la aplicación. Se verán los datos de Settings con los valores:
![Settings App](xcode_plist_sample_5.png)

Lo que se guarde en la aplicación se reflejará en Settings porque las claves de acceso a los valores son exactamente las mismas.
