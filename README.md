# Ud6_Ejemplo2
_Ejemplo 2 de la Unidad 6._ Navigation Drawer.

Vamos a implementar una aplicación que incorpore en su actividad principal un _Navigation Drawer_ de tal forma que mostraremos 
un panel lateral deslizable con todas las secciones de nuestra aplicación. Cada una de estas secciones consistirá en mostrar 
un _Fragment_.

Para ello tenemos que seguir los siguientes pasos:

## Paso 1: Creación de los _layouts_

### _activity_main.xml_
El primer paso será crear el _layout_ de la actividad principal que tendrá como elemento raíz un _DrawerLayout_ que, a su vez, 
contendrá un _frameLayout_ donde se mostrará cada uno de los _Fragments_ y un _NavigationView_ que mostrará el _layout_ de la 
cabecera (_header_navview.xml_) y el menú asociado (menu_navview.xml_):
```
<androidx.drawerlayout.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <FrameLayout
        android:id="@+id/content_frame"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
    </FrameLayout>

    <!-- La opción layout_gravity a "start" indica que aparecerá por la izquierda,
    "end" por la derecha (aunque material design no lo aconseja) -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navview"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        android:layout_gravity="start"
        app:headerLayout="@layout/header_navview"
        app:menu="@menu/menu_navview" />

</androidx.drawerlayout.widget.DrawerLayout>
```

### _header_navview.xml_
Consistirá en un _FrameLayout_ con una imagen, que será el fondo de la cabecera, y un texto para indicar el nombre del usuario.
```
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:src="@drawable/navheader"
        android:scaleType="centerCrop" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/nombre_usuario"
        android:textAppearance="@style/TextAppearance.AppCompat.Large.Inverse"
        android:textStyle="bold"
        android:layout_gravity="bottom"
        android:layout_marginBottom="10dp"
        android:layout_marginLeft="10dp" />
</FrameLayout>
```
### _menu_navview.xml_
El menú estará compuesto por dos grupos, uno con tres elementos y otro con dos. Todos con sus iconos asociados.
```
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item
            android:id="@+id/menu_seccion_1"
            android:icon="@drawable/ic_foto"
            android:title="@string/seccion_1"/>
        <item
            android:id="@+id/menu_seccion_2"
            android:icon="@drawable/ic_musica"
            android:title="@string/seccion_2"/>
        <item
            android:id="@+id/menu_seccion_3"
            android:icon="@drawable/ic_tv"
            android:title="@string/seccion_3"/>
    </group>

    <item
        android:id="@+id/navigation_subheader"
        android:title="@string/mas_opciones">
        <menu>
            <group android:checkableBehavior="single">
                <item
                    android:id="@+id/menu_opcion_1"
                    android:icon="@drawable/ic_subir"
                    android:title="@string/opcion_1"/>
                <item
                    android:id="@+id/menu_opcion_2"
                    android:icon="@drawable/ic_bajar"
                    android:title="@string/opcion_2"/>
            </group>
        </menu>
    </item>
</menu>
```
## Paso 2: Creación de los _Fragments_

Los tres _Fragments_ creados estarán prácticamente vacíos, solo mostrarán un texto por pantalla. La estructura será igual en los 
tres casos:

### _fragment_fragment1.xml_
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".Fragment1">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="@string/fragment1" />

</FrameLayout>
```

### _Fragment1.java_
```
public class Fragment1 extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_fragment1, container, false);
    }
}
```

## Paso 3: Creación de la clase _MainActivity_

En ella mostraremos el icono en la _Toolbar_ de la aplicación y controlaremos los elementos asociados a su _layout_, _DrawerLayout_ y 
_NavigationView_ de tal forma que cuando se pulse sobre este último podemos controlar sobre qué elemento se ha pulsado, y así 
mostrar un _Fragment_ u otro.
Además, controlaremos si volvemos a pulsar sobre el icono para cerrar el panel o si pulsamos _Atrás_ cuando éste está abierto para 
cerrarlo y no salir de la aplicación.

```
public class MainActivity extends AppCompatActivity {
    private DrawerLayout drawerLayout;
    private NavigationView navView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Mostramos el icono de la ToolBar
        getSupportActionBar().setHomeAsUpIndicator(R.drawable.ic_nav_menu);
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);

        drawerLayout = findViewById(R.id.drawer_layout);

        navView = findViewById(R.id.navview);

        // Dependiendo de la opción pulsada abrimos un fragment u otro.
        navView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
                    @Override
                    public boolean onNavigationItemSelected(MenuItem menuItem) {
                        boolean fragmentTransaction = false;
                        Fragment fragment = null;

                        switch (menuItem.getItemId()) {
                            case R.id.menu_seccion_1:
                            case R.id.menu_opcion_1:
                                fragment = new Fragment1();
                                fragmentTransaction = true;
                                break;
                            case R.id.menu_seccion_2:
                            case R.id.menu_opcion_2:
                                fragment = new Fragment2();
                                fragmentTransaction = true;
                                break;
                            case R.id.menu_seccion_3:
                                fragment = new Fragment3();
                                fragmentTransaction = true;
                                break;
                        }

                        if(fragmentTransaction) {
                            getSupportFragmentManager().beginTransaction()
                                    .replace(R.id.content_frame, fragment)
                                    .addToBackStack(null)
                                    .commit();

                            // Mostramos que la opción se ha pulsado.
                            menuItem.setChecked(true);

                            // Mostramos el título de la sección en el Toolbar.
                            setTitle(menuItem.getTitle());
                        }

                        // Cerramos el Drawer
                        drawerLayout.closeDrawer(GravityCompat.START);

                        return true;
                    }
                });
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch(item.getItemId()) {
            case android.R.id.home:
                // Si está abierto lo cerramos sino lo abrimos.
                if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
                    drawerLayout.closeDrawer(GravityCompat.START);
                }else {
                    drawerLayout.openDrawer(GravityCompat.START);
                }
                return true;
        }

        return super.onOptionsItemSelected(item);
    }

    @Override
    public void onBackPressed() {
        // Si está abierto al pulsar "Atrás" lo cerramos.
        if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
            drawerLayout.closeDrawer(GravityCompat.START);
        } else {
            super.onBackPressed();
        }
    }
}
```
