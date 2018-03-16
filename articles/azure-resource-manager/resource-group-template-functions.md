---
title: "Resource Manager Sablonfüggvényei |} Microsoft Docs"
description: "Az Azure Resource Manager-sablonok segítségével értékek lekérését, karakterláncok és írhatók, és központi telepítési információk beolvasása funkcióit ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: tomfitz
ms.openlocfilehash: 54580abdca8b6be10576cf74ad23e8ff2665341c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-resource-manager-template-functions"></a>Az Azure Resource Manager sablonfüggvényei
Ez a cikk ismerteti az Azure Resource Manager-sablonokban használható összes függvények.

Funkciók hozzáadása szögletes zárójelben befoglaló azokat a sablonokat: `[` és `]`, illetve. A rendszer kiértékeli üzembe helyezése során. Megírva egy szöveges karakterlánc, miközben a értékeli a kifejezés eredménye egy eltérő típusú JSON, például egy tömb, objektum vagy egész szám lehet. Csak a például a JavaScript, függvényhívások-ként formázott `functionName(arg1,arg2,arg3)`. A pont és a [index] operátorok használatával tulajdonságok hivatkozik.

Egy sablon kifejezés nem lehet 24,576 karakternél.

Sablon függvényeket és paramétereket nem különböztetik meg. Például az erőforrás-kezelő oldja fel **variables('var1')** és **VARIABLES('VAR1')** egyezhet. Amikor értékeli ki, kivéve, ha a függvény kifejezetten eset (például toUpper vagy toLower) módosítja, a függvény megőrzi az eset. Előfordulhat, hogy az egyes erőforrástípusok eset követelmények, függetlenül attól, milyen funkciók kiértékelése.

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="json" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>A tömb és objektum funkciók
Erőforrás-kezelő számos funkciókat nyújt, tömbök és objektumok.

* [array](resource-group-template-functions-array.md#array)
* [Egyesítés](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [tartalmazza](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [üres](resource-group-template-functions-array.md#empty)
* [első](resource-group-template-functions-array.md#first)
* [intersection](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [last](resource-group-template-functions-array.md#last)
* [Hossza](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [range](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [hajtsa végre a megfelelő](resource-group-template-functions-array.md#take)
* [a UNION](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Összehasonlítás funkciók
Erőforrás-kezelő számos funkciókat nyújt a sablonokban összehasonlításához.

* [egyenlő](resource-group-template-functions-comparison.md#equals)
* [kevesebb](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [greater](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Központi telepítési érték funkciók
Erőforrás-kezelő a következő funkciókat nyújt értékek lekérése a sablon és a központi telepítéshez kapcsolódó értékek szakaszait:

* [deployment](resource-group-template-functions-deployment.md#deployment)
* [paraméterek](resource-group-template-functions-deployment.md#parameters)
* [variables](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>Logikai funkciók
Erőforrás-kezelő a következő funkciókat biztosít a logikai feltételek használata:

* [És](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="numeric-functions"></a>Numerikus funkciók
Erőforrás-kezelő a következő funkciókat nyújt egész számok használata:

* [add](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [MOD](resource-group-template-functions-numeric.md#mod)
* [MUL számú](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Erőforrás-funkciók
Erőforrás-kezelő a következő funkciókat biztosít erőforrás értékek beolvasása:

* [listKeys és a {Value} lista](resource-group-template-functions-resource.md#listkeys)
* [szolgáltatók](resource-group-template-functions-resource.md#providers)
* [reference](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [előfizetést](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="guid" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>Karakterlánc
Erőforrás-kezelő a következő funkciókat nyújt karakterláncok használata.

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [tartalmazza](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [üres](resource-group-template-functions-string.md#empty)
* [megadott módon végződő](resource-group-template-functions-string.md#endswith)
* [első](resource-group-template-functions-string.md#first)
* [guid](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [last](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [Hossza](resource-group-template-functions-string.md#length)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [replace](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [felosztás](resource-group-template-functions-string.md#split)
* [startswith elemnek](resource-group-template-functions-string.md#startswith)
* [string](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [hajtsa végre a megfelelő](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [uri](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md)
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager eszközzel](resource-group-linked-templates.md)
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példányát az Azure Resource Manager létrehozása](resource-group-create-multiple.md)
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md)
