# 📚 Guía de Sincronización y Flujo de Trabajo (DAW)

Esta guía estandarizada está diseñada para el repositorio `newcarlosjaen-hash/Apuntes`. Cubre el entorno oficial de clase (Java 25 + Tomcat 11) y proyectos frontend (React + Vite + NodeJS).

Ideal para mantener sincronizado el trabajo entre los equipos con Fedora (Clase) y Windows (Casa).

## 1. 🚀 Clonar el Proyecto (Primer día en un PC nuevo)

```bash
# 1. Configura tu identidad (solo la primera vez en ese PC)
git config --global user.name "Tu Nombre"
git config --global user.email "tu_correo@ejemplo.com"

# 2. Descarga el repositorio
git clone https://github.com/newcarlosjaen-hash/Apuntes.git
```

## 2. 🔄 La Rutina Diaria (¡Sigue siempre este orden!)

### AL LLEGAR A CLASE (Antes de programar nada):
```bash
git pull origin main
```

### AL TERMINAR (Antes de apagar el PC):
```bash
git add .
git commit -m "Añadida clase del día XX-XX"
git push origin main
```

## 3. ⚙️ Entorno Oficial (Java 25 & Tomcat 11)

Al saltar entre Linux (Fedora) y Windows, el código es el mismo, pero **las configuraciones no se suben a GitHub**. Cada vez que cambies de PC:

* **Java (JDK 25):** Verifica en IntelliJ (*File > Project Structure > Project*) que el SDK seleccionado es el 25.
* **Tomcat 11:** El servidor no se sube a GitHub. Ve a *Edit Configurations* en IntelliJ, añade un *Local Tomcat* y apunta a la carpeta donde tienes instalado Tomcat 11 en tu PC.
* **React/Node:** Recuerda hacer `npm install` cada vez que clones el proyecto por primera vez para descargar la carpeta `node_modules`.

## 4. 🗂️ Cómo gestionar "PC Componentes" (El método del profesor)

Dado que el profesor suele borrar código de días anteriores para explicar cosas nuevas, la mejor forma de no perder apuntes (y no complicarte la vida con "ramas" complejas de Git todavía) es **versionar por carpetas**.

Dentro de tu repositorio de GitHub, estructura tu proyecto así:
```text
Apuntes/
├── teoria/
├── pc-componentes-16-09-2026/  (El estado del proyecto en este día)
├── pc-componentes-18-09-2026/  (Copias la carpeta anterior y sigues aquí)
└── react-proyectos/
```
**El truco:** Antes de que el profe borre cosas para empezar un tema nuevo, copia la carpeta entera del proyecto en tu PC, ponle la fecha de hoy, ábrela en IntelliJ y sigue trabajando sobre esa nueva. Así siempre tendrás "fotos" de cómo estaba el código cada día.

## 5. 🐛 Primeros Auxilios: Errores Recurrentes

Aquí tienes los fallos más típicos al moverse entre ordenadores y sus soluciones:

1. **Imports en rojo (`jakarta.servlet...`)**
   * *Por qué pasa:* IntelliJ no sabe dónde están las librerías de Tomcat 11 en tu PC nuevo.
   * *Solución:* Ve a *File > Project Structure > Modules > Dependencies*, pulsa el botón **+**, elige *Library* y selecciona Tomcat 11.

2. **Error de servidor: "Port 8080 already in use"**
   * *Por qué pasa:* Intentas arrancar Tomcat en IntelliJ, pero ya hay otro Tomcat ejecutándose de fondo (quizás se quedó pillado).
   * *Solución (Windows):* Abre el Administrador de tareas, busca un proceso llamado `java.exe` o `Tomcat` y finalízalo.

3. **Error al compilar: "Unsupported Class Version Error"**
   * *Por qué pasa:* Compilaste en clase con Java 25, pero tu PC de casa está intentando ejecutarlo con una versión más antigua (ej. Java 21).
   * *Solución:* Asegúrate de tener JDK 25 instalado y seleccionado en *Project Structure*.

4. **Error 404 al abrir el navegador ("The requested resource is not available")**
   * *Por qué pasa:* Tomcat arrancó bien, pero no encuentra tu Servlet o tu JSP.
   * *Solución:* Comprueba que tienes la anotación correcta en tu código Java (`@WebServlet("/ruta")`) y que en la configuración de Tomcat (pestaña *Deployment*), tienes añadido tu artefacto `war exploded` y el *Application context* es el correcto (suele ser `/`).

## 6. 🛡️ El Archivo `.gitignore` Definitivo

Asegúrate de tener este archivo `.gitignore` en la raíz de tu repositorio `Apuntes`:

```text
# === JAVA, MAVEN & INTELLIJ ===
.idea/
*.iml
target/
out/
build/

# === REACT, VITE & NODEJS ===
node_modules/
dist/
dist-ssr/
*.local
npm-debug.log*

# === SISTEMA OPERATIVO ===
.DS_Store
Thumbs.db
```