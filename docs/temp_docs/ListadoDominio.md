Listado de dominio - Detalle de entidades

<b>Entidades</b>

1️⃣ Persona
Descripción

Entidad base que representa a cualquier individuo registrado en el sistema.

Atributos

id
nombre
apellido
dni
telefono
email
fechaNacimiento
direccion

Métodos
actualizarDatosPersonales()
obtenerNombreCompleto()
calcularEdad()

Reglas de negocio
El DNI no puede repetirse en el sistema.
Los datos personales son obligatorios para la creación.

2️⃣ Paciente
Descripción
Representa a una persona que recibe atención kinesiológica.

Hereda de Persona.

Atributos
numeroHistoriaClinica
obraSocial
numeroAfiliado
activo
Métodos
solicitarTurno()
cancelarTurno()
consultarTurnos()

Reglas de negocio
Debe tener historia clínica única.
Puede tener múltiples turnos.
No puede solicitar turno si está inactivo.
Puede tener múltiples evoluciones asociadas.

3️⃣ Profesional
Descripción
Representa a un kinesiólogo que atiende pacientes.

Hereda de Persona.

Atributos
matricula
especialidad
activo

Métodos
definirDisponibilidad()
consultarAgenda()
asociarUsuario()

Reglas de negocio
La matrícula debe ser única.
Puede existir sin tener Usuario asociado.
Puede tener múltiples turnos asignados.
Solo podrá registrar evoluciones si tiene un Usuario activo con rol PROFESIONAL asociado.
No puede tener más de un Usuario asociado.

4️⃣ Usuario
Descripción
Representa una cuenta de acceso al sistema. Administra todos los services

Hereda de Persona.

Atributos
username
password
rol (ADMIN, SECRETARIO, PROFESIONAL)
activo
profesionalAsociado (opcional)

Métodos
autenticar()
cambiarPassword()
desactivar()
tienePermiso()

Reglas de negocio
El username debe ser único.
Si el rol es PROFESIONAL:
Debe estar asociado a un Profesional existente.
El Profesional no puede estar asociado a mas de 1 Usuario.
Puede existir Usuario sin estar vinculado a Profesional (ej: Secretario).
Un Usuario inactivo no puede operar en el sistema.
Solo con rol ADMIN se pueden crear o asociar usuario crearUsuario()

5️⃣ Turno
Descripción
Representa una reserva de atención para un Paciente con 1 o ningun profesional asignado, en una fecha y horario determinado y disponible.

Atributos
id
fecha
hora
box/camilla/gym
nroTurnoDiario <!-- se podria identificar cada turno con un codigo formado por nroCamilla, hora, dia, mes para las reservas -->
estado (DISPONIBLE, RESERVADO, CANCELADO, ATENDIDO)
paciente
profesional (opcional)

Métodos
reservar()
cancelar()
marcarComoAtendido()

Reglas de negocio
El turno se crea cuando coinciden el espacio, la hora, el dia y el profesional
Un Profesional puede (segun su decision) puede tener dos turnos en el mismo horario (prof: 1; 10hs; box 2 ---- prof: 1; 10hs; box 4)
Un Paciente no puede tener dos turnos simultáneos.
Solo puede reservarse si está DISPONIBLE.
Solo puede cancelarse si está RESERVADO.
Solo puede marcarse como ATENDIDO si está RESERVADO.

6️⃣ Evolucion
Descripción
Registro clínico generado luego de: una o varias sesiones y/o a la finalizacion de una etapa o tratamiento.

Atributos
id
fecha
descripcion
paciente
profesional
sesion
tipoEvolucion: Tratamiento

Métodos
editar()
eliminar()

Reglas de negocio
Solo puede ser creada por un Usuario con rol PROFESIONAL.
El Usuario debe estar asociado al Profesional que genera la evolución.
Solo puede registrarse si el turno está en estado ATENDIDO.
Un turno puede tener como máximo una evolución.

