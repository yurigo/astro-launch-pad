# Astro Launch Pad: diseño del template

Fecha: 2026-11-16. Estado: propuesta pendiente de aprobación; no autoriza todavía la implementación.
Repositorio: https://github.com/yurigo/astro-launch-pad

## 1. Propósito

Crear un template de GitHub para iniciar MVP, pruebas de concepto, proyectos personales, landings con diseño cuidado y wikis educativas. El usuario crea un repositorio desde el template y describe su idea al asistente, sin repetir sus preferencias técnicas ni pedirle que explore toda la plantilla.

El resultado combina una base Astro ejecutable con una entrada breve para agentes, skills que se consultan bajo demanda, especialistas y verificaciones reproducibles. La base debe poder mantenerse por una persona y adaptarse a proyectos pequeños sin convertir cada cambio en una ceremonia.

## 2. Decisiones acordadas

| Área | Decisión |
| --- | --- |
| Enfoque | Base ligera con arranque guiado, elegida frente a perfiles predefinidos. |
| Framework | Astro como base. |
| Interactividad | Vanilla para comportamiento sencillo; Vue cuando la reactividad lo justifique. |
| React | Prohibido en el código y en las dependencias propias del proyecto. |
| Estilos | CSS moderno por defecto; Tailwind opcional por proyecto. |
| Ejecución | Frontend y generación estática por defecto; sin backend obligatorio. |
| Persistencia | Almacenamiento del navegador cuando se necesite; localStorage para datos sencillos. |
| Backend futuro | Supabase es el candidato del usuario, sin SDK, autenticación ni servicio incorporados a la base. |
| Despliegue | Vercel como destino predeterminado. |
| Asistentes | Codex principal, Copilot utilizable y puerta abierta a Claude. |
| Diseño | Se ha propuesto Impeccable y el usuario lo conoce; su integración concreta se propone en este documento. |

## 3. Elecciones propuestas por el diseñador del template

Estas decisiones se someten a aprobación junto con el documento:

- TypeScript estricto para código nuevo, conservando scripts de navegador sencillos cuando corresponda. Vanilla describe ausencia de framework, no obliga a renunciar a TypeScript.
- pnpm como único gestor y un lockfile versionado. Node en una versión LTS compatible con las versiones elegidas de Astro, Vue y Vercel. Comprobar versiones reales durante la implementación y documentarlas, sin inventar números.
- Vitest para pruebas unitarias, Playwright para recorridos de navegador y axe para comprobaciones automáticas de accesibilidad. Dependencias de desarrollo; el proyecto publicado no las necesita.
- ESLint y Prettier con soporte de Astro; soporte de Vue al incorporarlo. Un comando de verificación común ejecutable localmente y en CI.
- Documentación y comunicación de los agentes en español; nombres de código, archivos y comandos en inglés. El idioma del producto se decide durante su arranque.
- Cinco especialistas además del coordinador principal: arquitectura/producto, diseño, Astro/vanilla, Vue y revisión/QA.
- Impeccable incluido como skill de proyecto revisada y fijada a una revisión concreta, con sus referencias necesarias, licencia y atribución. Actualizaciones explícitas; sin descargar ni ejecutar versiones cambiantes durante una sesión normal.

## 4. Alternativas consideradas

| Enfoque | Ventaja | Coste | Resultado |
| --- | --- | --- | --- |
| Base ligera y arranque guiado | Sirve a proyectos heterogéneos y añade herramientas según la necesidad. | El primer encargo necesita una breve definición del producto. | Elegido por el usuario. |
| Perfiles landing/wiki/app | Una estructura muy preparada para cada categoría. | Multiplica mantenimiento y genera casos híbridos. | Descartado como estructura de la primera versión. |

Las guías de landing, wiki y aplicación serán referencias bajo demanda, no tres aplicaciones de ejemplo ni ramas que haya que mantener.

## 5. Experiencia de inicio

