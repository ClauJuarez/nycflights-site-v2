# Creación y publicación de un sitio web con Blogdown+Hugo+Netlify

En esta práctica construiremos un sitio con **Blogdown**, utilizaremos **Git** para controlar versiones, **GitHub** para almacenar el proyecto y **Netlify** para publicar automáticamente el sitio.

El flujo general será:

```{text}
RStudio
   ↓
Blogdown + Hugo
   ↓
prueba local
   ↓
Git
   ↓
GitHub
   ↓
Netlify
   ↓
sitio público
```

---

# 1. Instalar Blogdown

En RStudio, ejecuta:

```{r}
install.packages("blogdown")
```

Carga el paquete:

```{r}
library(blogdown)
```

---

# 2. Instalar Hugo

Blogdown utiliza **Hugo** para construir el sitio web.

Ejecuta:

```{r}
blogdown::install_hugo()
```

Comprueba la versión instalada:

```{r}
blogdown::hugo_version()
```

---

# 3. Crear un proyecto en RStudio

En RStudio selecciona:

```{text}
File → New Project → New Directory → New Project
```

Asigna un nombre, por ejemplo:

```{text}
nycflights-site
```

Selecciona dónde guardar el proyecto y presiona:

```{text}
Create Project
```

---

# 4. Crear el sitio Blogdown

En la consola ejecuta:

```{r}
blogdown::new_site()
```

Blogdown creará la estructura inicial del sitio.

Para visualizarlo:

```{r}
blogdown::serve_site()
```

Deberá aparecer una vista previa del sitio en RStudio o en el navegador.

---

# 5. Estructura básica del proyecto

La estructura puede variar ligeramente dependiendo del tema y de la versión de Hugo, pero será similar a:

```{text}
nycflights-site/
│
├── content/
│   ├── about.md
│   └── post/
│
├── static/
│
├── themes/
│
├── config.yaml
├── index.Rmd
├── netlify.toml
└── nycflights-site.Rproj
```

## content/

Contiene páginas y publicaciones.

## static/

Contiene recursos que se publicarán directamente, por ejemplo:

* imágenes
* PDFs
* archivos HTML

## themes/

Contiene el tema visual utilizado por Hugo.

## config.yaml

Contiene la configuración general del sitio.

## netlify.toml

Indica a Netlify cómo construir el sitio.

> Si el archivo index.Rmd de la raíz indica que no debe modificarse, no es necesario editarlo.

---

# 6. Modificar la página About

Abre:

```{text}
content/about.md
```

Por ejemplo:

```{markdown}
---
title: "Acerca del proyecto"
---

## NYC Flights

Este sitio presenta diferentes análisis sobre los vuelos con salida de Nueva York durante 2013.

Los datos provienen del paquete `nycflights13`.

## Objetivo

Explorar patrones relacionados con los retrasos y comunicar los principales hallazgos mediante diferentes productos analíticos.

## Metodología

Los resultados se generan reproduciblemente utilizando R.

Los patrones observados representan asociaciones y no implican necesariamente relaciones causales.
```

Guarda el archivo.

---

# 7. Modificar la configuración del sitio

Abre:

```{text}
config.yaml
```

Cambia el titulo y otros parámetros que te interesen. No reemplaces todo el archivo. Modifica solamente los parámetros necesarios.

---

# 8. Crear un post con código R

Ejecuta:

```{r}
blogdown::new_post(
  title = "Los retrasos aumentan conforme avanza el día",
  ext = ".Rmd"
)
```

Blogdown creará una carpeta dentro de:

```{text}
content/post/
```

Por ejemplo:

```{text}
content/
└── post/
    └── los-retrasos-aumentan-conforme-avanza-el-dia/
        └── index.Rmd
```

Un ejemplo de contenido sería:

