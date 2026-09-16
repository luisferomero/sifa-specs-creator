# Constitucion del Proyecto: Sistema de Reservas de Padel

## 1. Naturaleza del Proyecto

Este es una aplicacion para la reserva de canchas de padel. Su proposito es permitir a los usuarios autenticarse y gestionar reservas de tiempo en espacios especi­ficos.

## 2. Stack Tecnologico (Reglas de Implementacion)

- **Frontend / UI:** React. Usar Tailwind CSS para los estilos.
- **Backend:** Node.js con Express.
- **Base de Datos:** SQLite local (archivo `padel.db`). No usar ORMs pesados para mantener la simplicidad; usar `better-sqlite3` o sentencias SQL puras.
- **Lenguaje:** TypeScript en todo el stack.

## 3. Reglas de Dominio y Logica de Negocio

Estas reglas son inmutables. El agente de IA debe respetarlas estrictamente:

- **Catalogo Cerrado:** El sistema SOLO maneja 5 canchas fijas: Cancha Laureles, Cancha El Poblado, Cancha Belen, Cancha Robledo, y Cancha Envigado.
- **Bloques de Tiempo:** Las reservas operan en formato de 24 horas.
- **Prevencion de Colisiones (Double-Booking):** Es la regla cri­tica del sistema. Bajo ninguna circunstancia se puede escribir una reserva en la base de datos sin validar primero que la cancha seleccionada este libre en ese horario.
- **Autenticacion:** Todo flujo de reserva exige que haya un usuario con sesion activa.

## 4. Estructura y Estilo de Codigo

- **Estructura Plana:** Evitar la sobreingenieri­a. No implementar "Clean Architecture" ni patrones complejos. Usar una estructura simple: `/frontend`, `/backend`, y `/db`.
- **Estilo:** Priorizar la programacion funcional y los componentes funcionales (Hooks en React). Evitar el uso de clases a menos que sea obligatorio.
- **Nomenclatura:** Usar `camelCase` para funciones/variables y `PascalCase` para Interfaces/Tipos.

## 5. Manejo de Errores y Validaciones

- **UI:** Nunca exponer errores crudos o *stack traces* al usuario final. Todo error tecnico debe traducirse a un mensaje amigable (ej: "La cancha ya fue reservada en este horario").
- **Backend:** Retornar siempre codigos de estado HTTP semanticos (400 peticion invalida, 401 no autenticado, 409 conflicto de reserva).

## 6. Comportamiento del Agente de IA (Reglas SDD)

- **Cero Codigo Sombra (Shadow Code):** Construye estrictamente lo documentado en `spec.md`. No agregues caracteri­sticas "por si acaso" (no pasarelas de pago, no perfiles complejos, etc.).
- **Fuente de la Verdad:** Si una instruccion del usuario contradice esta constitucion o si detectas una falla logica, detente. Advierte del problema y solicita actualizar el `spec.md` antes de tocar el codigo fuente.
