## Tinkar Overview

Over the past decades, biomedical terminologies have increasingly been recognized as key resources for knowledge management, data integration, and decision support [1]. Acceleration and development of Electronic Health Record (EHR) systems has precipitated the emergence of “standard terminologies” and their widespread adoption in the clinical community. These include Systematized Nomenclature of Medicine Clinical Terms (SNOMED CT®), the Logical Observation Identifiers, Names, and Codes (LOINC®) and RxNorm. The availability of these clinical terminologies through the terminology services of FHIR is facilitating their usage in support of interoperability in healthcare. 

Interoperability requires standardized semantics based on reference terminology provided by standards development organizations, professional organizations, or government agencies. These organizations publish their content with the intention of licensing it to health IT vendors, providers, and research organizations. In the U.S., the core clinical reference terminology is based on SNOMED CT®, LOINC®, and RxNorm. Healthcare organizations must adopt and integrate subsets or modules of various reference terminology and manage references, dependencies, versions, and releases. It is important for the integrity of medical records that the change history of concepts and value sets can be managed and tracked to allow the exchange of either current or retrospective medical records. Therefore, enterprise terminology requires integrated terminology using a common representation and management. 

Despite the need to use standard terminologies in a highly integrated way, there is no standard representation across SNOMED CT®, LOINC®, RxNorm, etc. Some partnerships have been created among development teams to facilitate interoperability and minimize duplication of effort. Further integration has been proposed but will require additional resources to bring these terminologies closer together. However, while this evolution leads to greater compatibility and interoperability, integration of SNOMED CT®, LOINC®, and RxNorm is non-trivial as these terminologies use different formalisms and tools for their representation. Various terminologies have different semantics, models, release cycles, and versioning mechanisms [1]. While there is recognition that terminologies are not standardized at the exchange level, there is no consensus about harmonized next steps to solve the challenges. 

This document focuses on the need for – and logical specification of – a Terminology Knowledge Architecture (Tinkar). The Tinkar Reference Model is a logical model that describes the standardized model for terminology and change management. Tinkar provides an architecture that delivers integrated terminology to the enterprise and its information systems. In doing so, it addresses the differences in management and structure across reference terminology, local concepts, and code lists/value sets. 

The Capability Maturity Model is a development model and can be viewed as a set of structured levels that describe how well the behaviors, practices, and processes of an organization can reliably and sustainably produce required outcomes. There are five levels defined along the continuum of the Capability Maturity Model (see below). The model provides a theoretical continuum along which process maturity can be developed incrementally from one level to the next. An implementation of the Tinkar Specification can provide a single representation for all terminologies required in the U.S. and other countries, while also providing a better foundation for maturing change management models as described by the Capability  Maturity Model [2]:

1. **Initial** – Tinkar aims to reduce and eliminate challenges with management of changes to terminology
being unpredictable, poorly controlled, and reactive.
2. **Repeatable** – Tinkar provides the foundation for robust configuration management and quality assurance
for terminologies.
3. **Defined** – Tinkar allows terminologies to have standardized update and extension processes.
4. **Managed** – Tinkar represents updates and changes to terminologies so that the changes can be measured
and controlled.
5. **Optimizing** – Tinkar aims to allow multiple stakeholders to apply and retrieve changes to shared terminology
content with equivalent and harmonized results.

Tinkar aims to adhere to the following statement from a publication about developments in clinical terminologies in the 2018 Yearbook of Medical Informatics [1]: “The benefits of the integrated terminologies in terms of homogenous semantics and inherent interoperability should, however, outweigh the complexity added to the system.” This specification provides the foundation of a knowledge architecture that is intended to integrate reference terminology from distributors (e.g., SNOMED CT®, LOINC®, RxNorm) with local concepts to support interoperable information semantics across the enterprise.

### Motivation: Why Tinkar?

