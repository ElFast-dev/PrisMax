# PrisMax — validación de Prisma

Este repositorio público sirve únicamente como punto de ejecución de GitHub Actions. **El repositorio privado `ElFast-dev/Prisma` continúa siendo la fuente principal del código.** Este repositorio no contiene una copia del código de Prisma ni datos personales de Showly. La validación automática quedó centralizada aquí porque los jobs de Actions del repositorio privado estaban fallando antes de iniciar pasos; en Prisma se conserva un workflow manual como alternativa.

## Configuración inicial (una sola vez)

El workflow público necesita permiso de solo lectura para descargar el repositorio privado.

1. Crea un **fine-grained personal access token** en GitHub que tenga acceso únicamente a `ElFast-dev/Prisma`. Concede `Contents: Read-only` (GitHub también requiere acceso de metadatos de lectura, incluido por defecto).
2. Entra en **Settings → Secrets and variables → Actions** de este repositorio público: <https://github.com/ElFast-dev/PrisMax/settings/secrets/actions>.
3. Crea un repository secret llamado exactamente `PRISMA_REPO_TOKEN` y pega el token ahí. No lo escribas en este README, en el código ni en una conversación.
4. La rama `main` de Prisma se valida automáticamente cada hora (minuto 17 UTC); cualquier cambio al workflow de PrisMax también dispara una validación. También puedes iniciar una prueba manual desde **Actions → Validar Prisma privado → Run workflow**.
5. En una ejecución manual, introduce la rama, etiqueta o SHA de Prisma que quieres verificar. El valor predeterminado es `main`; para volver a probar la corrección v0.14.6, selecciona `fix/showly-import-metadata-v0.14.6`.

## Qué hace

- Descarga directamente de `ElFast-dev/Prisma` la revisión indicada; no sincroniza ni publica su código en PrisMax.
- Ejecuta `npm run check` y `npm test` con Node.js 22 en un runner estándar `ubuntu-latest`. `check` valida también sintaxis de Python y los scripts Bash de Termux.
- La prueba del endpoint de parada verifica que el mensaje indique cómo reiniciar manualmente con `bash ~/prisma/termux/start.sh`, sin depender de Termux:Widget.
- Valida la rama `main` de Prisma cada hora (cron UTC minuto 17), al cambiar la configuración del runner y bajo demanda. La ejecución programada debe seguir observándose para confirmar que GitHub la lanza puntualmente.
- No despliega cambios, no cambia ramas en el repositorio principal y no copia perfiles personales.
- Usa `persist-credentials: false` para no dejar el token de acceso configurado en Git durante los pasos de prueba.

## Privacidad y facturación

Los logs de Actions pueden incluir nombres de pruebas y mensajes de error; antes de publicarlos, evita añadir datos personales o credenciales a los tests. El secreto no debe imprimirse en los logs.

Los runners estándar alojados por GitHub son gratuitos para repositorios públicos, pero siguen aplicando límites generales de ejecución, concurrencia y otras políticas de Actions. Este workflow no utiliza runners de mayor tamaño.
