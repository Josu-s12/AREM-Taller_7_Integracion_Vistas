# 📄 Informe Técnico – Taller 7: Integración de Vistas de Arquitectura

**Taller 7 – Integración de Vistas de Arquitectura**

**Equipo:** ARQTEAM-05  
**Integrantes:**
- Julián Andrés Barragán Pérez
- Juan David González Rubio
- Josue David Sarmiento Guarnizo

**Cliente:** Bray Controls Andina Ltda  
**Fecha:** 2026

---

## 1. Descripción general del trabajo

El objetivo de este taller fue integrar todas las vistas arquitectónicas desarrolladas a lo largo del curso en una narrativa coherente y articulada, que evidencie cómo cada capa de la arquitectura de Bray Controls Andina se relaciona con las demás y responde a los objetivos estratégicos del negocio.

A diferencia de los talleres anteriores, donde cada entregable se trabajó de forma independiente (BPMN, ERD, STRIDE, cumplimiento normativo, infraestructura, análisis de riesgos), este taller exigió dar un paso atrás y preguntarse: **¿cómo encaja todo esto en un solo sistema coherente?**

El trabajo se estructuró a partir de toda la evidencia levantada en el proyecto: entrevistas con Angélica (Operaciones), Felipe (Ventas internas), Julián David Rodríguez (Director Técnico) y Carlos Hernando Porras López (Gerente General), más el análisis documental realizado a lo largo del semestre.

---

## 2. Proceso de desarrollo

### 2.1 Punto de partida: el problema central

Desde las primeras entrevistas con el cliente, quedó claro que Bray Controls Andina no tiene un problema tecnológico en sentido estricto. Tiene un problema de arquitectura: sus sistemas son sólidos individualmente, pero no hablan entre sí. El ERP LN gestiona la operación, Dynamics 365 maneja las cotizaciones, la facturación electrónica corre aparte, y el seguimiento de órdenes vive en correos y hojas de Excel.

La coordinadora de Operaciones lo resumió con una frase que se convirtió en el hilo conductor de todo el taller:

> *"Me encantaría poder tener algo donde yo tenga todo en el mismo sistema. Tanto correo para dar una vaina de una orden. Lo que cambiaría es integrarlo: que yo pueda ver todo el proceso de una misma orden ahí mismo."*

Esa frase no es solo una queja operativa. Es la descripción exacta de una brecha arquitectónica: falta de integración entre capas, lo que obliga a procesos manuales que compensan lo que los sistemas no hacen solos.

### 2.2 Cómo se conectaron las vistas

El trabajo consistió en tomar cada entregable previo y encontrar los puntos de contacto entre ellos:

**Negocio → Datos:** El proceso BPMN de cotización y pedido (Taller 1) define qué entidades se crean en cada paso. Cada actividad del BPMN tiene un correlato directo en el ERD (Taller 2): una cotización en Dynamics genera una entidad `COTIZACION`; una orden de compra al corporativo genera una entidad `ORDEN_COMPRA`; una remisión dispara la `FACTURA_VENTA`. El modelo de datos no es abstracto — refleja exactamente el flujo de negocio.

**Datos → Aplicaciones:** Las entidades del ERD no existen en el aire. Viven en sistemas específicos: `CLIENTE` y `COTIZACION` en Dynamics 365; `PEDIDO_VENTA`, `INVENTARIO`, `ORDEN_COMPRA` y `REMISION` en el ERP LN; `FACTURA_VENTA` en el sistema de facturación electrónica (sin integración automática con LN). El mapa de infraestructura (Taller 5) muestra precisamente cómo estos sistemas se relacionan, o más importante, dónde **no** se relacionan.

**Aplicaciones → Infraestructura:** Los sistemas corren sobre infraestructura Microsoft administrada desde Houston: Azure para nube y backups, Active Directory para identidades, Service Desk para soporte. La infraestructura no es un problema de capacidad; es un problema de gobierno y dependencia. El Director Técnico local no puede cambiar nada sin un ticket a Houston.

