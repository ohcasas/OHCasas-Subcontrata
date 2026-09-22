# Portal de Subcontratas — OH Casas Modulares

App móvil para la gestión de subcontratistas de OH Casas Modulares: licitaciones de obras, postulaciones, Club OH Partner (puntos y canjes) y panel de administración.

Basada en el diseño exportado de Stitch (ver `informe_tecnico_stitch_oh_casas.pdf` si se incluyó en la entrega).

## Stack

- **Frontend**: React Native + Expo (SDK 57), NativeWind/Tailwind, React Navigation v7
- **Backend**: Supabase (Postgres + Auth + Storage), con RLS (Row Level Security) en todas las tablas
- **Monorepo**: `apps/mobile/`, `supabase/`, `packages/shared-types/`

## Estructura del repo

```
oh-casas-subcontratas/
├── apps/mobile/          # App React Native/Expo
│   ├── src/
│   │   ├── design-system/tokens.js   # Colores, tipografía, spacing (fuente única)
│   │   ├── services/supabase.ts      # Cliente Supabase
│   │   ├── navigation/                # Stacks y tabs por rol
│   │   ├── components/                # ScreenHeader, ObraImagePlaceholder, etc.
│   │   └── screens/
│   │       ├── auth/
│   │       ├── obras/
│   │       ├── postulaciones/
│   │       ├── partner/
│   │       ├── perfil/
│   │       └── admin/
│   ├── global.css
│   ├── metro.config.js   # Wiring de NativeWind con withNativeWind()
│   ├── tailwind.config.js
│   └── .env               # NO subido al repo — ver sección Configuración
├── supabase/
│   ├── migrations/
│   │   ├── 0001_init_schema.sql      # 10 tablas + RLS
│   │   ├── 0002_add_imagen_obras.sql
│   │   └── 0003_canje_rpc.sql        # función solicitar_canje()
│   └── seed_*.sql          # Datos de prueba (ejecutar manualmente en SQL Editor)
└── packages/shared-types/
```

## Estado actual

Desarrollo hecho en el orden acordado: **estructura → visual → correctitud final**.

✅ **Completado:**
- Las 10 pantallas funcionales con datos reales de Supabase (ambos roles: subcontratista y admin)
- Flujo completo probado end-to-end: login, listar/postular a obras, aceptar/rechazar postulaciones (admin), canjear puntos en Club Partner con descuento real de saldo, perfil con datos reales, logout
- Pase visual completo: iconos Feather, `ScreenHeader` compartido, placeholders de imagen, cabeceras de navegación con tema navy
- Esquema de base de datos con RLS, incluida función `security definer` para canjes seguros

⏳ **Pendiente (próxima fase):**
1. **Subida de imágenes** — bucket de Supabase Storage + políticas + selector de imagen, para fotos de obras y **foto de perfil** (esto quedó pendiente de decidir cuándo abordarlo)
2. **Adjuntos de documentación en postulaciones** (presupuesto desglosado, etc.) — explícitamente diferido
3. **Subida/actualización de documentos de homologación** en Perfil — explícitamente diferido
4. **Pantallas de Registro y Recuperar contraseña** — siguen siendo placeholders, nunca implementadas
5. **Tabla de configuración de niveles del Club Partner** — los umbrales (bronce/plata/oro/platino) están hardcodeados en la app, no en BD
6. **Icono de app y splash screen** reales — actualmente usa los valores por defecto de Expo
7. **Pase final de QA/correctitud** end-to-end de toda la app (la 3ª fase acordada) — aún no iniciado
8. Notificaciones push — no contempladas todavía

## Configuración necesaria

Crear `apps/mobile/.env` (no está en el repo por seguridad) con:

```
EXPO_PUBLIC_SUPABASE_URL=<url del proyecto Supabase>
EXPO_PUBLIC_SUPABASE_ANON_KEY=<anon key del proyecto Supabase>
```

Las credenciales reales se han compartido por un canal separado (no por GitHub).

## Cómo arrancar

```bash
cd apps/mobile
npm install
npx expo start
```

Escanear el QR con Expo Go (SDK 57) en el móvil.

## Migraciones de base de datos

Ejecutar en orden desde el SQL Editor de Supabase:
1. `supabase/migrations/0001_init_schema.sql`
2. `supabase/migrations/0002_add_imagen_obras.sql`
3. `supabase/migrations/0003_canje_rpc.sql`

Opcionalmente, los `seed_*.sql` para datos de prueba.

## Usuarios de prueba

- Subcontratista: perfil vinculado a "Empresa de Prueba SL"
- Admin: perfil con `role='admin'`, sin `empresa_id`

(Credenciales de acceso compartidas por canal separado.)