Information systems that are used across the healthcare enterprise record and manage clinical data using clinical statements and clinical terminologies in non-standardized ways. Interoperability specifications aim to require terminology bindings to concepts, code systems, and reusable value sets. Currently, there is variation in clinical data exchange across the enterprise, as existing payloads and clinical statements use inconsistent and highly variable enterprise terminologies. The management of the concepts, code systems, and value sets is non-trivial because developers, implementers, and end users are forced to manage “unnecessary complexity.” For example:

- Representation of medications: RxNorm codes overlap with CVX codes. Investigational vaccines from
the FDA are not represented in RxNorm, CVX, or SNOMED CT®.
- Representation of COVID-19 result codes are inconsistent and are not equivalent (e.g., detected, undetected,
positive, negative, etc.).

As a result of these complexities, there are many ways to say the same thing using standard terminologies and standard formats. The Institute of Medicine report, Health IT and Patient Safety: Building Safer Systems for Better Care, highlighted the unintended consequences of health IT-induced harm that can result in serious injury and death due to dosing errors, failure to detect serious illnesses, and delayed treatment due to poor human-computer interactions, confusing clinical terminology, or unreliable data quality [3]. Despite the widespread understanding of the importance of the quality of clinical data, there is currently a lack of integration and management of clinical terminologies across the healthcare enterprise.

Tinkar intends to support integration of clinical terminology and local concepts to support increased data quality for interoperable clinical information. High-quality clinical data enables healthcare systems across the enterprise to conduct robust and meaningful data analysis and increase overall interoperability, which ultimately enhances quality of care across all medical facilities.

<p align="center">
  <img width="460" height="300" src="/media/analysis_venn_diagram.jpg">
</p>

### The Problem Tinkar Addresses

The following four high level potential deficiencies related to poorly integrated terminology and inefficient change management describe preventable harm that Tinkar addresses:

1. **Inability to recognize equivalence.**
- Difficulty with determining that codes/terms using standard terminologies from disparate health IT systems represent a common clinical idea/concept (e.g., “Feels Feverish” in the Temperature Symptoms SNOMED CT® hierarchy versus “Feels Hot/Feverish” in the Observation and Sensation SNOMED CT® hierarchy. Both concepts are Findings in SNOMED CT® but there is no unifying way to identify equivalence).
2. **Inability to represent a pertinent phenomenon.**
- A new set of local terminology may be managed with value sets and concept gaps addressed in quick iterations (e.g., “COVID-19 negative test result” was needed in practical use before official SDO releases, or gaps like “mild anemia”, which was proposed, but not accepted, by both the international and U.S. SNOMED CT® release).
3. **Flawed information.**
- Incorrect usage or representation of clinical ideas/concepts from standard terminologies due to a lack of harmonization and multiple representations that currently exist (e.g., LOINC® and SNOMED CT® have overlapping concepts).
4. **Inability to reliably and safely evolve over time [4].**
- There is a lack of clear, detailed descriptions of changes to terminologies over time so that changes can be understood by implementers. Terminologies often change in ways that are convenient for the creators, but complex for the users (e.g., redundancy, major name changes, code reuse, and changed codes).

**Consider the following examples of implementations that have gone wrong:**

- **Computer error may have led to incorrect prescribing of statins to thousands of patients.**
  - Thousands of patients in England may have been incorrectly prescribed or taken off statins because of a major IT glitch.
  - Underlying cause: (1) code mapping errors, and (2) brittle means for determining equivalence.
- **Alert for monitoring thyroid function when taking Amiodarone stopped working.**
  - Amiodarone is associated with several side effects, including thyroid dysfunction, which is due to amiodarone’s high iodine content and its direct toxic effect on the thyroid.
  - Underlying cause: (1) the identifier for the drug amiodarone was changed in another system, and (2) uncoordinated means for determining equivalence.
- **62 percent of clinical decision support (CDS) malfunctions were attributable to changes in underlying codes or data fields.**
  - Change is a constant feature of providing healthcare.
  - Underlying cause: (1) poorly managed change.

Tinkar addresses challenges and problems from the above implementation examples:

