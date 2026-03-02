Listado de dominio - Detalle de entidades

Entidades reales

1. Personas:
   Representan a las personas reales, tanto los pacientes como los profecionales.

   Atributos:
   id: UUID
   nombre: string
   apellido: string
   dni: int
   celular: int
   email: string
   fechaNacimiento: Date
   direccion: string

   Metodos: + calcularEdad (fechaNacimiento: Date) : smallint

2. Paciente:
   Representan a las personas que se atienden el centro de rehabilitacion. Los clientes!!
   Hereda de Class Persona

   Reglas de negocio:
   Deben ser mayores de 18 anos o tener un adulto responsable registrado.
   El paciente debe poder ser cargado aunque no tenga todavía Historia Clinica
   Debe poder ser cargado aunque no tenga pedido médico
   Debe tener una y solo una Historia Clínica
   Los pacientes no se borraran del sistema, se les dará baja logica.

   Atributos:
   id:
   nroHistClinica: int
   fechaBaja: Date
   fechaAlta: Date
   OS: OS
   esAdulto: bool
   estado: bool
   tutor: Persona

   Metodos:
   determinarAdultes (fechaNacimiento: Date): bool
   determinarEstado (fechaBaja: Date): bool

3. Usuario:
   Encargado de la carga de la informacion y la asignacion de turnos
   Accede al servicios de turnos para
