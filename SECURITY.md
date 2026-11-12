# Política de seguridad de L2M

## Estado

L2M está en fase de planificación. Todavía no existe una versión funcional ni un servicio de producción.

## Principios

- Trata todos los datasets, modelos, archivos de configuración y artefactos importados como no confiables.
- Valida tamaño, formato, esquema, dimensiones, hashes y límites de recursos antes de procesar.
- No ejecutes código incluido en datasets o artefactos.
- No cargues serializaciones Python no confiables que puedan ejecutar código.
- No registres datos sensibles, tokens ni claves.
- No transmitas datos a servicios externos sin una acción explícita del usuario.
- Mantén separadas las licencias del código, datasets, dependencias y pesos de modelos.
- Los procesos de entrenamiento deberán aislarse y tener límites de CPU, RAM, disco y tiempo.

## Comunicación de vulnerabilidades

No publiques detalles explotables de una vulnerabilidad no corregida en una incidencia pública. Contacta con el mantenedor mediante los canales privados de seguridad disponibles en el perfil de GitHub del repositorio.

Incluye una descripción, impacto, pasos de reproducción seguros y versiones afectadas. No adjuntes datos personales ni secretos.

## Alcance

Esta política se refiere al código y documentación de L2M. No autoriza pruebas contra sistemas de terceros.
