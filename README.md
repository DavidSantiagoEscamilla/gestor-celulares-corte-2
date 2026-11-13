# Gestor de Celulares · Corte 2

Ejercicio 11 de Desarrollo de Apps. El cliente usa Kodular y el backend usa Node.js, Express y PostgreSQL.

## Arquitectura hexagonal

- `src/domain/datos.js` contiene las reglas de validación.
- `src/application/gestor.js` contiene los casos de uso. Recibe un puerto de almacenamiento para usuarios, sesiones e inventario.
- `src/adapters/http.js` es el adaptador de entrada REST.
- `src/adapters/postgres.js` es el adaptador de salida. La cuenta autenticada delimita todas las consultas del inventario.
- `src/index.js` conecta los adaptadores con los casos de uso.

## Publicar en Render

1. Sube el contenido de esta carpeta a la raíz de un repositorio de GitHub con rama base `main`.
2. En Render elige **New → Blueprint** y conecta el repositorio y la rama `main`. El archivo `render.yaml` crea un servicio web y PostgreSQL. La variable `DATABASE_URL` se obtiene de la base de datos.
3. Cuando el servicio quede activo, copia la URL HTTPS de Render y abre `/health`. Debe responder con `{"estado":"ok"}`.
4. El Blueprint configura despliegue con cada commit de la rama conectada, incluidos los commits que llegan por merge. La base gratuita de Render caduca a los 30 días de su creación.

## Conectar la app

Genera el AIA con la URL real, usando como entrada el AIA original del Corte 1.

```bash
python3 kodular/conectar.py ~/Downloads/GestorDeCelulares.aia GestorDeCelulares_Online.aia --api https://TU-SERVICIO.onrender.com
```

Importa el resultado en Kodular Creator. Si tu proyecto original está en la misma cuenta, usa un nombre distinto para la copia. El AIA incluido en el paquete sirve para revisar los bloques y contiene una URL de ejemplo.

- Registra una cuenta online. Las contraseñas de TinyDB se quedan en el proyecto anterior. En la copia solo se usa TinyDB para guardar el token de sesión en las pantallas activas.
- En **Administrar celulares**, registra un equipo. Para editar o eliminar, entra a **Ver listado** o **Reportes**, toca el equipo y usa el formulario.
- Cierra sesión, inicia sesión en otro dispositivo con la misma cuenta y comprueba que ves el equipo. Entra con una segunda cuenta y comprueba que su inventario está separado.
- En **Sensores**, agita el teléfono para comprobar el acelerómetro y concede permiso de ubicación para comprobar el GPS.
- El panel de administración de cuentas se conserva como pantalla del proyecto anterior y queda oculto en la copia. La pantalla de recuperación informa que el envío de correo está pendiente. La versión online implementa registro, inicio y cierre de sesión, y CRUD personal de celulares.

## Contrato REST

| Método | Ruta | Datos |
|---|---|---|
| GET | /health | Estado de conexión a PostgreSQL |
| POST | /api/auth/registro | nombre, email, password |
| POST | /api/auth/login | email, password |
| POST | /api/auth/salir | Token de sesión |
| GET | /api/celulares | Token de sesión |
| POST | /api/celulares | modelo, marca, almacenamiento en GB, precio en COP |
| PUT | /api/celulares/:id | Los cuatro campos del celular |
| DELETE | /api/celulares/:id | ID del celular |

Las rutas privadas usan `Authorization: Bearer TOKEN`. El componente Web envía formularios codificados y la API también admite JSON. El parámetro `?formato=kodular` permite que el inicio de sesión devuelva una lista `[token, email, nombre]` y el listado devuelva filas `[id, modelo, marca, almacenamiento, precio]`.

## Pruebas

`npm ci && npm test` verifica cuentas, credenciales, CRUD, aislamiento y cierre de sesión con un adaptador en memoria. `npm run test:api` requiere permiso para escuchar en un puerto local. Para arrancar el servicio real, configura `DATABASE_URL` y ejecuta `npm start`.
