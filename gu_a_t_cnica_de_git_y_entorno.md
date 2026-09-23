# Guía Técnica de Git y Entorno de Desarrollo (DAW)

Documentación técnica para la sincronización del repositorio `newcarlosjaen-hash/Apuntes`. Cubre el stack de backend (Java 25, Tomcat 11, Maven) y frontend (React, Vite, Node).

## 1. Conceptos y Terminología de Git

Para trabajar correctamente entre varios equipos sin provocar conflictos de código, es fundamental entender qué hace cada comando y los términos que utiliza Git:

*   **Repositorio Local vs. Remoto:** Tu PC tiene el repositorio *local*. Los servidores de GitHub alojan el repositorio *remoto*. Git se encarga de sincronizar ambos.
*   **`origin`:** Es el alias (nombre por defecto) que Git asigna al enlace de tu repositorio remoto cuando lo clonas. Cuando escribes `origin`, le estás diciendo a Git "conéctate a la URL de GitHub de este proyecto".
*   **`main`:** Es la rama (branch) principal de desarrollo. Representa la línea de tiempo oficial de tu código.
*   **`git pull origin main`:** Descarga los cambios del servidor (`origin`) en la rama `main` y los fusiona (*merge*) inmediatamente con los archivos de tu disco duro. 
*   **El Área de Preparación (Staging) y `git add .`:** Git no guarda los cambios automáticamente. `git add .` mueve todos los archivos modificados a un área temporal (staging area), indicando que están listos para ser guardados.
*   **`git commit -m "mensaje"`:** Toma una "fotografía" exacta de los archivos que estaban en el área de preparación y la guarda permanentemente en el historial de tu repositorio *local*.
*   **`git push origin main`:** Sube todos los commits que tienes en local y que el servidor aún no tiene, actualizando la rama `main` en `origin` (GitHub).

## 2. Flujo de Trabajo y Sincronización Estricta

Para evitar conflictos de fusión (merge conflicts) al alternar entre el PC de clase (Fedora) y el portátil (Windows), el ciclo de trabajo debe ser inmutable.

### Inicio de sesión (Antes de abrir el IDE)
Ejecuta esto para traer el trabajo del día anterior al PC actual:
```bash
git pull origin main
```
*Por qué:* Si empiezas a editar archivos sin hacer `pull` primero, crearás una línea de tiempo alternativa en tu local. Al intentar subirla luego, Git detectará una divergencia y bloqueará el `push`.

### Fin de sesión (Antes de apagar el equipo)
```bash
git add .
git commit -m "Descripción técnica de lo implementado (ej. Servlet Login y persistencia)"
git push origin main
```

## 3. Configuración del Entorno (Java 25 & Tomcat 11)

Los archivos de configuración del IDE (IntelliJ) y de los servidores no se suben al repositorio por seguridad y compatibilidad. En cada equipo debes configurar:

1.  **JDK (Java 25):** 
    En IntelliJ: `File > Project Structure > Project`. Asegúrate de que el *SDK* está asignado a la versión 25 local del sistema.
2.  **Apache Tomcat 11:**
    En IntelliJ: `Edit Configurations > Add New > Tomcat Server (Local)`. Asigna la ruta de instalación local de Tomcat.
3.  **Frontend (React/Node):**
    El directorio `node_modules` está excluido del control de versiones. Al clonar el proyecto en un PC nuevo, debes ejecutar `npm install` en la raíz del proyecto frontend para regenerar las dependencias locales.

## 4. Gestión Evolutiva del Proyecto "PC Componentes"

Dado que la metodología de clase implica la eliminación y reescritura constante de código sobre el mismo proyecto, el control de versiones tradicional por ramas (branches) puede resultar excesivo y propenso a errores en esta etapa.

**Estrategia de versionado por directorios:**
Mantén el histórico del proyecto encapsulando el estado de cada día en directorios independientes dentro del repositorio.

```text
Apuntes/
├── teoria/
├── pc-componentes-16-09-2026/  (Estado inmutable del proyecto de esta fecha)
├── pc-componentes-18-09-2026/  (Directorio de trabajo actual)
└── react-proyectos/
```
Antes de iniciar una clase donde se prevea destrucción de código, duplica el directorio del último día, renómbralo con la fecha actual y abre ese nuevo directorio como proyecto en IntelliJ.

## 5. Resolución de Errores Comunes (Troubleshooting)

1.  **Pérdida de dependencias de Servlets (Imports en rojo `jakarta.servlet`)**
    *Causa:* El IDE no tiene mapeada la librería del contenedor de servlets (Tomcat).
    *Solución:* `File > Project Structure > Modules > Dependencies > + > Library` e inyecta la librería de Tomcat 11.
2.  **Puerto ocupado: "Port 8080 already in use"**
    *Causa:* Una instancia previa de Tomcat (o de Java) sigue ejecutándose en segundo plano y reteniendo el puerto TCP.
    *Solución (Windows):* Finaliza el proceso `java.exe` desde el Administrador de Tareas. En Linux: `killall java`.
3.  **Fallo de compilación: "Unsupported Class Version Error"**
    *Causa:* El código fue compilado (target) con Java 25, pero el entorno de ejecución (JRE) actual es inferior (ej. Java 21).
    *Solución:* Estandariza la versión del JDK a la 25 en la configuración de IntelliJ y las variables de entorno del sistema (`JAVA_HOME`).
4.  **Error 404 en el despliegue del Servlet**
    *Causa:* Tomcat compila y despliega, pero no encuentra los mapeos de ruta.
    *Solución:* Verifica la anotación `@WebServlet("/ruta")` y asegúrate de que en la configuración de Tomcat (pestaña *Deployment*), el artefacto desplegado es del tipo `war exploded` y el *Application context* no interfiere con la ruta.

## 6. Archivo .gitignore Unificado

Este archivo es obligatorio en la raíz del repositorio para evitar la subida de binarios y configuraciones locales.

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