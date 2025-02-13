# SPARQL queries

Please note that the following code spills off the side of the page. This is intentional. This allows the code to be copied and pasted without breaking the line structure.

The SPARQL queries can also be found in plain text format in the project repository, in the docs folder.

```sparql
# 1. Identifies any areas not in Atlas
# ----------------------------------------------------------------------------------
PREFIX qb: <http://purl.org/linked-data/cube#>

select distinct ?area where {graph <http://statistics.gov.scot/graph/coronavirus-covid-19-management-information> {
?obs a qb:Observation ;
<http://purl.org/linked-data/sdmx/2009/dimension#refArea> ?area .
}
OPTIONAL {?area <http://publishmydata.com/def/ontology/foi/memberOf> ?collection .}
FILTER (!bound(?collection))
}


# 2. Identifies any archived geographies
# ----------------------------------------------------------------------------------
PREFIX qb: <http://purl.org/linked-data/cube#>

select distinct ?area where {graph <http://statistics.gov.scot/graph/coronavirus-covid-19-management-information> {
?obs a qb:Observation ;
<http://purl.org/linked-data/sdmx/2009/dimension#refArea> ?area .
}
?area <http://statistics.data.gov.uk/def/statistical-geography#status> "Archived"
}


# 3. Identifies any observations with multiple values - count
# ----------------------------------------------------------------------------------
PREFIX qb: <http://purl.org/linked-data/cube#>

SELECT ?DataSet ?s (count(?s) as ?NumValues)
WHERE {
?s <http://statistics.gov.scot/def/measure-properties/count> ?o.
?s qb:dataSet <http://statistics.gov.scot/data/coronavirus-covid-19-management-information>.
}
GROUP BY ?DataSet ?s
HAVING (?NumValues>1)


# 4. Identifies any observations with multiple values - ratio
# ----------------------------------------------------------------------------------
PREFIX qb: <http://purl.org/linked-data/cube#>

SELECT ?DataSet ?s (count(?s) as ?NumValues)
WHERE {
?s <http://statistics.gov.scot/def/measure-properties/ratio> ?o.
?s qb:dataSet <http://statistics.gov.scot/data/coronavirus-covid-19-management-information>.
}
GROUP BY ?DataSet ?s
HAVING (?NumValues>1)


# 5. Identifies multiple labels for units
# ----------------------------------------------------------------------------------
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?MeasureUnits (count(?MeasureUnits) as ?NumLabels)
WHERE {
?MeasureUnits a <http://purl.org/linked-data/sdmx/2009/concept#unitMeasure>.
?MeasureUnits rdfs:label ?label .
}
GROUP BY ?MeasureUnits
HAVING (?NumLabels>1)


# 6. Identifies multiple dimension values
# ----------------------------------------------------------------------------------
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?DimensionValue (count(?DimensionValue) as ?NumLabels)
WHERE {
?DimensionValue a <http://www.w3.org/2004/02/skos/core#Concept>.
?DimensionValue rdfs:label ?label .
}
GROUP BY ?DimensionValue
HAVING (?NumLabels>1)


# 7. Identifies duplicate concept schemes
# ----------------------------------------------------------------------------------
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?ConceptScheme (count(?ConceptScheme) as ?NumLabels)
WHERE {
?ConceptScheme a <http://www.w3.org/2004/02/skos/core#ConceptScheme>.
?ConceptScheme rdfs:label ?label .
}
GROUP BY ?ConceptScheme
HAVING (?NumLabels>1)


# 8. Identifies duplicate values in dataset
# ----------------------------------------------------------------------------------
PREFIX qb: <http://purl.org/linked-data/cube#>

SELECT ?DataSet ?s (count(?s) as ?NumValues)
WHERE {
?s <http://statistics.gov.scot/def/measure-properties/index> ?o.
?s qb:dataSet ?DataSet.
}
GROUP BY ?DataSet ?s
HAVING (?NumValues>1)
LIMIT 100


# 9. Identifies any datasets which have dropped dimensions:
# ----------------------------------------------------------------------------------
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT *
WHERE {
?s <http://purl.org/linked-data/cube#dimension> ?x.
FILTER( !EXISTS { ?x rdfs:label ?y.} )
}


# 10. Checks for missing Units
# ----------------------------------------------------------------------------------
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT distinct ?unit ?unit_label
WHERE {
?s <http://purl.org/linked-data/sdmx/2009/attribute#unitMeasure> ?unit .
OPTIONAL {?unit rdfs:label ?unit_label }
FILTER(!bound(?unit_label))
}
```
