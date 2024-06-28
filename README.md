# Gestión de Tablas Delta Lake en Databricks

[![Gestion-tablas-delta-lake.png](https://i.postimg.cc/J4HJx4B2/Gestion-tablas-delta-lake.png)](https://postimg.cc/3d7dJh1X)

## Introducción

En este proyecto, exploraremos el uso de Delta Lake en Databricks para la gestión eficiente de tablas de datos estructurados. Delta Lake es una tecnología que extiende los Data Lakes tradicionales con características transaccionales ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad), lo cual mejora significativamente la integridad y confiabilidad de los datos en entornos de big data.

## Beneficios de Delta Lake

- Atomicidad y Consistencia: Garantiza que las operaciones sean atómicas y consistentes, evitando corrupción de datos.
- Aislamiento y Durabilidad: Proporciona aislamiento entre transacciones y asegura que los datos persistan incluso en caso de fallos.
- Optimizaciones de Lectura y Escritura: Mejora el rendimiento de las operaciones de lectura y escritura en grandes volúmenes de datos.
- Gestión de Versiones: Permite mantener un historial de versiones de los datos, facilitando la auditoría y el análisis de cambios.

## Objetivo del Proyecto

El objetivo de este proyecto es implementar una tabla de ejemplo utilizando Delta Lake en Databricks, aplicando una serie de comandos SQL para crear, consultar y auditar la tabla, así como gestionar su mantenimiento utilizando las funcionalidades avanzadas proporcionadas por Delta Lake. Además, se crearán tablas externas y administradas para identificar las diferencias entre ellas. Las tablas administradas son aquellas donde Databricks gestiona tanto los metadatos como los datos, mientras que las tablas externas permiten al usuario gestionar la ubicación física de los datos, almacenándolos en una ruta específica del sistema de archivos.

Cuando se elimina una tabla administrada, tanto los datos como los metadatos se eliminan por defecto. En contraste, al eliminar una tabla externa, solo los metadatos se eliminan por defecto y los datos permanecen en su ubicación original.

## Detalles del Proyecto

#### Creación de la Tabla personas

- Utilizaremos SQL para definir una tabla llamada **tb_person** que contendrá información básica de personas.

#### Comandos a Implementar

###### Creación y Descripción de la Tabla:

- Utilizaremos comandos SQL para crear la tabla personas y para describir su estructura utilizando DESCRIBE EXTENDED.

###### Inserción de Datos:

- Insertaremos datos de ejemplo en la tabla personas utilizando el comando INSERT INTO.

###### Consulta de Datos:

- Realizaremos consultas básicas para mostrar el contenido de la tabla personas utilizando el comando SELECT.

###### Visualización del Almacenamiento Físico:

- Utilizaremos comandos de Databricks para visualizar el directorio físico donde se almacenan los datos de la tabla personas.

###### Creación de Tabla Externa personas_ext:

- Crearemos una tabla externa llamada personas_ext que apuntará a una ubicación específica en el sistema de archivos.

###### Historial de Versiones:

- Exploraremos el historial de versiones de la tabla personas_ext utilizando el comando DESCRIBE HISTORY.

###### Consulta de Datos Históricos:

- Realizaremos consultas para mostrar datos históricos específicos de la tabla personas_ext utilizando SELECT con VERSION AS OF.

###### Configuración y Ajustes de Delta Lake:

- Configuraremos propiedades de Delta Lake según sea necesario para deshabilitar la verificación de duración de retención y otras configuraciones.

###### Limpieza de Datos Obsoletos:

- Aplicaremos la operación VACUUM para eliminar archivos de datos obsoletos y mantener solo los más recientes en la tabla personas_ext.


## Creación de Cluster en Databricks

1 . Iniciar Sesión en Databricks y crear un nuevo cluster

- **Compute name :** cluster01

- **Databricks runtime version : Runtime: **14.3 LTS (Scala 2.12, Spark 3.5.0)

[![1-create-cluster.png](https://i.postimg.cc/Hs8ZXVbX/1-create-cluster.png)](https://postimg.cc/ZCZxhY75)

2 . Creamos un nuevo folder dentro de nuestro Workspace

[![2-create-folder-workspace.png](https://i.postimg.cc/brxRV2gB/2-create-folder-workspace.png)](https://postimg.cc/H8L5Vnfw)

3 . Nos ubicamos en el folder **delta_lake** y creamos un nuevo Notebook

[![3-create-Notebook.png](https://i.postimg.cc/x14LRRYd/3-create-Notebook.png)](https://postimg.cc/NKRyGRKh)

- Renombramos el Notebook con **tables_delta_lake** 
- Seleccionamos el lenguaje **SQL**
- Activamos el cluster creado anteriormente: **cluster01**

[![4-create-Notebook-SQL.png](https://i.postimg.cc/m2Ms7K6r/4-create-Notebook-SQL.png)](https://postimg.cc/mtbJsp4x)

Para crear un esquema en Databricks, utilizamos el comando **SQL CREATE SCHEMA**.

		create schema bd_personas;

Para utilizar el esquema **bd_personas** después de crearlo, puedes ejecutar el comando **USE**.

		use bd_personas;

Este comando crea una nueva tabla llamada **tb_person** dentro del esquema **bd_personas**. La tabla tiene las siguientes columnas:

- **co_person**: Un entero que representa un código único para cada persona.
- **co_docide**: Una cadena de texto que representa el código del documento de identidad de la persona.
- **no_nombre**: Una cadena de texto que representa el nombre de la persona.
- **no_apepat**: Una cadena de texto que representa el apellido paterno de la persona.
- **no_apemat**: Una cadena de texto que representa el apellido materno de la persona.

		create table tb_person(
			co_person int,
			co_docide string,
			no_nombre string,
			no_apepat string,
			no_apemat string
		);

[![5-create-tables.png](https://i.postimg.cc/8zDG1tkn/5-create-tables.png)](https://postimg.cc/56PTg5Cw)

## Descripción del Comando DESCRIBE EXTENDED

 El comando **DESCRIBE EXTENDED** proporciona una descripción detallada de la tabla **tb_person**. Incluye información sobre las columnas, sus tipos de datos, y otros metadatos como el propietario de la tabla, el formato de almacenamiento, y la ubicación física de los datos. Es útil para obtener una visión completa de la estructura y los metadatos de la tabla.

		describe extended tb_person;

[![6-describe-extended.png](https://i.postimg.cc/TYxNhPFC/6-describe-extended.png)](https://postimg.cc/MXsmFzWQ)

 El comando **INSERT INTO** se utiliza para agregar nuevas filas a la tabla **tb_person**. Cada comando INSERT INTO inserta una única fila con valores específicos para cada columna de la tabla.

		insert into tb_person values (1, '44952774', 'Juan Carlos', 'de la Cruz', 'Prado');
		insert into tb_person values (2, '96325874', 'Marianela', 'Rolando', 'Gutierrez');
		insert into tb_person values (3, '74125896', 'Tania', 'Soto', 'Diaz');

 El comando **SELECT** se utiliza para recuperar datos de una tabla específica en una base de datos. En este caso, **SELECT *** significa que queremos seleccionar todas las columnas (*) de la tabla **tb_person**.

		select * from tb_person;

[![7-insert-select.png](https://i.postimg.cc/WbwDPsQB/7-insert-select.png)](https://postimg.cc/F71FyXFp)

## Descripción del Comando %python display(dbutils.fs.ls(...))

Este comando en Databricks se utiliza para listar los archivos y directorios ubicados en el sistema de archivos distribuido de Databricks (DBFS) en una ruta específica.

##### Componentes del Comando:

- %python: Indica que el código que sigue es código Python dentro de un entorno de Databricks.

- dbutils.fs.ls("dbfs:/user/hive/warehouse/bd_personas.db/tb_person"): Es una función de Databricks (dbutils.fs.ls) que lista el contenido del directorio especificado en el DBFS.

##### Uso

- Visualización de Estructura: Muestra la estructura de archivos y subdirectorios dentro de la ruta dbfs:/user/hive/warehouse/bd_personas.db/tb_person.

- Inspección de Datos: Útil para verificar la presencia de archivos físicos que respaldan la tabla tb_person en el sistema de archivos de Databricks.

[![8-dbutils.png](https://i.postimg.cc/mZwvCqMY/8-dbutils.png)](https://postimg.cc/Sjn1pggR)

## Descripción del Comando CREATE TABLE ... LOCATIONDescripción del Comando CREATE TABLE ... LOCATION

		create table tbperson_ext location '/dbfs:/user/hive/warehouse/bd_personas.db/tb_person'

Este comando crea una nueva tabla llamada **tbperson_ext** y la define como una **tabla externa**. La tabla externa utiliza los datos almacenados en la ubicación especificada en el DBFS, en lugar de copiar los datos en una nueva ubicación administrada por Databricks.

##### Componentes del Comando:

**- CREATE TABLE** tbperson_ext: Indica la creación de una nueva tabla llamada tbperson_ext.

**- LOCATION** '/dbfs:/user/hive/warehouse/bd_personas.db/tb_person': Especifica la ubicación en el sistema de archivos donde se encuentran los datos de la tabla. En este caso, la ruta '/dbfs:/user/hive/warehouse/bd_personas.db/tb_person'.

##### Uso

- **Tabla Externa:** Define una tabla externa que utiliza los datos existentes en la ubicación especificada. No se mueve ni copia los datos; simplemente referencia los archivos en la ubicación indicada.

- **Gestión de Datos:** Útil para gestionar datos que ya existen en una ubicación específica del sistema de archivos sin necesidad de duplicarlos en el almacenamiento administrado por Databricks.

[![9-create-table-location.png](https://i.postimg.cc/RZLJBKDQ/9-create-table-location.png)](https://postimg.cc/fVkR7tZJ)

## Descripción del Comando DESCRIBE HISTORY

		DESCRIBE HISTORY tbperson_ext;

El comando **DESCRIBE HISTORY** proporciona un historial detallado de todas las operaciones realizadas en la tabla **tbperson_ext**. Esto incluye información sobre cuándo se realizaron las operaciones, quién las realizó, y qué tipo de operación fue (inserción, actualización, eliminación)

###### Componentes del Comando:

- **DESCRIBE HISTORY**: Palabras clave que indican que se desea ver el historial de la tabla.
- **tbperson_ext**: El nombre de la tabla cuya historia se desea consultar.

###### Uso

- **Auditoría**: Permite auditar las operaciones realizadas en la tabla, lo que es útil para el seguimiento de cambios y cumplimiento de normas.
- **Recuperación de Datos**: Facilita la identificación de versiones anteriores de la tabla, permitiendo revertir a un estado anterior si es necesario.
- **Transparencia**: Proporciona visibilidad completa sobre cómo se ha modificado la tabla a lo largo del tiempo.

Para ello se ha realizado de la ejecución de los siguientes comando SQL:

		update tbperson_ext set no_apemat = 'Barrera', no_nombre = 'Tania Guadalupe' where co_person = 3;

		insert into tbperson_ext values (4, '58521479', 'José Luis', 'Carranza', 'Diaz');

		delete from tbperson_ext where co_person = 2;

[![10-describe-history.png](https://i.postimg.cc/WbQw3G8B/10-describe-history.png)](https://postimg.cc/WhwkYJQ8)

## Descripción del Comando RESTORE

		RESTORE tbperson_ext VERSION AS OF 5;

Este comando restaura la tabla **tbperson_ext** a su estado en la versión 5. Es una forma de deshacer cambios y volver a una versión previa de la tabla.

###### Componentes del Comando:

- **RESTORE**: Palabra clave que indica que se quiere restaurar la tabla.
- **tbperson_ext**: El nombre de la tabla que se quiere restaurar.
- **VERSION AS OF 5**: Especifica la versión a la cual se quiere restaurar la tabla. En este caso, la versión 5.

###### Uso

- ** Recuperación de Datos**: Permite volver a un estado anterior de la tabla, deshaciendo cambios no deseados.
- **Auditoría y Control de Versiones**: Facilita la gestión de versiones y el mantenimiento del historial de cambios en la tabla.
- **Mitigación de Errores**: Útil para corregir errores al revertir a una versión conocida y estable de los datos.

###### Ejemplo de Escenario:

Si has realizado varias actualizaciones, inserciones, y eliminaciones en la tabla tbperson_ext y deseas revertir la tabla a su estado en la versión 5, este comando hará precisamente eso.

###### Beneficios:

- **Control de Cambios**: Proporciona un mecanismo para gestionar y revertir cambios de manera eficiente.
- **Seguridad y Confiabilidad**: Asegura que puedes recuperar el estado anterior de los datos, minimizando el riesgo de pérdida de datos debido a errores.

[![11-restore-version.png](https://i.postimg.cc/zvd7G4v5/11-restore-version.png)](https://postimg.cc/YLWYXnQy)

## Descripción del Comando VACUUM

		VACUUM tbperson_ext;

Este comando limpia la tabla **tbperson_ext** eliminando los archivos que ya no se necesitan para la consistencia y las versiones activas de la tabla. Por defecto, **VACUUM** retiene los archivos de los últimas 7 días para proteger contra operaciones de lectura concurrentes y operaciones de restauración.

###### Componentes del Comando:

- **VACUUM**: Palabra clave que indica que se desea limpiar la tabla de archivos antiguos.
- **tbperson_ext**: El nombre de la tabla sobre la cual se ejecutará el comando VACUUM.

###### Uso

- **Limpieza de Datos**: Elimina archivos antiguos y no referenciados que ya no son necesarios.
- **Optimización de Almacenamiento**: Libera espacio en el almacenamiento al eliminar archivos obsoletos.
- **Mantenimiento de la Tabla**: Asegura que la tabla no acumule archivos innecesarios, mejorando el rendimiento y reduciendo costos de almacenamiento.

###### Beneficios

- **Eficiencia de Almacenamiento**: Reduce el uso de espacio en disco eliminando archivos que no son necesarios.
- **Rendimiento**: Mejora el rendimiento de las consultas al reducir el número de archivos que deben ser leídos.

[![12-vacuum.png](https://i.postimg.cc/jqXnJzXW/12-vacuum.png)](https://postimg.cc/JtszfHSm)

## Descripción del Comando SET spark.databricks.delta.retentionDurationCheck.enabled = false

Este comando desactiva la verificación de la duración mínima de retención de archivos en Delta Lake. Por defecto, Delta Lake impide la eliminación de archivos más recientes que 7 días para proteger contra errores en la recuperación de datos. Al deshabilitar esta verificación, puedes especificar un período de retención menor.

###### Componentes del Comando:

- **SET**: Palabra clave utilizada para establecer un valor de configuración.
- **spark.databricks.delta.retentionDurationCheck.enabled**: La configuración específica que controla la verificación de la duración de retención en Delta Lake.
- **false**: Valor que desactiva la verificación de la duración de retención.

###### Uso

- **Permitir Limpieza Inmediata**: Facilita la ejecución del comando VACUUM con períodos de retención muy cortos, incluyendo 0 horas, lo que permite eliminar inmediatamente los archivos antiguos no referenciados.
- **Flexibilidad en Mantenimiento**: Proporciona más control sobre la gestión del almacenamiento y la limpieza de archivos en Delta Lake.

###### Ejemplo de Uso:

1.- Deshabilitar la Verificación de Retención:

		SET spark.databricks.delta.retentionDurationCheck.enabled = false;

2- Ejecutar VACUUM con Retención Cero:

		VACUUM tbperson_ext RETAIN 0 HOURS;

[![13-vacuum-retain.png](https://i.postimg.cc/PfYs64cx/13-vacuum-retain.png)](https://postimg.cc/Jtr20bRV)

## Descripción del Comando CREATE TABLE ... TBLPROPERTIES (delta.enableChangeDataFeed = true)

		CREATE TABLE tbperson_ext_changeDataCapture (
			co_person INT,
			co_docide STRING,
			no_nombre STRING
		)
		TBLPROPERTIES (delta.enableChangeDataFeed = true);

Este comando crea una tabla llamada **tbperson_ext_changeDataCapture** con tres columnas: **co_person, co_docide, y no_nombre**. Además, habilita el **Change Data Feed (CDF)** para esta tabla mediante la propiedad **delta.enableChangeDataFeed**.

###### Componentes del Comando:

- **CREATE TABLE tbperson_ext_changeDataCapture**: Indica la creación de una nueva tabla con el nombre tbperson_ext_changeDataCapture.
- **(co_person INT, co_docide STRING, no_nombre STRING**): Define las columnas de la tabla con sus respectivos tipos de datos.
- **TBLPROPERTIES (delta.enableChangeDataFeed = true)**: Establece propiedades adicionales para la tabla, en este caso, habilitando el Change Data Feed.

###### Uso

- **Change Data Capture**: Permite rastrear y capturar los cambios (inserciones, actualizaciones, eliminaciones) en los datos de la tabla.
- **Auditoría y Seguimiento**: Facilita el seguimiento de los cambios realizados en la tabla, útil para auditorías y sincronización de datos en tiempo real.

###### Beneficios:

- **Monitoreo de Cambios**: Permite detectar y responder a cambios en los datos de manera eficiente.
- **Integración de Datos**: Facilita la integración de datos en sistemas downstream que necesitan estar sincronizados con los cambios en la tabla

[![14-enable-Change-Data-Feed.png](https://i.postimg.cc/qqYyNKfD/14-enable-Change-Data-Feed.png)](https://postimg.cc/V0qdTdkX)

## Descripción del Comando SELECT * FROM TABLE_CHANGES

Este comando selecciona todos los cambios realizados en la tabla **tbperson_ext_changeDataCapture** a partir de la versión 0 y excluye los registros de tipo **update_preimage**. Los resultados se ordenan por la versión de confirmación (_commit_version).

###### Componentes del Comando:

- **TABLE_CHANGES('tbperson_ext_changeDataCapture', 0)**: Función que devuelve los cambios en la tabla tbperson_ext_changeDataCapture desde la versión 0.
- **WHERE _change_type != 'update_preimage':** Filtra los resultados para excluir los cambios de tipo update_preimage (estado previo a una actualización).
- **ORDER BY _commit_version**: Ordena los resultados por la versión de confirmación, mostrando los cambios en el orden en que ocurrieron.

###### Uso

- **Monitoreo de Cambios**: Permite ver todas las inserciones, actualizaciones y eliminaciones en la tabla desde la versión especificada.
- **Auditoría y Seguimiento**: Facilita el seguimiento de los cambios en los datos, útil para auditorías y análisis de datos.
- **Sincronización de Datos**: Ayuda a sincronizar sistemas downstream con los cambios en la tabla Delta.

###### Beneficios:

- **Visibilidad Completa**: Proporciona una visión completa de los cambios en la tabla, permitiendo analizar cómo los datos han evolucionado con el tiempo.
- **Integración Simplificada**: Facilita la integración de cambios de datos en sistemas de análisis y otros sistemas downstream.

[![15-table-changes.png](https://i.postimg.cc/7YRrLZ5C/15-table-changes.png)](https://postimg.cc/9ryn8VtV)
