ACTIVITY_MAIN.XML:




<?xml version="1.0" encoding="utf-8"?>

<!-- todos los objetos, no importa cuales sea tienen 2 propiedades obligatorias: Ancho y Alto -->

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="500dp"
    android:orientation="vertical"

    tools:context="MainActivity">


    <ImageView
        app:srcCompact="@android:drawable/ic_delete"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Bienvenido!"
        android:textSize="22sp"
        android:textStyle="bold"
        android:textAlignment="center"/>


    <TextView

        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="16dp"
        android:textSize="22sp"
        android:textAlignment="center"
        android:text="Mi Primera App" />
    <TextView

        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="18dp"
        android:textSize="18sp"
        android:textAlignment="center"
        android:textStyle="bold"
        android:text="@string/Universidad"
        android:textColor="@color/moradito"/>
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="text"
        android:hint="Nombre: "/>


    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textAlignment="gravity">


        <Button
            android:id="@+id/button3"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Mandar a Pantalla 2" />
    </LinearLayout>
</LinearLayout>



STRINGS.XML:

<resources>
    <string name="app_name">My Application</string>
    <string name="Universidad">Pontificia Universidad Javeriana</string>
</resources>





COLORS.XML:

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="moradito">#9063DC</color>
</resources>



MAINACTIVITY.KT:


package com.example.app_2510

import android.os.Bundle
//import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
//import androidx.core.view.ViewCompat
//import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        }
    }
}





