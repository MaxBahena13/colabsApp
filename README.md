# Gestión de colaboradores

App móvil para consultar, registrar y organizar colaboradores: búsqueda por nombre o puesto, alta con sueldo y RFC, edición y baja. Construida con **Ionic + Angular** (frontend) y una **API REST en Node + Express + MongoDB** (backend).

## Características

- Lista de colaboradores con búsqueda (ignora acentos y mayúsculas).
- Ficha de detalle con correo, sueldo (MXN) y RFC.
- Alta y edición con validación (RFC mexicano, correo, sueldo ≥ 0).
- Baja con confirmación.
- Modo claro y oscuro automático.
- Listo para empaquetarse en Android con Capacitor.

## Tecnologías

| Capa | Stack |
| --- | --- |
| Frontend | Ionic 9, Angular 22, Capacitor, Axios |
| Backend | Node ≥ 18, Express 4, Mongoose 8, bcryptjs, cors, dotenv |
| Base de datos | MongoDB |

## Estructura

```
colabs-app-maximus/
├── frontend/   # App Ionic/Angular
│   └── src/app/
│       ├── pages/      # welcome, colabs-list, colab-detail, colab-form, ...
│       ├── services/   # colabs.service.ts (cliente de la API)
│       └── config.ts   # URL de la API
└── backend/    # API REST
    └── src/
        ├── models/     # Colab, User
        ├── routes/     # colabs, users
        ├── middleware/ # manejo de errores
        └── seed.js     # datos de ejemplo
```

## Requisitos

- [Node.js](https://nodejs.org/) 18 o superior
- [MongoDB](https://www.mongodb.com/) corriendo en local (o una URI de Atlas)
- Ionic CLI: `npm install -g @ionic/cli`

## Instalación y arranque

### 1. Backend

```bash
cd backend
npm install
cp .env.example .env      # en Windows: copy .env.example .env
npm run seed              # (opcional) carga colaboradores de ejemplo
npm run dev               # o: npm start
```

La API queda en `http://localhost:3000/api`. Comprueba que funciona en `http://localhost:3000/api/health`.

**Variables de entorno (`backend/.env`):**

| Variable | Descripción | Valor por defecto |
| --- | --- | --- |
| `PORT` | Puerto de la API | `3000` |
| `MONGO_URI` | Cadena de conexión a MongoDB | `mongodb://127.0.0.1:27017/colabsAppMaximus` |

> Usa `127.0.0.1` y no `localhost`: en Node 18+ `localhost` puede resolver a IPv6 y Mongo no responde.

### 2. Frontend

```bash
cd frontend
npm install
ionic serve               # o: npm start
```

Se abre en `http://localhost:4200`.

## Conexión con la API desde Android

`frontend/src/app/config.ts` elige el host según la plataforma:

- **Navegador:** `localhost`
- **Emulador de Android Studio:** `10.0.2.2` (la PC vista desde el emulador)
- **Celular físico:** cambia `ANDROID_HOST` por la IP de tu PC en la red (ej. `192.168.1.50`)

El backend escucha en `0.0.0.0`, así que el celular puede entrar por WiFi si está en la misma red.

## API

Base: `/api`

### Colaboradores — `/api/colabs`

| Método | Ruta | Descripción |
| --- | --- | --- |
| GET | `/` | Lista (ordenada por nombre y apellidos) |
| GET | `/:id` | Un colaborador |
| POST | `/` | Crear |
| PUT | `/:id` | Actualizar |
| DELETE | `/:id` | Eliminar |

**Modelo**

```json
{
  "strNombre": "Sofía",
  "strApellidos": "Ramírez Cuevas",
  "strPuesto": "Diseñadora UX",
  "floatSueldo": 32000,
  "strRFC": "RACS890214HT5",
  "strCorreo": "sofia.ramirez@correo.com"
}
```

- `strRFC` es único y se guarda en mayúsculas; debe cumplir el formato de RFC mexicano.
- `strCorreo` es opcional.

### Usuarios — `/api/users`

CRUD de usuarios (`strCorreo`, `strPassword`, `strRol`: `usuario` | `colaborador` | `administrador`). La contraseña se guarda con hash (bcrypt, mínimo 6 caracteres) y nunca se devuelve en las consultas.

### Estado — `GET /api/health`

Devuelve `{ "status": "ok", "db": "conectada" }`.

## Scripts

**Backend**

| Comando | Acción |
| --- | --- |
| `npm start` | Inicia la API |
| `npm run dev` | Inicia con recarga automática |
| `npm run seed` | Carga colaboradores de ejemplo |

**Frontend**

| Comando | Acción |
| --- | --- |
| `npm start` | Servidor de desarrollo |
| `npm run build` | Build de producción |
| `npm run test:unit` | Pruebas unitarias |
| `npm run test:e2e` | Pruebas end-to-end (Playwright) |
| `npm run lint` | Linter y formato |

## Autor

Maximus Bahena de Lara