1. El usuario utiliza «Use this template» y crea su repositorio.
2. Lo abre con su asistente y escribe, por ejemplo: «Quiero una wiki para mis clases de programación».
3. El asistente lee `AGENTS.md`, el estado mínimo de `PRODUCT.md` y la guía de arranque. No recorre todas las skills, referencias, históricos ni archivos de terceros.
4. Si el producto todavía no está definido, pregunta solo por información que cambie el resultado: objetivo, público, experiencia principal, contenido, necesidades de datos y dirección visual. No vuelve a preguntar por Astro, React o el destino de despliegue.
5. Resume alcance y decisiones en `PRODUCT.md` y la dirección visual en `DESIGN.md`. Para tareas pequeñas basta una propuesta breve; para una aplicación nueva espera la conformidad del usuario con el alcance antes de desarrollar funcionalidades.
6. Personaliza nombre y metadatos con un comando de inicialización determinista. Añade Vue o Tailwind únicamente cuando estén elegidos; sigue las integraciones oficiales y actualiza configuración, lockfile, comprobaciones y documentación.
7. Implementa, verifica y entrega un resumen de los cambios y de las comprobaciones reales.

El arranque debe poder continuar tras una interrupción. `PRODUCT.md` tendrá frontmatter con un estado inicial explícito (`uninitialized`, después `ready`), descripción y decisiones de módulos. El comando de inicialización cambia solo metadatos y los marcadores que le pertenecen, requiere un nombre válido y nunca borra trabajo para volver a ejecutar el inicio. Una segunda ejecución debe conservar los archivos del usuario y avisar ante un cambio incompatible. El comando es una herramienta del agente; el usuario no necesita aprenderlo para empezar.

Después del inicio, el asistente usa el brief existente y consulta archivos relacionados con el encargo. No repite el cuestionario en cada issue. Las decisiones importantes pendientes quedan visibles y los supuestos rutinarios se anotan brevemente.

## 6. Arquitectura de instrucciones y contexto

| Ubicación | Responsabilidad |
| --- | --- |
| `AGENTS.md` | Entrada común breve: reglas invariables, comandos, detección de inicio, tabla de rutas y delegación. Objetivo: menos de 100 líneas. |
| `START_HERE.md` | Procedimiento de arranque y reanudación; se lee cuando el proyecto aún no está definido. |
| `README.md` | Guía humana corta: crear repo, instalar, ejecutar, verificar y conectar con Vercel. |
| `PRODUCT.md` | Fuente de verdad del proyecto derivado, con estado y elecciones concretas. |
| `DESIGN.md` | Identidad visual específica del producto; empieza como brief por completar, sin estética obligatoria. |
| `.agents/skills/` | Skills de proyecto: inicio, desarrollo Astro, calidad visual/Impeccable y verificación. Sus recursos se cargan bajo demanda. |
| `docs/agents/` | Contratos comunes de los especialistas: cuándo usarlos, límites y entregable esperado. |
| `.codex/agents/` | Definiciones nativas de especialistas de Codex, ajustadas al formato documentado vigente. |
| `.github/copilot-instructions.md` | Entrada pequeña de Copilot que enlaza las reglas comunes. |
| `.github/agents/` | Adaptadores de especialistas de Copilot con los mismos contratos. |
| `CLAUDE.md` | Entrada mínima hacia las reglas comunes para un posible uso futuro; sin prometer ejecución multiagente validada en Claude. |
| `docs/recipes/` | Guías consultables de Vue, Tailwind, contenidos/wiki, persistencia, CSS experimental y backend futuro. |

Las instrucciones comunes viven una vez. Los adaptadores solo contienen los datos imprescindibles de su formato y apuntan al contrato correspondiente; una comprobación detecta enlaces rotos, nombres desalineados y referencias a archivos inexistentes. Si un formato requiere texto repetido, se mantiene mediante una fuente o una generación verificable.

No es necesario instalar estas skills en la cuenta personal de ChatGPT. Son parte del repositorio. Superpowers se utiliza para construir este template; el template resultante podrá funcionar sin tener Superpowers instalado y aprovecharlo cuando el entorno lo ofrezca, evitando copiar toda su colección y cargar instrucciones redundantes.

