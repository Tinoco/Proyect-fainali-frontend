# Cambios de configuración para despliegue

## Problema inicial

El archivo `environment/environment.ts` y `main.ts` fueron movidos de sus ubicaciones originales dentro de `src/` a la raíz del proyecto, causando múltiples errores de imports relativos en el build.

---

## 1. Alias de ruta para environment

**Archivo:** `tsconfig.json` — línea 6

Se agregó un path alias `@env/*` para evitar imports relativos frágiles. Se eliminó `baseUrl` por estar deprecado en TypeScript 5.5+.

```json
"paths": {
  "@env/*": ["./environment/*"]
}
```

**Cambio:** `baseUrl` eliminado, `paths` ajustado a `"./environment/*"`.

---

## 2. Corrección del entry point de Angular

**Archivo:** `angular.json` — línea 20

```diff
- "browser": "src/main.ts",
+ "browser": "main.ts",
```

Se cambió la referencia al punto de entrada porque `main.ts` ahora está en la raíz del proyecto.

---

## 3. Configuración de TypeScript para app

**Archivo:** `tsconfig.app.json` — líneas 7, 11-14

```diff
- "rootDir": "./src",
+ "rootDir": ".",
```

```diff
  "include": [
+   "main.ts",
+   "environment/**/*.ts",
    "src/**/*.ts"
  ],
```

Se amplió `rootDir` y `include` para que TypeScript pueda resolver `main.ts` y los archivos de `environment/` que ya no están dentro de `src/`.

---

## 4. Corrección de imports en main.ts

**Archivo:** `main.ts` — líneas 2 y 3

```diff
- import { appConfig } from './app/app.config';
- import { App } from './app/app';
+ import { appConfig } from './src/app/app.config';
+ import { App } from './src/app/app';
```

Al estar `main.ts` en la raíz y `app/` dentro de `src/`, los paths relativos se actualizaron.

---

## 5. Actualización de imports de environment en servicios (13 archivos)

En todos los servicios se reemplazó el import relativo por el alias `@env/environment`:

```diff
- import { environment } from '<ruta relativa>/environment/environment';
+ import { environment } from '@env/environment';
```

### Archivos modificados:

| Archivo | Línea |
|---|---|
| `src/app/auth/service/auth-service.ts` | 2 |
| `src/app/features/ban/service/ban-service.ts` | 2 |
| `src/app/features/instituciones/service/instituciones.service.ts` | 3 |
| `src/app/features/problematicas/service/problematica.service.ts` | 3 |
| `src/app/features/roles/service/roles.service.ts` | 4 |
| `src/app/features/ubicacion/service/ubicacion-service.ts` | 4 |
| `src/app/features/ubicacion/service/ubicacion.service.ts` | 3 |
| `src/app/features/usuario/service/usuario-service.ts` | 12 |
| `src/app/pages/admin/dashboard/service/dashboard.ts` | 6 |
| `src/app/pages/admin/reportes/service/reportes.ts` | 6 |
| `src/app/pages/superAdmin/dashboard/service/super-admin-dashboard.service.ts` | 4 |
| `src/app/pages/superAdmin/mapa-reportes/service/mapa-reportes.service.ts` | 4 |
| `src/app/pages/usuario/nuevo-reporte/service/nuevo-reporte.service.ts` | 4 |
