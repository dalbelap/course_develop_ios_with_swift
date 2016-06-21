# Bar Button Item
-----------------

En la estructura de navegación de todos los eventos que se relacionen con push se podrán colocar elementos:
- Botón izquierda(atrás)
- Central (label título)
- Botón derecha (acciones como enviar)

1. Arrastrar el BarButtonItem a la barra de navegación de la pantalla de calendario.

2. Crear conexión del BarButtonItem como acción llamada _sendImage_ en el controlador de tipo AnyObject
```
    @IBAction func sendImage(sender: AnyObject) {
    }
```

3. Saber cuando se hace "Intro" con un _Delegate_ para realizar los eventos

Con un controlador se le pone una acción, no se va a hacer a mano pero sí que se va a establecer una relación de uso con otro controlador. En iOS se pueden utilizar otros controladores por ejemplo gracias al MessageUI se puede acceder a un controlador.

Por ejemplo _MailViuewController_, que envía emails pero que es una caja negra para el desarrollador con un método de salida llamado _send_. Pero necesitas saber si el envío ha ido bien. Para ello están los Protocolos Delegados implementado en el controlador y llamando a un método que permita preguntar el estado mediante un método.

4.  Importar MessageUI: https://developer.apple.com/library/ios/documentation/MessageUI/Reference/MessageUI_Framework_Reference/
```
import MessageUI
```

5. Añadir una propiedad MFMailViewComposer(https://developer.apple.com/library/ios/documentation/MessageUI/Reference/MFMailComposeViewController_class/)

6. Añadir el delegado
MFMailComposeViewControllerDelegate
```
class ImageViewController: UIViewController, MFMailComposeViewControllerDelegate {

```

7. Instanciar el MFMailViewComposer y asignar el correo
```

        mfComposer = MFMailComposeViewController()
        mfComposer.mailComposeDelegate = self
        mfComposer.setToRecipients(["probandoIOS12340@mailinator.com"])
        mfComposer.setSubject("Hola Mundo!")
        /*
        mfComposer.addAttachmentData(<#T##attachment: NSData##NSData#>, mimeType: <#T##String#>, fileName: <#T##String#>)
         */
        mfComposer.setMessageBody("Esto es una prueba del curso de iOS", isHTML: true)
```

8. Preguntar al framework si se le pueden enviar email

Antes de realizar la operación, comproboar si puede enviar emails con _canSendMail_
```
    @IBAction func sendImage(sender: AnyObject) {
        if MFMailComposeViewController.canSendMail() {
            presentViewController(mfComposer, animated: true, completion: nil)
        }
    }
```
9. Ver los detalles del protocolo _MFMailComposeViewControllerDelegate_

```
/*!
    @protocol    MFMailComposeViewControllerDelegate
    @abstract    Protocol for delegate callbacks to MFMailComposeViewController instances.
    @discussion  This protocol must be implemented for delegates of MFMailComposeViewController instances.  It will
                 be called at various times while the user is composing, sending, saving, or canceling email composition.
*/
public protocol MFMailComposeViewControllerDelegate : NSObjectProtocol {

    /*!
        @method     mailComposeController:didFinishWithResult:error:
        @abstract   Delegate callback which is called upon user's completion of email composition.
        @discussion This delegate callback will be called when the user completes the email composition.  How the user chose
                    to complete this task will be given as one of the parameters to the callback.  Upon this call, the client
                    should remove the view associated with the controller, typically by dismissing modally.
        @param      controller   The MFMailComposeViewController instance which is returning the result.
        @param      result       MFMailComposeResult indicating how the user chose to complete the composition process.
        @param      error        NSError indicating the failure reason if failure did occur.  This will be <tt>nil</tt> if
                                 result did not indicate failure.
    */
    @available(iOS 3.0, *)
    optional public func mailComposeController(controller: MFMailComposeViewController, didFinishWithResult result: MFMailComposeResult, error: NSError?)
}
```

A partir de _func_ copiar el método opcional hasta el final del paréntesis y pegarlo en el controlador:
```
    func mailComposeController(controller: MFMailComposeViewController, didFinishWithResult result: MFMailComposeResult, error: NSError?){
      switch(result){
                  case MFMailComposeResultCancelled:
                      print("canceled")
                  break
                  case MFMailComposeResultSaved:
                      print("Info: mail saved")
                  break
                  case MFMailComposeResultFailed:
                      print("Error: can not send")
                  case MFMailComposeResultSent:
                      print("Info: email successful sent")
                  break
                  default:
                  print("other")
              }

              controller.dismissViewControllerAnimated(true, completion: nil)
    }
```

_didFinishWithResult_ indica el resultado final. _MFMailComposeResultCancelled_ es si el usuario ha cancelado.

10. Seleccionar el simulador iPhone 5s y resetear simulador. En la aplicación del simulador la tercera opción: Resent and content Settings

NOTA: En el simulador no funciona.
