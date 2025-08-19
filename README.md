# Guía de Retrieval Augmented Generation (RAG) en Azure Confidential VMs

Este documento ofrece una guía completa para implementar **RAG (Retrieval Augmented Generation)** en **máquinas virtuales confidenciales de Azure (Confidential VMs)** con aceleración de hardware (procesadores AMD EPYC, Intel TDX y GPUs NVIDIA H100).

### Contenido principal:
- Introducción a RAG y sus beneficios sobre LLMs tradicionales.
- Arquitectura de referencia para RAG confidencial en Azure.
- Uso de herramientas open source como **Charmed OpenSearch** y **KServe**.
- Seguridad y confidencialidad con **Confidential AI** y Confidential Computing.
- Guía paso a paso para despliegue en Azure.

---

## Contenido original

La guía completa de Retrieval Augmented Generation (RAG) en Azure Confidential Virtual Machines

Arquitectura de referencia: Implementar RAG confidencial en Azure con aceleración por hardware (Procesadores AMD EPYC de 4.ª generación, Intel® TDX en procesadores Intel® Xeon Scalable de 4.ª generación y GPU NVIDIA H100 Tensor Core)

1. "La guía completa de Retrieval Augmented Generation (RAG) en Azure Confidential Virtual Machines"
Retrieval Augmented Generation (RAG) → Técnica de IA que combina modelos de lenguaje con búsqueda de información en bases de datos o documentos externos para dar respuestas más precisas y actualizadas.
Azure Confidential Virtual Machines (VMs confidenciales de Azure) → Servidores virtuales en la nube de Microsoft que usan hardware y software diseñados para proteger datos y código incluso mientras se están ejecutando, gracias a entornos de ejecución seguros (confidential computing).
Traducción en contexto: Es una guía sobre cómo usar RAG dentro de máquinas virtuales seguras en Azure, protegiendo datos sensibles.

2. "Arquitectura de referencia"
Significa que no es solo teoría, sino un diseño recomendado que describe qué componentes usar, cómo se conectan y qué configuraciones son óptimas para implementar RAG de manera segura en Azure.
Es como un plano técnico listo para adaptar a tu proyecto.

3. "Implementar RAG confidencial en Azure con aceleración por hardware"
“Confidencial” → se refiere a que los datos, el modelo y el código estarán protegidos mientras están en uso.
“Aceleración por hardware” → usar procesadores y GPUs especiales que procesan más rápido y con mejor rendimiento las tareas de IA.

4. Tecnologías de hardware mencionadas
AMD 4th Gen EPYC processors → procesadores de alto rendimiento de AMD, optimizados para cargas de trabajo de IA, big data y entornos de nube.
Intel® TDX (Trust Domain Extensions) en 4th Gen Intel® Xeon Scalable Processors → tecnología de Intel para aislar y proteger entornos de ejecución, asegurando que incluso el sistema operativo anfitrión no pueda acceder a los datos o código en uso.
Traducción y Adaptación: Rafael J. Rivas R.
NVIDIA H100 Tensor Core GPUs → tarjetas gráficas especializadas para IA y aprendizaje profundo, con alto rendimiento en entrenamiento e inferencia de modelos de gran tamaño.
Este título describe una guía que explica cómo montar un sistema de RAG dentro de entornos seguros de Azure, usando una arquitectura recomendada y hardware de última generación (procesadores AMD e Intel con funciones de seguridad avanzada y GPUs NVIDIA H100) para obtener máxima velocidad y protección de datos.

