# Adenda

**Pagos programables por adendas multiparte en Stellar.**

Adenda es una propuesta para organizaciones que externalizan trabajos y coordinan varios subcontratistas. Está pensada para proyectos con entregables interdependientes, como producción audiovisual, fabricación, montaje de eventos y consultoría. Cuando el cliente solicita un cambio, la plataforma identifica qué compromisos se ven afectados, calcula el presupuesto adicional y coordina la aceptación de una misma versión de la adenda. Un contrato Soroban activa los compromisos nuevos cuando se reúnen las autorizaciones requeridas y el saldo permite reservar todo el adicional.

## Problema

Una modificación acordada entre el cliente y la empresa contratante puede cambiar el trabajo de varios proveedores. Las cotizaciones y aprobaciones quedan repartidas entre correos, chats y órdenes de compra. Así, la empresa contratante puede asumir un costo sin respaldo y el proveedor puede empezar un trabajo que todavía no tiene fondos reservados.

## Cómo funciona una adenda multiparte

1. **Identificar el impacto.** Un grafo dirigido acíclico (DAG) representa las dependencias entre entregables. El backend propaga el cambio a las tareas relacionadas y prepara una propuesta con los nuevos entregables, plazos e importes por proveedor. La empresa contratante confirma qué condiciones se modifican y quién debe autorizarlo.
2. **Versionar y autorizar.** El cliente, la empresa contratante y cada proveedor cuyas condiciones cambian autorizan la misma versión. La adenda vincula la versión base, el hash del documento, los importes y los firmantes requeridos. Si cambian los términos, se emite otra versión y se recogen nuevas autorizaciones.
3. **Reservar y activar.** Soroban comprueba las autorizaciones y el saldo disponible para esa adenda. En una sola transacción, reserva los importes por obligación y marca la versión como activa. Si falta una firma o hay fondeo parcial, el cambio queda pendiente. Los entregables ya aceptados bajo el acuerdo anterior conservan sus condiciones de pago.
4. **Liquidar por entregable.** Tras la aprobación del hito, se habilita el pago del importe reservado para ese proveedor. La conciliación conserva la relación entre la adenda, la obligación y la transacción.

## Arquitectura propuesta en Stellar

| Componente | Responsabilidad |
| --- | --- |
| Backend de Adenda | Mantiene el DAG y los documentos comerciales fuera de cadena. Calcula el impacto, presenta la versión para revisión y concilia los eventos mediante Stellar RPC. |
| Contrato coordinador en Soroban | Valida el estado, la versión, los firmantes y el fondeo. Mantiene las reservas por adenda y por obligación y bloquea la doble activación. |
| Bell, Protocolo 30 (CAP-84) | Asigna una dirección multiplexada `W…` por adenda sobre el mismo contrato de tesorería. El Stellar Asset Contract (SAC) de USDC registra el identificador en el evento de transferencia. |
| Trustless Work | Integración prevista para gestionar escrows y liberar pagos por hitos. El contrato coordinador conserva la regla de activación multiparte. La integración deberá comprobar cómo fondear y liberar cada obligación sin duplicar el movimiento de fondos. |

Una dirección `W…` combina la dirección base del contrato con un identificador. Bell permite conciliar los depósitos sin desplegar una tesorería por adenda. **Los fondos comparten el saldo del contrato base y Adenda mantiene las reservas por separado.** El flujo de fondeo previsto usa una función del coordinador para transferir y acreditar el importe en la misma transacción. Los contratos comerciales y los datos personales permanecen fuera de cadena. [Bell y CAP-84](https://stellar.org/blog/developers/bell-protocol-30-upgrade-guide) · [Direcciones multiplexadas y eventos del SAC](https://developers.stellar.org/docs/tokens/stellar-asset-contract#sending-to-a-muxed-address) · [Trustless Work V2](https://www.trustlesswork.com/escrow-times/dev-update-trustless-work-v2)

## Ejemplo de alcance para Codember

El cliente cambia las dimensiones de la señalética de un evento. La adenda requiere **100 USDC de prueba para rediseño y 60 para corte**. Un hito de instalación ya aceptado en otra zona conserva su pago.

- Con las firmas completas y solo 100 USDC depositados, la adenda sigue pendiente.
- Cuando llegan los 60 restantes, el contrato activa la adenda y registra ambas reservas en una sola transacción.
- Un depósito enviado a la dirección `W…` de otra adenda queda asociado a esa otra orden. Un segundo intento de activación no duplica las reservas.

El alcance inicial es un cliente, una empresa contratante, dos proveedores afectados y un activo en Stellar Testnet. El modelo previsto es B2B SaaS. Una expansión a subcontrataciones internacionales dependerá de validar la liquidación y la conversión a moneda local con las contrapartes.

## Estado del repositorio

**Adenda está en fase de propuesta.** El código y el despliegue de testnet que ya existen corresponden al prototipo Mesa Editorial. Sus instrucciones de ejecución y verificación están en la [documentación de Mesa Editorial](docs/mesa-editorial.md). El [planteamiento original](message.txt) recoge los antecedentes de Adenda.
