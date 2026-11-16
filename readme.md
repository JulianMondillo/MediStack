# MediStack - Sistema de Gestión Clínica

MediStack es una aplicación web full-stack diseñada para la administración integral de clínicas y consultorios médicos. El sistema centraliza y automatiza la gestión de pacientes, profesionales, agendas, control de turnos, cobros y cierres de caja.

## Tecnologías y Arquitectura

La solución está diseñada bajo una **arquitectura en N-capas** (Web, Negocio, Dominio y Datos), garantizando la separación de responsabilidades y la escalabilidad del código.

- **Backend:** C# sobre .NET Framework 4.8 y ASP.NET Web Forms.
- **Acceso a Datos:** ADO.NET con consultas parametrizadas y control transaccional estricto.
- **Base de Datos:** Microsoft SQL Server (diseño relacional y validación de integridad referencial).
- **Frontend:** HTML5, CSS3, Bootstrap y hojas de estilo propias.
- **Seguridad:** Autenticación por sesiones, control de acceso basado en roles (RBAC) y almacenamiento seguro de contraseñas mediante algoritmos de hashing **PBKDF2**.

## Módulos Principales

### 1. Gestión Administrativa y Perfiles
- Administración completa (ABM) de Pacientes, Profesionales, Especialidades, Obras Sociales y Coberturas.
- Sistema de convenios que vincula dinámicamente obras sociales, coberturas y profesionales/especialidades.
- Ficha clínica unificada: información personal, historial cronológico de turnos y registros clínicos, y control de afiliaciones.

### 2. Motor de Agendas y Turnos
- Algoritmo de cálculo de disponibilidad en tiempo real basado en horarios de atención de los profesionales, duración de especialidades y turnos vigentes.
- Prevención automatizada de superposiciones y blindaje contra reservas en horarios fuera de agenda.
- Flujo de solicitud interactivo con vistas de próximas fechas libres (ventana de 60 días).
- Gestión transaccional de turnos: reserva, cancelación, y reprogramación (cancela el turno anterior y reserva el nuevo en una única transacción en la base de datos).
- Vistas de agenda profesional semanal (filtrando cancelaciones y diferenciando reprogramaciones) y vista de detalle diario.

### 3. Facturación y Control de Caja
- Módulo transaccional para el registro de señas y cobro de consultas médicas.
- Motor de cálculo de importes de consulta, aplicando automáticamente la cobertura del convenio vigente a la fecha del turno y descontando señas previas.
- Validación estricta de importes recibidos, cálculo de vueltos y conciliación de caja.
- Cierres de caja diarios con bloqueo transaccional: una vez ejecutado el cierre, la jornada no admite modificaciones ni cobros retroactivos.

### 4. Asistente Virtual Integrado (IA)
- Arquitectura base orientada a interfaces (`IAContratos`, `IProveedorIA`) para integrar un asistente conversacional para pacientes mediante el uso de la API de **Google Gemini**.
- Utilización del patrón *Factory* para inyectar dependencias y mantener el asistente bloqueado/desacoplado hasta la configuración de credenciales del entorno.
- Registro transaccional de las interacciones tipadas en la base de datos.

## Guía de Despliegue y Ejecución

### Prerrequisitos
- Visual Studio (2019 o superior).
- Microsoft SQL Server (Express o superior).
- .NET Framework 4.8 SDK.

### Instalación
1. Clonar el repositorio y abrir la solución `MediStack.sln` en Visual Studio.
2. Ejecutar el script SQL de inicialización ubicado en `database/01_MediStackDB.sql` para generar el esquema completo en el motor local. *(Para habilitar reprogramaciones de agenda, ejecutar posteriormente `database/07_ReprogramacionesAgenda.sql`)*.
3. Configurar la cadena de conexión en el archivo `src/MediStack.Web/Web.config` apuntando a la instancia local de SQL Server.
4. Establecer `MediStack.Web` como proyecto de inicio.
5. Ejecutar la solución utilizando **IIS Express**.

### Configuración del Adaptador de IA (Opcional)
Para habilitar el asistente inteligente, es necesario configurar las credenciales de la API de Gemini de forma segura (fuera del control de versiones). 
Crear un archivo `secrets.config` en `src/MediStack.Web/` con la siguiente estructura:

```xml
<appSettings>
  <add key="Gemini.ApiKey" value="TU_CLAVE_DE_API" />
  <add key="Gemini.Modelo" value="gemini-1.5-flash" />
</appSettings>
