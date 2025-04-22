

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



```kotlin

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

```



# SUSCRIPCION A UBICACION CON ACTUALIZACIONES AUTOMATICAS
## y calcular distancia al aeropuerto BOG


```kotlin
package com.example.application1

import android.Manifest
import android.annotation.SuppressLint
import android.content.pm.PackageManager
import android.location.Location
import android.os.Bundle
import android.util.Log
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.ActivityCompat
import com.example.application1.databinding.ActivityGpsBinding
import com.google.android.gms.location.*

class Gps : AppCompatActivity() {

    private lateinit var binding: ActivityGpsBinding
    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private lateinit var locationRequest: LocationRequest
    private lateinit var locationCallback: LocationCallback

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityGpsBinding.inflate(layoutInflater)
        setContentView(binding.root)

        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // Inicializar solicitud de ubicación
        locationRequest = LocationRequest.Builder(Priority.PRIORITY_HIGH_ACCURACY, 5000)
            .setMinUpdateIntervalMillis(3000)
            .build()

        // Configurar callback para recibir ubicación en tiempo real
        locationCallback = object : LocationCallback() {
            override fun onLocationResult(locationResult: LocationResult) {
                locationResult.lastLocation?.let { location ->
                    updateUI(location)
                }
            }
        }

        permissionInit()  // Verifica permisos al iniciar

        binding.btnGetLocation.setOnClickListener {
            getLastLocation()
        }
    }

    // Verifica permisos
    private fun permissionInit() {
        if (!permissionsGranted()) {
            userPermission()
        } else {
            startLocationUpdates() // Si los permisos están, inicia actualizaciones
        }
    }

    // Verifica si los permisos fueron concedidos
    private fun permissionsGranted(): Boolean {
        return ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED
    }

    // Solicita permisos al usuario
    private fun userPermission() {
        ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.ACCESS_FINE_LOCATION), 100)
    }

    // Obtener última ubicación conocida
    @SuppressLint("MissingPermission")
    private fun getLastLocation() {
        if (!permissionsGranted()) {
            Toast.makeText(this, "Permiso de ubicación no concedido", Toast.LENGTH_SHORT).show()
            return
        }

        fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
            if (location != null) {
                Log.d("GPS", "Ubicación obtenida: Lat ${location.latitude}, Lon ${location.longitude}")
                updateUI(location)
            } else {
                Log.e("GPS", "No se pudo obtener la ubicación. Iniciando actualización...")
                Toast.makeText(this, "Esperando ubicación...", Toast.LENGTH_SHORT).show()
                startLocationUpdates() // Si no hay ubicación, pedir una nueva
            }
        }.addOnFailureListener { exception ->
            Log.e("GPS", "Error al obtener la ubicación: ${exception.message}")
            Toast.makeText(this, "Error: ${exception.message}", Toast.LENGTH_SHORT).show()
        }
    }

    // Iniciar actualizaciones en tiempo real
    @SuppressLint("MissingPermission")
    private fun startLocationUpdates() {
        if (!permissionsGranted()) return

        fusedLocationClient.requestLocationUpdates(locationRequest, locationCallback, null)
        Toast.makeText(this, "Esperando nueva ubicación...", Toast.LENGTH_SHORT).show()
    }

    // Detener actualizaciones de ubicación cuando la app está en segundo plano
    override fun onPause() {
        super.onPause()
        fusedLocationClient.removeLocationUpdates(locationCallback)
    }

    private fun updateUI(location: Location) {
        val userLat = location.latitude
        val userLon = location.longitude

        binding.tvLatitude.text = "Latitud: $userLat"
        binding.tvLongitude.text = "Longitud: $userLon"
        binding.tvElevation.text = "Elevación: ${location.altitude}"

        // Coordenadas del Aeropuerto El Dorado
        val airportLat = 4.7016
        val airportLon = -74.1469

        // Calcular la distancia
        val airportLocation = Location("").apply {
            latitude = airportLat
            longitude = airportLon
        }

        val distance = location.distanceTo(airportLocation) / 1000  // Convertir a KM
        binding.tvDistance.text = "Distancia al Aeropuerto: %.2f km".format(distance)
    }

}


```