## 7. Especialistas y delegación

El asistente principal coordina la conversación, mantiene el alcance, decide qué delegar, asigna archivos y combina resultados. No es un sexto proceso obligatorio.

| Especialista | Responsabilidad | Entrega |
| --- | --- | --- |
| `product-architect` | Aterrizar el brief, estructura, elección de islas y persistencia. | Decisiones breves, límites y archivos afectados. |
| `ui-designer` | Dirección visual, composición, tipografía, estados y revisión con Impeccable. | Brief/tokens y hallazgos visuales concretos. |
| `astro-engineer` | Páginas, layouts, contenido, CSS y comportamiento vanilla. | Implementación y comprobaciones de su ámbito. |
| `vue-engineer` | Islas Vue, estado reactivo e hidratación, solo cuando Vue sea necesario. | Componentes y pruebas de comportamiento. |
| `qa-reviewer` | Revisar comportamiento, accesibilidad, datos locales, rendimiento básico y regresiones. | Hallazgos con pasos/evidencia; no modifica la implementación mientras la revisa. |

Reglas de coordinación:

- Delegar trabajos independientes o una revisión con contexto separado. Un cambio pequeño se hace directamente.
- El usuario no tiene que convocar manualmente a todos los especialistas. No se lanzan cinco agentes por cada encargo.
- Por defecto trabajar con un máximo propuesto de tres especialistas simultáneos, siempre limitado por las capacidades del entorno. No fijar modelos comerciales ni exigir una suscripción concreta en el template.
- Cada encargo incluye objetivo, contexto mínimo, archivos que puede modificar, restricciones, comprobaciones y formato de retorno.
- Dos agentes no editan a la vez el mismo archivo compartido. El coordinador mantiene la propiedad de integraciones, configuración y lockfile cuando haya trabajo paralelo.
- Reutilizar al implementador para correcciones. La revisión debe distinguir fallos reproducibles de preferencias de estilo.
- Detectar las herramientas disponibles. Con soporte multiagente, usar delegación real. Sin él, aplicar los contratos como fases secuenciales y explicar esa limitación, sin simular agentes ni asegurar que unos archivos Markdown activan procesos.
- Documentar cómo comprobar descubrimiento de especialistas/skills en Codex y cómo usar Copilot. No modificar configuración global del usuario ni relajar permisos.

## 8. Base técnica ejecutable

- Astro con salida estática. Dependencias de producción iniciales mínimas, sin Vue, React, Tailwind, SDK de Supabase, motor de juegos o biblioteca general de componentes.
- Estructura previsible: `src/pages`, `src/layouts`, `src/components`, `src/styles` y `src/lib`; crear dominios adicionales cuando exista una necesidad real.
- Una página inicial pequeña y reemplazable que confirme que la base funciona y permita comprobar navegación, foco y adaptación a móvil. No incluir un catálogo de pantallas ni una landing SaaS ficticia.
- Layout accesible con idioma configurable, título, descripción, enlace para saltar al contenido y metadatos básicos. URL canónica y recursos sociales solo cuando haya datos reales; no publicar URLs de ejemplo como definitivas.
- CSS organizado en tokens, base y estilos de componente. CSS encapsulado de Astro cuando resulte apropiado. Variables semánticas con valores iniciales discretos y sustituibles; sin tipografía remota, color de marca o tema oscuro obligatorio.
- CSS moderno y animaciones como mejora progresiva: contenido utilizable si la API falta y respeto a movimiento reducido. View Transitions o enrutamiento de cliente se eligen por producto; no activar un router de cliente por defecto.
- Vue se añade mediante la integración oficial y se hidrata únicamente donde haga falta. No convertir toda la aplicación en una SPA por hábito.
- Tailwind se añade bajo elección explícita del proyecto, conservando los criterios de diseño y comprobaciones. La receta contempla evitar estilos globales duplicados o conflictos con el CSS existente.
- Para wikis, empezar con Markdown/contenido Astro; documentar cuándo incorporar MDX o Starlight. No instalar todos los motores de contenido en el starter.

