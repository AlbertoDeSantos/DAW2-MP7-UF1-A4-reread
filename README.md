
----
## Desarrollo de aplicaciones web: mysqli, queries y sesiones

:arrow_down: **Estructura de ficheros y directorios:**

```
reread
    - index.php
    - img/
    - css/
        style.css
    - services/
        connection.php
    - processes/
        login.proc.php
        logout.proc.php
    - view/
        index.php
        libros.php
        ebooks.php
        login.html
        zona.admin.php
```

# Establecimiento de conexiones


```php
echo
mysqli_connect
mysqli_connect_errno
mysqli_connect_error
mysqli_set_charset
die
exit
PHP_EOL
```

**Ejemplo de conexión (`connection.php`):**

```php
<?php
// ESTILO POR PROCEDIMIENTOS

$host = "localhost";
$user = "root";
$pass = "";
$db = "reread";

// Crear la conexión
$conn = mysqli_connect($host, $user, $pass, $db);

// Checkear la conexión
if (!$conn) {
    echo "Error: No se pudo conectar a MySQL." . PHP_EOL;
    echo "Error de depuración: " . mysqli_connect_errno() . PHP_EOL;
    exit;
} else {
	mysqli_set_charset($conn, "utf8");
}

/* //ESTILO ORIENTADO A OBJETOS
// Crear la conexión
$conn = new mysqli('localhost', 'root', '', 'imagenes');

// Checkear la conexión
if ($conn->connect_errno) {
	die("Error: No se pudo conectar a MySQL: " . $conn->connect_errno);
} else {
	$conn->set_charset("utf8");
}
*/

?>
```

> **Establecer la conexión en el proyecto reread.** :rotating_light:

# Ejecución de sentencias SQL


```php
empty
include
require
include_once
require_once
mysqli_query
mysqli_num_rows
mysqli_fetch_array
mysqli_fetch_all
```
**Ejemplo de ejecución de query:**

```php
<?php
// 1. Conexión con la base de datos	
include '../services/connection.php';

// 2. Selección y muestra de datos de la base de datos
$result = mysqli_query($conn, "SELECT Books.Title FROM Books WHERE eBook != '0'");

if (!empty($result) && mysqli_num_rows($result) > 0) {
// datos de salida de cada fila (fila = row)
    while ($row = mysqli_fetch_array($result)) {
    echo "<p>".$row['Title']."</p>";
    }
} else {
    echo "0 resultados";
}
?>
```

> **Establecer en los ficheros index.php, ebooks.php y libros.php del proyecto reread el código para mostrar los libros Top ventas.** :rotating_light:

# Mantenimiento del estado (crear sesión)

```php
$_REQUEST
mysqli_real_escape_string
MD5
mysqli_query
mysqli_num_rows
session_start
$_SESSION
header
mysqli_free_result
```

**Página de login (`login.html`):**
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="../css/styles.css">
    <title>Bookshop Zona Admin</title>
    <link rel="shortcut icon" href="../img/book-dead-solid.svg" type="image/x-icon">
    <link rel="preconnect" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400&display=swap" rel="stylesheet">
    <script src="../js/scripts.js"></script>
</head>

<body>
    <div class="row">
        <div class="column-2 height-100" id="portada"></div>
        <div class="column-2 height-100 flex" id="login">
            <form action="../processes/login.proc.php" method="post">
                <input type="email" name="email" id="email" placeholder="email...">
                <input type="password" name="password" id="password" placeholder="contraseña...">
                <input type="submit" value="Iniciar sesión">
                <div id="mensaje"></div>
            </form>
        </div>
    </div>
</body>

</html>
```

**Hoja de estilos (cambiar el contenido actual por este)**:

```css
/*Algunos términos:
    width - ancho
    height - alto (altura)
    padding - relleno
    border - borde
*/


/*El selector universal (*) selecciona todos los elementos HTML en la página.*/

* {
    /*https://www.w3schools.com/css/css3_box-sizing.asp
  CSS Box Sizing
  Por defecto, el width y el height de un elemento se calcula 
    sumando width + padding + border = width total
    sumando height + padding + border = height total
  La propiedad box-sizing nos permite incluir el relleno
  y el borde en el ancho y alto total de un elemento.

  Nos será útil para el diseño en columnas de la página web.
  */
    box-sizing: border-box;
    font-family: monospace;
}


/*Quitamos el margin que viene por defecto en la etiqueta body*/

body {
    margin: 0;
}


/* Estilo del <div> del logo */

.logo {
    background-color: #0B5345;
    color: white;
    padding: 5px;
    font-size: 10px;
}


/* Estilo del encabezado */

.header {
    padding: 20px;
    font-size: 20px;
    text-align: center;
}


