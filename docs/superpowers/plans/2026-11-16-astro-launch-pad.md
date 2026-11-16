# Astro Launch Pad Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Entregar un template GitHub de Astro estático con arranque guiado, especialistas reales, diseño cuidado y verificaciones reproducibles, sin React ni backend obligatorio.

**Architecture:** Una sola base ejecutable incorpora Astro y CSS moderno; Vue y Tailwind se añaden por proyecto mediante recetas probadas. Las instrucciones comunes y los contratos de especialistas se comparten mediante adaptadores pequeños para Codex y Copilot. Scripts locales verifican políticas, documentación e inicialización; la utilidad de persistencia se mantiene independiente de la UI.

**Tech Stack:** Astro, TypeScript estricto, pnpm, CSS, Vitest, Playwright, axe, ESLint, Prettier y GitHub Actions. Vue y Tailwind son integraciones opcionales; Vercel es el destino estático predeterminado.

**Spec:** `docs/superpowers/specs/2026-11-16-astro-launch-pad-design.md` (aprobado el 2026-11-16).

## Global Constraints

- «Base ligera con arranque guiado, elegida frente a perfiles predefinidos.»
- «Astro como base.»
- «Vanilla para comportamiento sencillo; Vue cuando la reactividad lo justifique.»
- «React: Prohibido en el código y en las dependencias propias del proyecto.»
- «CSS moderno por defecto; Tailwind opcional por proyecto.»
- «Frontend y generación estática por defecto; sin backend obligatorio.»
- «Vercel como destino predeterminado.»
- «Codex principal, Copilot utilizable y puerta abierta a Claude.»
- TypeScript estricto, pnpm, un lockfile; seleccionar y registrar versiones estables compatibles reales al comenzar la tarea 1. No cambiar configuración global del usuario.
- «Documentación y comunicación de los agentes en español; nombres de código, archivos y comandos en inglés.»
- «No fijar modelos comerciales ni exigir una suscripción concreta en el template.»
- «El template resultante podrá funcionar sin tener Superpowers instalado».
- Las integraciones opcionales y la publicación en Vercel no se activan en el template base. No migrar otros repositorios del usuario.

## Review Focus

1. Proyecto ya personalizado o ejecución interrumpida: el inicio conserva texto y código del usuario y no sobrescribe un nombre activo distinto. Tarea 4.
2. Navegador sin almacenamiento, con JSON corrupto o con una versión futura: la lectura falla de forma explícita sin borrar datos ni romper el build estático. Tarea 3.
3. React oculto tras un alias npm o un peer obligatorio: la política muestra un diagnóstico útil, pero no bloquea ejemplos de documentación ni peers opcionales. Tarea 2.
4. Asistente sin ejecución multiagente o con adaptadores desactualizados: puede trabajar secuencialmente y detecta rutas/definiciones inválidas. Tarea 5.
5. Vue y Tailwind añadidos juntos: siguen pasando build, tipos y política; al volver de las pruebas la base sigue sin esas dependencias. Tarea 6.

## Mapa de archivos y límites

| Área | Archivos principales | Responsabilidad |
| --- | --- | --- |
| Aplicación | `astro.config.mjs`, `tsconfig.json`, `src/config/site.json`, `src/layouts/BaseLayout.astro`, `src/pages/index.astro`, `src/pages/404.astro`, `src/styles/{tokens,global}.css` | Starter estático y accesible, metadatos editables. |
| Verificación | `eslint.config.mjs`, `.prettierrc.json`, `.prettierignore`, `vitest.config.ts`, `playwright.config.ts`, `tests/e2e/starter.spec.ts` | Comandos estables y evidencia del comportamiento. |
| Política | `scripts/{check-policies,check-docs}.mjs`, `scripts/lib/policies.mjs`, `tests/tooling/policies.test.ts` | Sin React y documentación/adaptadores coherentes. |
| Persistencia | `src/lib/storage.ts`, `tests/unit/storage.test.ts` | Contrato pequeño con errores explícitos y acceso al navegador diferido. |
| Inicio | `scripts/project-init.mjs`, `scripts/lib/{project-init,product}.mjs`, `tests/tooling/project-init.test.ts`, `PRODUCT.md`, `DESIGN.md` | Estado de inicio, validación y personalización no destructiva. |
| Agentes | `AGENTS.md`, `START_HERE.md`, `docs/agents/*.md`, `config/agent-roles.json`, `scripts/sync-agents.mjs`, `.codex/agents/*.toml`, `.github/agents/*.agent.md` | Reglas y cinco especialistas, con adaptadores generados. |
| Skills | `.agents/skills/{launchpad-start,astro-development,web-verification,impeccable}/` | Procedimientos breves y referencias bajo demanda. |
| Recetas | `docs/recipes/{vue,tailwind,content,storage,modern-css,supabase}.md`, `scripts/test-integrations.mjs`, `tests/integrations/` | Ampliaciones opcionales verificadas en copias temporales. |
| Entrega | `README.md`, `docs/agents/usage.md`, `docs/verification.md`, `.github/workflows/{ci,integrations}.yml`, `.github/pull_request_template.md`, `.vscode/extensions.json`, `THIRD_PARTY_NOTICES.md` | Uso, evidencia, CI y atribución. |

