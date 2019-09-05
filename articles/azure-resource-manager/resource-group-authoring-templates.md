---
title: Azure Resource Manager a sablon szerkezete és szintaxisa | Microsoft Docs
description: Ismerteti Azure Resource Manager sablonok felépítését és tulajdonságait a deklaratív JSON-szintaxis használatával.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275140"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése

Ez a cikk egy Azure Resource Manager sablon szerkezetét ismerteti. Bemutatja a sablon különböző részeit és az ezekben a részekben elérhető tulajdonságokat. A sablon JSON-ből és kifejezésből áll, amelyek használatával értékeket lehet létrehozni a központi telepítéshez.

Ez a cikk a Resource Manager-sablonok megismerését végző felhasználók számára készült. Részletes információkat tartalmaz a sablon struktúrájáról és szintaxisáról. Ha szeretne bevezetést létrehozni egy sablon létrehozásához, tekintse [meg az első Azure Resource Manager sablon létrehozását](resource-manager-create-first-template.md)ismertető témakört.

## <a name="template-format"></a>Sablon formátuma

A legegyszerűbb struktúrájában a sablon a következő elemekből áll:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| $schema |Igen |A sablon nyelvének verzióját leíró JSON-sémafájl helye.<br><br> Erőforráscsoport-telepítések esetén használja a következőt:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Az előfizetés központi telepítéséhez használja a következőt:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Igen |A sablon verziója (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Ha a sablonnal telepít erőforrásokat, ezzel az értékkel meggyőződhet arról, hogy a megfelelő sablon használatban van-e. |
| apiProfile |Nem | Egy API-verzió, amely az erőforrástípusok API-verzióinak gyűjteménye szolgál. Ezzel az értékkel nem kell megadnia az API-verziókat a sablon egyes erőforrásaihoz. Ha megad egy API-profil verzióját, és nem ad meg API-verziót az erőforrás típushoz, a Resource Manager a profilban definiált erőforrástípus API-verzióját használja.<br><br>Az API-profil tulajdonság különösen hasznos lehet egy sablon különböző környezetekben, például a Azure Stack és a globális Azure-ban való telepítésekor. Az API-profil verziójának használatával győződjön meg arról, hogy a sablon automatikusan használ mindkét környezetben támogatott verziót. Az API-profilok aktuális verzióinak és a profilban definiált erőforrások API-verzióinak listáját lásd: [API-profil](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>További információ: [verziók nyomon követése az API-profilokkal](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nem |A központi telepítés végrehajtásakor megadott értékek az erőforrás-telepítés testreszabásához. |
| [variables](#variables) |Nem |A sablonban JSON-töredékként használt értékek a sablon nyelvi kifejezésének egyszerűsítése érdekében. |
| [functions](#functions) |Nem |A sablonon belül elérhető, felhasználó által definiált függvények. |
| [resources](#resources) |Igen |Erőforráscsoport vagy előfizetés által központilag telepített vagy frissített erőforrástípusok. |
| [outputs](#outputs) |Nem |Az üzembe helyezés után visszaadott értékek. |

Minden elemnek van beállítható tulajdonsága. Ez a cikk részletesebben ismerteti a sablon szakaszait.

## <a name="parameters"></a>Paraméterek

A sablon paraméterek szakaszában megadhatja, hogy az erőforrások telepítésekor milyen értékeket adhat meg. Ezek a paraméterérték lehetővé teszik az üzemelő példány testreszabását egy adott környezethez (például fejlesztési, tesztelési és éles) igazított értékek biztosításával. Nem kell paramétereket megadnia a sablonban, de paraméterek nélkül a sablon mindig ugyanazokat az erőforrásokat fogja telepíteni, mint a nevek, a helyszínek és a tulajdonságok.

Egy sablonban legfeljebb 256 paramétert használhat. Az ebben a cikkben látható módon csökkentheti a paraméterek számát több tulajdonságot tartalmazó objektumok használatával.

### <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A paraméterek elérhető tulajdonságai a következők:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| parameterName |Igen |A paraméter neve. Érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméter értékének típusa A megengedett típusok és értékek a következők: **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject**és **Array**. |
| defaultValue |Nem |A paraméter alapértelmezett értéke, ha a paraméterhez nincs megadva érték. |
| allowedValues |Nem |A paraméter számára engedélyezett értékek tömbje, hogy meggyőződjön arról, hogy a megfelelő érték van megadva. |
| minValue |Nem |Az int Type paraméterek minimális értéke, ez az érték tartalmazza a befogadó értéket. |
| maxValue |Nem |Az int Type paraméterek maximális értéke, ez az érték tartalmazza a befogadó értéket. |
| minLength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömb típusú paraméterek minimális hossza, ez az érték a következő: inclusive. |
| maxLength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömb típusú paraméterek maximális hossza, ez az érték a következő: inclusive. |
| description |Nem |A felhasználók számára a portálon megjelenő paraméter leírása. További információ: [Megjegyzések a sablonokban](#comments). |

### <a name="define-and-use-a-parameter"></a>Paraméter definiálása és használata

Az alábbi példa egy egyszerű paraméter-definíciót mutat be. Meghatározza a paraméter nevét, és megadja, hogy karakterlánc-értéket vesz fel. A paraméter csak azokat az értékeket fogadja el, amelyek ésszerűek a kívánt használatra. Alapértelmezett értéket ad meg, ha a telepítés során nincs megadva érték. Végül a paraméter tartalmazza a használat leírását.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

A sablonban a következő szintaxissal hivatkozhat a paraméter értékére:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Sablon függvények paraméterekkel

Egy paraméter alapértelmezett értékének megadásakor használhatja a legtöbb sablon-függvényt. Az alapértelmezett érték kiépítéséhez használhat egy másik paraméter értékét is. A következő sablon a függvények használatát mutatja be az alapértelmezett értékben:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

A `reference` függvény nem használható a parameters (paraméterek) szakaszban. A paraméterek kiértékelése az üzembe helyezés `reference` előtt történik, így a függvény nem tudja lekérni egy erőforrás futtatókörnyezeti állapotát. 

### <a name="objects-as-parameters"></a>Objektumok paraméterként

A kapcsolódó értékek könnyebben rendezhetők úgy, hogy egy objektumként adják át őket. Ez a módszer a sablonban található paraméterek számát is csökkenti.

Adja meg a paramétert a sablonban, és adjon meg egy JSON-objektumot az üzembe helyezés során egyetlen érték helyett. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Ezután a pont operátor használatával hivatkozzon a paraméter altulajdonságaira.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Paraméterek – példa sablonok

Ezek a példák a paraméterek használatára vonatkozó forgatókönyveket mutatnak be. Telepítse őket a paraméterek különböző helyzetekben történő kezelésének teszteléséhez.

|Sablon  |Leírás  |
|---------|---------|
|[az alapértelmezett értékek függvényeit tartalmazó paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható a Template functions a paraméterek alapértelmezett értékeinek definiálásához. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |
|[paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Egy paraméter objektumának használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |

## <a name="variables"></a>Változók

A változók szakaszban a sablonban használható értékeket hozhat létre. Nem kell megadnia a változókat, de gyakran egyszerűsíti a sablont a komplex kifejezések csökkentésével.

### <a name="available-definitions"></a>Elérhető definíciók

A következő példa egy változó definiálásához elérhető lehetőségeket mutatja be:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

További információ a változó `copy` értékének a használatával történő létrehozásáról: [változó iteráció](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Változó meghatározása és használata

Az alábbi példa egy változó definícióját mutatja be. Létrehoz egy karakterlánc-értéket a Storage-fiók nevéhez. Számos sablon függvényt használ a paraméterérték beolvasásához, és összefűzi azt egy egyedi karakterlánchoz.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Az erőforrás definiálásakor a változót kell használnia.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Konfigurációs változók

Az összetett JSON-típusok használatával meghatározhatja a környezet kapcsolódó értékeit.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

A paraméterek területen létrehoz egy értéket, amely jelzi, hogy mely konfigurációs értékeket kell használni.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Az aktuális beállításokat a alábbiak szerint kérdezi le:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Példák a változóra

Ezek a példák a változók használatára vonatkozó forgatókönyveket mutatnak be. Telepítse őket a változók különböző helyzetekben történő kezelésének teszteléséhez. 

|Sablon  |Leírás  |
|---------|---------|
| [változók definíciói](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Bemutatja a különböző típusú változókat. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | A konfigurációs értékeket meghatározó változó használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Változó értékeket hoz létre, és visszaadja ezeket az értékeket. |
| [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméter fájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | A megfelelő formátumú tömböt hoz létre a biztonsági szabályok hálózati biztonsági csoporthoz való hozzárendeléséhez. |


## <a name="functions"></a>Funkciók

A sablonon belül létrehozhat saját függvényeket is. Ezek a függvények a sablonban használhatók. Általában bonyolult kifejezést kell megadnia, amelyet nem kíván megismételni a sablon során. A felhasználó által definiált függvényeket a sablonok által támogatott kifejezésekből és [függvényekből](resource-group-template-functions.md) hozza létre.

A felhasználói függvények meghatározásakor bizonyos korlátozások vonatkoznak:

* A függvény nem fér hozzá a változókhoz.
* A függvény csak a függvényben definiált paramétereket tudja használni. Ha a [Parameters függvényt](resource-group-template-functions-deployment.md#parameters) egy felhasználó által definiált függvényen belül használja, akkor a függvény paraméterei vannak korlátozva.
* A függvény nem hívhat meg más, felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [Reference függvényt](resource-group-template-functions-resource.md#reference).
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.

A függvények névtér-értéket igényelnek, így elkerülhető a sablon függvényekkel való névütközés. A következő példa egy olyan függvényt mutat be, amely egy Storage-fiók nevét adja vissza:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

A függvényt a alábbiak szerint hívja meg:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>További források
Az erőforrások szakaszban megadhatja a telepített vagy frissített erőforrásokat.

### <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

Az erőforrásokat az alábbi struktúrával definiálhatja:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| condition | Nem | Logikai érték, amely azt jelzi, hogy az erőforrás a telepítés során lesz-e kiépítve. Amikor `true`az erőforrás létrejön az üzembe helyezés során. Ha `false`az erőforrás ki van hagyva ehhez a központi telepítéshez. Lásd: [feltételes üzembe helyezés](conditional-resource-deployment.md). |
| apiVersion |Igen |Az erőforrás létrehozásához használandó REST API verziója. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). |
| type |Igen |Az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrás típusának (például a **Microsoft. Storage/storageAccounts**) a kombinációja. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). Gyermek erőforrás esetén a típus formátuma attól függ, hogy a szülő erőforráson belül van-e beágyazva, vagy a szülő erőforráson kívül van-e definiálva. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |
| name |Igen |Az erőforrás neve. A névnek követnie kell a RFC3986-ben definiált URI-összetevők korlátozásait. Emellett az Azure-szolgáltatások, amelyek az erőforrás nevét külső feleknek teszik elérhetővé, érvényesítik a nevet annak biztosítására, hogy ne Próbálkozzon másik identitás hamisításával. Gyermek erőforrás esetén a név formátuma attól függ, hogy a szülő erőforráson belül van-e beágyazva, vagy a szülő erőforráson kívül van-e definiálva. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |
| location |Változó |A megadott erőforrás támogatott földrajzi helyei. Bármelyik elérhető helyet kiválaszthatja, de általában érdemes lehet a felhasználókhoz közelebbi választ adni. Általában az is fontos, hogy olyan erőforrásokat helyezzen el, amelyek egymással együttműködnek ugyanabban a régióban. A legtöbb erőforrástípus egy helyet igényel, de bizonyos típusok (például a szerepkör-hozzárendelés) nem igényelnek helyet. Lásd az [erőforrás helyének beállítása](resource-location.md) című témakört. |
| címkék |Nem |Az erőforráshoz társított címkék. Címkék alkalmazása az erőforrások logikai rendszerezéséhez az előfizetésen belül. |
| Megjegyzések |Nem |A sablon erőforrásainak dokumentálására szolgáló megjegyzései. További információ: [Megjegyzések a sablonokban](resource-group-authoring-templates.md#comments). |
| másolás |Nem |Ha több példányra van szükség, a létrehozandó erőforrások száma. Az alapértelmezett üzemmód párhuzamos. Ha nem szeretné, hogy az összes vagy az erőforrások egyszerre legyenek telepítve, akkor a soros módot kell megadnia. További információ: az [erőforrások több példányának létrehozása Azure Resource Managerban](resource-group-create-multiple.md). |
| dependsOn |Nem |Az erőforrás üzembe helyezése előtt telepítendő erőforrások. A Resource Manager kiértékeli az erőforrások közötti függőségeket, és a megfelelő sorrendben telepíti őket. Ha az erőforrások nem függnek egymástól, párhuzamosan lesznek üzembe helyezve. Az érték lehet az erőforrásnevek vagy az erőforrás egyedi azonosítóinak vesszővel tagolt listája. Csak a sablonban üzembe helyezett erőforrások listázása. A sablonban nem definiált erőforrásoknak már léteznie kell. Kerülje a szükségtelen függőségek hozzáadását, és lassíthatja az üzembe helyezést, és körkörös függőségeket hozhat létre. A függőségek beállításával kapcsolatos útmutatásért lásd: [függőségek meghatározása Azure Resource Manager sablonokban](resource-group-define-dependencies.md). |
| properties |Nem |Erőforrás-specifikus konfigurációs beállítások. A tulajdonságok értékei megegyeznek a REST API művelet (PUT metódus) által a kérelem törzsében megadott értékekkel az erőforrás létrehozásához. Egy másolási tömböt is megadhat egy tulajdonság több példányának létrehozásához. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). |
| sku | Nem | Egyes erőforrások lehetővé teszik a telepítendő SKU-t meghatározó értékek használatát. Megadhatja például, hogy milyen típusú redundancia van egy Storage-fiókhoz. |
| típusú | Nem | Egyes erőforrások lehetővé teszik egy olyan érték használatát, amely meghatározza a telepített erőforrás típusát. Megadhatja például a létrehozandó Cosmos DB típusát. |
| csomag | Nem | Egyes erőforrások lehetővé teszik az olyan értékek használatát, amelyek meghatározzák az üzembe helyezési tervet. Megadhatja például a virtuális gép Marketplace-rendszerképét. | 
| erőforrások |Nem |A definiált erőforrástól függő alárendelt erőforrások. Csak a szülő erőforrás sémája által engedélyezett erőforrástípusok megadása. A fölérendelt erőforrástól való függőség nincs befoglalva. Explicit módon meg kell határoznia ezt a függőséget. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |

### <a name="resource-names"></a>Erőforrások nevei

Általánosságban elmondható, hogy három típusú erőforrás-névvel dolgozik a Resource Managerben:

* Az erőforrások neveinek egyedinek kell lenniük.
* Az olyan erőforrásnevek, amelyeknek nem kell egyedinek lenniük, de meg kell adnia egy nevet, amely segíthet az erőforrás azonosításában.
* Az általános erőforrások nevei.

Adjon meg **egyedi nevet** minden olyan erőforrástípus számára, amely adatelérési végponttal rendelkezik. Az egyedi nevet igénylő általános erőforrástípusok például a következők:

* Azure Storage<sup>1</sup> 
* Web Apps funkció az Azure App Service-ben
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> a Storage-fiókok nevének kisbetűvel, legfeljebb 24 karakterből vagy kevesebbből kell állnia, és nem lehet kötőjel.

A név beállításakor manuálisan létrehozhat egy egyedi nevet, vagy használhatja a [uniqueString ()](resource-group-template-functions-string.md#uniquestring) függvényt a név létrehozásához. Érdemes lehet előtagot vagy utótagot hozzáadni a **uniqueString** eredményéhez is. Az egyedi név módosításával könnyebben azonosítható az erőforrástípus a névben. Létrehozhat például egy egyedi nevet a Storage-fiókhoz a következő változó használatával:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Egyes erőforrástípusok esetében érdemes megadnia az **Azonosítás nevét**, de a névnek nem kell egyedinek lennie. Ilyen típusú erőforrástípusok esetén adjon meg egy nevet, amely leírja az IT-használatot vagy-tulajdonságokat.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Az olyan erőforrástípusok esetében, amelyekhez többnyire egy másik erőforráson keresztül férhet hozzá, használhat egy, a sablonban rögzített **általános nevet** . Beállíthat például egy szabványos, általános nevet a tűzfalszabályok számára egy SQL Serveren:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>Kimenetek

A kimeneti szakaszban adjon meg értékeket, amelyek a központi telepítés rendszer adja vissza. Jellemzően az üzembe helyezett erőforrások értékeit kell visszaadnia.

### <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A következő példa egy kimeneti definíciót szerkezetét mutatja:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| outputName |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| condition |Nem | Logikai érték, amely jelzi, hogy a rendszer visszaadja-e ezt a kimeneti értéket. Ha `true`a érték szerepel a központi telepítés kimenetében. `false`Ekkor a rendszer kihagyja a kimeneti értéket a központi telepítéshez. Ha nincs megadva, az alapértelmezett érték `true`:. |
| type |Igen |A kimeneti érték típusát. Sablon bemeneti paraméterként azonos kimeneti értékeket támogatásához. Ha a kimeneti típushoz **SecureString** ad meg, az érték nem jelenik meg a telepítési előzményekben, és nem kérhető le másik sablonból. Ha egy titkos értéket több sablonban szeretne használni, tárolja a titkot egy Key Vaultban, és hivatkozzon a titkos kulcsra a paraméter fájljában. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](resource-manager-keyvault-parameter.md)átadására az üzembe helyezés során. |
| value |Igen |Sablonnyelv-kifejezés, amely értékeli ki és adja vissza a kimeneti értéket. |

### <a name="define-and-use-output-values"></a>Definiálja és kimeneti értékeket

Az alábbi példa bemutatja, hogyan állítható vissza a nyilvános IP-cím erőforrás-azonosító:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A következő példa azt mutatja be, hogyan lehet feltételesen visszaadni egy nyilvános IP-cím erőforrás-AZONOSÍTÓját attól függően, hogy egy újat telepített-e:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A feltételes kimenet egyszerű példáját lásd: [feltételes kimeneti sablon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Az üzembe helyezés után az érték-parancsfájllal kérheti le. PowerShell esetén használja az alábbi parancsot:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Kérheti le a kimeneti értéket egy hivatkozott sablonnak a használatával a [referencia](resource-group-template-functions-resource.md#reference) függvény. Egy kimeneti értéket egy hivatkozott sablonnak a lekéréséhez a szintaxissal tulajdonság értékét a vizualizációhoz: `"[reference('deploymentName').outputs.propertyName.value]"`.

Amikor egy kimeneti tulajdonság lekérése egy hivatkozott sablonnak, a tulajdonság neve nem tartalmazhatja az kötőjellel.

Az alábbi példa bemutatja, hogyan állíthatja be az IP-címet egy terheléselosztó számára egy érték egy csatolt sablonból való beolvasásával.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nem használhatja a `reference` függvény kimenetek szakaszában egy [beágyazott sablont](resource-group-linked-templates.md#link-or-nest-a-template). Az értékeket egy üzembe helyezett erőforrás visszaadása egy beágyazott sablont, váltson egy hivatkozott sablonnak a beágyazott sablont.

### <a name="output-example-templates"></a>Példa kimeneti sablonokra

|Sablon  |Leírás  |
|---------|---------|
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Komplex változók hoz létre, és kiírja ezeket az értékeket. Nem telepíti az erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosítója. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablon mutató hivatkozásokat tartalmaz. A terheléselosztó létrehozásakor használja a kimenetben az erőforrás-azonosítója. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Megjegyzések és metaadatok

Van néhány lehetőség, amelyekkel megjegyzéseket és metaadatokat adhat hozzá a sablonhoz.

A sablonhoz szinte `metadata` bárhol hozzáadhat objektumokat. A Resource Manager figyelmen kívül hagyja az objektumot, de a JSON-szerkesztő figyelmeztetni lehet, ha a tulajdonság érvénytelen. Az objektumban adja meg a szükséges tulajdonságokat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

**Paraméterek**esetén adjon hozzá egy `metadata` objektumot egy `description` tulajdonsággal.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

A sablonnak a portálon keresztüli üzembe helyezése során a leírásban megadott szöveget a rendszer automatikusan tippként használja a paraméterhez.

![Paraméteres tipp megjelenítése](./media/resource-group-authoring-templates/show-parameter-tip.png)

**Erőforrások**esetén adjon hozzá egy `comments` elemet vagy egy metaadat-objektumot. Az alábbi példa egy Megjegyzés elemet és egy metaadat-objektumot mutat be.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

A **kimenetek**esetében adjon hozzá egy metaadat-objektumot a kimeneti értékhez.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Nem adhat hozzá metaadat-objektumot felhasználó által definiált függvényekhez.

A beágyazott megjegyzések esetében használhatja `//` ezt a szintaxist, de az összes eszközzel nem működik. Nem használhatja az Azure CLI-t a sablon beágyazott megjegyzésekkel való üzembe helyezéséhez. A portál sablon-szerkesztője nem használható beágyazott megjegyzésekkel rendelkező sablonokon való működéshez. Ha ezt a megjegyzést adja hozzá, győződjön meg arról, hogy az eszköz támogatja a beágyazott JSON-megjegyzéseket.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

A VS Code-ban megadhatja a nyelvi módot a JSON-be megjegyzésekkel. A beágyazott megjegyzések már nem érvénytelenként vannak megjelölve. A mód módosítása:

1. Nyitott nyelvi üzemmód kiválasztása (CTRL + K M)

1. Válassza **a JSON elemet megjegyzésekkel**.

   ![Nyelvi mód kiválasztása](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Ha több sablont szeretne egyesíteni az üzembe helyezés során, olvassa el a [csatolt sablonok használata Azure Resource Manager használatával](resource-group-linked-templates.md)című témakört.
* A sablonok létrehozásával kapcsolatos javaslatokért lásd: [Azure Resource Manager sablon ajánlott eljárásai](template-best-practices.md).
* Az összes Azure-környezetben és Azure Stackban használható Resource Manager-sablonok létrehozásával kapcsolatos javaslatokért lásd: [Azure Resource Manager-sablonok fejlesztése a felhő konzisztenciája érdekében](templates-cloud-consistency.md).
