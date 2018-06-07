---
title: A bemeneti adatok jegyzet és egy kognitív keresési folyamat az Azure Search kimenetek |} Microsoft Docs
description: A megjegyzés szintaxisa és hogyan hivatkozható a be- és kimenetekkel, egy skillset egy kognitív keresési folyamat az Azure Search a jegyzet mutatja be.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 0e074e93ecbe80c3acf3481c0d33917fbe5090c6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640906"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Hogyan hivatkozható széljegyzetet egy kognitív keresési skillset

Ebből a cikkből megismerheti, hogyan hivatkozható széljegyzetet szakértelem definíciók példák segítségével különböző lehetőségeket mutatja be. Mivel a dokumentum tartalmát képességek között zajló kommunikációról, azt lekérdezi növelést jegyzeteket. Jegyzetek további alárendelt dúsító bemeneteként használt, vagy egy index kimeneti mező rendel. 
 
A cikkben szereplő példák alapuló a *tartalom* mező automatikusan generált [Azure Blob indexelők](search-howto-indexing-azure-blob-storage.md) a dokumentum repedés fázis részeként. A Blob-tárolóból való dokumentumok, formátumának a használatára, mint `"/document/content"`, ahol a *tartalom* mező része a *dokumentum*. 

## <a name="background-concepts"></a>Háttér fogalmak

Előtt tekintse át a szintaxist, most le újra jobb megértése érdekében a cikkben bemutatott példák néhány fontosabb fogalmakra.

| Időtartam | Leírás |
|------|-------------|
| Bővített dokumentum | Az bővített dokumentum olyan egyik belső struktúra létrehozása és a folyamat használja, a dokumentum kapcsolódó összes jegyzetet tárolásához. Egy bővített dokumentum gondol a jegyzeteket fának. Létrehozott egy előző Megjegyzés Megjegyzés általában alárendelt válik.<p/>Bővített dokumentumokat csak skillset végrehajtási ideje alatt található. Ha a keresési index tartalom van rendelve, a bővített dokumentum már nem szükséges. Bár nem működik együtt, bővített dokumentumokat közvetlenül, az hasznos lehet a dokumentumok a szellemi modell egy skillset létrehozásakor. |
| Dúsító környezetben | A környezet, amelyben a dúsító kerül sor, szempontjából elem dúsulnak. Alapértelmezés szerint a dúsító környezet jelenleg a `"/document"` szint, egyes dokumentumok hatókörébe. Amikor szakértelem fut, a kimenetének válik, hogy szakértelem [tulajdonságok az adott környezet](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>1. példa: Egyszerű jegyzet referencia

Az Azure Blob storage tegyük fel, személyek nevét, amelyet szeretne kinyerni a névvel rendelkező entitás nevéből felismert tartománynév használatával való hivatkozásokat tartalmazó fájlok különböző. Az alábbi, a készséglefedettség definícióban `"/document/content"` a teljes dokumentum képviselő szöveges alakot, továbbá "személyek" egy kibontásával személyek azonosítottak entitások teljes nevét.

Mivel az alapértelmezett környezetben `"/document"`, személyek listáját most lehet hivatkozni `"/document/people"`. Ebben az esetben `"/document/people"` megjegyzés, most kell index mező leképezve, illetve az azonos skillset a munkája használt van.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>2. példa: Hivatkozik a dokumentum egy tömb

Ebben a példában az előzőre, hogy bemutatja, hogyan lehet meghívni egy dúsító lépést többször egyazon dokumentumban keresztül építkezik. Tegyük fel, az előző példában létrehozott 10 személyek nevével karakterláncokból álló egyetlen dokumentum. Előfordulhat, hogy ésszerű a következő lépésben egy második dúsító, amely a Vezetéknév kiolvassa a teljes nevet. Mivel 10 nevet, érdemes ezt a lépést 10-szer ebben a dokumentumban egyszer hívható meg minden egyes személy. 

A jobb oldali az ismétlések száma meghívni, állítsa be a környezetben, `"/document/people/*"`, ahol a csillag (`"*"`) a bővített dokumentumban található minden csomópontot leszármazott meghatalmazottként képviseli `"/document/people"`. Bár szakértelem csak egyszer definiálták a képességek tömbben, a neve a dokumentum tagjaihoz tartozó összes tag feldolgozásáig.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Ha jegyzetek tömbök vagy karakterláncok gyűjteményei, érdemes, amelyekre a tömb egész helyett meghatározott tagjainak. A fenti példa állít elő, nevű jegyzet `"last"` a környezet által képviselt minden csomópont alatt. Tekintse meg a jegyzeteket család szeretné, használhatja a szintaxis `"/document/people/*/last"`. Szeretné egy adott megjegyzés vonatkozik, használhatja az explicit index: `"/document/people/1/last`"való hivatkozáshoz a dokumentum az első személy vezetékneve. Figyelje meg, hogy ez a szintaxis a tömbök "1 indexed".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>3. példa: Hivatkozott tag szerepel egy tömbben

Néha kell átadni őket egy adott szakértelem adott típusú összes jegyzetet csoportosításához. Fontolja meg, amely azonosítja a 2. példa kivont utolsó nevéből leggyakoribb vezetékneve elméleti egyéni szakértelem. Adja meg az egyéni szakértelem csak az utolsó nevét, adja meg a környezetben, mint a `"/document"` , amikor a bemeneti, `"/document/people/*/lastname"`.

Vegye figyelembe, hogy a számossága `"/document/people/*/lastname"` ennél nagyobb, a dokumentum. Valószínűleg 10 Vezetéknév csomópontok, amíg ez a dokumentum csak egy dokumentum-csomópont. Ebben az esetben a rendszer automatikusan létrehoz egy tömbjét `"/document/people/*/lastname"` tartalmazó minden a dokumentumban eleme.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Lásd még
+ [Hogyan integrálhatók a egyéni szakértelem egy dúsító folyamat](cognitive-search-custom-skill-interface.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hozzon létre Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan képezheti bővített mezők index](cognitive-search-output-field-mapping.md)
