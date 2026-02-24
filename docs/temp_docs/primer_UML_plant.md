@startuml
skinparam classAttributeIconSize 0
skinparam linetype ortho

' =====================
' Enumerations
' =====================

enum EstadoTurno {
PENDIENTE
CONFIRMADO
CANCELADO
REPROGRAMADO
AUSENTE
}

enum EstadoCobro {
PENDIENTE
PAGADO
ANULADO
}

enum MedioPago {
EFECTIVO
TRANSFERENCIA
TARJETA
OBRA_SOCIAL
}

enum TipoRecordatorio {
WHATSAPP
EMAIL
}

enum EstadoRecordatorio {
PENDIENTE
ENVIADO
FALLIDO
}

enum RolUsuario {
ADMIN
RECEPCION
PROFESIONAL
}

' =====================
' Core Classes
' =====================

class Usuario {
id: UUID
email: String
passwordHash: String
rol: RolUsuario
activo: Boolean
}

class Paciente {
id: UUID
nombre: String
apellido: String
dni: String
fechaNacimiento: Date
telefono: String
email: String
activo: Boolean
}

class Profesional {
id: UUID
nombre: String
apellido: String
especialidad: String
matricula: String
activo: Boolean
}

class Turno {
id: UUID
fecha: Date
horaInicio: Time
duracionMin: Integer
estado: EstadoTurno
motivo: String
}

class HistoriaClinica {
id: UUID
fechaApertura: Date
observacionesGenerales: Text
}

class Evolucion {
id: UUID
fecha: Date
evolucion: Text
plan: Text
}

class Cobro {
id: UUID
fecha: Date
monto: Decimal
medioPago: MedioPago
estado: EstadoCobro
}

class Recordatorio {
id: UUID
tipo: TipoRecordatorio
fechaEnvio: DateTime
estado: EstadoRecordatorio
}

' =====================
' Associations
' =====================

Paciente "1" -- "1" HistoriaClinica : posee >
HistoriaClinica "1" -- "0..\*" Evolucion : contiene >

Paciente "1" -- "0.._" Turno : solicita >
Profesional "1" -- "0.._" Turno : atiende >

Turno "1" -- "1" Paciente
Turno "1" -- "1" Profesional

Turno "1" -- "0..1" Evolucion : documenta >
Profesional "1" -- "0..\*" Evolucion : registra >

Turno "1" -- "0..1" Cobro
Paciente "1" -- "0..\*" Cobro

Turno "1" -- "0..\*" Recordatorio

@enduml