**Introducción**
Una de las brechas más críticas en los Modelos de Lenguaje Extensos (LLMs) tradicionales es que dependen de conocimiento estático ya contenido en ellos. Básicamente, pueden ser muy buenos para comprender y responder a instrucciones (prompts), pero a menudo fallan al proporcionar información más reciente o altamente específica.
Aquí es donde entra Retrieval Augmentation Generation (RAG); RAG aborda estas brechas críticas en los LLM tradicionales incorporando información actual y nueva que sirve como una fuente confiable de verdad para estos modelos.
RAG mejora los modelos de IA generativa utilizando fuentes de conocimiento externas como documentos y bases de datos. Estas bases de conocimiento externas proporcionan información verificable y actualizada que puede mejorar los modelos de aprendizaje automático (ML) al reducir errores, simplificar la implementación y disminuir el costo de un reentrenamiento continuo. Para aprender más sobre RAG y entenderlo en mayor profundidad, puedes leer nuestro blog que cubre esta técnica.
Construir una infraestructura sólida de IA generativa, como las usadas para RAG, puede ser complejo y desafiante. Requiere una cuidadosa consideración de la pila tecnológica, los datos, la escalabilidad, la ética y la seguridad. En cuanto a la pila tecnológica, el hardware, los sistemas operativos, los servicios en la nube y los servicios de IA generativa deben ser resilientes y eficientes según la escala que las empresas requieran.
Dadas las preocupaciones que tienen las empresas sobre el costo, el bloqueo de proveedores, la soberanía de datos, el cumplimiento regulatorio y la complejidad de los proyectos de IA, el software de código abierto representa una poderosa forma de alcanzar los objetivos del proyecto mientras se mantiene el control total sobre los datos sensibles y se evitan restricciones y sobrecostos pesados. Hay varias opciones en el mercado, pero en este documento técnico nos centraremos en Charmed OpenSearch y KServe.
Al implementar un caso de uso de IA generativa y desarrollar una infraestructura robusta, es crucial priorizar la seguridad y la confidencialidad tanto de los datos como de los modelos de aprendizaje automático. Estos activos son invaluables, y protegerlos es esencial para mantener la confianza, garantizar el cumplimiento legal, apoyar la continuidad del negocio y prevenir ataques maliciosos. RAG puede ser particularmente vulnerable en este sentido, lo cual es especialmente preocupante para aplicaciones críticas en sectores como los servicios financieros y los servicios públicos.
Por supuesto, las empresas también tienen preocupaciones sobre los riesgos de realizar tareas de aprendizaje automático en la nube. Por ejemplo, pueden preocuparse por el posible compromiso de sus datos sensibles o de su propiedad intelectual. En algunos casos, existen retos regulatorios que considerar, como regulaciones estrictas de la industria que pueden prohibir el intercambio de datos sensibles. Esto dificulta, o incluso hace imposible, utilizar grandes cantidades de datos privados valiosos, lo que puede limitar fuertemente el verdadero potencial de la IA en dominios cruciales.
Confidential AI aborda directamente este problema, proporcionando un entorno de ejecución con raíz en hardware que abarca tanto la Unidad Central de Procesamiento (CPU) como la Unidad de Procesamiento Gráfico (GPU). Este entorno mejora la protección de los datos y el código de IA durante la ejecución, ayudando a resguardarlos contra software del sistema con privilegios (como el hipervisor o el sistema operativo anfitrión) y contra operadores privilegiados en la nube.

**Objetivo**
Esta guía se centra en la creación de un flujo de trabajo RAG utilizando herramientas de código abierto. Además, la guía enfatiza la importancia de la seguridad y la confidencialidad de los datos, y presenta innovaciones relacionadas con la seguridad, como la computación confidencial (confidential computing), que pueden ayudar a proteger tu implementación de RAG.
Está dirigida a entusiastas de los datos, ingenieros, científicos y profesionales del aprendizaje automático que deseen desarrollar soluciones RAG en plataformas de nube pública como Azure, utilizando herramientas empresariales de código abierto que no forman parte de la oferta nativa de microservicios de Azure.
Esta guía puede apoyar diversos proyectos, incluidos pruebas de concepto, desarrollo y producción.
Ten en cuenta que, aunque esta guía resalta herramientas de código abierto específicas, también pueden utilizarse otras herramientas no mencionadas como alternativas. Si decides usar herramientas diferentes, asegúrate de ajustar las especificaciones de hardware —como la capacidad de almacenamiento, la potencia de cómputo y la configuración— para cumplir con los requisitos específicos de tu caso de uso.

**Solución de referencia RAG**
- Cuando se realiza una consulta en un chatbot de IA, el sistema basado en RAG primero recupera información relevante de un conjunto de datos o base de conocimiento, y luego utiliza esta información para guiar y orientar la generación de la respuesta.

- El sistema RAG está compuesto por dos componentes clave:

- Retriever (Recuperador) → Es responsable de localizar fragmentos de información relevantes que puedan ayudar a responder la consulta del usuario. Busca en una base de datos para seleccionar la información más pertinente.
Generator (Generador) → Es un modelo de lenguaje grande (Large Language Model) que produce la respuesta final.

- Antes de usar tu sistema basado en RAG, primero debes crear tu base de conocimiento, que está formada por datos externos que no están incluidos en los datos de entrenamiento de tu LLM. Estos datos externos pueden provenir de diversas fuentes, incluyendo documentos, bases de datos y llamadas a API.