7️⃣ Disponibilidad
Descripción
Representa los bloques horarios en los que un Profesional ofrece atención.
Permite separar la agenda estructural (horarios posibles) de los Turnos concretos reservados.

Atributos
id
profesional
fecha
diaSemana
horaInicio
horaFin
duracionTurno
disponible
estadoTurno (por si lo cancela el profesional)

Métodos
generarTurnos()
modificarDiasSemana()
modificarHorario()
editarDuracion()
desactivar()

Reglas de negocio
Un Profesional puede tener múltiples disponibilidades.
La duración del turno debe ser mayor a cero.
Solo pueden generarse turnos dentro del rango horaInicio–horaFin.
Si la disponibilidad false, no deben generarse nuevos turnos.
La fecha debe ser posterior a fecha acutual.
El profesional debe estar ACTIVO

8️⃣Especialidad
Descripción
Representa el área de especialización de un Profesional.

Atributos
id
nombre
descripcion
activa
matriculaEspecialidad (opcional)

Métodos
activar()
desactivar()
agragar()
editar()

Reglas de negocio
El nombre debe ser único.
Un Profesional puede tener una o varias especialidades.
No puede asignarse una especialidad a un profesional inactivo.

9️⃣ObraSocial
Descripción
Representa una entidad financiadora o cobertura médica.

Atributos
id
nombre
plan
montoSesion
coseguroOS
coseguroInstitucion
codigo
activa
email
telefono
detalles

Métodos
activar()
desactivar()
editar()
agregar()

Reglas de negocio
El nombre debe ser único.
Un Paciente puede tener una ObraSocial activa. Es la que usa por defecto.
No puede asignarse una ObraSocial inactiva.

🔟 Rol
Descripción
Define los permisos operativos dentro del sistema.

Tipos posibles
ADMIN
SECRETARIO
PROFESIONAL

Reglas de negocio
El rol determina los permisos del Usuario.
Solo PROFESIONAL puede registrar evoluciones.
ADMIN puede gestionar usuarios y configuraciones.
SECRETARIO puede gestionar turnos y cobros, facturacion pero no evoluciones.

SERVICIOS DE DOMINIO

1️⃣TurnoService
Responsabilidad

Gestionar la creación, cancelación y validación de turnos.

Reglas que aplica
CRUD disponibilidad de turnos.
Verificar si se permiten sobreturnos.
Verificar la disponibilidad horaria.
Verificar disponibilidad del Profesional.
Verificar que el Paciente no tenga turno simultáneo.
Asignacion de turno disponible a paciente (crear Turno)
Cambiar estados correctamente.
Validar permisos del Usuario que opera.
Varidar que la persona no posea deuda. (if deuda no turno)

Relaciones
-pacientes
-disponibilidad
-turnos
-usuarios

2️⃣EvolucionService
Responsabilidad
Gestionar el registro de evoluciones clínicas.

Reglas que aplica.
Validar que el Usuario tenga rol PROFESIONAL.
Validar que el Turno esté en estado ATENDIDO o sesion realizada.
Garantizar que el turno no tenga evolución previa.

Relaciones
-profesional
-sesion
-tratamiento
-historia clinica

3️⃣ SesionService
Responsabilidad
Gestionar la efectivizacion de un turno atendido por un profecional.

Reglas que aplica.
Validar profesional activo.
Validar paciente activo.
Validar estado del turno.

4️⃣ UsuarioService
Responsabilidad
Gestionar autenticación y administración de usuarios.

Reglas que aplica
Username único.
Asociación válida con Profesional si rol = PROFESIONAL.
Encriptación de contraseña.

5️⃣ CobroService
Responsabilidad
Gestionar el registro de las deudas y los pagos que realizan las personas por los turnos.
Controlar el estado de la sesion.
Controlar el estado del turno.
Buscar montos de las OS

Reglas que aplica.
La persona que paga debe ser mayor de edad.
Debe tener un medio de pago
