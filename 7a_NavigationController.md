# Table View Controller
----------------------

Las estructuras _TableView_ con navegación con UICustom. Normalmente se llama estructura de datos subyacente. Suele ser una matriz o cuando se tienen secciones separadas se utilizan estructuras de datos como conjuntos (Set), que no están ordenador ni adminten duplicados. 

En este ejercicio se utilizará

- Ejercicio Planetas

En el botón de cerrar busca un método del controlador o un Protocolo que gestiona la pila del TableView y que haga un pop.

```
@IBAction func buttonCloseTab(sender: AnyObject) {
    //self.dismissViewControllerAnimated(true, completion: nil)
    self.navigationController?.popToRootViewControllerAnimated(true)
}
```

En el menú de navegación tiene más sentido en un modal que en el navigationController.

## Navigation Controller

Es más habitual de lo que parece. La aplicación suele tener varias pantallas y cada una con secciones diferentes. Imaginemos que tiene 3 secciones. Una pantalla que tenga 3 secciones con la barra inferior _TabBarController_: en cada una de estas tabs tendrá su propio NavigationController.

