IFC2Neo4j converts the non-geometrical information of IFC files in STEP format to Neo4j.

The conversion process is executed in two different steps:

The first step is translating the entities and relationships of the IFC file into nodes and relations. For this purpose, a generic property-graph data model is generated. This model is used to ensure that the data transformation is consistent for all nodes and relations in the graph. [Pydantic](https://pydantic.dev/opensource) is used for this purpose.

In the second step, the [Neo4j Python driver](https://neo4j.com/docs/api/python-driver/current/) is utilized to import the nodes and relations into the database. Nodes and relations are imported to the database using Cypher statements. Utilizing this two-step process allows decoupling the whole
conversion tool from the target graph DB. This second step can customized to import the IFC data into other available graph databases based on the LPG model.

The conversion process iterates over all the elements in the IFC file. The process results in a property graph that is close to the concepts and relations defined in the IFC schema. However, it does not maintain backward compatibility with the IFC format due to modifications that do not conform with the IFC Schema. The transformation process is made considering the following aspects:

1. Nodes in the graph correspond to IFC entities defined in the imported file. Each node is assigned with labels representing the IFC class and its parent classes in the IFC hierarchy.
2. All the inherited properties for IFC schema associated with the IFC entity are associated to its node in the graph.
3. IfcPropertySetDefinitions associated with the entity in the file are also included as part of its corresponding node properties.
4. The IFC-GUID property is expanded to stadard GUIDs.
5. Every node contains additional ’system properties’ that are not part of the IFC schema. They are used to improve the filtering and usability of the data within the system.
6. All the geometry-related classes are excluded from the conversion. Instead, the presence of a geometric representation is indicated by including a Booleansystem property in the node called presentation. If presentation is true, the geometry can be obtained from the GLB file resulting from the IfcConvert process using the GUID.
7. IfcRelationships are not included in the conversion as nodes in order to optimize the amount of graph data generated. They are bridged to enable direct relations between entities in the graph.

