---
title: Sablonfüggvények
description: A Azure Resource Manager-sablonban az értékek beolvasására, a karakterláncok és a numerikus karakterek használatára, valamint az üzembe helyezési adatok lekérésére használt függvények leírását ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: d237f5c42c1d1eaab7b2236b15a3122bcb55865b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331406"
---
# <a name="arm-template-functions"></a>ARM-sablon függvények

Ez a cikk a Azure Resource Manager-(ARM-) sablonban használható összes funkciót ismerteti. További információ a függvények használatáról a sablonban: [sablon szintaxisa](template-expressions.md).

Saját függvények létrehozásához tekintse meg a [felhasználó által definiált függvények](template-syntax.md#functions)című témakört.

A legtöbb függvény ugyanúgy működik, amikor az erőforráscsoportot, az előfizetést, a felügyeleti csoportot vagy a bérlőt telepíti. Néhány függvény nem használható minden hatókörben. Ezeket az alábbi listán fel kell tüntetni.

<a id="array" aria-hidden="true"></a>
<a id="concatarray" aria-hidden="true"></a>
<a id="contains" aria-hidden="true"></a>
<a id="createarray" aria-hidden="true"></a>
<a id="empty" aria-hidden="true"></a>
<a id="first" aria-hidden="true"></a>
<a id="intersection" aria-hidden="true"></a>
<a id="last" aria-hidden="true"></a>
<a id="length" aria-hidden="true"></a>
<a id="min" aria-hidden="true"></a>
<a id="max" aria-hidden="true"></a>
<a id="range" aria-hidden="true"></a>
<a id="skip" aria-hidden="true"></a>
<a id="take" aria-hidden="true"></a>
<a id="union" aria-hidden="true"></a>

## <a name="array-functions"></a>Tömb függvények

A Resource Manager számos funkciót biztosít a tömbök használatához.

* [tömb](template-functions-array.md#array)
* [concat](template-functions-array.md#concat)
* [tartalmaz](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [üres](template-functions-array.md#empty)
* [első](template-functions-array.md#first)
* [kereszteződés](template-functions-array.md#intersection)
* [utolsó](template-functions-array.md#last)
* [hossza](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [tartomány](template-functions-array.md#range)
* [kihagyása](template-functions-array.md#skip)
* [eltarthat](template-functions-array.md#take)
* [Union](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true"></a>
<a id="equals" aria-hidden="true"></a>
<a id="less" aria-hidden="true"></a>
<a id="lessorequals" aria-hidden="true"></a>
<a id="greater" aria-hidden="true"></a>
<a id="greaterorequals" aria-hidden="true"></a>

## <a name="comparison-functions"></a>Összehasonlító függvények

A Resource Manager számos funkciót biztosít a sablonokban való összehasonlításhoz.

* [összefonódik](template-functions-comparison.md#coalesce)
* [egyenlő](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true"></a>
<a id="parameters" aria-hidden="true"></a>
<a id="variables" aria-hidden="true"></a>

## <a name="date-functions"></a>Dátumfüggvények

A Resource Manager a következő függvényeket biztosítja a dátumok használatáról.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Központi telepítési érték függvények

A Resource Manager a következő függvényeket biztosítja a sablon és a telepítéshez kapcsolódó értékek beolvasásához:

* [telepítési](template-functions-deployment.md#deployment)
* [környezet](template-functions-deployment.md#environment)
* [paraméterek](template-functions-deployment.md#parameters)
* [változók](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true"></a>
<a id="bool" aria-hidden="true"></a>
<a id="if" aria-hidden="true"></a>
<a id="not" aria-hidden="true"></a>
<a id="or" aria-hidden="true"></a>

## <a name="logical-functions"></a>Logikai függvények

A Resource Manager a következő funkciókat biztosítja a logikai feltételekkel való együttműködéshez:

* [és](template-functions-logical.md#and)
* [logikai](template-functions-logical.md#bool)
* [Ha](template-functions-logical.md#if)
* [nem](template-functions-logical.md#not)
* [vagy](template-functions-logical.md#or)

<a id="add" aria-hidden="true"></a>
<a id="copyindex" aria-hidden="true"></a>
<a id="div" aria-hidden="true"></a>
<a id="float" aria-hidden="true"></a>
<a id="int" aria-hidden="true"></a>
<a id="minint" aria-hidden="true"></a>
<a id="maxint" aria-hidden="true"></a>
<a id="mod" aria-hidden="true"></a>
<a id="mul" aria-hidden="true"></a>
<a id="sub" aria-hidden="true"></a>

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

<a id="json" aria-hidden="true"></a>

## <a name="object-functions"></a>Objektumfüggvények

A Resource Manager számos funkciót biztosít az objektumok használatához.

* [tartalmaz](template-functions-object.md#contains)
* [üres](template-functions-object.md#empty)
* [kereszteződés](template-functions-object.md#intersection)
* [JSON](template-functions-object.md#json)
* [hossza](template-functions-object.md#length)
* [Union](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true"></a>
<a id="listkeys" aria-hidden="true"></a>
<a id="list" aria-hidden="true"></a>
<a id="providers" aria-hidden="true"></a>
<a id="reference" aria-hidden="true"></a>
<a id="resourcegroup" aria-hidden="true"></a>
<a id="resourceid" aria-hidden="true"></a>
<a id="subscription" aria-hidden="true"></a>
<a id="subscriptionResourceId" aria-hidden="true"></a>
<a id="tenantResourceId" aria-hidden="true"></a>

## <a name="resource-functions"></a>Erőforrásfüggvények

A Resource Manager a következő függvényeket biztosítja az erőforrások értékeinek lekéréséhez:

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

<a id="base64" aria-hidden="true"></a>
<a id="base64tojson" aria-hidden="true"></a>
<a id="base64tostring" aria-hidden="true"></a>
<a id="concat" aria-hidden="true"></a>
<a id="containsstring" aria-hidden="true"></a>
<a id="datauri" aria-hidden="true"></a>
<a id="datauritostring" aria-hidden="true"></a>
<a id="emptystring" aria-hidden="true"></a>
<a id="endswith" aria-hidden="true"></a>
<a id="firststring" aria-hidden="true"></a>
<a id="guid" aria-hidden="true"></a>
<a id="indexof" aria-hidden="true"></a>
<a id="laststring" aria-hidden="true"></a>
<a id="lastindexof" aria-hidden="true"></a>
<a id="lengthstring" aria-hidden="true"></a>
<a id="padleft" aria-hidden="true"></a>
<a id="replace" aria-hidden="true"></a>
<a id="skipstring" aria-hidden="true"></a>
<a id="split" aria-hidden="true"></a>
<a id="startswith" aria-hidden="true"></a>
<a id="string" aria-hidden="true"></a>
<a id="substring" aria-hidden="true"></a>
<a id="takestring" aria-hidden="true"></a>
<a id="tolower" aria-hidden="true"></a>
<a id="toupper" aria-hidden="true"></a>
<a id="trim" aria-hidden="true"></a>
<a id="uniquestring" aria-hidden="true"></a>
<a id="uri" aria-hidden="true"></a>
<a id="uricomponent" aria-hidden="true"></a>
<a id="uricomponenttostring" aria-hidden="true"></a>

## <a name="string-functions"></a>Sztringfüggvények

A Resource Manager a következő függvényeket biztosítja a karakterláncok használatához:

* [base64](template-functions-string.md#base64)
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
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [utolsó](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [hossza](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [csere](template-functions-string.md#replace)
* [kihagyása](template-functions-string.md#skip)
* [felosztása](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [sztring](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [eltarthat](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>További lépések

* Az ARM-sablon fejezeteinek leírását lásd: ARM- [sablonok készítése](template-syntax.md)
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](linked-templates.md) című témakört.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezéséről az [alkalmazás üzembe helyezése ARM-sablonokkal](deploy-powershell.md) című témakörben talál további információt.
