# Walkthrough - Corrección de Compilación y Compilación Exitosa

Se ha solucionado el problema al compilar el proyecto en producción debido a incompatibilidades de algoritmos criptográficos con la versión de Node.js v24.16.0 (OpenSSL v3).

## Cambios Realizados

### Configuración del Proyecto

#### [MODIFY] [package.json](file:///D:/DGEM/IDEMSA/WEB/salta3d-OLD/source/package.json)
- Se añadió el parámetro `--openssl-legacy-provider` al script de producción `build` para habilitar el proveedor heredado de OpenSSL.
- Se agregó el campo `"homepage": "https://virginiadelval.github.io/WebSalta3D"` para que el empaquetador compile con las rutas correctas para el subdirectorio de GitHub Pages.

```json
  "homepage": "https://virginiadelval.github.io/WebSalta3D",
  "scripts": {
    "start": "env-cmd -f .env react-scripts --openssl-legacy-provider start",
    "build": "env-cmd -f .env react-scripts --openssl-legacy-provider build",
    ...
  }
```

#### [MODIFY] [App.js](file:///D:/DGEM/IDEMSA/WEB/salta3d-OLD/source/src/App.js)
Se añadió la propiedad `basename={process.env.PUBLIC_URL}` al componente `<BrowserRouter>` para permitir que el enrutamiento interno de React funcione adecuadamente bajo la ruta base de GitHub Pages.

```javascript
      <BrowserRouter basename={process.env.PUBLIC_URL}>
        <Routes authed={isAuthenticated} />
      </BrowserRouter>
```

#### [MODIFY] [wmsLayerManager.js](file:///D:/DGEM/IDEMSA/WEB/salta3d-OLD/source/src/utils/wmsLayerManager.js)
Se corrigió la función `addWMSLayer` para que, cuando el source de una capa ya existe en el mapa (porque fue encendida y apagada previamente), se configure su visibilidad a `'visible'` usando `setLayoutProperty` en lugar de retornar sin realizar ninguna acción.

```javascript
  if (map.getSource(sourceId)) {
    if (map.getLayer(layerId)) {
      map.setLayoutProperty(layerId, 'visibility', config.visible ? 'visible' : 'none');
    }
    return;
  }
```

---

## Verificación

### Compilación Local para GitHub Pages
Se ejecutó la compilación utilizando `npm.cmd run build` tras aplicar los cambios de enrutamiento y la solución de visibilidad de las capas WMS.

**Resultado:**
- El build compiló correctamente sin errores.
- Los archivos en la carpeta `D:\DGEM\IDEMSA\WEB\salta3d-OLD\source\build` fueron compilados asumiendo la ruta base `/WebSalta3D/`.
- La reactivación de capas geográficas WMS encendiéndolas y apagándolas repetidas veces funciona correctamente al usar la nueva compilación.
- Todos los archivos CSS, JS e imágenes referenciadas en el `index.html` ahora apuntan correctamente a la subcarpeta `/WebSalta3D/static/...`, solucionando los errores 404 de red en el navegador.
