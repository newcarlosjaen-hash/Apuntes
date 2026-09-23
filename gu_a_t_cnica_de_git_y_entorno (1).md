# Guía Técnica de Git y Entornos de Desarrollo (DAW)

Documentación técnica para la sincronización del repositorio `newcarlosjaen-hash/Apuntes`. Este manual estandariza el flujo de trabajo para el stack de backend (Java 25, Tomcat 11, Maven) y frontend (React, Vite, Node).

---

## Índice
1. [Diccionario de Git: Conceptos y Comandos](#1-diccionario-de-git-conceptos-y-comandos)
2. [Flujo de Trabajo Estricto](#2-flujo-de-trabajo-estricto)
3. [Resolución de Conflictos en Git](#3-resolución-de-conflictos-en-git)
4. [Configuración de Entornos (Java & React)](#4-configuración-de-entornos-java--react)
5. [Gestión de Versiones (PC Componentes)](#5-gestión-de-versiones-pc-componentes)
6. [Troubleshooting (Errores Frecuentes)](#6-troubleshooting-errores-frecuentes)
7. [Archivo .gitignore Unificado](#7-archivo-gitignore-unificado)

---

## 1. Diccionario de Git: Conceptos y Comandos

Comprender la arquitectura subyacente evita la pérdida de código. Git opera moviendo archivos a través de tres estados locales antes de enviarlos al servidor.

| Comando / Concepto | Ubicación | Descripción Técnica |
| :--- | :--- | :--- |
| **Repositorio Local** | Disco Duro | Tu copia aislada del proyecto con todo su historial. |
| **Repositorio Remoto**| Servidor (GitHub) | La versión centralizada del proyecto. |
| `origin` | - | Alias estándar que apunta a la URL de tu repositorio en GitHub. |
| `main` | - | Rama (branch) principal. La línea de tiempo oficial del código. |
| `git pull origin main`| Remoto -> Local | Descarga los cambios del servidor y los fusiona (*merge*) con tu entorno local. |
| `git add .` | Working -> Staging| Mueve los archivos modificados al "Área de Preparación" (Staging). |
| `git commit -m "..."` | Staging -> Local | Consolida los cambios preparados en el historial local con un mensaje. |
| `git push origin main`| Local -> Remoto | Transfiere los commits consolidados al servidor de GitHub. |

---

## 2. Flujo de Trabajo Estricto

Para garantizar la integridad del código al alternar entre el equipo de clase (Fedora) y el portátil (Windows), este ciclo es **innegociable**.

### A. Inicio de sesión (Antes de escribir código)
Sincroniza tu entorno local con el servidor para obtener el trabajo del día anterior.

```bash
git pull origin main
```

> **Advertencia:** Modificar archivos antes de ejecutar este comando generará líneas de tiempo divergentes y provocará conflictos de fusión al intentar subir el código.

### B. Fin de sesión (Antes de cerrar el IDE)
Empaqueta y transmite los cambios.

```bash
git add .
git commit -m "Refactorización de Servlets y actualización de vistas JSP"
git push origin main
```

---

## 3. Resolución de Conflictos en Git

Si omites el `pull` inicial, modificas un archivo y luego intentas hacer `push`, Git abortará la operación mostrando un error de tipo `(fetch first)`.

**Procedimiento de recuperación:**

1. Fuerza la descarga de los cambios del servidor:
   ```bash
   git pull origin main
   ```
2. Git intentará fusionarlos. Si hay colisión en las mismas líneas de código, te indicará qué archivos tienen conflictos.
3. Abre esos archivos en tu IDE. Verás marcadores inyectados por Git:
   ```text
   <<<<<<< HEAD
   Tu código local actual
   =======
   El código que viene de GitHub
   >>>>>>> a1b2c3d4...
   ```
4. Elimina manualmente los marcadores (`<<<<<<<`, `=======`, `>>>>>>>`) y deja únicamente el código que deba prevalecer.
5. Registra la resolución:
   ```bash
   git add .
   git commit -m "Merge: Resolución de conflicto en LoginServlet.java"
   git push origin main
   ```

---

## 4. Configuración de Entornos (Java & React)

Los binarios y las configuraciones del IDE no viajan por el repositorio. El entorno debe ensamblarse en cada máquina.

### Stack Backend (Java 25 + Maven + Tomcat 11)

*   **JDK (Java 25):** 
    Ruta en IntelliJ: `File > Project Structure > Project`. Asigna el *SDK* a la versión 25 instalada en el SO anfitrión.
*   **Contenedor de Servlets (Tomcat 11):** 
    Ruta en IntelliJ: `Edit Configurations > Add New > Tomcat Server (Local)`. Apunta al directorio de instalación local (`CATALINA_HOME`). En la pestaña *Deployment*, asegúrate de añadir el artefacto tipo `war exploded`.
*   **Maven:** 
    Si las dependencias no cargan, abre la pestaña lateral de *Maven* en IntelliJ y haz clic en el icono de recargar (Sync).

### Stack Frontend (React + Vite + NodeJS)

El directorio `node_modules` está excluido vía `.gitignore`. En un equipo nuevo:

1. Navega al directorio del frontend: `cd ruta/del/frontend`
2. Reconstruye el árbol de dependencias:
   ```bash
   npm install
   ```
3. Levanta el servidor de desarrollo:
   ```bash
   npm run dev
   ```

---

## 5. Gestión de Versiones (PC Componentes)

Dado que la docencia implica reescritura de código sobre una misma base, usaremos una **estrategia de aislamiento por directorios** en lugar de ramas complejas.

```text
Apuntes/
├── teoria-clase/
├── pc-componentes-16-09-2026/  (Snapshot inmutable del día 16)
├── pc-componentes-18-09-2026/  (Snapshot inmutable del día 18)
└── pc-componentes-actual/      (Directorio de trabajo vivo)
```

**Flujo de la clase:** Antes de que el profesor elimine código para explicar un concepto nuevo, copia el directorio `pc-componentes-actual/`, renómbralo con la fecha de hoy, y continúa trabajando en la carpeta actual.

---

## 6. Troubleshooting (Errores Frecuentes)

| Síntoma / Error | Causa Raíz | Solución Técnica |
| :--- | :--- | :--- |
| **Imports en rojo (`jakarta.servlet.*`)** | El IDE no localiza las librerías del contenedor. | `File > Project Structure > Modules > Dependencies > + > Library`. Selecciona Tomcat 11. |
| **"Port 8080 already in use"** | Un proceso zombie de Tomcat o Java retiene el puerto TCP. | **Win:** Finaliza `java.exe` en el Task Manager.<br>**Linux:** Ejecuta `killall java` en terminal. |
| **"Unsupported Class Version Error"** | Incompatibilidad de bytecode. Compilado con Java 25, ejecutado con JRE inferior. | Estandariza la variable de entorno `JAVA_HOME` y el SDK de IntelliJ a la versión 25. |
| **Error 404 al llamar a un Servlet** | Desalineación entre la anotación `@WebServlet` y el Contexto de Despliegue. | Revisa el *Application context* en la pestaña *Deployment* de Tomcat. Si es `/`, tu servlet responde en `localhost:8080/tu-ruta`. |

---

## 7. Archivo .gitignore Unificado

Copia este bloque exactamente en un archivo llamado `.gitignore` en la raíz del repositorio.

```gitignore
# =========================
# JAVA, MAVEN & INTELLIJ
# =========================
.idea/
*.iml
target/
out/
build/

# =========================
# REACT, VITE & NODEJS
# =========================
node_modules/
dist/
dist-ssr/
*.local
npm-debug.log*

# =========================
# SISTEMA OPERATIVO
# =========================
.DS_Store
Thumbs.db
```