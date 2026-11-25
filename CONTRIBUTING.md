# Cómo trabajamos

## Flujo de una tarea

1. **Issue**: toda tarea de desarrollo vive como issue en Curioso_Project. Si no hay issue, se crea antes de empezar.
2. **Rama**: una rama corta por issue, creada desde `dev`.
3. **PR**: se abre hacia `dev`, enlazando el issue con `Closes #<número>` para que se cierre solo al hacer merge.
4. **Review**: todo PR necesita la aprobación de la otra persona antes del merge.
5. **Merge**: con el CI en verde.

## Ramas

```
<tipo>/<numero-issue>-<descripcion-corta>
```

Ejemplos: `feat/12-groups-service`, `fix/30-form-validation`, `docs/3-onboarding`.

## Commits

[Conventional Commits](https://www.conventionalcommits.org/es/):

```
feat(core): crear servicio de grupos
fix(api): validar vencimiento del link de formulario
docs: agregar guía de onboarding
```

Tipos: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`.
El alcance es la capa o el módulo: `core`, `api`, `web`, `auth`, `client-db`, `groups`, `forms`…

## Labels

- **Capa**: `inicio`, `client-db`, `auth`, `core`, `api`, `web`, `mobile`
- `good first issue`: buenas tareas para empezar

## Milestones

Cada capa del proyecto es un milestone: `inicio`, `client-db`, `auth`, `core`, `api`, `web`, `mobile`.
Cuando un milestone llega al 100 %, esa capa no tiene tareas pendientes.

## Definición de terminado

- [ ] El código cumple lo que pide el issue
- [ ] Tests pasando
- [ ] CI en verde (lint, tipos, tests)
- [ ] Contrato de la API actualizado, si cambió
- [ ] Probado de punta a punta en la web, si aplica
- [ ] PR revisado y aprobado

## Documentación

- **Tareas**: GitHub Projects. Una tarea vive en un solo lugar.
- **Decisiones, notas de reuniones e ideas**: Notion. Cuando un issue depende de una decisión, enlaza a la página.