Los comodines de los adaptadores corresponden exactamente a `product-architect`, `ui-designer`, `astro-engineer`, `vue-engineer` y `qa-reviewer`. Las rutas de referencias de Impeccable se fijan al importar su cierre de documentos en la tarea 6 y se enumeran en su manifiesto de procedencia.

## Task 1: Base Astro ejecutable y accesible

**Files:** Crear los archivos de aplicación/verificación del mapa, `package.json`, `pnpm-lock.yaml`, `.node-version`, `.npmrc`, `.gitignore` y `tests/e2e/starter.spec.ts`.

**Interfaces:**
- Produce `src/config/site.json`: `{name: string, title: string, description: string, lang: string, storageNamespace: string}`; valores iniciales identifican el starter sin fingir un producto real.
- Produce `BaseLayout.astro` con props `{title?: string, description?: string, lang?: string}`; conserva el `<slot />` del contenido.
- Produce comandos `dev`, `build`, `preview`, `check`, `lint`, `format`, `format:check`, `test`, `test:e2e` y `verify`.
- `verify` ejecuta formato, lint, tipos, políticas/documentación cuando existan y unitarias. `test:e2e` ejecuta Playwright sobre `build` y `preview`, sin requerir un servidor iniciado manualmente.

- [ ] **Step 1: Resolver el entorno y las versiones.** Durante la ejecución, leer el estado Git y usar `using-git-worktrees` para aislar la implementación. Verificar las versiones publicadas en fuentes oficiales/registro y elegir Node LTS compatible con Astro y Vercel. Registrar las versiones elegidas en `package.json`, `.node-version` y posteriormente en la evidencia. Fijar pnpm mediante `packageManager`; no aceptar un lockfile generado con otro gestor.
- [ ] **Step 2: Preparar las herramientas de prueba mínimas y escribir el recorrido que debe fallar.** En `starter.spec.ts`: `page.goto('/')` responde 200, hay exactamente un `main` y un `h1`, el primer Tab permite activar el enlace de salto y llega al contenido, el botón «Comprobar interacción» cambia un estado anunciado en un `role="status"`, y `/404.html` muestra una vuelta al inicio. No existe sitio todavía: el recorrido debe fallar por no disponer de página funcional.
- [ ] **Step 3: Implementar el starter y su configuración.** Astro estático, CSS de tokens/base, tipografía de sistema, contenido breve en español, `lang` y metadatos desde `site.json`. El botón prueba comportamiento vanilla sin persistencia ni estado de demostración duradero. Incluir 404 estática; no incluir router de cliente, URL canónica ficticia, scripts externos o fuentes remotas. Configurar Vitest para unitarias/tooling y excluir E2E/integraciones de su búsqueda normal.
- [ ] **Step 4: Comprobar el resultado.** Ejecutar `pnpm install --frozen-lockfile`, `pnpm check`, `pnpm lint`, `pnpm format:check`, `pnpm build` y `pnpm test:e2e`. Añadir al recorrido axe sin violaciones automáticas, ausencia de errores de consola, ausencia de overflow horizontal a 390 y 1440 px y movimiento reducido. Revisar las capturas de ambas anchuras; corregir jerarquía, foco y legibilidad antes de continuar. Si el navegador no puede ejecutarse, registrar el impedimento y dejar esa verificación pendiente.
- [ ] **Step 5: Commit.** `feat: add accessible static Astro foundation` con solo los archivos de esta tarea.

