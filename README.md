# ¿Qué es REST?
REST, REpresentational State Transfer, es un tipo de arquitectura de desarrollo web que se apoya totalmente en el estándar HTTP.

REST nos permite crear servicios y aplicaciones que pueden ser usadas por cualquier dispositivo o cliente que entienda HTTP, por lo que es increíblemente más simple y convencional que otras alternativas que se han usado en los últimos diez años como SOAP y XML-RPC.
Podríamos considerar REST como un framework para construir aplicaciones web respetando HTTP.

Por lo tanto REST es el tipo de arquitectura más natural y estándar para crear APIs para servicios orientados a Internet.

Existen tres niveles de calidad a la hora de aplicar REST en el desarrollo de una aplicación web y más concretamente una API que se recogen en un modelo llamado Richardson Maturity Model. Estos niveles son:

1. ## Uso correcto de URIs
    Cuando desarrollamos una web o una aplicación web, las URLs nos permiten acceder a cada uno de las páginas, secciones o documentos del sitio web.

    Cada página, información en una sección, archivo, cuando hablamos de REST, los nombramos como recursos.

    El recurso por lo tanto es la información a la que queremos acceder o que queremos modificar o borrar, independientemente de su formato.

    Las URL, Uniform Resource Locator , son un tipo de URI, Uniform Resource Identifier, que además de permitir identificar de forma única el recurso, nos permite localizarlo para poder acceder a él o compartir su ubicación.

    Una URL se estructura de la siguiente forma:
    ```
    {protocolo}://{dominio o hostname}[:puerto (opcional)]/{ruta del recurso}?{consulta de filtrado}
    ```
    **Las URIs no deben implicar acciones y deben ser únicas**

    Por ejemplo, la URI /facturas/234/editar sería incorrecta ya que tenemos el verbo editar en la misma.

    Para el recurso factura con el identificador 234, la siguiente URI sería la correcta, independientemente de que vayamos a editarla, borrarla, consultarla o leer sólo uno de de sus conceptos: /facturas/234

    **Las URIs deben ser independientes de formato**

    Por ejemplo, la URI /facturas/234.pdf no sería una URI correcta, ya que estamos indicando la extensión pdf en la misma.

    Para el recurso factura con el identificador 234, la siguiente URI sería la correcta, independientemente de que vayamos a consultarla en formato pdf, epub, txt, xml o json: /facturas/234

    **Las URIs deben mantener una jerarquía lógica**

    Por ejemplo, la URI /facturas/234/cliente/007 no sería una URI correcta, ya que no sigue una jerarquía lógica.

    Para el recurso factura con el identificador 234 del cliente 007, la siguiente URI sería la correcta: /clientes/007/facturas/234

    **Filtrados y otras operaciones.**

    Para filtrar, ordenar, paginar o buscar información en un recurso, debemos hacer una consulta sobre la URI, utilizando parámetros HTTP en lugar de incluirlos en la misma.

    Por ejemplo, la URI /facturas/orden/desc/fecha-desde/2007/pagina/2 sería incorrecta ya que el recurso de listado de facturas sería el mismo pero utilizaríamos una URI distinta para filtrarlo, ordenarlo o paginarlo.

    **La URI correcta en este caso sería:**
    /facturas?fecha-desde=2007&orden=DESC&pagina=2

