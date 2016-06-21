# CloudKit (continuación)
-------------------------


Se verá CloudKit, sensores e internalización y localización Location.

Por último se verá el proceso de subida a la tienda de Apple.

*NOTA*: para hacer funcionar este proyecto es necesario disponer de cuenta Apple ID en el programa de desarrollador (previo pago). Con la cuenta normal no se podrá añadir la capacidad de iCloud. Ver [Support App Capabilities](https://developer.apple.com/support/app-capabilities/).

Abrir el proyecto de sample/CloudBug

1. Importar el framework cloudKit en MasterViewController

```
import CloudKit
```

2. Añadir la consulta contra iCloud en _viewDidLoad_

```
//TODO: lanzar query contra iCloud
// se require indicar el espacio iCloud, el container, y especificar si es la parte pública o privada
let container = CKContainer.defaultContainer()
let publicData = container.publicCloudDatabase

// lanzar la query contra el espacio público, en formato predicado sin filtros
let query = CKQuery(recordType: "Bug", predicate: NSPredicate(format: "TRUEPREDICATE", argumentArray: nil))
publicData.performQuery(query, inZoneWithID: nil) { (results, error) in
  if error != nil {
      print(error)
  }else {
      for bug in results! {
          let newBug = Bug()
          newBug.title = bug["Title"] as! String
          newBug.description = bug["Description"] as! String

          self.objects.append(newBug)

          // TODO recargar objects en el hilo principal
      }
  }
}
```

performQuery lanza una consulta sobre el contenedor público de iCloud del usuario.

3. Saltar del thread de proceso al de interfaz

La ejecución de la query se ejecuta en un hilo diferente al principal, para actualizar los datos de la vista es necesario regargar la tabla en el hilo principal.

Hay una instrucción que se llama _dispatch_async_ que salta un proceso entre thread, de un hilo al principal. Esto permite recargar los datos del array bug en el controlador para que sean recargados desde el hilo de ejecución al hilo principal, el User Interface.

```
self.objects.append(newBug)
....

// saltar al hilo de ejecución principal y recargar el widget TableView
dispatch_async(dispatch_get_main_queue(), {
    () -> Void in
      self.tableView.reloadData()
})
```

4. Almacenar en el método _receiveBug_ las notificaciones recibidas de Bug

```
// se require indicar el espacio iCloud, el container, y especificar si es la parte pública o privada
let container = CKContainer.defaultContainer()
let publicData = container.publicCloudDatabase

// crear un registro llamado "Bug"
let record = CKRecord(recordType: "Bug")
// almacenar en un registro con los valores
record.setValue(bug.title, forKey: "Title")
record.setValue(bug.description, forKey: "Description")
publicData.saveRecord(record, completionHandler: {
 record, error in
   if error != nil{
     print(error)
   }
})
```

5. Crear Bug en iCloud.

NOTA: para realizar este paso es necesario disponer de cuenta Apple ID del programa de desarrollador. Con la cuenta normal no se podrá añadir la capacidad de iCloud.

Ir a las preferencias del Proyecto en Targets -> CloudBug -> pestaña Capabilities. Ir a la opción _iCloud_ y pulsar en el botón "CloudKit dashboard".

Se abrirá un navegador que irá a la cuenta del [desarrollador](https://icloud.developer.apple.com/dashboard/).
  - En "Record Types" crear un registro llamado "Bug" y en el botón más añadir campos "Title" y "Description" de tipos String.
  - En "Public Data" añadir un registro de prueba con un título y descripción, por ejemplo "prueba de cloud description".

Adicionalmente se pueden crear API Token que permitan interactuar a través de un servicio web contra iCloud usando aplicaciones externas.