## Task 2: Política verificable de Astro, Vue y cero React

**Files:** Crear `scripts/lib/policies.mjs`, `scripts/check-policies.mjs`, `tests/tooling/policies.test.ts`; modificar `package.json` y la configuración de análisis cuando proceda.

**Interfaces:**
- Exportar `scanPolicies(rootDir: string): Promise<PolicyFinding[]>`, con `PolicyFinding = {file: string, rule: string, message: string}`.
- `pnpm policy:check` termina con código 1 y diagnósticos al haber hallazgos; 0 en un proyecto válido. Incluirlo en `verify`.
- Alcance: manifiesto propio, dependencias instaladas a las que el manifiesto apunta y código propio bajo `src/`; no skills, documentación, builds, fixtures ni `node_modules` de forma recursiva.

- [ ] **Step 1: Escribir fixtures que fallen.** `rejectsReactPackages` detecta `react`, `react-dom`, `@astrojs/react` y tipos React directos; `rejectsNpmAliases` detecta `"ui": "npm:react@..."`; `rejectsMandatoryReactPeer` detecta un paquete directo que declare peer React obligatorio y `allowsOptionalPeer` permite el marcado como opcional. `rejectsRuntimeImports` cubre imports, exports, import dinámico literal y `require` literales desde React, además de archivos `.jsx`/`.tsx`; `ignoresDocsAndVendoredSkills` permite textos de documentación con esos mismos ejemplos. La prueba válida permite `.astro`, `.vue` y vanilla.
- [ ] **Step 2: Ejecutar las pruebas.** `pnpm test -- tests/tooling/policies.test.ts`; deben fallar antes de implementar el escáner.
- [ ] **Step 3: Implementar el escáner y CLI.** Resolver también alias npm al identificar paquetes. Consultar `peerDependencies` y `peerDependenciesMeta` de cada dependencia propia instalada. Analizar imports con TypeScript para JS/TS y bloques script/frontmatter extraídos de Astro/Vue, evitando confundir una cadena de ejemplo con un import real. La ausencia de dependencias instaladas necesarias para revisar peers produce un diagnóstico de instalación pendiente, no una validación falsa. Documentar que imports construidos en tiempo de ejecución no son una prueba formal de ausencia de React.
- [ ] **Step 4: Verificar.** Repetir la prueba, ejecutar `pnpm policy:check` sobre la base real y `pnpm verify`. Comprobar que el diagnóstico indica el archivo y la corrección necesaria sin exponer datos del entorno.
- [ ] **Step 5: Commit.** `feat: enforce the Astro and Vue framework policy`.

## Task 3: Persistencia local segura y pequeña

**Files:** Crear `src/lib/storage.ts`, `tests/unit/storage.test.ts`.

**Interfaces:**
- `createLocalStore<T>(options: {namespace: string; key: string; version: number; validate: (value: unknown) => value is T; storage?: () => Storage | undefined}): {read(): StorageResult<T | undefined>; write(value: T): StorageResult<void>}`.
- `StorageResult<T> = {ok: true; value: T} | {ok: false; error: 'unavailable' | 'read-failed' | 'invalid-data' | 'unsupported-version' | 'write-failed'}`.
- Clave `${namespace}:${key}` y envelope serializado `{version: number, data: unknown}`. La versión va en el envelope para que los cambios de esquema sean detectables. No hacer acceso al navegador en el nivel de módulo.

