# ADL Lambda Implementation Guide - Context Navigation

## Propósito
Esta guía ayuda a los agentes IA a navegar y utilizar el contexto ADL para generar planes de implementación de servicios de pagos Lambda.

## Arquitectura Objetivo
- **Patrón**: Arquitectura Hexagonal (Clean Architecture)  
- **IoC**: Inversify para inyección de dependencias
- **Runtime**: AWS Lambda con Node.js/TypeScript
- **Validación**: Joi para esquemas
- **Testing**: Jest con 85% cobertura mínima

## Orden de Implementación Recomendado

### Fase 1: Configuración Base
1. **01-package-template.md** - Generar package.json estándar
2. **02-project-structure.md** - Crear estructura de directorios
3. **03-environment-config.md** - Configurar variables de entorno

### Fase 2: Implementación Core
4. **04-entry-point-patterns.md** - Implementar index.js y assembler.js
5. **05-domain-models.md** - Definir modelos y validaciones
6. **06-infrastructure-services.md** - Implementar servicios de infraestructura

### Fase 3: Lógica de Negocio
7. **07-application-layer.md** - Implementar servicios de aplicación
8. **08-controller-patterns.md** - Implementar controladores

### Fase 4: Testing y Seguridad
9. **09-testing-standards.md** - Configurar tests y cobertura
10. **10-e2e-cypher-integration.md** - Integrar cifrado E2E

### Fase 5: Documentación y Deploy
11. **11-documentation-templates.md** - Generar documentación obligatoria
12. **12-deployment-scripts.md** - Scripts de despliegue

## Reglas Críticas para Agentes IA

### OBLIGATORIO - Usar Siempre
- Template de package.json exacto de la sección 2.1
- Dependencias ADL específicas con versiones exactas
- Patrón de entry point con e2e.setHandler()
- Estructura de directorios obligatoria
- Validaciones Joi para headers y body
- Analytics obligatorio via SQS

### NUNCA Omitir
- Integración E2E Cypher
- Error logging estructurado
- Cobertura de tests mínima 85%
- Documentación obligatoria (5 archivos)
- Variables de entorno de seguridad

### Referencias Rápidas
- **Dependencias críticas**: `@avaldigitallabs/*` libraries
- **Patrón IoC**: Inversify con symbols
- **Validación**: Headers + Body schemas obligatorios
- **Testing**: Jest + ts-jest configuration
- **Build**: TypeScript compilation + ZIP packaging

## Contexto por Tipo de Tarea

| Tipo de Implementación | Archivos de Contexto Requeridos |
|------------------------|----------------------------------|
| **Nueva Lambda completa** | Todos los archivos 01-12 |
| **Solo configuración** | 01-03 |
| **Solo lógica de negocio** | 04-08 |
| **Solo testing** | 09 |
| **Solo documentación** | 11 |
| **Solo seguridad/E2E** | 10 |

## Validación de Completitud

Un servicio Lambda ADL está completo cuando tiene:
- ✅ package.json con template estándar
- ✅ Estructura de directorios obligatoria  
- ✅ Entry point con e2e.setHandler()
- ✅ Contenedor IoC configurado
- ✅ Validaciones Joi implementadas
- ✅ Tests con 85%+ cobertura
- ✅ 5 archivos de documentación
- ✅ Variables E2E configuradas
- ✅ Scripts de build/deploy
