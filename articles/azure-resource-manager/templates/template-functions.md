---
title: Sablonfüggvények
description: Ismerteti az Azure Resource Manager-sablonban az értékek lekéréséhez, a karakterláncok és numerikus értékek használatához, valamint a központi telepítési adatok beolvasásához használandó függvényeket.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 1d2789e59c091b4e6c39be48b83fe610a592abe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156259"
---
# <a name="arm-template-functions"></a>ARM sablonfüggvények

Ez a cikk ismerteti az Azure Resource Manager (ARM) sablonban használható összes funkciót. A függvények sablonban való használatáról a [sablon szintaxisában](template-expressions.md)olvashat.

Saját függvények létrehozásáról a [Felhasználó által definiált függvények című témakörben látható.](template-syntax.md#functions)

A legtöbb függvény ugyanúgy működik, ha egy erőforráscsoport, előfizetés, felügyeleti csoport vagy bérlő üzembe helyezésekor. Néhány függvény nem használható minden hatókörben. Az alábbi listákon szerepelnek.

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

Az Erőforrás-kezelő számos függvényt biztosít a tömbök és objektumok számára.

* [tömb](template-functions-array.md#array)
* [összeolvad](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [Tartalmaz](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [Üres](template-functions-array.md#empty)
* [Első](template-functions-array.md#first)
* [Kereszteződés](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Utolsó](template-functions-array.md#last)
* [Hossza](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [Tartomány](template-functions-array.md#range)
* [Ugrál](template-functions-array.md#skip)
* [venni](template-functions-array.md#take)
* [Unió](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Összehasonlító függvények

Az Erőforrás-kezelő számos funkciót biztosít a sablonok összehasonlításához.

* [egyenlő](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Telepítési érték függvények

Az Erőforrás-kezelő a következő függvényeket biztosítja a sablon szakaszaiból és a központi telepítéshez kapcsolódó értékek beszerzéséhez:

* [Telepítési](template-functions-deployment.md#deployment)
* [Környezet](template-functions-deployment.md#environment)
* [Paraméterek](template-functions-deployment.md#parameters)
* [Változók](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logikai függvények

Az Erőforrás-kezelő a következő funkciókat biztosítja a logikai feltételekkel való munkához:

* [és](template-functions-logical.md#and)
* [Bool](template-functions-logical.md#bool)
* [Ha](template-functions-logical.md#if)
* [Nem](template-functions-logical.md#not)
* [Vagy](template-functions-logical.md#or)

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

Az Erőforrás-kezelő a következő funkciókat biztosítja az egész számokkal való munkához:

* [Hozzáadása](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [lebegőpontos](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [Mul](template-functions-numeric.md#mul)
* [Al](template-functions-numeric.md#sub)

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

Az Erőforrás-kezelő a következő függvényeket biztosítja az erőforrásértékek beszerzéséhez:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listabillentyűk](template-functions-resource.md#listkeys)
* [listSecrets (Titkok listika)](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [Szolgáltatók](template-functions-resource.md#providers)
* [Hivatkozás](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) – csak egy erőforráscsoportban történő központi telepítésben használható.
* [resourceId](template-functions-resource.md#resourceid) - bármely hatókörben használható, de az érvényes paraméterek a hatókörtől függően változnak.
* [előfizetés](template-functions-resource.md#subscription) – csak erőforráscsoportvagy előfizetés központi telepítéseiben használható.
* [előfizetésResourceId](template-functions-resource.md#subscriptionresourceid)
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

Az Erőforrás-kezelő a következő funkciókat biztosítja a karakterláncok közös működéséhez:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [Tartalmaz](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Üres](template-functions-string.md#empty)
* [végződikWith](template-functions-string.md#endswith)
* [Első](template-functions-string.md#first)
* [Formátum](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [Utolsó](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [Hossza](template-functions-string.md#length)
* [newGuid között](template-functions-string.md#newguid)
* [padBalra](template-functions-string.md#padleft)
* [Helyettesít](template-functions-string.md#replace)
* [Ugrál](template-functions-string.md#skip)
* [felosztás](template-functions-string.md#split)
* [kezdődik](template-functions-string.md#startswith)
* [sztring](template-functions-string.md#string)
* [Substring](template-functions-string.md#substring)
* [venni](template-functions-string.md#take)
* [lassabbra](template-functions-string.md#tolower)
* [toUpper (felső](template-functions-string.md#toupper)
* [Berendezés](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriKomponens](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow között](template-functions-string.md#utcnow)

## <a name="next-steps"></a>További lépések

* Az ARM-sablonok szakaszainak leírását az [ARM-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el a [Csatolt sablonok használata az Azure Resource Managerrel](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az [Alkalmazás telepítése ARM-sablonokkal című](deploy-powershell.md) témakörben olvashat.