- [ ] **Step 1: Escribir pruebas que fallen.** `readsMissingValue` devuelve `{ok:true,value:undefined}`; `roundTripsValidatedData` conserva dato y versión; `defersBrowserAccess` permite importar el módulo sin `window`; `reportsUnavailableStorage`, `reportsReadFailure` y `reportsWriteFailure` devuelven el error esperado. `preservesCorruptAndFutureData` verifica que JSON inválido, schema inválido y versión desconocida no llaman a `removeItem` ni `setItem` durante la lectura. Cubrir namespace/clave vacíos y versiones no enteras positivas con rechazo de opciones antes del acceso al almacenamiento.
- [ ] **Step 2: Ejecutar.** `pnpm test -- tests/unit/storage.test.ts`; esperar fallos por contrato ausente.
- [ ] **Step 3: Implementar la utilidad.** Evaluar el proveedor de almacenamiento dentro de cada operación y capturar también el acceso bloqueado al getter de `localStorage`. Validar los datos antes de devolverlos o guardarlos. Distinguir ausencia de dato de dato ilegible; no introducir migraciones automáticas, stores globales o dependencia de Vue.
- [ ] **Step 4: Verificar.** Ejecutar la prueba, `pnpm check` y `pnpm build`. Confirmar que la utilidad no añade JavaScript a la página inicial cuando no está importada.
- [ ] **Step 5: Commit.** `feat: add validated browser storage utility`.

## Task 4: Inicio guiado reanudable y no destructivo

**Files:** Crear `scripts/lib/product.mjs`, `scripts/lib/project-init.mjs`, `scripts/project-init.mjs`, `tests/tooling/project-init.test.ts`, `PRODUCT.md`, `DESIGN.md`; modificar `package.json` y usar `src/config/site.json` de la tarea 1.

**Interfaces:**
- Frontmatter `PRODUCT.md`: `{status: 'uninitialized' | 'ready', packageName: string, title: string, description: string, styling: 'css' | 'tailwind', vue: boolean, storage: 'none' | 'local'}`. El cuerpo conserva objetivo, público, alcance, exclusiones y decisiones.
- `readProduct(rootDir: string): Promise<{metadata: ProductMetadata; body: string}>`, con validación y error explícito si el estado está corrupto.
- `initializeProject(rootDir: string, options: {name: string; title?: string; description?: string}): Promise<{changed: boolean}>`.
- CLI `pnpm project:init --name my-project [--title 'Mi proyecto'] [--description 'Descripción']`. Nombre: 1–64 caracteres, minúsculas alfanuméricas separadas por guiones; rechazar otros formatos con ayuda clara.

- [ ] **Step 1: Escribir pruebas que fallen en directorios temporales.** `initializesMetadata` actualiza el nombre de `package.json`, metadatos de `site.json` y frontmatter, y pasa a `ready`. `preservesUserContent` deja intactos el cuerpo Markdown y un archivo de código centinela. `isIdempotent` no cambia bytes al repetir el mismo nombre ya activo. `refusesRenamingReadyProject`, `rejectsInvalidName` y `rejectsMalformedProduct` no escriben ningún archivo. `canResumeUninitializedBrief` conserva un brief escrito por el agente antes de ejecutar el comando. Un fallo de permisos/preparación debe detectarse antes de reemplazar los originales; al fallar un reemplazo, restaurar los originales y devolver error.
- [ ] **Step 2: Ejecutar.** `pnpm test -- tests/tooling/project-init.test.ts`; esperar fallos por módulos ausentes.
- [ ] **Step 3: Implementar lectores, inicialización y CLI.** Usar un parser YAML mantenido en dependencias de desarrollo para el frontmatter; no reemplazar libremente palabras dentro del Markdown. Preparar/validar las modificaciones antes de escribir; preservar todos los campos ajenos al comando. Marcar `ready` al terminar, nunca antes. `DESIGN.md` contiene preguntas y apartados útiles, sin paleta o estética obligatoria. El algoritmo no instala módulos, crea repos, cambia remotos ni ejecuta despliegues.
- [ ] **Step 4: Verificar en una copia desechable.** Ejecutar inicialización dos veces, comparar el centinela y los cuerpos del brief y ejecutar `pnpm verify` y `pnpm build` dentro de la copia. La base fuente conserva `uninitialized`.
- [ ] **Step 5: Commit.** `feat: add resumable project initialization`.

## Task 5: Instrucciones breves, skills y especialistas portables