```{markdown}
---
title: "Los retrasos aumentan conforme avanza el día"
---

{r setup, include=FALSE}
library(dplyr)
library(ggplot2)
library(nycflights13)


## ¿Existe un patrón durante el día?

Calculamos el retraso promedio según la hora programada de salida.

{r}
delay_hour <- flights |>
  filter(!is.na(dep_delay)) |>
  group_by(hour) |>
  summarise(
    retraso_promedio = mean(dep_delay),
    vuelos = n(),
    .groups = "drop"
  )


```{r}
ggplot(delay_hour, aes(hour, retraso_promedio)) +
  geom_line() +
  geom_point() +
  labs(
    title = "Los retrasos tienden a aumentar durante el día",
    x = "Hora programada",
    y = "Retraso promedio (min)"
  ) +
  theme_minimal()


## Interpretación

El retraso promedio presenta un patrón creciente conforme avanza el día.

Este patrón representa una asociación observada en los datos y no demuestra por sí mismo causalidad.
```

---

# 9. Incorporar un Flexdashboard existente

Si ya tienes un Flexdashboard generado como HTML, puedes incorporarlo al sitio.

Supongamos que tienes:

```{text}
flexdashboard_nycflights.html
```

Crea:

```{text}
static/dashboard/
```

Copia el archivo HTML y renómbralo:

```{text}
static/dashboard/index.html
```

La estructura será:

```{text}
static/
└── dashboard/
    └── index.html
```

Podrás enlazarlo desde cualquier página con:

```{markdown}
[Explorar dashboard](/dashboard/)
```

---

# 10. Revisar el sitio localmente

Durante el desarrollo utiliza:

```{r}
blogdown::serve_site()
```

Si realizas cambios importantes, como cambiar el tema, reinicia el servidor:

```{r}
blogdown::stop_server()
```

y vuelve a ejecutar:

```{r}
blogdown::serve_site()
```

Comprueba:

* portada
* página About
* posts
* gráficas
* enlaces

---

# 11. Construir el sitio completo

Antes de publicar, detén el servidor:

```{r}
blogdown::stop_server()
```

Construye el sitio completo:

```{r}
blogdown::build_site()
```

Esto generará o actualizará:

```{text}
public/
```

La carpeta `public/` contiene el sitio HTML final generado por Hugo.

Finalmente `build_site()` funciona como una validación final de que el sitio puede construirse correctamente.

---

# 12. Configurar Git 

Git necesita saber quién está creando los commits. Esto se tiene que hacer solo una vez en tu computadora. Puedes hacerlo directamente desde R. Abre la pestaña:

```text
Terminal
```

de RStudio y ejecutar:

```bash
git config --global user.name "Tu Nombre"
```

después:

```bash
git config --global user.email "correo@example.com"
```

usando el mismo correo asociado a la cuenta de GitHub. Podemos comprobar la configuración con:

```bash
git config --global user.name
git config --global user.email
```

Estas configuraciones no son nuestras credenciales ni nuestra contraseña de GitHub. sirven para indicar quién está creando los commits.

---

# 13. Crear un Personal Access Token en GitHub

Si trabajamos con GitHub mediante HTTPS, necesitamos autenticarnos. GitHub no utiliza la contraseña normal de la cuenta para hacer operaciones como `git push`. Podemos usar un **Personal Access Token** (**PAT**).

En GitHub ir a:

```text
Foto de perfil → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token → Generate new token (classic)
```

Asignar un nombre descriptivo, elegir una fecha de expiración. Para trabajar con repositorios normalmente necesitaremos seleccionar el scope:

```text
repo
```

Finalmente:

```text
Generate token
```

GitHub mostrará una cadena similar a:

```text
ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Copiar el token inmediatamente.

OBSERVACIÓN: El token funciona como una contraseña. Nunca debe aparecer en ningún archivo y nunca debe subirse al repositorio.

---

# 14. Guardar el token mediante `gitcreds`

En la consola de R, instalar `gitcreds` si todavía no está instalado:

```r
install.packages("gitcreds")
```

Después ejecutar:

```r
gitcreds::gitcreds_set()
```

R preguntará:

```text
Enter new password or token:
```

Pegar el token generado en GitHub y presionar Enter. Podemos comprobar que existe una credencial con la misma función:

```r
gitcreds::gitcreds_get()
```

---

# 15. Crear .gitignore

Desde R:

```{r}
file.create(".gitignore")
```

