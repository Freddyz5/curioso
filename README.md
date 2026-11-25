# Curioso

**Curioso digital**: app para recolectar el cumpleaños, los gustos de comida y la info básica de las personas de un grupo, para poder celebrarlas.

> Proyecto en etapa de planeación. Muchas decisiones se irán tomando sobre la marcha; este README refleja el estado actual y se actualiza a medida que se decide.

## Objetivos del proyecto

- **Senior**: introducirse al ecosistema Python sin abandonar las buenas prácticas del stack actual (monorepo, tipado, arquitectura modular).
- **Junior**: introducirse de forma gradual a un ambiente de trabajo real (issues, ramas, PRs, code review, CI).

## Funcionalidad

- **Grupos** de usuarios, cada uno con uno o varios **admins**.
- El **admin** es el único obligado a registrarse.
- El admin puede generar:
  1. **Invitaciones** para unirse a la app (personales, de un solo uso).
  2. **Links de formulario** (del grupo, reutilizables, revocables y con vencimiento).
- Los **usuarios normales** no están obligados a registrarse: basta con llenar el formulario. Si quieren, pueden crear una cuenta y reclamar su perfil.
- Se empieza como **app web**; la **app móvil** es el siguiente paso.

### Decisiones de producto

| Tema | Decisión |
|---|---|
| Formulario | Una base de campos predefinida para que el admin no piense mucho, con opción de agregar o eliminar campos |
| Perfil | Por persona y por grupo |
| Visibilidad | El admin ve todo y puede permitir que los demás miembros vean la información |
| Admins por grupo | Puede haber varios |
| Recordatorios | No por el momento |

## Arquitectura

**Monorepo sí, monolito no.** Flujo por capas:

```
web = mobile  →  api  →  core  →  client-db  →  Postgres
       ↑
      auth (Better Auth)
```

### Estructura del monorepo

```
apps/
  web/          → Astro
  mobile/       → Expo / React Native (fase posterior)
  api/          → API REST en Python (FastAPI)
  auth/         → Hono + Better Auth (registro, login, sesiones, JWT)
packages/
  core/         → lógica de negocio en Python, organizada por módulos
  client-db/    → conexión, modelos y migraciones de Postgres
  api-client/   → cliente TS generado del OpenAPI de la API (lo usan web y mobile)
```

### Arquitectura del core: capas por módulo

El core **no es hexagonal**: usa una arquitectura por capas organizada por módulos, más fácil para empezar.

| Capa | Responsabilidad | Dónde vive |
|---|---|---|
| Router | Recibe HTTP, valida entrada y devuelve la respuesta | `apps/api` |
| Service | Reglas de negocio | `packages/core` |
| Repository | Lee y escribe en la base de datos | `packages/core` |
| Model | Tablas y migraciones | `packages/client-db` |

Regla: **cada capa solo llama a la que está justo debajo**, nunca se salta una.

### Módulos

El mismo nombre de módulo se usa en todas las capas:

- **Accounts**: vincula el usuario de Better Auth con su Persona
- **Groups**: grupos, membresías y roles (owner / admin / member)
- **Forms**: plantilla de formulario, campos y links públicos
- **People**: personas y perfiles por grupo, formulario público, link de edición
- **Invitations**: invitar, aceptar y reclamar un perfil existente
- **Birthdays**: próximos cumpleaños

### Autenticación

- **Better Auth** (TypeScript) en un servicio propio (`apps/auth`).
- El plugin de JWT publica un JWKS; la API en Python solo **verifica el token**.
- Better Auth crea y migra sus propias tablas (`user`, `session`, `account`, `verification`). Alembic las ignora y el modelo de datos las trata como externas.
- **No se usa** el plugin de organizaciones de Better Auth: grupos e invitaciones viven en el core de Python, porque el modelo incluye personas sin cuenta.

### Stack

| Área | Herramienta |
|---|---|
| Web | Astro + Tailwind |
| Mobile | Expo / React Native |
| API | FastAPI |
| Auth | Hono + Better Auth |
| Base de datos | Postgres |
| ORM y migraciones | SQLAlchemy 2 + Alembic |
| Paquetes Python | `uv` (workspaces) |
| Calidad Python | ruff, pyright, pytest |

> Las herramientas Python están propuestas y se confirman sobre la marcha.

## Datos personales

- Se pide consentimiento explícito en el formulario.
- El cumpleaños se guarda como **día y mes**, sin año.
- Datos sensibles (como alergias) son opcionales y visibles solo para el admin.
- Cada persona puede editar o eliminar sus datos.

## Fases

| Fase | Contenido |
|---|---|
| 0 · Fundaciones | Monorepo, tooling, CI, base de datos |
| 1 · Auth + Groups | Servicio de auth, base de la API y la web, Groups como feature de referencia |
| 2 · Forms + People | Formulario configurable, formulario público, perfiles |
| 3 · Invitations | Invitaciones, registro opcional, reclamar perfil |
| 4 · Birthdays | Próximos cumpleaños |
| 5 · Mobile | App móvil con el mismo `api-client` |

Las tareas se llevan en **GitHub Projects** (Curioso_Project). Ver [CONTRIBUTING.md](CONTRIBUTING.md) para el flujo de trabajo.
