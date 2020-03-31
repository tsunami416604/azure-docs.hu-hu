---
title: Referencia bemenetek és outputok a skillsets
titleSuffix: Azure Cognitive Search
description: Bemutatja a jegyzet szintaxisát, és hogyan hivatkozik egy jegyzetre egy askillset bemeneteiben és kimeneteiben egy AI-bővítési folyamatban az Azure Cognitive Search-ben.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113868"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Hogyan lehet hivatkozni a jegyzetekre az Azure Cognitive Search skillsetben?

Ebben a cikkben megtudhatja, hogyan hivatkozhat a szakértelem-definíciók ban lévő jegyzetekre, példákkal a különböző forgatókönyvek szemléltetésére. Ahogy a dokumentum tartalma egy sor készségen keresztül áramlik, a program jegyzetekkel gazdagítja. A jegyzetek bemenetként használhatók a későbbi dúsításhoz, vagy leképezhetők egy index kimeneti mezőjére. 
 
Ebben a cikkben példák az Azure [Blob indexelők](search-howto-indexing-azure-blob-storage.md) által a dokumentum repedési fázis részeként automatikusan létrehozott *tartalommezőalapján.* Blob-tárolóból származó dokumentumokra való hivatkozáskor `"/document/content"`használjon olyan formátumot, mint például a , ahol a *tartalommező* a *dokumentum*része. 

## <a name="background-concepts"></a>Háttérfogalmak

A szintaxis áttekintése előtt tekintsünk át néhány fontos fogalmat, hogy jobban megértsd a cikk későbbi részében megadott példákat.

| Időtartam | Leírás |
|------|-------------|
| Bővített dokumentum | A bővített dokumentum egy belső struktúra, amelyet a folyamat hoz létre és használ a dokumentumhoz kapcsolódó összes jegyzet tárolására. Gondoljon a bővített dokumentumra a jegyzetek fájaként. Általában egy korábbi jegyzetből létrehozott jegyzet lesz a gyermeke.<p/>A bővített dokumentumok csak a skillset-végrehajtás időtartamára léteznek. Miután a tartalom le lett képezve a keresési indexhez, a bővített dokumentumra már nincs szükség. Bár nem lép kapcsolatba közvetlenül a bővített dokumentumokkal, hasznos, ha egy skillset létrehozásakor rendelkezik a dokumentumok mentális modelljével. |
| Dúsítási környezet | Az a környezet, amelyben a dúsítás történik, amelynek szempontjából az elem gazdagodik. Alapértelmezés szerint a dúsítási `"/document"` környezet az egyes dokumentumokra ható szinten van. Amikor egy szakértelem fut, a szakértelem kimenetei [a megadott környezet tulajdonságaivá](#example-2)válnak.|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>1. példa: Egyszerű jegyzethivatkozás

Az Azure Blob storage-ban tegyük fel, hogy számos olyan fájltalálható, amely az entitásfelismerés használatával kiszeretne nyerni személyek nevére mutató hivatkozásokat. Az alábbi szakértelem-meghatározásban a teljes dokumentum szöveges ábrázolása látható, `"/document/content"` az "emberek" pedig a személyekként azonosított entitások teljes nevének kinyerése.

Mivel az alapértelmezett `"/document"`környezet a , a személyek `"/document/people"`listája most már hivatkozhat . Ebben a `"/document/people"` konkrét esetben egy jegyzet, amely most már leképezhető egy mező egy indexben, vagy használt egy másik szakértelem ugyanabban skillset.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

## <a name="example-2-reference-an-array-within-a-document"></a>2. példa: Dokumentumon belüli tömbre való hivatkozás

Ez a példa az előzőre épül, amely bemutatja, hogyan lehet egy dúsítási lépést többször meghívni ugyanazon a dokumentumon. Tegyük fel, hogy az előző példa egy karakterlánctömböt hozott létre 10 fős névvel egyetlen dokumentumból. Ésszerű következő lépés lehet egy második gazdagítás, amely kinyeri a vezetéknevet egy teljes névből. Mivel 10 név van, azt szeretné, hogy ezt a lépést 10-szer hívják meg ebben a dokumentumban, egyszer minden egyes személy esetében. 

A megfelelő számú ismétlés meghívásához állítsa be `"/document/people/*"`a környezetet úgy,`"*"`hogy a csillag ( ) a bővített dokumentum összes `"/document/people"`csomópontját a származékaként ábrázolja. Bár ez a szakértelem csak egyszer van definiálva a skills tömbben, a dokumentum on-line tagjainak a dokumentumon belüli minden egyes tagra van meghívva, amíg az összes tagot fel nem dolgozzák.

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

Ha a jegyzetek tömbök vagy karakterláncok gyűjteményei, érdemes lehet a tömb egésze helyett bizonyos tagokat megcélozni. A fenti példa létrehoz egy `"last"` jegyzetet, amelyet a környezet által képviselt egyes csomópontok alatt hívnak meg. Ha a jegyzetek családjára szeretne hivatkozni, használhatja a szintaxist. `"/document/people/*/last"` Ha egy adott megjegyzésre szeretne hivatkozni, használhat explicit indexet: `"/document/people/1/last`" a dokumentumban azonosított első személy vezetéknevére. Figyelje meg, hogy ebben a szintaxistömbben a "0 indexelt" elem található.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>3. példa: Hivatkozási tagok egy tömbön belül

Néha egy adott típusú összes jegyzetet kell csoportosítania ahhoz, hogy átadják őket egy adott szakértelemnek. Vegyünk egy hipotetikus egyéni szakértelem, amely azonosítja a leggyakoribb vezetéknév az összes kinyert 2. Ha csak az egyéni szakértelem vezetéknevét szeretné `"/document"` megadni, adja `"/document/people/*/lastname"`meg a környezetet a és a bemeneti szövegként.

Figyelje meg, `"/document/people/*/lastname"` hogy a számossága nagyobb, mint a dokumentum. 10 vezetéknév-csomópont lehet, miközben csak egy dokumentumcsomópont tartozik ehhez a dokumentumhoz. Ebben az esetben a rendszer automatikusan `"/document/people/*/lastname"` létrehoz egy tömböt, amely a dokumentum összes elemét tartalmazza.

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
+ [Egyéni szakértelem integrálása egy gazdagítási folyamatba](cognitive-search-custom-skill-interface.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Bővített mezők hozzárendelése indexhez](cognitive-search-output-field-mapping.md)