**Infraestructura → Seguridad:** El análisis STRIDE (Taller 4) identificó que los riesgos más críticos no son de arquitectura técnica sino de proceso: el Reorder Point en Excel sin auditoría, la facturación manual sin trazabilidad, y la dependencia total del corporativo para responder ante incidentes. El ataque cibernético de 2022 — que paralizó la operación durante una semana — es la prueba más contundente de que la resiliencia local es la brecha más urgente.

**Seguridad → Cumplimiento:** El análisis de cumplimiento normativo (Taller 3) cierra el ciclo: la Ley 1581 requiere un responsable local de datos, mecanismos para derechos del titular y una política de tratamiento accesible. Nada de eso existe formalmente a nivel Colombia, aunque la política corporativa sí existe a nivel Houston.

### 2.3 Herramientas utilizadas

- **Draw.io** para el tablero integrado de vistas.
- **Markdown** para documentación narrativa.
- Entrevistas y transcripciones como evidencia primaria.
- Repositorio GitHub del equipo para consolidar todos los entregables.

---

## 3. Análisis del modelo integrado

### 3.1 Estructura del tablero

El tablero de vistas integradas representa la arquitectura de Bray Controls Andina en cinco capas horizontales, con flechas que muestran las dependencias e integraciones entre ellas. Cada capa tiene un código de color:

- **Morado:** capa de negocio (procesos y actores)
- **Verde:** capa de datos (entidades y flujos de información)
- **Azul:** capa de aplicaciones (sistemas y sus integraciones)
- **Gris:** capa de infraestructura (nube, backups, soporte)
- **Rojo/Naranja:** capa de seguridad y gobierno

Las líneas continuas representan integraciones existentes. Las líneas punteadas representan integraciones pendientes o procesos que hoy son manuales.

### 3.2 Lo que el tablero revela

Al poner todas las capas juntas, emergen tres patrones que no eran evidentes al ver cada vista por separado:

**Patrón 1 — El Excel como capa fantasma.** En el tablero se puede ver que entre la capa de aplicaciones y la capa de datos existe una "capa informal": Excel y correos de Outlook que actúan como puente entre sistemas que deberían estar integrados. El Reorder Point debería vivir en LN pero vive en Excel. El seguimiento de órdenes debería estar en Order Track pero está en correos. Esta capa informal no aparece en ningún diagrama oficial, pero es la que mantiene la operación funcionando.

**Patrón 2 — Houston como punto único de control en todas las capas.** En la capa de infraestructura, Houston administra Azure. En la capa de aplicaciones, Houston administra LN y Dynamics. En la capa de seguridad, Houston controla los accesos y responde ante incidentes. El tablero integrado hace visible lo que cada vista individual insinuaba: la dependencia del corporativo no es un problema de una sola capa — es transversal a toda la arquitectura.

**Patrón 3 — El ciclo de una orden como hilo conductor.** El tablero permite trazar el recorrido completo de una orden desde que el cliente la solicita hasta que recibe la factura. En ese recorrido, la orden pasa por cinco sistemas distintos y dos procesos manuales. Al verlo en el tablero, queda claro por qué Angélica dijo que quiere verlo "todo en el mismo sistema": no es un lujo, es una necesidad operativa.

### 3.3 Supuestos del modelo integrado

- La integración entre Dynamics 365 y LN es híbrida: puede ser automática (conversión de cotización a Sales Order) o manual, dependiendo del tipo de pedido. En el tablero se representa como integración bidireccional con nota de proceso híbrido.
- Order Track lee datos del LN pero aún no está operativo (kickoff 22 de mayo de 2025). Se representa en el tablero como integración en estado "en implementación".
- La integración LN → facturación electrónica se representa como proceso manual pendiente de completar, con nota del bloqueo técnico de la migración anterior.
- Las tres bodegas (Bogotá, Cali, Barranquilla) comparten el mismo ERP LN; el inventario está diferenciado por bodega dentro del mismo sistema.

---

## 4. Diagrama final entregado

El tablero de vistas integradas está disponible en:

**`tablero-integrado-cliente.drawio`**

