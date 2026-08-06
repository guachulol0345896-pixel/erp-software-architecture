Introducción y Metas {#section-introduction-and-goals}

Este documento describe la arquitectura del Sistema ERP, enfocado en el Módulo de Compras, que gestiona los procesos de negocio relacionados con productos, proveedores y órdenes de compra de la empresa.

Vista de Requerimientos {#_vista_de_requerimientos}

Requisitos de negocio más importantes del Módulo de Compras:

Mantener un catálogo de productos con su información básica (nombre, descripción, unidad).
Registrar proveedores y asociarlos a los productos que ofrecen.
Generar órdenes de compra a partir de necesidades de reabastecimiento.
Permitir aprobación o rechazo de las órdenes de compra generadas.
Registrar la recepción de mercancía y actualizar el inventario correspondiente.
Metas de Calidad {#_metas_de_calidad}
Prioridad	Atributo de calidad	Motivación
1	Usabilidad	El personal que usa el sistema no necesariamente tiene formación técnica.
2	Disponibilidad	El sistema debe estar disponible durante el horario de operación del negocio.
3	Trazabilidad	Cada movimiento de compras e inventario debe quedar registrado para auditoría.
Partes interesadas (Stakeholders) {#_partes_interesadas_stakeholders}

+-------------+---------------------------+---------------------------+ | Rol/Nombre | Contacto | Expectativas | +=============+===========================+===========================+ | Administrador de Compras | Encargado del área de compras | Gestionar productos, proveedores y órdenes de compra de forma ágil. | +-------------+---------------------------+---------------------------+ | Administrador del negocio | Dirección de la empresa | Visibilidad y control sobre el gasto en compras. | +-------------+---------------------------+---------------------------+

Restricciones de la Arquitectura {#section-architecture-constraints}

Restricciones técnicas

Frontend: Single-Page Application (JavaScript, React).
Backend: API Monolítica (Java, Spring Boot).
Base de datos: PostgreSQL.
Modelado y diagramas: PlantUML, siguiendo la notación C4 (Contexto y Contenedores).
Control de versiones: GitHub (repositorio erp-software-architecture).
Gestión del backlog: Notion (tablero Kanban).

Restricciones organizacionales

Proyecto desarrollado de forma individual en el marco de un curso académico.
Duración limitada al semestre académico.
Alcance y Contexto del Sistema {#section-context-and-scope}
Contexto de Negocio {#_contexto_de_negocio}

![Diagrama de Contexto](./c1.png)

El Administrador de Compras registra productos y proveedores en el Sistema ERP.
El Sistema ERP envía datos de facturas y asientos contables a un Sistema Contable Externo.
Contexto Técnico {#_contexto_técnico}

El sistema se expone como una aplicación web accesible por HTTPS. La comunicación con el Sistema Contable Externo se realiza mediante el envío de datos de facturación y asientos contables.

Estrategia de solución {#section-solution-strategy}

Se optó por una arquitectura de aplicación web monolítica, compuesta por una Single-Page Application (React) que consume una API Monolítica (Java, Spring Boot), la cual centraliza toda la lógica de negocio y se conecta a una base de datos relacional (PostgreSQL). Esta estrategia es adecuada para el alcance de un MVP, priorizando simplicidad de despliegue sobre la escalabilidad de una arquitectura distribuida.

Vista de Bloques {#section-building-block-view}
Sistema General de Caja Blanca {#_sistema_general_de_caja_blanca}

![Diagrama de Contenedores](./plantuml.png)

Motivación : Separar la interfaz de usuario, la lógica de negocio y la persistencia en capas claras.

Bloques de construcción contenidos : Single-Page Application, API Monolítica, Base de Datos.

Interfases importantes : HTTPS entre el usuario y la SPA; HTTPS/JSON entre la SPA y la API; JDBC entre la API y la base de datos.

Single-Page Application {#_caja_negra_1}

Propósito/Responsabilidad: interfaz de usuario en el navegador, construida en JavaScript con React.

API Monolítica {#_caja_negra_2}

Propósito/Responsabilidad: maneja toda la lógica de negocio del módulo de Compras (Java, Spring Boot).

Base de Datos {#_caja_negra_n}

Propósito/Responsabilidad: almacena todos los datos del ERP (PostgreSQL).

Vista de Ejecución {#section-runtime-view}
Escenario: Registrar un producto nuevo {#_escenario_de_ejecución_1}

![Diagrama de Secuencia](./datos.png)

Este escenario corresponde a la historia de usuario "Como gestor de inventario, quiero registrar nuevos productos...".

El administrador rellena y envía el formulario de nuevo producto desde la SPA.
La SPA envía una petición POST /api/productos a la API con los datos del producto.
La API valida los datos recibidos.
La API ejecuta un INSERT INTO productos en la base de datos.
La base de datos confirma la creación del producto con su ID.
La API responde 201 Created con el producto creado.
La SPA muestra un mensaje de éxito y actualiza la lista de productos.
Vista de Despliegue {#section-deployment-view}

(Opcional — no desarrollado en esta fase del taller)

Conceptos Transversales (Cross-cutting) {#section-concepts}
Modelo de datos del módulo de Compras {#_concepto_1}

![Diagrama Entidad-Relación](./producto.png)

Las entidades principales del módulo son Producto, Proveedor, y Producto_Proveedor (relación muchos-a-muchos entre productos y proveedores, con el precio unitario que cada proveedor ofrece por producto).

Decisiones de Diseño {#section-design-decisions}

Se decidió separar la interfaz (SPA) de la lógica de negocio (API Monolítica) para permitir que ambas capas evolucionen de forma independiente, a costa de una mayor complejidad de despliegue frente a una aplicación monolítica de servidor tradicional.

Requerimientos de Calidad {#section-quality-scenarios}
Árbol de Calidad {#_árbol_de_calidad}

Usabilidad > Disponibilidad > Trazabilidad (ver tabla de Metas de Calidad en la sección de Introducción).

Escenarios de calidad {#_escenarios_de_calidad}

(No desarrollado en detalle en esta fase del taller)

Riesgos y deuda técnica {#section-technical-risks}
No se ha implementado aún autenticación/autorización por roles.
La vista de despliegue no está desarrollada en detalle en esta fase.
Glosario {#section-glossary}

+----------------------+-----------------------------------------------+ | Término | Definición | +======================+=================================================+ | Producto | Artículo del catálogo gestionado por el módulo de Compras. | +----------------------+-----------------------------------------------+ | Proveedor | Empresa externa que suministra productos, identificada por razón social y datos de contacto. | +----------------------+-----------------------------------------------+ | Orden de Compra | Documento que formaliza la solicitud de productos a un proveedor. | +----------------------+-----------------------------------------------+ | MVP | Minimum Viable Product: versión mínima funcional del sistema. | +----------------------+-----------------------------------------------+ | C4 Model | Notación de diagramas de arquitectura por niveles: Contexto, Contenedores, Componentes y Código. | +----------------------+-----------------------------------------------+