| Challenge | Tinkar Solution |
| :--- | :--- |
| Uncoordinated or brittle terminology integration frequently breaks across systems | Standardize (and facilitate sharing) of terminology representation across systems |
| Management of change over time | Consistent representation and configuration management |
| SNOMED CT’s® proprietary aspects prevents use as a common format for LOINC® and similar | Build on existing SNOMED CT® foundation, rather than reinvent, using an open-source initiative approved permissive licenses (i.e., Apache 2)|



### About Tinkar

Tinkar provides the foundation of a knowledge architecture that is intended to integrate reference terminology from distributors (e.g., SNOMED CT®, LOINC®, RxNorm) with local concepts to support interoperable information semantics across the enterprise. 

This specification introduces an agile approach to terminology design and formatting that promotes the use of self-describing data. It is a shift from hard-coded models that have been favored due to their prescriptive nature but have shown limited flexibility and extensibility. Like FHIR, this specification places the focus on a self-describing, extensible approach to representing terminology. Therefore, Tinkar aims to be both
self-describing and completely machine processed:

1. Self-describing machine-readable representation of terminology, such that if an application can process the metadata, it should be able to import the content/concepts and make it available to enterprise applications.
2. The machine-readable terminology could generate human-readable documentation so that business analysts and developers can understand and apply it correctly.

### Tinkar Objectives and Purpose

This specification describes the requirements and characteristics of systems required to manage terminology produced by a variety of organizations across a healthcare enterprise. This foundation must allow enterprise to extend terminology standards and implement extensions in a timely fashion.

This specification is intended to support healthcare organizations’ standard terminology modules, value sets, and coding systems as well as local terms and equivalence mappings.

A standard-based Tinkar specification is necessary to support the operation of a variety of systems intended to deliver knowledge management for terminology to vendors, providers, and even standards-development organizations, like HL7.

### Related Efforts

Previous efforts have attempted to create a common set of terminology capabilities and services by specifying a single predefined structure for managing terminology. Unfortunately, a hardwired structure that works for one standard may not work for another. The inability to integrate content across terminology standards is a barrier to implementing services and modules that can deliver integrated concepts, code lists, and value sets required by enterprise systems for treatment, research, business process automation, quality measures, and outcome analysis.

- Clinical applications require integrated terminology to create interoperable clinical statements that are organized into messages, documents, or resources.
- Data analysis and research require integrated terminology to analyze aggregated information. Interoperability, CDS, or other types of automation require common semantics based on a set of integrated models across reference standards (e.g., SNOMED CT®, LOINC®, RxNorm).

The Unified Medical Language System (UMLS) Metathesaurus is a compilation of multiple sources organized into ‘concepts’ that contain terms from many sources. The terms within a concept are declared synonyms by UMLS editors. However, its use in terminology systems has limited utility for several reasons. First, UMLS concepts are created on lexically-based rules and use very little of the additional information (relationships between concepts) that may be available from the source terminology. It does not permit classification to identify cases of possible missed synonymy. Second, issues of currency occur because of dyssynchrony of release dates between source terminologies and the UMLS itself. Third, the UMLS does not support a contribution model. That is, it is a static file that cannot be amended to support additional terms that may be required to fill gaps in existing terminologies subsumed by the Metathesaurus; it does not support extensions. Lastly, there is no efficient format for sharing integrated Metathesaurus content (though there is Rich Release Format [RRF]). The UMLS is not an architecture for terminology management. It may only serve as a reference, noting the aforementioned limitations. An implementation of Tinkar may help address these limitations.

CTS2 is an architecture for terminology management that supports history retrieval, though it does not support an arbitrarily granular change set model for versioning. The Tinkar specification, in contrast, provides that every new assertion, whether a new component or a change to an existing component, must have a precise version coordinate that govern granular change control. CTS2 asserts a specific terminology model and does not support unanticipated properties with a self-describing model.

