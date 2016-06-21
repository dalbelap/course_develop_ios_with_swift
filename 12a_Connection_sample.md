# Connection
-----------

1. Para ejecutar una petición en red en PlayGround hay que realizar una ejecución con [XCPlaygroundPage](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/XCPlaygroundModuleRef/XCPlayground.html)


```
  import XCPlayground

  XCPlaygroundPage.currentPage.needsIndefiniteExecution = true

```

2. Crear la configuración inicial de la sesión de conexión

```

let configuration = NSURLSessionConfiguration.defaultSessionConfiguration()

// no permitir acceso a Internet desde la red GRPS
configuration.allowsCellularAccess = false

// crear la sesión de conexión
let session = NSURLSession(configuration: configuration)
```

3. Llamada al recurso REST

```
let url = NSURL(string: "https://poloniex.com/public?command=returnTicker")!
let url = NSURL(string: "https://api.flickr.com/services/feeds/photos_public.gne?format=json&nojsoncallback=1")!

// Llamada al recurso desde una clousure
let task = session.dataTaskWithURL(url) {
  (data, response, error) in
  guard let data = data else { print(error); return }
}
```

4. Obtener el resultado en UTF8

Al finalizar la llamada, detener el PlayGroundPage

```
    let result = NSString(data : data, encoding: NSUTF8StringEncoding)
    XCPlaygroundPage.currentPage.finishExecution()
```

5. Iniciar el proceso de la tarea definida

```
task.resume()
```

## API Yahoo! Sample

Crear un proyecto con un Label y un texto que obtenga la temperatura. Hará una petición contra Yahoo! para obtener la temperatura de un servicio REST:
```
let url = NSURL(string: "https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast%20where%20woeid%20in%20%28select%20woeid%20from%20geo.places%281%29%20where%20text%3D%22nome%2C%20ak%22%29&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys
")!
```

https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast%20where%20woeid%20in%20%28select%20woeid%20from%20geo.places%281%29%20where%20text%3D%22nome%2C%20ak%22%29&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys


La temperatura hay que mostrarla en el textfield. No usar el código en el controlador, usar una clase _helper_ que realice la petición. Hay que realizar llamadas asíncronas, por tanto no puede ser una llamada a una función que devuelva un resultado, podría guardarse el valor en una propiedad una vez invocada la función que llama al recurso web. Otra alternativa es usar una _clousure_ con los parámetros y una que sea _success_ indicando si ha sido ejecutado con éxito.
```
func A(param1: String, param2: Int, callback: () -> Void)
```

Consejos:
  A. Poner siempre el tipo clousure como último parámetros
  B. Invocar la función con el parámetro _clousure_ despúes de las llaves de apertura:
  ```
  A("data", 0){
    // parámetro clousere
    () -> Void in
    // código interno
  }
  ```



1. Crear el controlador APIHelper

```
//
//  YahooApi.swift
//  TemperatureYahoo
//
//  Created by CNTG Mañanas on 7/6/16.
//  Copyright © 2016 CNTG Mañanas. All rights reserved.
//

import UIKit

class APIHelper {

    let url = NSURL(string: "https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast%20where%20woeid%20in%20%28select%20woeid%20from%20geo.places%281%29%20where%20text%3D%22nome%2C%20ak%22%29&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys")!

    func getTemp(tempAquired: (temp: String, success: Bool) -> Void){
        // Inicializar una sesión simple con sharedSession
        NSURLSession.sharedSession().dataTaskWithURL(url){
            (data, response, error) -> Void in
            if error != nil && data != nil {
                do{
                    // 1. convert result in JSON
                    let weatherDictionary = try NSJSONSerialization;.
                    JSONObjectWithData(data!, options: .MutableContainers) as! [String: AnyObject]

                    if let query = weatherDictionary["query"] as? [String: AnyObject]{
                        if let results = query["results"] as? [String: AnyObject] {
                            if let channel = results["channel"] as? [String : AnyObject] {
                                if let item = channel["item"] as? [String: AnyObject] {
                                    if let condition = item["condition"] as? [String: AnyObject]{
                                        if let temp = condition["temp"] as? String {
                                            // invocar el parámetro clousure
                                            tempAquired(temp: temp, success: true)
                                        }
                                    }
                                }
                            }
                        }
                    }
                }catch{
                    tempAquired(temp: "", success: false)
                }
            }
        // finalizar lanzando la task
        }.resume()
    }
}

```

2. En el controllador
```
@IBAction func getTemperature(sender: AnyObject) {

       let helper = APIHelper()
       helper.getTemp { (temp, success) in
           if success {
               temperatureTextField.text = temp
           }else {
              temperatureTextField.text = "Error!"
           }
       }
       // petición asíncrona para obtener los datos desde internet
       // mostrar una barra de información de se está realizando la petición
   }
```

3. Problema al lanzar en el hilo UI. Por lo tanto hay que ejecutar en segundo plano:

```

       helper.getTemp { (temp, success) in
           if success {
               dispatch_async(dispatch_get_main_queue(), {
                   () -> Void in
                   self.temperatureTextField.text = temp
               })
           }else {
               dispatch_async(dispatch_get_main_queue(), {
                   () -> Void in
                   self.temperatureTextField.text = "ERROR!"
               })
           }
       }
```