2. ## Uso correcto de HTTP.
    Para desarrollar APIs REST los aspectos claves que hay que dominar y tener claros son:
    **Métodos HTTP**
    Para manipular los recursos, HTTP nos dota de los siguientes métodos con los cuales debemos operar:
    * GET: Para consultar y leer recursos
    * POST: Para crear recursos
    * PUT: Para editar recursos
    * DELETE: Para eliminar recursos.
    * PATCH: Para editar partes concretas de un recurso.
    
    **Códigos de estado.**
    Uno de los errores más frecuentes a la hora de construir una API suele ser el reinventar la rueda creando nuestras propias herramientas en lugar de utilizar las que ya han sido creadas, pensadas y testadas. La rueda más reinventada en el desarrollo de APIs son los códigos de error y códigos de estado.

    Cuando realizamos una operación, es vitar saber si dicha operación se ha realizado con éxito o en caso contrario, por qué ha fallado.

    Un error común sería por ejemplo:
    ![alt text](http://res.cloudinary.com/tonychagolla/image/upload/v1479792548/1_prkm09.tiff)
    En este ejemplo se devuelve un código de estado 200, que significa que la petición se ha realizado correctamente, sin embargo, estamos devolviendo en el cuerpo de la respuesta un error y no el recurso solicitado en la URL.

    Este es un error común que tiene varios inconvenientes:

   * No es REST ni es estándar.
   *  El cliente que acceda a este API debe conocer el funcionamiento especial y cómo tratar los errores de la misma, por lo que requiere un esfuerzo adicional importante para trabajar con nosotros.
   *  Tenemos que preocuparnos por mantener nuestros propios códigos o mensajes de     error, con todo lo que eso supone.
   
    El siguiente ejemplo sería correcto de la siguiente forma:
    ![alt text](http://res.cloudinary.com/tonychagolla/image/upload/v1479792729/2_yf8fmo.tiff)
    
    **Aceptación de tipos de contenido** 
    HTTP nos permite especificar en qué formato queremos recibir el recurso, pudiendo indicar varios en orden de preferencia, para ello utilizamos el header Accept.

    Nuestra API devolverá el recurso en el primer formato disponible y, de no poder mostrar el recurso en ninguno de los formatos indicados por el cliente mediante el header Accept, devolverá el código de estado HTTP 406.

    En la respuesta, se devolverá el header Content-Type, para que el cliente sepa qué formato se devuelve, por ejemplo:
    ![alt text](http://res.cloudinary.com/tonychagolla/image/upload/v1479792843/3_yj5q73.tiff)
    En este caso, el cliente solicita la factura en epub comprimido con ZIP y de no tenerlo, en pdf o json por orden de preferencia. El servidor le devuelve finalmente la factura en pdf.
    
3. ## Implementar Hypermedia.
    Conectar mediante vínculos las aplicaciones clientes con las APIs, permitiendo a dichos clientes despreocuparse por conocer de antemano del cómo acceder a los recursos.

    Con Hypermedia básicamente añadimos información extra al recurso sobre su conexión a otros recursos relacionados con él.

    Aquí tenemos un ejemplo:
    ![alt text](http://res.cloudinary.com/tonychagolla/image/upload/v1479793017/4_obmm5g.tiff)
    En este ejemplo vemos cómo indicar en un xml que representa un pedido, el enlace al recurso de la factura relacionada con el mismo.

    Sin embargo, necesitamos que el cliente que accede a nuestra API entienda que esa información no es propia del recurso, sino que es información añadida que puede utilizar para enlazar el pedido con la factura.

    Para ello conseguir esto, debemos utilizar las cabeceras Accept y Content-Type, para que tanto el cliente como la API, sepan que están hablando hypermedia.

    Por ejemplo:
    ![alt text](http://res.cloudinary.com/tonychagolla/image/upload/v1479793129/5_o6pbq9.tiff)
    Como vemos, el cliente solicita el formato application/nuestra_api+xml de forma preferente al formato text/xml. De esta forma, le indica al servicio web, que entiende su formato hypermedia y puede aprovecharlo.

    El servicio web por lo tanto, como implementa hypermedia, le devuelve la información de recurso y la información de hypermedia que puede utilizar el cliente.

    Hypermedia es útil por ejemplo para que el cliente no tenga que conocer las URLs de los recursos, evitando tener que hacer mantenimientos en cada uno de los mismos si en un futuro dichas URLs cambian (cosa que no debería pasar). También es útil para automatizar procesos entre APIs sin que haya interacción humana.

---
Fuente: http://asiermarques.com/2013/conceptos-sobre-apis-rest/