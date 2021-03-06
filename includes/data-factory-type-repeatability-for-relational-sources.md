## <a name="repeatability-during-copy"></a>Repetibilidad durante la copia
Cuando se copian datos desde y a almacenes relacionales, tiene que tener en cuenta la repetibilidad para evitar resultados imprevistos. 

Un segmento se puede volver a ejecutar automáticamente en Data Factory de Azure según la directiva de reintento especificada. Se recomienda establecer una directiva de reintento para protegerse frente a errores transitorios. Por lo tanto, la repetibilidad es un aspecto importante que hay que tener en cuenta durante el movimiento de datos. 

**Como un origen:**

> [!NOTE]
> Los ejemplos siguientes son para SQL Azure, pero son aplicables a cualquier almacén de datos que admita conjuntos de datos rectangulares. Puede que deba ajustar el **tipo** de origen y la propiedad de la **consulta** (por ejemplo, consulta en lugar de sqlReaderQuery) del almacén de datos.   
> 
> 

Normalmente, al leer desde almacenes relacionales, lo que quiere es leer únicamente los datos correspondientes a ese segmento. Una manera de hacerlo sería mediante el uso de las variables WindowStart y WindowEnd disponibles en Factoría de datos de Azure. Obtenga información acerca de las variables y funciones de Factoría de datos de Azure aquí en el artículo sobre [Programación y ejecución](../articles/data-factory/data-factory-scheduling-and-execution.md) . Ejemplo: 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta leerá los datos de MyTable que se encuentran en el intervalo de duración del segmento. Una segunda ejecución de este segmento también garantizará siempre este comportamiento. 

En otros casos, puede que desee leer toda la tabla (supongamos que solo para un movimiento único) y puede definir qlReaderQuery como sigue:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```


<!--HONumber=Nov16_HO3-->