**Files:** Crear `AGENTS.md`, `START_HERE.md`, `.github/copilot-instructions.md`, `CLAUDE.md`, los cinco contratos `docs/agents/<role>.md`, `docs/agents/usage.md`, `config/agent-roles.json`, `scripts/{sync-agents,check-docs}.mjs`, `.agents/skills/{launchpad-start,astro-development,web-verification}/SKILL.md`, `tests/tooling/agents.test.ts`; generar los diez adaptadores de Codex/Copilot del mapa.

**Interfaces:**
- `config/agent-roles.json` es un array `{name: string, description: string, contract: string, readOnly: boolean}`. Contiene exactamente los cinco roles del diseño; solo QA es obligatoriamente de revisión sin modificaciones.
- `pnpm agents:sync` genera `.codex/agents/<role>.toml` y `.github/agents/<role>.agent.md`. `pnpm agents:check` compara con la generación esperada sin escribir y falla si hay drift.
- `pnpm docs:check` valida rutas locales de documentación propia, referencias de skill, contratos y comandos documentados. Excluir URLs remotas de solicitudes de red; tratar anchors locales de forma consistente.
- Adaptadores contienen el formato nativo documentado y la instrucción de leer el contrato, `AGENTS.md` y el brief relevante. No incrustar modelos, tokens, permisos globales o MCP obligatorios.

- [ ] **Step 1: Escribir pruebas que fallen.** `generatesFiveEquivalentRoles` comprueba nombres y contrato compartido en ambos formatos, parsea TOML/YAML reales con parsers de desarrollo y rechaza definiciones inválidas. `detectsAdapterDrift`, `rejectsMissingContract` y `rejectsBrokenLocalLinks` producen salida no cero. `supportsSequentialFallback` usa un caso de trabajo sin herramientas de delegación para comprobar el recorrido documentado; evaluarlo como comportamiento del agente, no como búsqueda de una frase. `keepsEntryBrief` limita `AGENTS.md` a menos de 100 líneas.
- [ ] **Step 2: Ejecutar las pruebas.** `pnpm test -- tests/tooling/agents.test.ts`; confirmar que fallan antes de crear generador y contratos.
- [ ] **Step 3: Escribir contratos y procedimientos.** `AGENTS.md` enruta por estado y tarea. `START_HERE.md` usa `readProduct`/frontmatter, no reinicia un proyecto `ready`, pregunta solo por decisiones materiales y utiliza la tarea 4 después de acordar el brief. Los contratos definen objetivo, entradas, ownership, comprobaciones y retorno. Delegar con contexto mínimo, un máximo de tres especialistas cuando sea posible, sin editar archivos compartidos a la vez. Mantener lógica propia de Astro/vanilla en un especialista y Vue en otro. La guía documenta proyecto confiable/descubrimiento según los clientes vigentes sin modificar sus ajustes globales.
- [ ] **Step 4: Generar y verificar adaptadores.** Usar `name`, `description` e instrucciones en TOML según Codex vigente; validar los campos de Copilot contra su documentación oficial al ejecutar. Ejecutar `pnpm agents:sync`, las pruebas, `pnpm agents:check` y `pnpm docs:check`; incluir los checks en `verify`.
- [ ] **Step 5: Probar las instrucciones con contexto nuevo.** Invocar un agente independiente con los artefactos y un encargo realista para una wiki, otro para una pequeña app local, y un caso `ready` con una modificación. Comprobar que localizan solo las guías necesarias, respetan el stack, no repiten el inicio ni instalan React, y delimitan ownership al delegar. Si el cliente Codex está disponible, comprobar descubrimiento nativo sin modificar configuración personal ni provocar operaciones externas. Registrar por separado pruebas estáticas, comportamiento en este entorno y pruebas nativas realmente realizadas; Copilot/Claude no ejecutados se declaran pendientes.
- [ ] **Step 6: Commit.** `feat: add guided agent workflows and specialist adapters`.

## Task 6: Impeccable local y ampliaciones opcionales probadas

**Files:** Crear `.agents/skills/impeccable/SKILL.md` y sus documentos de referencia necesarios, `.agents/skills/impeccable/UPSTREAM.json`, `THIRD_PARTY_NOTICES.md`, las seis recetas del mapa, `scripts/test-integrations.mjs`, `tests/integrations/{vue,tailwind,combined}.spec.ts`; modificar la ruta de diseño de la tarea 5.