The USCDI is an amalgamation of various encoding standards. The standards being identified for specific data elements do not themselves provide consistency for how encoding is represented, how those encoding standards change over time, and how those encoding standards are distributed. As demonstrated by COVID-19 data needs, coordinated extension of content, timely distribution of updates, and consistency of representation are required to effectively respond to needs of public health and syndromic surveillance. Tinkar could help make it easier to standardize the representation, distribution, version and configuration management, and ability to share extensions to the USCDI as well as the underlying terminology systems themselves.

### Benefits of Self-Describing Architecture

Tinkar is self-describing and completely machine-processed. A self-describing architecture is defined in a report from Queensland University of Technology as follows: “[t]he idea is that self-descriptions of data and other techniques would allow context-understanding programs to selectively find what users want, or for programs to work on behalf of humans and organizations to make them more scalable, efficient and productive.” [9] Key advantages of a self-describing architecture (or metadata driven architecture) [10] include the following details: 

**Changes can be reviewed immediately** – Every action or change by end users can be immediately previewed or tested, without needing any compilation or deployment process. The review can also be done before saving or publishing the changes, which makes it an interactive development environment for designers to create functionality in an iterative manner.

**Version control with easy rollback** – Every time any changes are made to published terminology artifacts, the historic versions of the metadata files are maintained. This enables easy version control and rollback when necessary. Every time a change is made to any artifact, the prior version that existed is archived. When a developer needs to roll back to the prior version, it can be achieved easily.

**Any data source can be added** – A self-describing architecture facilitates the ability for multiple types of terminology data sources to be connected to the system.

**Define granular coordinates and configuration management** – The functionality for defining granular, user-defined settings and controls for granular elements of clinical terminology management is supported. This includes create, read, and append settings, as well as management of individual elements, like fields or other controls. 

**Faster extensions** – A benefit of a self-describing architecture is that it can abstract a lot of the deep internal complexities that makes development of standard terminologies complicated. This approach can improve processes around extensions to terminology.

### Architectural Separation of Concerns

Increased reliance on computerized health records, including Electronic Health Records Systems, requires standardized medical terminology to encode health information consistently across systems and enterprises. Clinicians require not only objective quantitative measurements (e.g., 90 beats per minute for a patient’s pulse), but also procedural context (e.g., pulse oximetry, manual) about past observations or requests for future interventions. While two quantitative measurements may be the same, the procedural information could indicate meaningful semantic differences and lead to different clinical interpretation and treatment.  As information is exchanged across systems, the solution requires a common understanding of data, a method to support knowledge-representation, and clinical decision rules based on common terminology and statements. Each component must address an aspect, and together need to address the requirements of clinicians. Current HL7 standard implementations rely on profiles and templates to disambiguate statement and terminology, and provide sufficient precision for transactions, documents, and standards-based APIs. Therefore, the architectural approach described here is applicable to standards organizations developing interoperability for enterprise and project-specific implementations in equal measure.

Functional decomposition—often referred to as a *Separation of Concerns (SoC)* —across components or sections with a specific purpose is a foundational design principle for complex system architecture. SoC allows a complete system to be subdivided into distinct sections or components with well-defined functionality and dependencies. If successful, this approach allows individual sections to be able to be reused, as well as designed, implemented, and updated independently to address emerging requirements. This is especially useful and important in a medical context given how many different health information and clinical terminology projects are ongoing at any given time. Efforts are often uncoordinated and led by disparate and unrelated standards development organizations. In these cases, SoC allows teams to work independently, in coordination with each other, and reuse the resulting artifacts.

<p align="center">
  <img src="/media/separation_of_concerns_knowledge_architecture.jpg">
</p>

*Separation of Concerns is an architectural design principle, whereby a system is divided into distinct sections, such that each section can address separate concerns. In this case, each architectural layer may build upon artifacts from lower layers.*

**Foundational Architecture** – The Foundational layer of the Knowledge Architecture provides the common elements of interoperability, such as: object identity, versioning, modularity, and knowledge representation. It includes (a) the foundation and building blocks of the common model; (b) how the repeatable transformation process of disparate standards into the common model promotes interoperability with other environments; and (c) how the modules of the architecture are tightly version controlled over time. The Tinkar Reference Model belongs in this layer.

