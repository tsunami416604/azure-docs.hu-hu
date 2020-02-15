---
title: Sablonfüggvények
description: A Azure Resource Manager-sablonban az értékek beolvasására, a karakterláncok és a numerikus karakterek használatára, valamint az üzembe helyezési adatok lekérésére használt függvények leírását ismerteti.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a9d10ad4899f35acd45069cb3d351a60632fed3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207042"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager sablon függvények

Ez a cikk a Azure Resource Manager-sablonokban használható összes funkciót ismerteti. További információ a függvények használatáról a sablonban: [sablon szintaxisa](template-expressions.md).

Saját függvények létrehozásához tekintse meg a [felhasználó által definiált függvények](template-syntax.md#functions)című témakört.

A legtöbb függvény ugyanúgy működik, amikor az erőforráscsoportot, az előfizetést, a felügyeleti csoportot vagy a bérlőt telepíti. Néhány függvény nem használható minden hatókörben. Ezeket az alábbi listán fel kell tüntetni.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Tömb- és objektumfüggvények

A Resource Manager számos funkciót biztosít a tömbök és objektumok használatához.

* [tömb](template-functions-array.md#array)
* [összefonódik](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [tartalmaz](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [üres](template-functions-array.md#empty)
* [első](template-functions-array.md#first)
* [kereszteződés](template-functions-array.md#intersection)
* [JSON](template-functions-array.md#json)
* [utolsó](template-functions-array.md#last)
* [hossza](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [tartomány](template-functions-array.md#range)
* [kihagyása](template-functions-array.md#skip)
* [eltarthat](template-functions-array.md#take)
* [Union](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Összehasonlító függvények

A Resource Manager számos funkciót biztosít a sablonokban való összehasonlításhoz.

* [egyenlő](template-functions-comparison.md#equals)
* [kisebb](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [nagyobb](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Központi telepítési érték függvények

A Resource Manager a következő függvényeket biztosítja a sablon és a telepítéshez kapcsolódó értékek beolvasásához:

* [telepítési](template-functions-deployment.md#deployment)
* [környezet](template-functions-deployment.md#environment)
* [paraméterek](template-functions-deployment.md#parameters)
* [változók](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logikai függvények

A Resource Manager a következő funkciókat biztosítja a logikai feltételekkel való együttműködéshez:

* [és](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [Ha](template-functions-logical.md#if)
* [nem](template-functions-logical.md#not)
* [vagy](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numerikus függvények

A Resource Manager a következő függvényeket biztosítja az egész számokkal való használathoz:

* [hozzáadása](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Erőforrásfüggvények

Resource Manager az alábbi funkciókat biztosít erőforrás-értékeinek beolvasása:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [Listkeys műveletének beolvasása](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [listáját](template-functions-resource.md#list)
* [szolgáltatók](template-functions-resource.md#providers)
* [referencia](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) – csak az erőforráscsoporthoz történő központi telepítések esetén használható.
* [resourceId](template-functions-resource.md#resourceid) – bármely hatókörben használható, de az érvényes paraméterek a hatókörtől függően változnak.
* [előfizetés](template-functions-resource.md#subscription) – csak erőforráscsoporthoz vagy előfizetéshez használható központi telepítések esetén.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Sztringfüggvények

A Resource Manager a következő függvényeket biztosítja a karakterláncok használatához:

* [Base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [tartalmaz](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [üres](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [első](template-functions-string.md#first)
* [formátumban](template-functions-string.md#format)
* [GUID](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [utolsó](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [hossza](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [csere](template-functions-string.md#replace)
* [kihagyása](template-functions-string.md#skip)
* [felosztás](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [karakterlánc](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [eltarthat](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Következő lépések

* Azure Resource Manager sablonban található részekről a következő témakörben talál leírást: [Azure Resource Manager sablonok készítése](template-syntax.md)
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](linked-templates.md) című témakört.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezéséről lásd: [alkalmazás központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md)