```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:id="@+id/tvLatitude"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Latitud: "
        android:textSize="18sp"/>

    <TextView
        android:id="@+id/tvLongitude"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Longitud: "
        android:textSize="18sp"/>

    <TextView
        android:id="@+id/tvElevation"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Elevación: "
        android:textSize="18sp"/>

    <Button
        android:id="@+id/btnGetLocation"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Obtener Ubicación"/>

    <TextView
        android:id="@+id/tvDistance"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Distancia al Aeropuerto: -- km"
        android:textSize="18sp"
        android:textStyle="bold"
        android:layout_marginTop="16dp"/>


</LinearLayout>

```




---
---

## CLASE 7 MAP ACTIVITY 

```kotlin
package com.example.application1

import android.content.Context
import android.graphics.Bitmap
import android.graphics.Canvas
import android.graphics.drawable.Drawable
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.core.content.ContextCompat

import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.OnMapReadyCallback
import com.google.android.gms.maps.SupportMapFragment
import com.google.android.gms.maps.model.LatLng
import com.google.android.gms.maps.model.MarkerOptions
import com.example.application1.databinding.ActivityMapsBinding
import com.google.android.gms.maps.model.BitmapDescriptor
import com.google.android.gms.maps.model.BitmapDescriptorFactory

class MapsActivity : AppCompatActivity(), OnMapReadyCallback {

    private lateinit var mMap: GoogleMap
    private lateinit var binding: ActivityMapsBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityMapsBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        val mapFragment = supportFragmentManager
            .findFragmentById(R.id.map) as SupportMapFragment
        mapFragment.getMapAsync(this)
    }

    /**
     * Manipulates the map once available.
     * This callback is triggered when the map is ready to be used.
     * This is where we can add markers or lines, add listeners or move the camera. In this case,
     * we just add a marker near Sydney, Australia.
     * If Google Play services is not installed on the device, the user will be prompted to install
     * it inside the SupportMapFragment. This method will only be triggered once the user has
     * installed Google Play services and returned to the app.
     */
    override fun onMapReady(googleMap: GoogleMap) {
        mMap = googleMap

        //INICIALIZO DETALLES (GESTOS, BRUJULA, ETC.)
        mMap.uiSettings.isZoomGesturesEnabled = false
        mMap.uiSettings.isZoomControlsEnabled = true


        // Add a marker in Sydney and move the camera
        val titan = LatLng(4.694708, -74.086188)
        
        //propiedades de este marcador especifico
        val mkrJaveriana = mMap.addMarker(MarkerOptions().position(titan)
            .title("PUJ")
            .snippet("textotextotexto")
            .alpha(0.8F)
            //.icon(BitmapDescriptorFactory.defaultMarker(BitmapDescriptorFactory.HUE_AZURE)))
            .icon(bitmapDescriptorFromVector(this,R.drawable.ic_jave)))

        //propiedades de camara, se hacen al mapa
        mMap.moveCamera(CameraUpdateFactory.zoomTo(15F))
        mMap.moveCamera(CameraUpdateFactory.newLatLng(titan))

        //hacerlo invisible, "usuario se desconecto"
        // mkrJaveriana?.isVisible = false







    }



    //función de la presentación
    private fun bitmapDescriptorFromVector(context: Context, vectorResId: Int): BitmapDescriptor {
        val vectorDrawable: Drawable? = ContextCompat.getDrawable(context, vectorResId)
        vectorDrawable?.setBounds(0,
            0,
            vectorDrawable.intrinsicWidth,
            vectorDrawable.intrinsicHeight)

        val bitmap = Bitmap.createBitmap(vectorDrawable!!.intrinsicWidth,
            vectorDrawable.intrinsicHeight,
            Bitmap.Config.ARGB_8888)

        val canvas = Canvas(bitmap)
        vectorDrawable.draw(canvas)
        return BitmapDescriptorFactory.fromBitmap(bitmap)
    }


}
```