**Terminology Knowledge** - The Terminology Knowledge layer is responsible for structured sets of medical terms and codes that define concepts of interest, including descriptions, dialects, language, and semantic hierarchy. SNOMED CT®, LOINC®, and RxNorm are part of this layer. It defines what valid codes or expressions may be used by higher level layers.

**Statement Model** – The Statement Model layer is responsible for defining how data elements are combined to create a statement. This layer reuses the artifacts defined in the Terminology Knowledge layer. The ANF Reference Model [11] belongs in this layer.

**Assertional Knowledge** – The Assertional Knowledge layer makes use of the Terminology Knowledge layer concepts to specify non-defining facts that may be used by procedural knowledge algorithms. An example fact might be that “thiazide diuretics treat hypertension.” Assertional Knowledge may also indicate what symptoms may be associated with a disorder.

**Procedural Knowledge** – The Procedural Knowledge layer, also known as imperative knowledge, is the knowledge exercised in the performance of some task. An example would be determining a hypertension treatment plan by analyzing a combination of a patient’s clinical statements and the available assertional knowledge. The procedural knowledge is responsible for information about standard ways to carry out specific procedures, as well as other procedural guidelines, (e.g., treatment protocols for diseases and order sets focused on certain patient situations). Procedural knowledge, together with assertional knowledge, enables clinical decision support, quality measurement, and patient safety. This layer relies on the architectural foundation and terminology layers, incorporates the statement model for information retrieval, and uses the assertional knowledge. Procedural knowledge artifacts may include clinical alert rules, reminders, etc., that trigger actions or recommend interventions.

Examining a clinical procedure for controlling hypertension illustrates each of the layers of the informatics architectural separation of concerns.

- At the Terminology Knowledge layer there may be various codes and terms from disparate source terminologies to define a concept (e.g., hypertension). Ideally, these overlapping codes and terms would be oriented to the same parent concept during the transformation and integration process at the Foundational Architecture layer.
- The Statement Model layer enables representation of blood pressure measurement values (e.g., systolic BP = 140 mmHg), or the categorical data (e.g., pregnancy induced hypertension vs. renal hypertension) within a standard data structure to facilitate information exchange or retrieval, such as within a standards- based clinical statement (i.e., CIMI, CDA, FHIR, ANF, etc.).
- The Assertional Knowledge layer represents non-procedural statements, or facts, such as “Stage 2 high blood pressure is over 140 systolic or 90 diastolic,” or “beta-blockers and ACE inhibitors may be used to treat hypertension”, or “beta-blockers are contraindicated in patients with a diagnosis of reactive airway disease.”
- Finally, the Procedural Knowledge layer provides algorithms to analyze clinical statements about a patient, in combination with the Assertional Knowledge, to recommend a treatment protocol for different kinds of hypertension, including the considerations of, (e.g., patient age, co-morbidities etc., which can be generated by an electronic clinical decision support system [Statement + Assertional layers]). This layer adds support for workflow and conditional logic (i.e., if-then-else).
A clear separation of concerns enables the isosemantic transformation of standards-based clinical statements to normal form in the Statement Model layer by decoupling structure from semantics and workflow.

HL7 relies on implementation guides (for V2, CDA, and FHIR) to add sufficient terminology knowledge to standards-based clinical statements. Terminology constraints documented as profiles or templates are the mechanism to create interoperable implementation guides from health IT standards. Only after the Terminology Knowledge is fully defined can the standards-based statements be used to support business and workflow decision points consistent with the Assertional and Procedural layers described above.

### About this Document