Abre el archivo y agrega:

```{text}
.Rproj.user
.Rhistory
.RData
.Ruserdata
public/
.DS_Store
```

La carpeta:

```{text}
public/
```

no necesita subirse a GitHub porque Netlify la volverá a construir.

---

# 16. Inicializar Git

Desde la Terminal de RStudio:

```{bash}
git init
```

Después agrega los archivos:

```{bash}
git add .
```

Crea el primer commit:

```{bash}
git commit -m "Create Blogdown site"
```

---

# 17. Crear un repositorio en GitHub

En GitHub selecciona:

```{text}
+ → New repository
```

En:

```{text}
Repository name
```

escribe, por ejemplo:

```{text}
nycflights-blogdown
```

Sin cambiar la configuración. 

El repositorio debe quedar vacío porque el proyecto ya existe localmente.

---

# 18. Conectar el repositorio local con GitHub

GitHub mostrará una dirección parecida a:

```{text}
https://github.com/USUARIO/nycflights-blogdown.git
```

Desde la Terminal de RStudio ejecuta:

```{bash}
git remote add origin https://github.com/USUARIO/nycflights-blogdown.git
```

---

# 19. Cambiar la rama principal a main

Ejecuta:

```{bash}
git branch -M main
```

---

# 20. Realizar el primer push

Ejecuta:

```{bash}
git push -u origin main
```

---

# 21. Comprobar GitHub

Regresa a:

```{text}
github.com
```

Abre tu repo, actualiza la página. Deberías ver ahora todos los archivos del proyecto.

---

# 22. Crear una cuenta en Netlify

Entra a:

https://www.netlify.com

Selecciona:

Sign up

Puedes registrarte utilizando tu cuenta de GitHub.

---

# 23. Importar el proyecto desde GitHub

Desde el panel de Netlify selecciona:

```{text}
Add new project → Import an existing project
```
 
Selecciona `GitHub`. Autoriza a Netlify para acceder a tus repositorios.

Selecciona `nycflights-blogdown`

---

# 24. Revisar la configuración de despliegue

Comprueba:

```{text}
Branch to deploy: main
Build command: hugo
Publish directory: public
```

Si existe netlify.toml, Netlify normalmente leerá esos valores automáticamente.

---


# 25. Publicar

Selecciona `Deploy site`

Netlify hará automáticamente:

```{text}
GitHub
   ↓
descarga el proyecto
   ↓
instala la versión configurada de Hugo
   ↓
ejecuta hugo
   ↓
genera public/
   ↓
publica public/
```

Sólo el contenido del directorio de publicación es desplegado por Netlify.

---


# 26. Abrir el sitio publicado

Cuando el despliegue termine, Netlify mostrará una dirección similar a:

```{text}
https://nombre-aleatorio.netlify.app
```

Haz clic en esa dirección.

Tu sitio ya está publicado.

---

# 27. Flujo para realizar cambios posteriormente

Una vez configurados GitHub y Netlify, no es necesario volver a crear el proyecto ni volver a configurar Netlify.

El flujo cotidiano será:

1. Editar el sitio en local 
2. Revisar durante el trabajo `blogdown::serve_site()`
3. Construir el sitio completo `blogdown::build_site()`para verificar que todo funciona localmente
4. Enviar cambios a Git

En Terminal:

git status
git add .
git commit -m "Update flight analysis"
git push

Netlify detectará el nuevo commit, reconstruirá el sitio y publicará automáticamente la nueva versión.

---

# 28. Cambiar el tema

Los temas de Hugo pueden consultarse en:

https://themes.gohugo.io

Por ejemplo, para instalar PaperMod:

```{r}
blogdown::install_theme(
  "adityatelange/hugo-PaperMod"
)
```

Revisa después `config.yaml` y comprueba el valor de `theme`.

Después reinicia el servidor:

blogdown::stop_server()
blogdown::serve_site()

Cuando esté correcto:

blogdown::stop_server()
blogdown::build_site()

Después:

git add .
git commit -m "Change site theme"
git push

Netlify publicará automáticamente la nueva versión.