---
---

## CLASE FIREBASE:



### Signin simple con autenticacion, y mandando UID por intent:



```
package com.example.application1

import android.content.ContentValues.TAG
import android.content.Intent
import android.os.Bundle
import android.text.TextUtils
import android.util.Log
import android.widget.Toast
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.example.application1.databinding.ActivityFirebaseBinding
import com.example.application1.databinding.ActivityMainBinding
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.auth.FirebaseUser
import com.google.firebase.auth.ktx.auth
import com.google.firebase.ktx.Firebase


class FirebaseActivity : AppCompatActivity() {

    private lateinit var binding: ActivityFirebaseBinding

    private lateinit var auth: FirebaseAuth

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Initialize binding
        binding = ActivityFirebaseBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Initialize Firebase Auth
        auth = Firebase.auth

        // Sign in button click listener
        binding.Boton1.setOnClickListener {
            val email = binding.CorreoTxt.text.toString()
            val password = binding.PassTxt.text.toString()

            // Validate email and password before calling Firebase Auth
            if (email.isBlank() || password.isBlank()) {
                Toast.makeText(this, "Por favor ingrese todos los datos", Toast.LENGTH_LONG).show()
                return@setOnClickListener
            }

            // Perform Firebase sign-in
            auth.signInWithEmailAndPassword(email, password)
                .addOnCompleteListener(this) { task ->
                    if (task.isSuccessful) {
                        // Sign-in successful
                        Log.d(TAG, "signInWithEmail:onComplete:" + task.isSuccessful)
                        val user = auth.currentUser
                        updateUI(user)
                    } else {
                        // Sign-in failed
                        Log.w(TAG, "signInWithEmail:failure", task.exception)
                        Toast.makeText(this, "Authentication failed.", Toast.LENGTH_SHORT).show()
                        binding.CorreoTxt.setText("")
                        binding.PassTxt.setText("")
                    }
                }
        }

        binding.Boton2.setOnClickListener {
            val intent = Intent(this, OSMActivity::class.java)
            startActivity(intent)
        }
    }

    private fun validateForm(): Boolean {
        var valid = true
        val email = binding.CorreoTxt.text.toString()
        if (TextUtils.isEmpty(email)) {
            binding.CorreoTxt.error = "Required."
            valid = false
        } else {
            binding.CorreoTxt.error = null
        }
        val password = binding.PassTxt.text.toString()
        if (TextUtils.isEmpty(password)) {
            binding.PassTxt.error = "Required."
            valid = false
        } else {
            binding.PassTxt.error = null
        }
        return valid
    }

    override fun onStart() {
        super.onStart()
        val currentUser = auth.currentUser
        updateUI(currentUser)
    }


    // el que revisa si ya esta autenticado. ESTE MANDA A LA OTRA PANTALLA, NO EL BOTON
    private fun updateUI(currentUser: FirebaseUser?) {
        if (currentUser != null) {
            val intent = Intent(this, UidActivity::class.java)
            intent.putExtra("uid", currentUser.uid)  // Enviamos el UID por el intent
            startActivity(intent)
        } else {
            binding.CorreoTxt.setText("")
            binding.PassTxt.setText("")
        }
    }




    private fun signInUser(email: String, password: String){
        if(validateForm() && isEmailValid(email)){
            auth.signInWithEmailAndPassword(email,password)
                .addOnCompleteListener(this) { task ->
                    if (task.isSuccessful) {
// Sign in success, update UI
                        Log.d(TAG, "signInWithEmail:success:")
                        val user = auth.currentUser
                        updateUI(auth.currentUser)
                    } else {
                        Log.w(TAG, "signInWithEmail:failure", task.exception)
                        Toast.makeText(this, "Authentication failed.",
                            Toast.LENGTH_SHORT).show()
                        updateUI(null)
                    }
                }
        }
    }


    private fun isEmailValid(email: String): Boolean {
        if (!email.contains("@") ||
            !email.contains(".") ||
            email.length < 5)
            return false
        return true
    }




}
```