This document describes how encoded clinical data can be improved with a terminology management model. This terminology can be unified for HL7 and non-HL7 systems. The Terminology Knowledge Architecture, known as Tinkar, treats terminology in a common way for managing enhanced patient care and improved record keeping. The unification of models such as SNOMED CT®, LOINC®, and RxNorm will allow more robust computable medical records. The following sections contain the Tinkar Reference Model, along with illustrative examples as to the complexity and necessity of type of structure. Tinkar will take different language sources and cohesively manage terminology data. Section 2 lays out the specific business requirements necessary for this task. The model representation is outlined in Section 3.

Section 4 shows how Tinkar brings together the biomedical terminologies by a common description format. An implementation of the Tinkar specification can be used to fill the gaps between the common HL7 Terminologies and other systems like SNOMED CT®, RxNorm, UCUM, etc. The result is the distribution and sharing of cohesive data across all platforms.

Tinkar overview, business requirements, model overview, Komet user guide, next steps, and Tinkar model concepts to be included here.

## Business Requirements

This section details specific business requirements for a Tinkar logical model.

### Clinical Requirements

The ultimate goal of this effort is to <ins>support the coordination of safe, effective medicine (Requirement 1)</ins>. This goal requires <ins>quality information in the patient record (Requirement 2)</ins>, wherever it comes from, and the increasingly distributed nature of care that requires <ins>commonly understood data standards (Requirement 3)</ins> to ensure mutual comprehension across the care team and over time. There are four outlined clinical use cases:

- **Record Patient Data**

A care provider, already authenticated and authorized to the system and using the appropriate context to ensure the system records the data for the correct patient, adds or modifies information in the patient record. This may include signs, symptoms, impressions, diagnoses, orders, notes, or other assets. 

This operation may initiate workflow processes or automated processes such as clinical decision support suggestions.

For structured data using standard terminologies, the <ins>terms available are appropriate (Requirement 4)</ins> for the clinical context, for the role (i.e., terms may differ for different kinds of users), and for the data context (e.g., data entry fields may not support inactive or deprecated terms that would be allowed in search or analytical contexts).

If the available terminology does not support the provider’s needs, the provider may assert a need for a new term.

- **Propose Terminology Change**

If a provider attempts to enter a term that is not supported by the enterprise terminology, the <ins>effort will be captured as a proposed term (Requirement 5)</ins>. 

Systems may capture this information unobtrusively as text, or prompt further information from the clinician to assist the authoring process. The system will convey at least the text and the identity of the clinician to the terminologist.

- **Review Patient Data**

A provider, already authenticated and authorized to the system and using the appropriate context to ensure the system records the data for the correct patient, finds and reviews information in the patient record. For structured data using standard terminologies, the terms available are appropriate for the clinical context, for the role (i.e., terms may differ for different kinds of users), and for the data context (i.e., data entry fields may not support inactive or deprecated terms that would be allowed in search or analytical contexts).

<ins>Changes to the terminology that could affect record interpretation will be indicated (Requirement 6), along with a way to identify the change and its effect</ins>.

- **Review Knowledge Base Changes Relevant to Record**

If the system identifies a relevant change, the provider may request further information. 

This will include the ability to <ins>see available values and CDS results for specific dates and contexts (Requirement 7)</ins>, including those under which the data was recorded or specific decisions were made.

*Clinical Use Cases*

The key capability for a clinician should be to record and review data quickly and accurately (Requirement 8), taking advantage of up-to-date classifications and decision support rules. This should be accomplished by knowing when a change in the knowledge base might affect a record. The change management capability that supports these operations should be as unobtrusive as possible to patients and care providers, and always readily available.

These operations depend on the availability not only of currently accurate terminology assets, but also assets from prior points in time (Requirement 7). These may include assets as defined or refined by different stakeholders with different sets of assumptions. For instance, whether a disorder meets a criterion defined by a standard terminology, a payor, a professional society, or a locally chartered board of specialists.

To support these needs, the Enterprise Terminology that supports the clinical systems must <ins>manage change systematically (Requirement 9)</ins>, and it must do so for both internally-managed and externally-sourced assets.

### Asset Curation Requirements

### Configuration Requirements

### List of Requirements