# Uso de IA en el proyecto

En este documento contamos qué herramientas de IA usamos, para qué, qué reglas seguimos y en qué se equivocó la IA. La idea es que quien lea el repo sepa qué parte del trabajo tuvo ayuda y cómo la controlamos.

## Herramientas

| Herramienta | Quién | Para qué |
| --- | --- | --- |
| Claude (claude.ai, con acceso al repo público y a las capturas que le pasábamos) | Macarena | Planificar las fases, guía paso a paso, borradores de código y de documentación, revisión de PR y diagnóstico de errores |
| Claude | Juliana | *(Juliana completa: para qué lo usó en el bootstrap, los workflows, la matriz IAM y el runbook)* |
| Checkov, tflint, `tofu validate` y pytest | Las dos | No son IA, pero son la red de seguridad: todo lo que propuso la IA pasó por estos chequeos en cada PR |

## Para qué la usamos

- **Planificar:** dividir el enunciado en fases, estimar tiempos y repartir el trabajo entre las dos.
- **Aprender sobre la marcha:** que nos explique con ejemplos conceptos nuevos (OIDC, identidades administradas, Flex Consumption, cómo se cobra cada servicio).
- **Borradores de código:** los módulos de OpenTofu, la Function en Python y sus tests. Cada borrador lo corrimos, lo probamos en Azure y lo ajustamos antes de subirlo.
- **Diagnosticar errores:** le pasábamos la salida completa de la terminal o una captura y nos ayudaba a encontrar la causa (por ejemplo, el 403 al crear la tabla y el bug del provider con el CORS).
- **Revisar:** leer los PR de la otra, chequear que los datos de la documentación coincidan con los logs y con el código.
- **Documentación:** borradores del README, del informe FinOps, del blog y de las diapositivas, que después reescribimos o corregimos para que digan lo que hicimos con nuestras palabras.

## Reglas que seguimos

1. **Nada secreto ni personal en los prompts.** Nunca pegamos claves, tokens ni el contenido de `terraform.tfvars`. El CV es de una persona ficticia.
2. **No subir nada que no entendiéramos.** Si no sabíamos qué hacía un comando o un bloque de código, lo preguntábamos antes de usarlo.
3. **Todo entra por Pull Request y lo revisa la otra.** La IA nunca unió nada ni hizo un `apply` en `dev`: eso lo hace el pipeline, con la aprobación de una de las dos.
4. **Los datos se verifican.** Los precios, los límites de Azure y el comportamiento de los servicios los confirmamos en la documentación oficial, en la calculadora de Azure o en Cost Management, no solo con lo que decía la IA.
5. **Las capturas se revisan antes de subirlas,** para que no aparezcan mails, pestañas ni datos personales (el repo es público).

## Prompts representativos

Algunos de los pedidos que hicimos, tal como los escribimos:

- "Empecemos entonces... Paso a paso" (al arrancar cada fase, para que nos guíe de a un paso).
- "Me explicás el diagrama" / "para porque hacemos esto?" (cuando algo no se entendía).
- "Me ayudas a revisar el PR" (con el número del PR o el log de la corrida).
- "Tú ve diciéndome qué capturas tengo que sacar" (para juntar las evidencias a medida que avanzábamos).

## En qué se equivocó la IA (y cómo lo detectamos)

| Qué dijo | Qué era en realidad | Quién lo encontró |
| --- | --- | --- |
| Para subir la Function, `sku: flex` | La acción pide `sku: flexconsumption` | Juliana, leyendo la documentación de la acción |
| Que los dos mails estaban verificados porque decían "Suscrito" | "Suscrito" y "Verificado/Pendiente" son estados distintos; el de Macarena seguía pendiente | Juliana, en el portal después del redeploy |
| Checkov tenía 19 excepciones | Son 25: el CI revisa toda la carpeta `infra`, no solo los módulos | Juliana, contando en el código |
| El costo de la Function con un millón de visitas, unos USD 10,70 | La calculadora de Azure da USD 15,10 en Canada Central (usamos el precio de otra región) | Macarena, al cargarlo en la calculadora |

## Qué aprendimos usando IA

- Sirve mucho para destrabarse y para aprender rápido, pero hay que **verificar todo**: los errores de arriba los encontramos porque revisábamos los PR entre las dos y contrastábamos con la documentación.
- Funciona mejor cuando le das contexto real (la salida completa del error, el estado del repo) que cuando le preguntás en abstracto.
- La documentación queda mejor si después la reescribimos con nuestras palabras: así también nos aseguramos de entender lo que dice.
