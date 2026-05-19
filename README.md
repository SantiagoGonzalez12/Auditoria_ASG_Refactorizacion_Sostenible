# Auditoría ASG y Refactorización Sostenible
## Escuela Española de Vela – [escuela-vela.com](https://www.escuela-vela.com)

**Módulo:** Sostenibilidad Aplicada al Sistema Productivo · DAM Superior  

**Autores:** Álvaro López de San Román · Santiago González González

**Centro:** FP Superior - Cámara De Comercio De Sevilla

**Trimestre:** 3º Trimestre

---

## Índice
1. [Contexto y empresa auditada](#1-contexto-y-empresa-auditada)
2. [Fase 1 – Dimensión Ambiental (A)](#2-fase-1--dimensión-ambiental-a)
3. [Fase 2 – Dimensión Social y Accesibilidad (S)](#3-fase-2--dimensión-social-y-accesibilidad-s)
4. [Fase 3 – Dimensión de Gobernanza y Ética (G)](#4-fase-3--dimensión-de-gobernanza-y-ética-g)
5. [Fase 4 – Propuesta de Refactorización Green Coding](#5-fase-4--propuesta-de-refactorización-green-coding)
6. [Resumen comparativo Antes | Después](#6-resumen-comparativo-antes--después)
7. [Herramientas utilizadas](#7-herramientas-utilizadas)
8. [Fuentes y referencias](#8-fuentes-y-referencias)

---

## 1. Contexto y empresa auditada

**Escuela Española de Vela (EEV)** es una empresa de turismo activo con sede en Islantilla (Huelva), con más de 30 años de experiencia en actividades náuticas: Windsurf, Wing Foil, Catamarán, Paddlesurf, Kayak, y campamentos de verano. Su web corporativa ([escuela-vela.com](https://www.escuela-vela.com)) es el principal canal de captación de clientes y de difusión de sus servicios.

El sitio está construido con **WordPress + Elementor 4.0.7**, alojado bajo el dominio `escuela-vela.com`, cuenta con versiones en 5 idiomas (ES, EN, PT, DE, IT, FR) y dispone de tienda online integrada.

Como **Junior GreenOps Developers**, nuestro encargo es realizar una auditoría completa bajo el marco ASG (Ambiental, Social, Gobernanza), basándonos en los principios del Green Software Engineering y los estándares WCAG 2.2 del W3C.


## 2. Fase 1 – Dimensión Ambiental (A)

### 2.1 Medición inicial de la huella de carbono

Para obtener una estimación objetiva del impacto ambiental se han utilizado las siguientes herramientas:

#### Lighthouse
![lighthouse-1](imagenes/lighthouse-1.png) 

![lighthouse-2](imagenes/lighthouse-2.png)

#### Website Carbon Calculator
![Website_Carbon_Calculator](imagenes/Website_Carbon_Calculator.png)

#### PageSpeed Insights
![PageSpeed_Insights](imagenes/PageSpeed_Insights.png)

| Herramienta | Métrica obtenida |
|---|---|
| **Website Carbon Calculator** | ~0,80 g de CO₂ por visita · Peor que el 60% de páginas testadas |
| **Lighthouse (Performance)** | Puntuación estimada: 56 / 100 |
| **PageSpeed Insights** | LCP (Largest Contentful Paint): ~5,2 s · TBT (Total Blocking Time): ~370 ms |
| **Chrome DevTools – Network** | Peso total: ~4,2 MB · Peticiones HTTP: +65 |

**¿Por qué importa esta cifra?**  
Según el informe de The Shift Project (2023), el sector digital representa ya el **4% de las emisiones globales de gases de efecto invernadero**, esto es una cifra significante en el cual se duplica cada cuatro años y si seguimos asi no vamos a poer hacer nada cuando sea demasiado tarde. Una web que emite 0,80 g de CO₂ por visita, con 10.000 visitas mensuales, genera aproximadamente unas **96 kg de CO₂ al año** equivalente a conducir un coche de gasolina unos 450 km estas comparaciones son las que nos hacen dar un choque de realidad.

---

### 2.2 Identificación de los 3 recursos más pesados (Bloatware)

Análisis realizado con la pestaña **Network** de Chrome DevTools, filtrando por tamaño descendente:
#### Recurso 1 — Imágenes de galería en JPG/PNG sin optimizar

La galería principal contiene imágenes directamente exportadas de dispositivos móviles, como `IMG_9883-scaled.jpg` o `IMG_6307-1024x682.png`, con pesos que superan los 800 KB por imagen lo cual hace que sea mas lento el tiempo de carga. Ninguna está en formato moderno (WebP/AVIF) ni tiene dimensiones adaptadas al tamaño en pantalla.

```
Recurso: /wp-content/uploads/2018/11/IMG_9883-scaled.jpg
Tipo: image/jpeg
Peso: ~820 KB
```

---

#### Recurso 2 — Bundles de Elementor (CSS + JavaScript)

Elementor carga múltiples hojas de estilo y archivos JavaScript de forma global, independientemente de si los módulos que los requieren aparecen o no en la página actual. Esto genera lo que se conoce como **CSS bloat**: estilos descargados que nunca se aplican.

---

####  Recurso 3 — iFrame de cámara en vivo con autoplay

La página principal esta dentro una cámara en tiempo real del proveedor externo `ipcamlive.com` con los parámetros `autoplay=1` y `mute=1`. Esto significa que la cámara comienza a transmitir vídeo en el momento en que el usuario carga la página, aunque no tenga ningún interés en verla eso no tiene sentido esta iniciando una cosa que alomejor el usuario no tiene sentido.

```html
<!-- Código actual: carga automática e inmediata del stream de vídeo -->
<iframe
  src="https://g0.ipcamlive.com/player/player.php?alias=eevislantilla&skin=white&autoplay=1&mute=1"
  width="640"
  height="360">
</iframe>
```

Adicionalmente, hay un segundo iframe embebido del widget meteorológico de `weatherlink.com` y un tercer iframe de Google Maps al final de la página, que se cargan los tres de forma simultánea y sin lazy loading.

---

### 2.3 ¿Sufre la web de "inflación de software"?

La respuesta es sí, de forma clara. La web de la Escuela Española de Vela EEV en su web presenta múltiples síntomas de subidas de software o como se puede decir tecnicamnete *software bloat*:

1. **Constructor de páginas pesado:** Elementor es conocido por generar HTML innecesariamente profundo y cargar recursos que no se usan en cada página concreta.

2. **Imágenes no optimizadas:** Las fotografías tienen nombres de fichero del tipo `IMG_X`, indicativos de que se subieron directamente sin procesamiento previo eso lo que consta que no esta optimizada tal como debería de ser para no pesara demasiado.

3. **GIF animado en el footer** (`logo-visas-gif.gif`): el formato GIF tiene una eficiencia de compresión muy inferior a WebP animado o SVG. Este fichero pesa varias veces más de lo que debería ademas que el usuario cuando entra ya tiene la pagina en movimiento que es decir que cuando el suuario ya se de la opagina principal el GIF sigue estando en movimiento consumiendo carga.

4. **Iframes de terceros sin lazy loading:** Tres iframes externos (cámara, meteorología, mapa) como hemos mencionado antes de tal forma que se cargan al arrancar aunque el usuario solo visite la cabecera de la página.

5. **Plugin de traducción (TranslatePress)** este plugin de WordpRess lo que hace es ser conocido es que se carga flags e inyecta scripts en todas las páginas, aunque el usuario no cambie de idioma.

> **Conclusión ambiental:** La web consume más del doble de lo que debería.

---

## 3. Fase 2 – Dimensión Social y Accesibilidad (S)

### 3.1 Test de accesibilidad

Herramientas utilizadas: **WAVE Web Accessibility Evaluation Tool** y **Lighthouse**. 
> Lo hemos utilizado antes en los primeros puntos

| Herramienta | Resultado |
|---|---|
| **Lighthouse Accessibility** | ~65 / 100 |
| **WAVE** | 4+ errores graves aparece en rojo, 8+ alertas (amarillo) |

---

### 3.2 Problema grave 1 - Atributos _alt_ ausentes o no descriptivos
Todas las imágenes de la web utilizan como atributo _alt_ el nombre del fichero tal cual fue subido al servidor, o directamente tienen el atributo vacío.

Un lector de pantalla como *NVDA* o *JAWS*, usado por personas con discapacidad visual, leerá literalmente "IMG guión 9883 guión scaled punto png" en lugar de describir el contenido de la imagen.

```html
<!--  ANTES: el alt describe el fichero, no el contenido -->

<img src="/wp-content/uploads/2018/10/cropped-eev-1.png"
     alt="cropped-eev-1.png">
     <!--  En el lector de pantalla dirá "cropped-eev-1.png" — esto no aporta nada de información -->

<img src="/wp-content/uploads/2018/11/IMG_9883-scaled.jpg"
     alt="">
     <!--  Alt vacío en imagen de contenido: el lector de pantalla la ignorará -->

<img src="/wp-content/uploads/2022/07/descarga-150x150.jpg"
     alt="">
     <!--  Imagen sin ninguna descripción ya que es demaisado reciente dentro de la web, lo que dice que aun no lo han actualizado la información dentro de la pagina -->


<!-- DESPUÉS: el alt describe de forma clara el contenido que hay dentro -->

<!-- Logo principal esto haraimos que funciones como el inicio osea main -->
<a href="https://www.escuela-vela.com">
  <img src="/assets/img/logo-eev.webp"
       alt="Escuela Española de Vela – Ir a la página de inicio"
       width="200" height="80">
</a>

<!-- Imagen de galería de contenido -->
<img src="/assets/img/clases-catamaran-islantilla.webp"
     alt="Grupo de alumnos practicando catamarán en la playa de Islantilla"
     loading="lazy"
     width="1024" height="682">

<!-- Imagen de tarjeta de actividad -->
<img src="/assets/img/wing-foil-portada.webp"
     alt="Instructor de Wing Foil demostrando la técnica sobre el agua en Islantilla"
     loading="lazy"
     width="150" height="150">
```

---

### 3.3 Problema grave 2 - Bajo contraste de texto sobre imágenes
Los encabezados principales del _hero_ (`ESCUELA ESPAÑOLA DE VELA`, `Islantilla`) se muestran con un texto blanco directamente en la imagen de colores. En varias partes de la imagen, el contraste entre el texto y esta es muy pobre, por lo que no se diferencian muy bien y dan lugar a confusión.

```css
/* ANTES: texto blanco puro sobre imagen de fondo variable */

.hero-title {
  color: #ffffff;        
  font-size: 48px;
  font-weight: bold;
}

/* DESPUÉS: Usamos varias técnicas para garantizar contraste mayor en todos los fondos posibles */

.hero-title {
  color: #ffffff;

  /* Técnica 1: sombra de texto */
  text-shadow:
    0 1px 3px rgba(0, 0, 0, 0.85), /* sombra principal oscura */
    0 2px 8px rgba(0, 0, 0, 0.60); /* sombra difusa de apoyo */

  font-size: 48px;
  font-weight: 700;
}

/* Técnica 2: overlay oscuro semitransparente */
.hero-section::before {
  content: "";
  position: absolute;
  inset: 0; /* cubre toda la sección */
  background: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.55) 0%, /* más oscuro arriba */
    rgba(0, 0, 0, 0.20) 60%, /* se aclara hacia abajo */
    rgba(0, 0, 0, 0.00) 100%
  );
  z-index: 1; /* por encima de la imagen */
  pointer-events: none; /* no bloquea clics */
}

/* El texto debe estar por encima del overlay */
.hero-title {
  position: relative;
  z-index: 2;
  color: #ffffff;
}
```

---
## 5. Fase 4 – Propuesta de Refactorización Green Coding

### 5.1 Optimización de activos Dimensión A

#### 5.1.1 Conversión de imágenes a formatos modernos

| Situación actual | Solución propuesta | Ahorro estimado |
|---|---|---|
| Imágenes JPG de galería | Convertir a **WebP** | 30–50% menos peso |
| Logos y gráficos PNG con transparencia | Convertir a WebP con canal alfa | 25–35% menos peso |
| GIF animado (`logo-visas-gif.gif`) | Sustituir por SVG estático | 70–90% menos peso |
| Imágenes de fondo decorativas | Formato AVIF | 50–60% menos peso |

**Proceso de conversión recomendado:**

```bash
cwebp -q 80 IMG_9883-scaled.jpg -o clases-catamaran-islantilla.webp

# Conversión de toda una carpeta con un bucle Bash
for file in *.jpg *.png; do
  cwebp -q 80 "$file" -o "${file%.*}.webp"
done
# Para imágenes con transparencia, añadir el flag -alpha_q```

**Implementación con `<picture>` para compatibilidad total de navegadores:**

```html

<picture>
  <!-- Para navegadores modernos: AVIF (máxima compresión) -->
  <source
    srcset="/assets/img/clases-catamaran-islantilla.avif"
    type="image/avif">

  <!-- Para navegadores con soporte WebP pero sin AVIF -->
  <source
    srcset="/assets/img/clases-catamaran-islantilla.webp"
    type="image/webp">

  <!-- Fallback: JPG para navegadores antiguos. -->
  <img
    src="/assets/img/clases-catamaran-islantilla.jpg"
    alt="Grupo de alumnos practicando catamarán en la playa de Islantilla"
    loading="lazy"
    decoding="async"
    width="1024"
    height="682">
</picture>
```

---
#### 5.1.2 Implementación de Lazy Loading

El **Lazy Loading** esto significa,  que las imágenes que fuera del área visible no se descarguen hasta que el usuario se aproxima a ellas. eso reduce el peso de la pagina
```html

<!--  Antes: podemos observar todas las imágenes se descargan al cargar      -->

<img src="/wp-content/uploads/2019/04/catamaran.png" alt="">
<img src="/wp-content/uploads/2019/04/paddle.png" alt="">
<img src="/wp-content/uploads/2019/04/summer_camp.png" alt="">

<!-- Despues: podemos observar en este codigo con el anterior que se descargan al acercarse al viewport   -->

<img
  src="/assets/img/catamaran-actividad.webp"
  alt="Actividad de catamarán disponible en la Escuela Española de Vela"
  loading="lazy"
  decoding="async"
  width="150"
  height="150">

<img
  src="/assets/img/paddlesurf-actividad.webp"
  alt="Alquiler de tablas de paddlesurf en Islantilla"
  loading="lazy"
  decoding="async"
  width="150"
  height="150">

<!--
  Awui vamos hacer un marca especila en donde las imágenes "above the fold" osea visibles sin scroll
  no deberian tener lazy loading, ya que reatrasa la carga de lso eementos de las paginas 
  El logo y la imagen hero se cargan con prioridad alta:
-->
<img
  src="/assets/img/logo-eev.webp"
  alt="Escuela Española de Vela – Inicio"
  loading="eager"
  fetchpriority="high"
  width="200"
  height="80">
```

---

#### 5.1.2 Scripts externos: eliminar, aplazar ...

| Script / Recurso | Acción propuesta | Ahorro estimado |
|---|---|---|
| **Elementor CSS/JS** | Activar modo "External File" + eliminar CSS no usado con PurgeCSS | ~400 KB |
| **Google Fonts** | Auto-hospedar con `@font-face` local | Elimina 1 petición DNS externa |
| **TranslatePress** | Cargar scripts solo cuando el usuario interactúa con el selector de idioma | ~80 KB por visita |
| **Google Maps** | Añadir `loading="lazy"` | No se carga hasta que el usuario hace scroll hasta el mapa |
| **Widget meteorológico Weatherlink** | Añadir `loading="lazy"` al iframe | Igual, está fuera del viewport inicial |

```html
<!-- Antes en esta petición se utilizaba el dominio externo de Google en cada carga -->
<link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;700&display=swap" rel="stylesheet">

<!-- Despues esta fuente esta servida desde el propio servidor de la escuela de vela -->
<style>
  @font-face {
    font-family: 'Open Sans';
    font-style: normal;
    font-weight: 400;
    font-display: swap;     
    src: url('/assets/fonts/open-sans-regular.woff2') format('woff2');
  }

  @font-face {
    font-family: 'Open Sans';
    font-style: normal;
    font-weight: 700;
    font-display: swap;
    src: url('/assets/fonts/open-sans-bold.woff2') format('woff2');
  }
</style>


<!-- Iframe de mapa con lazy loading , osea ell mapa que está en el footer, no tiene sentido cargarlo ya que no lo pide el usuario     -->

<!--  Antes el mapa carga siempre, esté visible o no -->
<iframe src="https://maps.google.com/maps?q=EEV+Islantilla&output=embed"
        width="400" height="300"></iframe>

<!-- Despues para eviatr lazy loading nativo del iframe + título accesible -->
<iframe
  src="https://maps.google.com/maps?q=EEV+Islantilla&output=embed"
  width="400"
  height="300"
  loading="lazy"
  title="Ubicación de la Escuela Española de Vela en Islantilla, Huelva"
  referrerpolicy="no-referrer-when-downgrade">
</iframe>
```

---

---

## 6. Herramientas utilizadas en el proyecto de la Escuela Española de Vela

| Herramienta | Uso en la auditoría | Enlace |
|---|---|---|
| **Website Carbon Calculator** | Medición de g CO₂ por visita | [websitecarbon.com](https://www.websitecarbon.com) |
| **Google Lighthouse** | Performance, Accessibility, Best Practices, SEO | [Chrome DevTools](https://developer.chrome.com/docs/lighthouse/overview/) |
| **WAVE Evaluation Tool** | Errores y alertas de accesibilidad WCAG 2.2 | [wave.webaim.org](https://wave.webaim.org) |
| **PageSpeed Insights** | Métricas Core Web Vitals reales | [pagespeed.web.dev](https://pagespeed.web.dev) |
| **Chrome DevTools – Network** | Análisis de recursos y peso de la página | Incluido en Chrome/Edge |
| **WebAIM Contrast Checker** | Verificación de ratios de contraste WCAG | [webaim.org/resources/contrastchecker](https://webaim.org/resources/contrastchecker/) |
| **cwebp / ffmpeg** | Conversión de imágenes a WebP y AVIF | [developers.google.com/speed/webp](https://developers.google.com/speed/webp) |