## 9. Datos en el navegador

Incluir una utilidad pequeña y comprobada para persistencia sencilla, sin estado de demostración obligatorio ni biblioteca global de stores. No debe acceder a `window` o al almacenamiento al evaluar módulos durante el build.

- Claves con namespace del producto y versión de formato.
- Validación del dato leído; recuperación controlada ante JSON inválido o versión desconocida, sin borrar silenciosamente datos del usuario.
- Capturar fallos de lectura/escritura y falta de espacio. La aplicación informa si un cambio no ha podido guardarse; no muestra confirmación falsa.
- Probar migraciones cuando un proyecto introduzca una versión nueva de su esquema. La base define el contrato, no un motor genérico de migraciones.
- Documentar que localStorage no sincroniza dispositivos, que puede perderse y que no es adecuado para secretos. Exportación/importación e IndexedDB se añaden cuando los datos del producto lo justifiquen.
- La receta de Supabase es una vía futura, sin activar red, credenciales ni backend. Un proyecto que lo elija necesitará su propio diseño de datos, autenticación y permisos.

## 10. Diseño y calidad visual

Impeccable se integra como recurso de proyecto con una revisión fijada y procedencia registrada. Incluir la skill y su cierre de referencias necesario; no incorporar herramientas, binarios o servicios extra ajenos al uso acordado. Verificar licencias y vínculos antes de distribuir. No instalarla en la cuenta personal del usuario como efecto lateral.

Cada producto define en `DESIGN.md` público, tarea principal, contenido real, dirección visual, paleta, tipografía, ritmo, componentes y comportamiento de movimiento. La base proporciona preguntas y criterios, no un estilo que convierta todas las webs en iguales.

La revisión visual comprueba jerarquía, legibilidad, coherencia, personalidad acorde al contenido, formularios, estados vacíos/error, teclado, móvil y movimiento reducido. Solicita referencias visuales solo si cambian materialmente la dirección. Los gradientes, tarjetas o efectos se justifican por el producto; una lista de prohibiciones estéticas no sustituye al criterio.

Para cambios de UI significativos, capturar y revisar escritorio y móvil. Registrar lo realmente observado; si no hay navegador disponible, indicar la verificación pendiente. No asegurar una interfaz exquisita por el mero hecho de incluir una skill ni inventar métricas de Lighthouse.

## 11. Comandos y CI

Contrato propuesto de comandos: `dev`, `build`, `preview`, `check` (tipos/Astro), `lint`, `format:check`, `test` (unitarias), `test:e2e`, `verify` (comprobaciones sin navegador) y `project:init`. Las comprobaciones de políticas se incluyen en `verify`; E2E es explícito porque necesita instalar un navegador.

GitHub Actions ejecuta instalación con lockfile congelado, `verify`, build y una suite breve de navegador sobre el sitio construido. Configuración con permisos mínimos y sin secretos necesarios para la CI básica. El comando de desarrollo, la CI y Vercel usan el mismo gestor y versión de Node compatible.

La política «sin React» debe rechazar dependencias directas de React/integraciones y usos propios de React/JSX, e indicar dónde corregirlos. Revisar también nuevas dependencias con peers obligatorios de React. No recorrer código vendorizado de skills, dependencias instaladas, ejemplos citados en documentación o archivos generados como si fueran aplicación propia. El chequeo es una salvaguarda de alcance documentado, no una prueba formal sobre cualquier biblioteca transitiva.

No introducir pruebas que solo confirmen la existencia de texto. Cubrir inicialización no destructiva, políticas, persistencia corrupta/bloqueada, comportamiento visible y enlaces de documentación. Probar las recetas de Vue y Tailwind sobre copias temporales para que el template publicado siga siendo ligero.

## 12. Vercel y GitHub

