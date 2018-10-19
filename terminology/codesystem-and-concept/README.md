# CodeSystem

## Overview

The [`CodeSystem`](https://www.hl7.org/fhir/codesystem.html) resource specifies a set of Concepts included in this code system. 

Aidbox assumes a separate creation of the `CodeSystem` resource and a set of `Concepts` composing it. This means that the CodeSystem resource describes only meta information of the code system: url, name, publisher, etc. Whereas Concept resources describe the content of the code system and are associated with the code system by the Concept.system attribute with the same value as the CodeSystem.url element.

For FHIR conformance, we allow to create the CodeSystem resource with a list of included concepts. In the moment of saving a CodeSystem, if it contains listed Concepts, then Aidbox saves submitted Concepts as separate resources, and the CodeSystem resource itself is saved without the concept attribute. This method of the CodeSystem creation may be used for small dictionaries \(generally, not more than 100 concepts\). In case when your code system is big, Aidbox strongly recommends to create the CodeSystem resource separately and upload Concepts in parts.

## CRUD

### Create

[`CodeSystem`](https://www.hl7.org/fhir/codesystem.html) resource can be created as a FHIR resource with embedded concepts itself. This approach is applicable for those cases if your code system contains a small number of concepts, usually no more than 100. 

For example, we will create `CodeSystem` for eye color, contained `Brown`, `Blue`, `Green`, `Hazel`,  `Heterochromia` coded concepts.

{% tabs %}
{% tab title="Request" %}
```javascript
POST [base]/CodeSystem

{
	"resourceType" : "CodeSystem",
	"id": "custom-eye-color",
	"status": "draft",
	"url": "http://code.system/eyes.color",
	"content": "example",
	"concept" : [     
		{
			"code": "ec-bn",
			"display": "Brown"
		},
		{
			"code": "ec-be",
			"display": "Blue"
		},
		{
			"code": "ec-gn",
			"display": "Green"
		},
		{
			"code": "ec-hl",
			"display": "Hazel"
		},
		{
			"code": "ec-h",
			"display": "Heterochromia"
		}
	]	
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 201

{
    "resourceType": "CodeSystem",
    "id": "custom-eye-color"
    "url": "http://code.system/eyes.color",
    "status": "draft",
    "content": "example"
}
```
{% endtab %}
{% endtabs %}

As you can see request return only `CodeSystem` meta information \(url, status, content....\), and do not return `concept` listed in then request. It is was because `Aidbox` divide `CodeSystem` to `CodeSystem` body and contained concepts list, and create all concepts as a independent `Concept` resources.

And if we get `Concept` with `system` = `http://code.system/eyes.color`, we are receive all concepts for this CodeSystem.

{% tabs %}
{% tab title="Request" %}
```javascript
GET [base]/CodeSystem?system=http://code.system/eyes.color
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 200

{
    "resourceType": "Bundle",
    "type": "searchset",
    "entry": [
        {
            "resource": {
                "id": "custom-eye-color-ec-bn",
                "code": "ec-bn",
                "system": "http://code.system/eyes.color",
                "display": "Brown",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-be",
                "code": "ec-be",
                "system": "http://code.system/eyes.color",
                "display": "Blue",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-gn",
                "code": "ec-gn",
                "system": "http://code.system/eyes.color",
                "display": "Green",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-hl",
                "code": "ec-hl",
                "system": "http://code.system/eyes.color",
                "display": "Hazel",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-h",
                "code": "ec-h",
                "system": "http://code.system/eyes.color",
                "display": "Heterochromia",
                "resourceType": "Concept"
            }
        }
    ],
    "total": 5,
    ......
}
```
{% endtab %}
{% endtabs %}



The another way to create `CodeSystem` is separate creation CodeSystem body and Concepts.

For example, lets create CodeSystem with custom subset of Units of measurement \(aka [UCUM](http://unitsofmeasure.org/trac)\). This CodeSystem we are compose with most common healthcare units.

| Code | Descriptive Name | Display  |
| :--- | :--- | :--- |
| % | Persent | % |
| /uL | PerMicroLiter | /uL |
| \[iU\]/L | InternationalUnitsPerLiter | IU/L |
| kPa | KiloPascal | kPa |
| ng/mL | NanoGramsPerMilliLiter | ng/mL |
| U/L | UnitsPerLiter | U/L |
| u\[iU\]/mL | MicroInternationalUnitsPerMilliLiter | uIU/mL |
| ug/dL | MicroGramsPerDeciLiter | ug/dL |
| ug/L | MicroGramsPerLiter | ug/L |
| ug/mL | MicroGramsPerMilliLiter | ug/mL |
| umol/L | MicroMolesPerLiter | umol/L |

For this subset lets create `CodeSystem` resource.

{% tabs %}
{% tab title="Request" %}
```javascript
POST [base]/CodeSystem

{
    "resourceType": "CodeSystem",
    "id": "custom-ucum-subset",
    "url": "http://code.system/ucum.subset",
    "status": "active",
    "content": "complete"
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 201

{
    "id": "custom-ucum-subset",
    "resourceType": "CodeSystem"
    "url": "http://code.system/ucum.subset",
    "status": "active",
    "content": "complete"
}
```
{% endtab %}
{% endtabs %}

And then, create all of listed concepts. We can create a set of concepts via multiple `POST` requests or via one [`Transaction`](https://www.hl7.org/fhir/http.html#transaction) request.

{% tabs %}
{% tab title="Multiple requests" %}
```javascript
POST [base]/Concept

{
 "resourceType": "Concept",
 "system": "http://code.system/ucum.subset",
 "code": "%",
 "display": "%",
 "definition": "Persent"
}

POST [base]/Concept

{
 "resourceType": "Concept",
 "system": "http://code.system/ucum.subset",
 "code": "/uL",
 "display": "/uL",
 "definition": "PerMicroLiter"
}

POST [base]/Concept

{
 "resourceType": "Concept",
 "system": "http://code.system/ucum.subset",
 "code": "[iU]/L",
 "display": "IU/L",
 "definition": "InternationalUnitsPerLiter"
}

......

POST [base]/Concept

{
 "resourceType": "Concept",
 "system": "http://code.system/ucum.subset",
 "code": "umol/L",
 "display": "umol/L",
 "definition": "MicroMolesPerLiter"
}
```
{% endtab %}

{% tab title="Transaction request" %}
```javascript
POST [base]

{
  "type": "transaction",
  "entry": [
    {
      "request": {
        "method": "POST",
        "url": "/Concept"
      },
      "resource": {
        "resourceType": "Concept",
        "system": "http://code.system/ucum.subset",
        "code": "%",
        "display": "%",
        "definition": "Persent"
      }
    },
    {
      "request": {
        "method": "POST",
        "url": "/Concept"
      },
      "resource": {
        "resourceType": "Concept",
        "system": "http://code.system/ucum.subset",
        "code": "/uL",
        "display": "/uL",
        "definition": "PerMicroLiter"
      }
    },
    {
      "request": {
        "method": "POST",
        "url": "/Concept"
      },
      "resource": {
        "resourceType": "Concept",
        "system": "http://code.system/ucum.subset",
        "code": "umol/L",
        "display": "umol/L",
        "definition": "MicroMolesPerLiter"
      }
    }
  ]
}
```
{% endtab %}
{% endtabs %}



### Read

Read operation work as the same as FHIR read.

{% tabs %}
{% tab title="Request" %}
```javascript
GET [base]/CodeSystem/custom-eye-color
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 201

{
    "resourceType": "CodeSystem",
    "id": "custom-eye-color"
    "url": "http://code.system/eyes.color",
    "status": "draft",
    "content": "example"
}
```
{% endtab %}
{% endtabs %}



### Update

{% hint style="warning" %}
We are not supported updating CodeSystem
{% endhint %}

In the terminology is better practice is not update existing Coded Systems. Aidbox is follow this principe. If you need update any existing CodeSystem resource, you need explicitly delete this CodeSystem and re create it again with your changes.

### Delete

On delete `CodeSystem`,  Aidbox delete `CodeSystem` resource, and notes all of them `Concept` as `deprecated` = `true`.

{% tabs %}
{% tab title="Request" %}
```javascript
DELETE [base]/CodeSystem/custom-eye-color
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 201

{
    "url": "http://code.system/eyes.color",
    "status": "draft",
    "content": "example",
    "id": "custom-eye-color",
    "resourceType": "CodeSystem"
}
```
{% endtab %}
{% endtabs %}

And check all concepts for 

{% tabs %}
{% tab title="Request" %}
```javascript
GET [base]/Concept/?system=http://code.system/eyes.color
```
{% endtab %}

{% tab title="Response" %}
```javascript
STATUS: 200

{
    "resourceType": "Bundle",
    "type": "searchset",
    "entry": [
        {
            "resource": {
                "id": "custom-eye-color-ec-bn",
                "deprecated": true,
                "code": "ec-bn",
                "system": "http://code.system/eyes.color",
                "display": "Brown",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-be",
                "deprecated": true,
                "code": "ec-be",
                "system": "http://code.system/eyes.color",
                "display": "Blue",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-gn",
                "deprecated": true,
                "code": "ec-gn",
                "system": "http://code.system/eyes.color",
                "display": "Green",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-hl",
                "deprecated": true,
                "code": "ec-hl",
                "system": "http://code.system/eyes.color",
                "display": "Hazel",
                "resourceType": "Concept"
            }
        },
        {
            "resource": {
                "id": "custom-eye-color-ec-h",
                "deprecated": true,
                "code": "ec-h",
                "system": "http://code.system/eyes.color",
                "display": "Heterochromia",
                "resourceType": "Concept"
            }
        }
    ],
    "total": 5,
    ......
}
```
{% endtab %}
{% endtabs %}

## Operations

Aidbox support all standard [FHIR terminology](https://www.hl7.org/fhir/terminology-module.html) CodeSystem operations.

| FHIR specification | Status | Documentation and samples |
| :--- | :--- | :--- |
| [$lookup](https://www.hl7.org/fhir/codesystem-operations.html#lookup) | `supported` | [CodeSystem Concept Lookup](concept-lookup.md) |
| [$subsumes](https://www.hl7.org/fhir/codesystem-operations.html#subsumes) | `supported` | [CodeSystem Subsumption testing](subsumption-testing.md) |
| [$compose](https://www.hl7.org/fhir/codesystem-operations.html#compose) | `supported` | [CodeSystem Code composition](codesystem-code-composition.md) |