/*Estilo del título <h1> del encabezado 
  ¿Cómo seleccionamos únicamente el h1 de dentro del header?
  En la hoja de CSS ponemos:
  .header h1{ ...estilos... }
  Con esto indicamos lo siguiente:
  A todo aquel elemento que tenga la clase header y tenga un 
  descendiente <h1> asignale los siguientes estilos.
*/

.header h1 {
    font-size: 80px;
}


/*Estilo del párrafo <p> del encabezado 
  Igual que antes: A todo aquel elemento que tenga la clase header y 
  tenga un descendiente <p> asignale los siguientes estilos. */

.header p {
    padding: 0 20%;
}


/*Estilos del menú de navegación
  https://www.w3schools.com/css/css_overflow.asp
  CSS Overflow
  La propiedad overflow especifica si se debe recortar el contenido o agregar barras de desplazamiento cuando el contenido de un elemento es demasiado grande para caber en el área especificada.
*/

.topnav {
    overflow: hidden;
    border-bottom: 2px solid #D35400;
}


/* Style the topnav links */

.topnav a {
    float: left;
    display: block;
    text-align: center;
    padding: 14px 16px;
    text-decoration: none;
    color: black;
    font-size: 15px;
}


/* Change color on hover */

.topnav a:hover {
    background-color: #D35400;
    color: white;
}

.topnav a.active {
    background-color: #D35400;
    color: white;
}


/*https://www.w3schools.com/css/css_website_layout.asp
  Diseño en columnas del sitio web.

*/


/* Create three unequal columns that floats next to each other */

.column {
    float: left;
    padding: 15px;
}

.textpage {
    margin: auto;
    width: 80%;
}


/*right column */

.column.side {
    width: 33.3%;
}


/* right column - parrafs*/

.column.side p {
    padding: 20px;
    background-color: #117A65;
    color: white;
}


/* left column */

.column.middle {
    width: 66.6%;
}


/* Clear floats after the columns */

.row:after {
    content: "";
    display: table;
    clear: both;
}


/*Gallery*/

.gallery {
    padding: 20px;
    float: left;
    width: 33%;
}

.gallery img,
.inlineImg img {
    width: 100%;
}

div.desc {
    padding: 15px;
    text-align: center;
}

div.gallery:hover {
    background-color: #D35400;
    color: white;
}

/* menú de navegación fijo*/

ul {
    list-style-type: none;
    margin: 0;
    padding: 0;
    overflow: hidden;
    background-color: #4CAF50;
    position: fixed;
    top: 0;
    width: 100%;
}

li {
    float: left;
}

li a {
    display: block;
    color: white;
    text-align: center;
    padding: 14px 16px;
    text-decoration: none;
    font-size: 15px;
}

li a:hover:not(.active) {
    background-color: #111;
}

/*alineación*/

.inlineImg {
    padding: 20px;
    float: left;
    width: 33.3%;
}

.clear {
    clear: both;
}
p{
    font-size: 17px;
}
h3{
    font-size: 20px;
}

/* columnas */

.column-2 {
    width: 50%;
    float: left;
}

/* botones */

input[type=submit] {
    background-color: #4CAF50;
    color: white;
    padding: 5px 20px;
    text-decoration: none;
    cursor: pointer;
    border-radius: 5px;
    border: solid 1px #4CAF50;
}

input[type=text],
input[type=email],
input[type=password] {
    padding: 5px 15px;
    text-decoration: none;
    border-radius: 5px;
    border: solid 1px #4CAF50;
}


/* reglas auxiliares */

.height-100 {
    height: 100vh;
}

.padding-top {
    padding-top: 70px;
}

.padding-top-less {
    padding-top: 20px;
}

.flex {
    display: flex;
    align-items: center;
    justify-content: center;
}

.right {
    float: right;
}

#portada {
    background-image: url(../img/bookshop.jpeg);
    background-size: cover;
}

.padding-lat {
    padding-left: 10%;
    padding-right: 10%;
}

#mensaje {
    margin: 3% 0;
    width: 80%;
    color: red;
}

#login {
    background-color: #4caf4f5a;
}

/* Responsive layout - makes the three columns stack on top of each other instead of next to each other */

@media screen and (max-width: 600px) {
    .column.side,
    .column.middle {
        width: 100%;
    }
    .topnav a {
        float: none;
    }
    .gallery {
        float: none;
        margin: auto;
        width: 80%;
    }
    .header {
        padding: 5px;
    }
    .header p {
        padding: 0 5%;
    }
    .column-2 {
        width: 100%;
    }
    #portada {
        display: none;
    }
    #filter {
        padding-top: 20px;
    }
    #login input {
        margin: 3% 10%;
        width: 80%;
    }
    #mensaje {
        margin: 3% 10%;
        width: 80%;
    }
    .padding-lat {
        padding-left: 0.5%;
        padding-right: 0.5%;
    }
}
```

**Imágenes a añadir al directorio img:**
https://drive.google.com/drive/folders/1YCNuUuglZBSoT9YPJvDOIGHrdhsAS1Ap?usp=sharing


**Ejemplo de login y establecimiento de sesión (`login.proc.php`):**

```php
<?php
// 1. Conexión con la base de datos	
include '../services/connection.php';