La base estática se prepara para importarse en Vercel sin backend, secretos ni adaptador SSR obligatorio. Documentar instalación, build y directorio de salida. Añadir adaptador solo si un proyecto pide capacidades de servidor.

La primera entrega incluye contenido funcional del repo y guía para «Use this template». Activar el indicador de template mediante la capacidad disponible; si no hay acceso a esa configuración, dejar documentado el único paso manual pendiente sin afirmar que está activado.

La entrega no crea un proyecto Vercel en la cuenta del usuario ni publica una web. El repositorio será el entregable; cualquier despliegue real tendrá su encargo propio.

## 13. Criterios de aceptación de la primera versión

1. Una copia limpia instala con el lockfile congelado, pasa las comprobaciones y genera un sitio estático utilizable.
2. La base no requiere React, Vue, Tailwind, backend, cuentas de pago ni claves API para arrancar.
3. Un agente nuevo encuentra las reglas y el flujo de inicio sin leer todo el repositorio; un agente que retoma trabajo detecta que el proyecto ya está definido.
4. El inicio personaliza metadatos, rechaza nombres inválidos y preserva trabajo al repetirse.
5. Hay contratos y definiciones reales de especialistas para Codex y adaptadores de Copilot. Si se dispone de esos clientes, comprobar descubrimiento/delegación; si no, validar estructura/formato y documentar que falta la prueba de runtime. No confundir ambas verificaciones.
6. Una tarea de muestra puede repartir implementación y revisión sin escrituras concurrentes en archivos compartidos; si solo existe ejecución secuencial, queda identificado ese límite.
7. Las integraciones opcionales Vue y Tailwind se prueban en copias desechables con build y comprobaciones; su combinación también se prueba. La base original conserva sus dependencias mínimas.
8. La política detecta una dependencia o importación prohibida de React en aplicación propia y no bloquea la documentación de esa prohibición ni terceros vendorizados.
9. Las pruebas de persistencia cubren ausencia de almacenamiento, lectura corrupta, versión desconocida y escritura fallida.
10. La página inicial y su interacción mínima se comprueban en escritorio, móvil, teclado y movimiento reducido; axe detecta problemas automáticos sin presentarse como auditoría completa.
11. Impeccable tiene origen/revisión/licencia registrados y referencias locales válidas.
12. README, instrucciones, scripts y CI describen los mismos comandos. No quedan datos de producto ficticios presentados como definitivos.

## 14. Fuera de alcance

Migrar los repositorios anteriores; construir un generador completo con perfiles; traer autenticación, SSR, pagos, CMS, sincronización o backend; incluir motores de juegos y librerías creativas en todos los proyectos; obligar a instalar Superpowers; modificar configuración global de asistentes; montar un framework propio de orquestación; y prometer que todo asistente ejecuta los mismos agentes.

## 15. Decisiones que puede resolver quien implemente

Elegir las versiones estables compatibles reales, los detalles de utilidades y fixtures, la organización interna de pruebas, los valores neutros iniciales de CSS y el formato nativo exacto de los adaptadores conforme a documentación actual. Registrar esas elecciones sin convertirlas en nuevas preguntas salvo que cambien alcance o comportamiento acordado.

## 16. Fuentes de diseño y compatibilidad consultadas

- Codex, especialistas de proyecto: https://learn.chatgpt.com/docs/agent-configuration/subagents
- Codex, descubrimiento de skills: https://learn.chatgpt.com/docs/build-skills
- Copilot, instrucciones, agentes y skills: https://docs.github.com/en/copilot/reference/customization-cheat-sheet
- Astro en Vercel: https://docs.astro.build/en/guides/deploy/vercel/
- Integración oficial de Vue: https://docs.astro.build/en/guides/integrations-guide/vue/
- Impeccable, origen a revisar/fijar al implementar: https://github.com/pbakaus/impeccable

Estas referencias justifican la compatibilidad prevista. Las pruebas de implementación deberán distinguir capacidad documentada, validación estática y ejecución realmente observada.