**Interfaces:**
- `UPSTREAM.json`: `{repository: string, commit: string, originalPaths: string[], license: string, modifications: string[]}`. El commit debe ser un SHA real e inmutable.
- La integración de Impeccable usa contexto local de `PRODUCT.md`/`DESIGN.md` y referencias fijadas. La versión actual inspeccionada invoca un lanzador con descarga de binario: distribuir una adaptación explícita de sus instrucciones que preserve atribución y deje fuera el lanzador, hooks, live mode y detector binario. Las referencias incluidas no deben mandar ejecutar esos componentes ausentes. Identificar la adaptación, sin presentarla como una copia íntegra sin cambios.
- `pnpm test:integrations` crea tres copias temporales de aplicación/configuración/lockfile, añade Vue, Tailwind o ambos con las recetas, verifica y limpia. Nunca modifica el repositorio fuente ni usa todo el árbol de usuario como fixture.

- [ ] **Step 1: Auditar y fijar Impeccable.** Resolver un commit real de upstream; revisar licencia/NOTICE y los documentos que se distribuyen. Conservar texto de licencia y registrar cambios. Incorporar guía de nueva UI, dirección visual, crítica, pulido, accesibilidad y adaptación, junto con el cierre de sus enlaces locales. Sustituir el paso de CLI por lectura de los briefs existentes y dejar documentadas las funcionalidades upstream opcionales no incluidas. No ejecutar scripts de terceros durante esta auditoría.
- [ ] **Step 2: Escribir los casos de integración que deben fallar.** Una isla Vue con botón reactivo pasa de 0 a 1 tras click, se hidrata y no instala React. Una página Tailwind compila una utilidad y su estilo computado cambia como se espera. El caso combinado conserva CSS base, Vue, foco y tipos. Cada copia ejecuta `pnpm check`, `pnpm policy:check`, `pnpm lint` y `pnpm build`; el recorrido de navegador observa el resultado real. La prueba también compara hash de `package.json`, configuración y lockfile originales antes/después. Ejecutar los casos antes de añadir las integraciones a las copias: deben fallar por componentes/estilos ausentes.
- [ ] **Step 3: Escribir recetas y hacer pasar los casos.** Usar integración oficial Vue con hidratación explícita y configuración oficial actual de Tailwind/Vite; incorporar soporte de lint/tipos de Vue dentro de cada copia que lo necesite. Las recetas incluyen comandos, archivos a cambiar, comprobación y fuente oficial. `content.md` cubre Markdown/colecciones y cuándo optar por MDX/Starlight; `modern-css.md` explica mejora progresiva, movimiento reducido y ciclo de scripts si se eligen View Transitions. `storage.md` consume exactamente la tarea 3 y trata migraciones/IndexedDB/exportación como decisiones por producto. `supabase.md` es orientación futura sin SDK ni credenciales preinstalados.
- [ ] **Step 4: Verificar diseño y referencias.** Ejecutar `pnpm docs:check`, `pnpm test:integrations` y `pnpm verify` en el repo fuente. Probar la adaptación de Impeccable con un brief de landing y uno de wiki mediante agentes con contexto nuevo; deben respetar modos de uso diferentes, CSS elegido, identidad específica y comprobación visual, sin pedir la ejecución de un binario omitido. Registrar si esas pruebas fueron solo de planificación o incluyeron UI renderizada.
- [ ] **Step 5: Commit.** `feat: integrate local design guidance and optional stack recipes`.

## Task 7: CI, documentación de uso y entrega como template

**Files:** Crear `.github/workflows/ci.yml`, `.github/workflows/integrations.yml`, `.github/pull_request_template.md`, `README.md`, `.vscode/extensions.json`, `docs/verification.md`; ajustar exclusivamente los comandos/documentación que hayan cambiado durante la implementación.

