

PREGUNTAS DE QUIZ 2:

activity = pantalla

los strings se ubican en: res/values/strings.xml

los colores se ubican en: res/values/colors.xml

si se modifica una coma en cualquiera de los gradles toca sincronizar



QUIZ 3:

- ciclo de vida
-     Layouts:
- LINEAR LAYOUT
- peso solo existe en linear layout
- linear , si no se pone peso, asume que es 0. si no, hace regla de 3 para calcularlo
- en que pantalla existe la propiedad peso: linear layout

- RELATIVE LAYOUT: objetos orientados a otros objetos
- cuantos puntos de anclaje minimos se requieren en un relative layout = 1
- 0dp para que se estire lo mas posible el align

- WEB LAYOUT: para cargar una pagina web
- FRAME LAYOUT: para superponer cosas

- CONSTRAINT LAYOUT: tambien tiene puntos de anclaje
- minimo requiere 2 puntos de anclaje 







QUIZ 4 - PERMISOS
- permisos normales: los que no requieren info. sensible: alarma, internet, audio (parlante), bluetooth [no siemprre tienen info sensible)
- permisos con riesgo: siempre contienen info sensible.: microfono, camara, escribir en almacenamiento, calendario

- permiso normal : allow y deny
- insistir: allow, deny, deny & never ask again
- si se deniega dos veces, se asume deny & never ask again

- PARCIAL: se debe pedir un permiso, por lo que se debe desinstalar y volver a instalar (never ask again)




        //dentro del onCreate
        when {
            ContextCompat.checkSelfPermission(
                this, android.Manifest.permission.READ_CONTACTS
            ) == PackageManager.PERMISSION_GRANTED -> {
                // You can use the API that requires the permission.
                // performAction(...)
            }
            ActivityCompat.shouldShowRequestPermissionRationale(
                this, android.Manifest.permission.READ_CONTACTS) -> {
                // In an educational UI, explain to the user why your app requires this
                // permission for a specific feature to behave as expected, and what
                // features are disabled if it's declined.
                // showInContextUI(...)
                pedirPermiso(this, android.Manifest.permission.READ_CONTACTS, "", PERMISSION_READ_CONTACTS )

            }
            else -> {
                // You can directly ask for the permission.
                pedirPermiso(this, android.Manifest.permission.READ_CONTACTS, "", PERMISSION_READ_CONTACTS )

            }
        }





private fun pedirPermiso(context: Activity, permiso: String, justificacion: String,
                             idCode: Int) {
        if (ContextCompat.checkSelfPermission(context, permiso)
            != PackageManager.PERMISSION_GRANTED
        ) {
            //metodo de android
            requestPermissions(arrayOf(permiso), idCode)
        }

    }


    override fun onRequestPermissionsResult(requestCode: Int,
                                            permissions: Array<String>, grantResults: IntArray) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        when (requestCode) {
            PERMISSION_READ_CONTACTS -> {
                // If request is cancelled, the result arrays are empty.
                if ((grantResults.isNotEmpty() &&
                            grantResults[0] == PackageManager.PERMISSION_GRANTED)) {
                    // Permission is granted. Continue the action or workflow
                    // in your app.
                } else {
                    // Explain to the user that the feature is unavailable
                    Toast.makeText(baseContext, "experiencia de usuario disminuida!!", Toast.LENGTH_SHORT).show()
                }
                return
            }


            PERMISSION_CAMERA -> {
                // If request is cancelled, the result arrays are empty.
                if ((grantResults.isNotEmpty() &&
                            grantResults[0] == PackageManager.PERMISSION_GRANTED)) {
                    // Permission is granted. Continue the action or workflow
                    // in your app.
                } else {
                    // Explain to the user that the feature is unavailable
                }
                return
            }


            else -> {
                // Ignore all other requests.
            }
        }
    }









