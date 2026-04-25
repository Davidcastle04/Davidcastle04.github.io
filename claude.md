# Pasos a seguir para realizar búsquedas en WikiData y representarlas en un mapa de Leaflet

> [!TIP] 
> Antes empezar traduce todo este markdown al ingles y piensa en el idioma que más fácil te resulte. La pagina html que necesito tiene que estar en español.

# Objetivos para obtener una buena pagina web con las prácticas que te dare

> [!IMPORTANT]
> Mi pagina sirve para consultar los castillos que hay en el mundo filtrando por países y posteriormente por ciudades, por ello quiero que aparezca el mapa de Leaflet y que pregunte porque país queremos ver , después preguntar las ciudades de ese país y una vez hacer el  

1. Las consultas de sparql te las dare yo resultas, ya que estás funcionan y devuelven lo que yo quiero. Asi que no tienes que preocuparte por resolver esta parte
2. La estética de la página web tiene que ser web response y que sea agradable
3. Mi página web sirve como explique antes en la nota para mostrar los castillos de los diferentes paises del mundo filtrando por ciudad, donde mostraremos la posición en el mapa , una foto si esta en la BD de Wikidata, y la pequeña descripción. Este Pop-Up quiero que sea bonito asi que hazlo con un css asi chulo
---

> [!IMPORTANT] 
> Es importante seguir este orden.

**Como quiero que hagas las query de Sparql**
1. Preguntar cuales son los países que hay en el mundo.

Paises del Mundo con etiqueta para poder llamar a otras query´s

 `
SELECT ?Paises  ?PaisesLabel WHERE {
 ?Paises wdt:P31 wd:Q6256; 
 SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". } 
}`

Aqui guardaremos en un diccionario las etiquetas de Paises y el nombre en español de los paises que sera donde despues mostraremos al usuario de los paises que puede consultar por la ciudad de este

2. Preguntar por las Provincias del Pais X
    
  `SELECT Provincia ?ProvinciaLabel WHERE {
   ?Provincia wdt:P31 wd:PAIS-X; # donde cambiaremos esto dependiendo de cada país
   SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }     
  }`

Aqui guardaremos en un diccionario las etiquetas de la provincias o ciudades de cada pais  y el nombre en español de los ciudades que sera donde despues mostraremos al usuario los castillos de esta zona

3. Aquí obtenemos las cordenadas , ademas de descripciones , imagenes y nombres de los castillos de una determinada ciudad 

  `SELECT DISTINCT?castilloLabel ?castilloDescription ?lat ?lon ?img WHERE {
      ?castillo wdt:P31 wd:Q23413;
                wdt:P131* wd:CIUDAD-PROVINCIA.
    
      ?castillo p:P625/psv:P625 [
        wikibase:geoLatitude ?lat;
        wikibase:geoLongitude ?lon
      ].
    
      OPTIONAL { ?castillo wdt:P18 ?img. }
    
      SERVICE wikibase:label { bd:serviceParam wikibase:language "es,en". }
    }
    ORDER BY ?castilloLabel `


## Eficiencia de nuestro programa

Como no tenemos un backend sino que va a ser un html y un css que subiremos a github.io lo que haremos es guardar en diccionarios de las diferentes consultas que hacemos en el pc del usuario/ si quieres hazlo como una Storage Local Session por si el usuario vuelve a consultar la misma que no tengamos que llamar a la api de WikiData para obtener los mismos datos

## Diseño de la página Web

Quiero un diseño bonito e innovador donde el componente del mapa de leaflet sea en pantalla completa y aparezca al iniciar la pagina en mitad de la pantalla un componente con trasparencia azul oscuro donde ponga Consultemos los Castillos por el Mundo incluye el icono de castillo este 🏰 , después pregunta por que Pais queremos consultar , una vez seleccionado el país , nos tiene que aparecer las ciudades de ese país , a continuación tenemos que seleccionar la ciudad. Y que haya un botón de color verde morado con graduación para que se vea moderno que sea Consultar Castillos , (si quieres añade algún emoji en este botón como una lupa). 

Una vez le demos al botón seguimos estos pasos 
1. El pop-up desaparece apareciendo en el mapa los aguacates invertidos o clips seleccionando donde se encuentra cada castillo
2. Que en la esquina superior izquierda haya un menu donde aparezca lo siguiente 
2.1. Consultas Recientes
2.2. Nueva Consulta -> Abre el PopUp de buscar los castillos
2.3. Creditos
3. En la pantalla del pop-up inicial quiero que en la parte superior del pop-up aparezca un menu pequeño donde sea seleccionar ver todos los castillos de x país , ver todos los castillos


## Tambien quiero un README.md 

En este repositorio que me vas a dar me vas a dar un readme explicando los pasos que te di , que IA use , que funcionalidades tiene y que pongas mi nombre David Castillo Serrano, que esto es para una asignatura llamada Web semántica Social y que dejes huecos para que incluya fotografías de ejemplos de la aplicación

## Que quiero obtener de este proyecto 
1. Un html con el javascript dentro 
2. Un css con todo para que se vea bonito
3. El readme.md con todo corregido ortograficamente y lexicamente correcto.