**Interfaces:**
- CI principal: instalación congelada, `pnpm verify`, `pnpm build`, instalación de Chromium Playwright y `pnpm test:e2e`; permisos `contents: read`, sin secretos. Ejecutar en push/PR con cancelación de runs obsoletos.
- Integraciones: workflow manual y en cambios de recetas/scripts/lockfile. Ejecuta `pnpm test:integrations`; configura Node/pnpm igual que CI principal y conserva logs de fallos.
- README permite crear un repo y arrancar con una idea en lenguaje natural, además de los comandos manuales. Vercel: Astro estático, build `pnpm build`, salida `dist`; sin adapter SSR obligatorio ni despliegue de este template en una cuenta externa.
- `docs/verification.md` separa resultados ejecutados, limitaciones y pasos de reproducción. Contiene versiones reales, revisión de Impeccable, clientes de agentes probados y estado del flag GitHub template.

- [ ] **Step 1: Escribir CI y guías finales.** Incluir recomendación de extensiones Astro/Vue de VS Code sin instalar extensiones al usuario. La plantilla de PR pide problema, cambio y validación con tamaño proporcional. No publicar datos de ejemplo como canónica o métricas inventadas. Mantener las recetas largas fuera del README y de `AGENTS.md`.
- [ ] **Step 2: Verificar desde un checkout limpio.** Instalar con lockfile congelado, ejecutar `pnpm verify`, `pnpm build`, `pnpm test:e2e` y `pnpm test:integrations`. Confirmar `git diff` sin cambios producidos por checks; las recetas solo dejan artefactos temporales. Revisar página inicial en móvil/escritorio, teclado y reduced motion; conservar evidencia de revisión. Comprobar instalación sin backend, variables de entorno ni autenticación externa.
- [ ] **Step 3: Revisión independiente del conjunto.** Usar `requesting-code-review` y un revisor nuevo para comparar la rama completa con el diseño y el plan; priorizar integridad del inicio, aislamiento de pruebas, detección de React, persistencia y capacidad real de agentes. Corregir hallazgos materiales, repetir solo las comprobaciones afectadas y registrar las verificaciones pendientes de clientes no disponibles.
- [ ] **Step 4: Commit y preparación de integración.** `chore: add CI and template delivery documentation`. Usar `verification-before-completion` antes de afirmar resultados y `finishing-a-development-branch` para integrar mediante el mecanismo disponible. Preservar trabajo ajeno; no forzar pushes ni borrar ramas/remotos.
- [ ] **Step 5: Activar y comprobar template.** Usar capacidad de configuración de GitHub disponible para marcar `is_template=true` y leerla después. Si el conector no permite esa operación, usar el fallback autorizado disponible; si no se puede completar, documentar el paso exacto de Settings pendiente. No afirmar que el flag está activo sin lectura de confirmación.
- [ ] **Step 6: Entregar.** Enlazar el repositorio/PR y los pasos mínimos de uso. Informar brevemente qué se implementó, qué pasó y qué limitación real queda. El diseño aprobado y este plan se conservan como histórico, pero no forman parte de la lectura obligatoria del agente en proyectos derivados.

## Dependencias, ownership y auto-revisión del plan

Orden recomendado: 1 → 2 → 3 → 4 → 5 → 6 → 7. Las tareas 2 y 3 son aislables tras la base, pero `package.json`, lockfile y configuración compartida tienen un único propietario: el implementador principal.

- Cobertura: el inicio y contexto se implementan en 4–5; roles/delegación en 5; aplicación/CSS en 1; datos en 3; diseño/recetas en 6; políticas/CI/Vercel en 2 y 7. Los doce criterios de aceptación del diseño tienen verificación asignada.
- Contratos: `site.json` se define en 1 y se consume en 4; frontmatter y CLI se definen en 4 y se consumen en 5; almacenamiento se define en 3 y se documenta en 6. Los adaptadores comparten contratos de 5.
- Fallos de entrada: los cinco elementos de Review Focus tienen casos concretos en sus tareas. No se requiere que documentación por sí sola demuestre conducta multiagente: se separan pruebas estructurales y observaciones de runtime.
- Proporción: siete entregables revisables; no construir un generador de perfiles, un motor genérico de persistencia ni un orquestador propio.
- Elección de ejecución propuesta: implementación directa en esta sesión con revisión independiente final. Evita repartir cambios repetidos en manifiesto/lockfile; la alternativa con implementador y revisor por tarea sigue disponible si el usuario la prefiere.

Estado: plan preparado y auto-revisado, pendiente de revisión del usuario y elección de ejecución. No se ha implementado código de producto.