$email=$_REQUEST['email'];
$psswd=$_REQUEST['password'];

$email=mysqli_real_escape_string($conn,$email);

$user = mysqli_query($conn,"SELECT * FROM Users WHERE Email='$email' and Pass=MD5('{$psswd}')");

if (mysqli_num_rows($user) == 1) {
    // coincidencia de credenciales
    session_start();
    $_SESSION['email']=$email;
    header("location: ../view/zona.admin.php");
} else {
    // usuario o contraseña erróneos
    header("location: ../view/login.html");
}

mysqli_free_result($user);
```
> **Dentro del proyecto reread establecer el proceso de login (front-end y backend), así como la creación de la sesión del usuario.** :rotating_light:

# Mantenimiento del estado y destrucción (recuperar sesión y logout)

```php
session_start
$_SESSION
isset
header
$_REQUEST
mysqli_query
foreach
print_r
session_destroy
```

**Ejemplo de página de administración y mantenimiento de sesión (`zona.admin.php`):**

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="../css/styles.css">
    <title>Zona Admin</title>
    <link rel="shortcut icon" href="../img/book-dead-solid.svg" type="image/x-icon">
    <link href="../fontawesome/css/all.css" rel="stylesheet">
    <link rel="preconnect" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400&display=swap" rel="stylesheet">
</head>

<body>
    <?php
    include '../services/connection.php';
    session_start();
    /* Controla que la sesión esté iniciada */
    if (!isset($_SESSION['email'])) {
        header('Location: login.html');
    }
    ?>
    <ul class="padding-lat">
        <li><a>Hola <?php echo $_SESSION['email'];?></a></li>
        <li class="right">
            <a href="../processes/logout.proc.php">Logout</a>
        </li>
    </ul>
    <div class="row padding-top padding-lat">
        <div class="column-2">
            <form action="" method="post">
                <input type="submit" value="añadir libro">
            </form>
        </div>
        <div class="column-2" id="filter">
            <form action="zona.admin.php" method="post">
                <input type="text" placeholder="buscar por título..." name="titulo">
                <input type="submit" value="filtrar" name="filtro">
            </form>
        </div>
    </div>

    <div class="row padding-top-less padding-lat">
        <div class="column-1">
            <table>
                <tr>
                    <th>Titulo</th>
                    <th>Descripción</th>
                    <th>Autor</th>
                </tr>
                <!-- Recoger libros de la base de datos -->
 
            </table>
        </div>
    </div>
</body>

</html>
```

**Ejemplo de página de logout y cierre de sesión (`logout.proc.php`):**

```php
<?php
session_start();
session_destroy();
header('Location: ../view/login.html')
?>
```

> **Dentro del proyecto reread establecer una página de administración de la web, así como el mantenimiento de la sesión del usuario y logout.** :rotating_light:

## Actividades

* **La actividad 1, 2, 4 y 5 se han de desarrollar en una rama nueva llamada `filtros`, creada a partir de `main`**

* **La actividad 3, 6, 7 y 8 se han de desarrollar en una rama nueva llamada `session`, creada a partir de `main`**

**Actividad 1. (`filtros`)** En zona.admin.php recoger libros (título, descripción y autor) de la BD y mostrarlos por pantalla usando foreach.

```php
foreach ($libros as $libro) {
    //code...
}
```

**Actividad 2. (rama `filtros`)** En zona.admin.php se ha de poder filtrar los libros que se muestran por título de libro.

**Actividad 3. (rama `session`)** Haz los cambios respectivos en login.proc.php y zona.admin.php para que la aplicación muestre `Hola Test` (nombre) en lugar de `Hola test@test.com` (email).

**Actividad 4. (rama `filtros`)** En ebooks.php se han de mostrar las imágenes de los libros recuperadas mediante una consulta en la base de datos.

**Actividad 5. (rama `filtros`)** En ebooks.php se ha de poder filtrar libros (ebooks) por autor y país (**los valores del campo país se han de sacar de la base de datos**).

![form1](https://github.com/dannylarrea/reread-to-php/blob/main/form_1.png)

**Actividad 6. (rama `session`)** En index.php, ebooks.php y libros.php se ha añadir un enlace `Iniciar sesión` que provoque una redirección a login.html.

**Actividad 7. (rama `session`)** En login.html añadir una validación JS tal que:

![form1](https://github.com/dannylarrea/reread-to-php/blob/main/form_2.png)

**Actividad 8. (rama `session`)** En login.html añadir a la validación JS anterior que:
* si algún campo no está informado este pasará a tener su propiedad CSS `campo.style.borderColor= "red"`
* sino, se quedará con su color por defecto `#4CAF50`
