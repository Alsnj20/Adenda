# Adenda

**Modificaciones contractuales multiparte para proyectos con subcontratistas.**

## Adendas versionadas

Cada versión vincula los cambios de alcance con los **entregables afectados, sus costos, plazos y firmantes requeridos**. La plataforma recalcula automáticamente el impacto presupuestario entre proveedores.

## Activación y pagos

**Soroban activa la adenda en una sola transacción atómica** después de validar las dependencias y comprobar que están todas las firmas y los depósitos necesarios.

Los fondos comprometidos quedan protegidos y cada pago se libera cuando se confirma la conformidad del entregable correspondiente. Así se evitan los cambios informales y los compromisos sin respaldo financiero.

## Arquitectura en Stellar

| Componente | Función |
| --- | --- |
| **Soroban** | Mantiene el grafo versionado y valida las condiciones para activar la adenda. |
| **Bell (Protocolo 30, CAP-84)** | Usa direcciones multiplexadas `W…` para identificar y conciliar los depósitos por adenda dentro de una arquitectura compartida. |
| **Trustless Work** | Proporciona los contratos de escrow para custodiar fondos y liberar pagos por hitos. |
