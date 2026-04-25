# 🏰 CastleQuest — Castillos del Mundo

> Aplicación web semántica para explorar castillos de todo el mundo, construida con WikiData, SPARQL y Leaflet.js.

**Autor:** David Castillo Serrano  
**Asignatura:** Web Semántica Social  
**IA utilizada:** [Claude](https://www.anthropic.com/claude) de Anthropic

---

## 📸 Capturas de pantalla

*[Añade aquí una captura del modal inicial con el formulario de búsqueda]*

---

*[Añade aquí una captura del mapa con los marcadores de castillos desplegados]*

---

*[Añade aquí una captura de un pop-up de castillo con imagen y descripción]*

---

*[Añade aquí una captura del menú lateral izquierdo abierto]*

---

## 🎯 ¿Qué es CastleQuest?

CastleQuest es una aplicación web estática (sin backend) que permite consultar los castillos registrados en la base de datos de [WikiData](https://www.wikidata.org) filtrando primero por país y luego por provincia o región. Los resultados se visualizan sobre un mapa interactivo gracias a la biblioteca [Leaflet.js](https://leafletjs.com), mostrando la posición geográfica de cada castillo junto a su nombre, descripción e imagen cuando están disponibles.

---

## 🛠️ Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| **HTML5 / CSS3 / JavaScript** | Base de la aplicación |
| **WikiData SPARQL API** | Fuente de datos de conocimiento |
| **Leaflet.js 1.9.4** | Mapa interactivo |
| **CartoDB Dark Matter** | Capa de tiles del mapa (estilo oscuro) |
| **Google Fonts — Cinzel + Crimson Text** | Tipografía de estética medieval |
| **localStorage** | Caché de consultas en el navegador |
| **Claude (Anthropic)** | IA utilizada para el desarrollo |

---

## ✨ Funcionalidades

### Búsqueda de castillos
- **Por provincia/región:** selecciona un país → selecciona una provincia → consulta los castillos de esa zona.
- **Por país completo:** obtén hasta 500 castillos de un país entero.
- **Todo el mundo:** carga hasta 1.000 castillos de WikiData con un solo clic (consulta pesada, puede tardar).

### Mapa interactivo
- Mapa en pantalla completa con fondo oscuro (CartoDB Dark Matter).
- Marcadores personalizados con icono de castillo 🏰.
- Al pulsar un marcador aparece un pop-up con: nombre, descripción, imagen, coordenadas y enlace a Google Maps.
- El mapa hace zoom automático para encuadrar todos los castillos de la búsqueda.

### Menú lateral (esquina superior izquierda)
- **📜 Consultas Recientes:** historial de las últimas 10 búsquedas realizadas.
- **🔍 Nueva Consulta:** reabre el modal de búsqueda.
- **✨ Créditos:** información sobre el proyecto y sus fuentes.

### Caché inteligente
- Todas las consultas se almacenan en `localStorage` del navegador.
- Si se repite la misma búsqueda, los datos se sirven desde el caché local sin volver a llamar a WikiData.
- Las claves siguen el patrón `cq_countries`, `cq_prov_{QID}`, `cq_cast_city_{QID}`, etc.

---

## 🔎 Consultas SPARQL explicadas

La aplicación realiza hasta cinco tipos de consultas al endpoint público de WikiData:
`https://query.wikidata.org/sparql`

### 1. Países del mundo
Obtiene todas las entidades marcadas como `instancia de (P31) → país (Q6256)`:
```sparql
SELECT ?Paises ?PaisesLabel WHERE {
  ?Paises wdt:P31 wd:Q6256.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
}
```

### 2. Provincias/divisiones administrativas de un país
Obtiene las primeras divisiones administrativas (`Q10864048`) pertenecientes al país elegido:
```sparql
SELECT DISTINCT ?Provincia ?ProvinciaLabel WHERE {
  ?Provincia wdt:P31/wdt:P279* wd:Q10864048;
             wdt:P17 wd:{QID_PAIS}.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
}
ORDER BY ?ProvinciaLabel
```

### 3. Castillos de una ciudad/provincia
Obtiene castillos (`Q23413`) localizados dentro de la región seleccionada, incluyendo coordenadas e imagen opcional:
```sparql
SELECT DISTINCT ?castilloLabel ?castilloDescription ?lat ?lon ?img WHERE {
  ?castillo wdt:P31 wd:Q23413;
            wdt:P131* wd:{QID_CIUDAD}.

  ?castillo p:P625/psv:P625 [
    wikibase:geoLatitude ?lat;
    wikibase:geoLongitude ?lon
  ].

  OPTIONAL { ?castillo wdt:P18 ?img. }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
}
ORDER BY ?castilloLabel
```

### 4. Castillos de un país completo
Igual que la anterior pero filtrando por `wdt:P17` (país) en lugar de `wdt:P131*` (localizado en), con `LIMIT 500`.

### 5. Todos los castillos del mundo
Sin filtro geográfico, con `LIMIT 1000` para no sobrecargar el endpoint.

---

## 📁 Estructura del proyecto

```
castlequest/
│
├── index.html     ← Aplicación completa (HTML + JavaScript embebido)
├── styles.css     ← Estilos (diseño medieval oscuro, glassmorphism, animaciones)
└── README.md      ← Este archivo
```

---

## 🚀 Cómo desplegar en GitHub Pages

1. Crea un repositorio en GitHub (puede ser público o privado con GitHub Pro).
2. Sube los tres archivos (`index.html`, `styles.css`, `README.md`) a la rama `main`.
3. Ve a **Settings → Pages → Source** y selecciona la rama `main`, carpeta raíz `/`.
4. GitHub generará una URL del tipo `https://{tu-usuario}.github.io/{nombre-repo}/`.
5. Accede a esa URL y la aplicación estará lista.

> **Nota:** la aplicación no requiere servidor ni instalación de dependencias. Todo funciona desde el navegador del usuario.

---

## ⚙️ Cómo usar localmente

Simplemente abre `index.html` en cualquier navegador moderno. No requiere instalación ni servidor local.

> Si el navegador bloquea las peticiones CORS al ejecutar desde `file://`, puedes arrancar un servidor simple con:
> ```bash
> python3 -m http.server 8080
> ```
> y acceder a `http://localhost:8080`.

---

## 📝 Proceso de desarrollo

El desarrollo de esta aplicación siguió los siguientes pasos, definidos en colaboración con la IA Claude (Anthropic):

1. **Diseño de la arquitectura:** aplicación completamente estática (HTML + CSS + JS), sin backend, desplegable en GitHub Pages.
2. **Definición de las consultas SPARQL** sobre WikiData para obtener países, divisiones administrativas y castillos con coordenadas.
3. **Sistema de caché** en `localStorage` para evitar consultas repetidas a la API.
4. **Integración de Leaflet.js** con tiles oscuros de CartoDB y marcadores personalizados.
5. **Diseño visual** con estética medieval oscura: fuente Cinzel, paleta dorada y azul noche, glassmorphism, animaciones CSS.
6. **Flujo de usuario:** modal inicial → selección de país → selección de provincia → consulta → marcadores en el mapa.
7. **Funcionalidades adicionales:** historial de consultas recientes, búsqueda por país completo y búsqueda global.

---

## 📄 Licencia

Proyecto académico de la asignatura **Web Semántica Social**.  
Los datos provienen de [WikiData](https://www.wikidata.org) bajo licencia [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
