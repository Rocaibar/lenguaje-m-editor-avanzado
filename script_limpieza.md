// Script de Limpiezaen lenguaje M
// TechStore - Práctica de editor avanzado en Power Query
// Autor: Rocío Castro

// Este script implementa la limpieza completa del dataset de ventas, siguiendo los pasos indicados en la consigna: eliminación de espacios, estandarización de categorías, filtrado de registros de prueba y tipado correcto de columnas.
// ---

let
    // Paso 1: Fuente de datos original
    // Este paso no se modifica. Power BI lo genera automáticamente al cargar la tabla manual.
    Origen = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("VZDRasMgFIZf5eB1A2rill0ma2FjK5Q1YxehF9Z6ISQaTIS9Tp9hj5AX29Ey6q7UDz7Pf/6+J4xsCLzLaXETHLwDJgDJsxunsEhl1h+LT8YpxYNTXhWUFVSQ06YnHNHehVnDq5XDeh3P3iiHUCqlZ+eNm6NU52adzDIO7bQa5MXBXqv1am/m4eNz1zZ4eRKZxXiyqjTPmsV5qN5iTPU/ZiXuKXlBy2TFj6AJGC0M0usZ2g5JEy7G3TbLFEaT8oDoeNzC7nvR3mInXQt5OFbepfKvjUdEX/qs5Agv2zghL6EWmRDXOf0C", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [id_venta = _t, nombre_producto = _t, categoria = _t, precio = _t, fecha_venta = _t]),
    
    // Paso 2: Eliminar espacios en blanco al inicio y al final de la columna nombre_producto usando Text.Trim que remueve espacios al inicio y al final, dejando el texto limpio.
    LimpiarEspacios = Table.TransformColumns(Origen,{{"nombre_producto",Text.Trim, type text }}),

    // Paso 3: Estandarizar la columna a categoría Title Case.
    //Text.Proper convierte "computación", "COMPUTACIÓN" y "Accesorios" en "Computación" y "Accesorios".
    EstandarizacionCategoria = Table.TransformColumns(LimpiarEspacios,{{"categoria",Text.Proper, type text}}),

    // Paso 4: Filtrar y eliminar registros de prueba.
    // Se filtran filas donde categoria sea exactamente "Prueba".
    // Esto se hace después de estandarizar porque M es case sensitive.
    EliminarPruebas = Table.SelectRows(EstandarizacionCategoria,each[categoria]<>"Prueba"),

    // Paso 5: Definir tipos de datos correctos.
    // Se asignan los tipos indicados en la consigna.
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    })
in
    TiparColumnas