El tablero incluye:
- Vista de negocio: actores y proceso principal (cotización → pedido → despacho → factura)
- Vista de datos: entidades principales del ERD y su sistema de residencia
- Vista de aplicaciones: ecosistema de sistemas con estado de integración
- Vista de infraestructura: arquitectura cloud + local con gestión Houston
- Vista de seguridad: controles existentes, brechas y riesgo del ataque 2022
- Flechas de integración: continuas (activas), punteadas (manuales o pendientes)

---

## 5. Coherencia de la arquitectura

La arquitectura de Bray Controls Andina es **coherente en su estado actual** en el sentido de que los sistemas que existen tienen roles bien definidos y no se contradicen entre sí. LN es el ERP, Dynamics es el CRM, Azure es la nube. No hay sistemas duplicados ni redundancias innecesarias.

Sin embargo, la arquitectura es **incoherente en su integración**: los sistemas no fluyen entre sí de forma automática, lo que obliga al personal a operar como integrador humano entre capas. Esto no es un fallo de los sistemas — es un fallo de arquitectura que no completó las integraciones necesarias.

La propuesta TO-BE que el equipo desarrolló responde directamente a esta incoherencia: no propone reemplazar nada, sino completar lo que está a medio hacer y eliminar la capa informal de Excel y correos que hoy sostiene la operación.

---

## 6. Investigación complementaria

### Tema: Integración de vistas en arquitectura empresarial — buenas prácticas y ejemplos reales

La integración de vistas arquitectónicas es uno de los desafíos más frecuentes en proyectos de arquitectura empresarial, especialmente en organizaciones que han crecido adoptando sistemas de forma incremental sin una estrategia unificada. Este patrón — conocido como "arquitectura accidental" — es precisamente lo que se observa en Bray Controls Andina.

El estándar TOGAF ADM aborda este problema a través de su fase de Opportunities & Solutions, que es exactamente donde nos encontramos con el cliente: después de documentar el AS-IS en detalle, el trabajo de integración de vistas permite identificar qué iniciativas tienen mayor impacto cruzado entre capas. En el caso de Bray, la integración LN → facturación electrónica afecta simultáneamente la capa de aplicaciones (elimina un proceso manual), la capa de datos (garantiza consistencia entre LN y el sistema de facturación) y la capa de seguridad (agrega trazabilidad automática).

ArchiMate, el lenguaje de modelado recomendado por TOGAF, permite representar estas relaciones entre capas de forma explícita mediante relaciones de `serving`, `triggering` y `association`. En el tablero de este taller se adoptaron convenciones visuales similares (flechas con etiquetas de tipo de relación) dado que el equipo trabajó en Draw.io, que no tiene soporte nativo para ArchiMate pero permite aproximar su lógica de forma visual.

Un principio clave identificado en la investigación es que la integración de vistas no debe hacerse como un ejercicio de consolidación documental — debe hacerse como un ejercicio de detección de inconsistencias. Cuando una entidad del ERD no tiene un sistema de residencia claro, o cuando un proceso del BPMN no tiene una aplicación que lo soporte, esa inconsistencia es una brecha arquitectónica. En Bray Controls Andina, el caso más claro es el Reorder Point: existe como proceso de negocio, tiene una entidad implícita en el modelo de datos, pero no tiene un sistema que lo soporte formalmente — vive en Excel.

---

## 7. Reflexión del equipo

Este taller fue, en retrospectiva, el más valioso del curso. No porque fuera el más complejo técnicamente, sino porque fue el primero en el que todos los entregables anteriores cobraron sentido como partes de un todo.

Cuando hicimos el BPMN, lo vimos como un ejercicio de modelado de procesos. Cuando hicimos el ERD, lo vimos como un ejercicio de modelado de datos. Cuando hicimos el STRIDE, lo vimos como un ejercicio de seguridad. Solo al integrar todo en un tablero fue evidente que cada uno de esos modelos estaba describiendo el mismo sistema desde ángulos distintos, y que las inconsistencias entre ellos no eran errores de modelado sino síntomas reales de brechas arquitectónicas en la empresa.

El caso de Bray Controls Andina demostró que los problemas más importantes de una arquitectura empresarial no son los que se ven en una sola vista, sino los que solo aparecen cuando se ponen todas las vistas juntas.

---

## 8. Referencias

Ver archivo `referencias.md`.
