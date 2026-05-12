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