- La mayoría de los sistemas RAG utilizan una técnica de IA llamada model embedding (modelo de incrustaciones), que convierte los datos en representaciones numéricas y los almacena en una base de datos vectorial.

- Al usar un modelo de embeddings, puedes crear un modelo de conocimiento que sea fácil de entender y rápido de recuperar en el contexto de la IA.

- Una vez que tienes tu base de conocimiento y tu base de datos vectorial configuradas, ya puedes ejecutar tu proceso RAG; a continuación se presenta un flujo conceptual.

**Este flujo conceptual sigue 5 pasos generales:**
* El usuario introduce un prompt o consulta.
* El Retriever (Recuperador) busca información relevante en una base de conocimiento. La relevancia puede determinarse mediante cálculos matemáticos vectoriales y representaciones a través de funciones de búsqueda y bases de datos vectoriales.
* Se recupera la información relevante para proporcionar un contexto enriquecido al generador.
* La consulta y el prompt ahora se enriquecen con este contexto y están listos para ser aumentados para su uso con un modelo de lenguaje grande, utilizando técnicas de prompt engineering (ingeniería de prompts). El prompt aumentado permite que el modelo de lenguaje responda con precisión a tu consulta.
* Finalmente, se entrega la respuesta generada en texto al usuario.

**Solución de referencia avanzada de RAG y GenAI con código abierto**
RAG puede utilizarse en diversas aplicaciones, como chatbots de IA, búsqueda semántica, resumen de datos e incluso generación de código.
La solución de referencia que se presenta a continuación describe cómo RAG puede combinarse con arquitecturas de referencia avanzadas de IA generativa para crear proyectos de LLM optimizados que ofrezcan soluciones contextuales a diversos casos de uso de GenAI.

**Explicación del diagrama “RAG enhanced GenAI Reference Solution”**
Este diagrama describe cómo funciona un sistema de Retrieval-Augmented Generation (RAG), que combina modelos de lenguaje grandes (LLM) con bases de datos de búsqueda para dar respuestas más precisas, usando información interna de la empresa.

El blueprint de GenAI mencionado anteriormente fue publicado por OPEA (Open Platform for Enterprise AI), un proyecto de la Linux Foundation. El objetivo de crear este blueprint es establecer un marco de bloques de construcción componibles para sistemas de IA generativa de última generación, que incluyen LLM, almacenamiento de datos y motores de prompts.

Además, proporciona un blueprint para RAG y describe flujos de trabajo de extremo a extremo. La versión 1.1 recientemente lanzada del proyecto OPEA presentó múltiples proyectos de GenAI que demuestran cómo los sistemas RAG pueden mejorarse mediante herramientas de código abierto.

Cada servicio dentro de los bloques tiene tareas específicas que realizar, y existen diversas soluciones de código abierto disponibles que pueden ayudar a acelerar estos servicios según las necesidades de la empresa.

* Enterprise Data (Datos de la Empresa)
Aquí está la información interna: documentos, imágenes, videos, correos, chats, bases técnicas, datos de herramientas de trabajo, y sistemas de negocio (como SAP o Workday).
Función: Fuente de conocimiento para el sistema.

* Ingest/Data Processing (Ingesta/Procesamiento de Datos)
Extrae y limpia los datos para que puedan usarse en el sistema.
Función: Convertir la información bruta en un formato estructurado.

* Embedding Model (Modelo de Embeddings)
Transforma los datos y las consultas en vectores numéricos que el sistema pueda comparar.
Función: Representar el significado del texto en un espacio matemático.

* Index/Vector Database (Base de Datos de Vectores)
Guarda los embeddings de todos los documentos procesados.
Función: Permitir búsquedas rápidas de información relevante.

* User Query (Consulta del Usuario)
Pregunta o instrucción que el usuario introduce en el sistema.
Función: Punto de partida de la interacción.

* Retrieval/Rank (Recuperación/Clasificación)
Busca en la base de datos de vectores la información más relevante y la ordena por relevancia.
Función: Filtrar los datos más útiles para la respuesta.

* Prompt Processing (Procesamiento del Prompt)
Combina la consulta del usuario y la información recuperada en una sola entrada para el modelo de lenguaje.
Función: Preparar la entrada que recibirá el LLM.

* LLM/LMM Inference (Inferencia de LLM/LMM)
El modelo (por ejemplo, LLaMA2, GPT-4, LLaVA) genera una respuesta usando la información del prompt.

**Traducción y Adaptación: Rafael J. Rivas R.**