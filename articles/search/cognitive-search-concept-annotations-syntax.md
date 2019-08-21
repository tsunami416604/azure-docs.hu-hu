---
title: A kognitív keresési folyamatokban lévő bemenetek és kimenetek hivatkozása – Azure Search
description: Ismerteti a jegyzet szintaxisát, valamint azt, hogyan lehet egy készségkészlet bemenetében és kimenetében lévő jegyzeteket Azure Search-ben kognitív keresési folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 5ce2c1aebebe802aa0fd3bda74c168e7a12341c0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639296"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Megjegyzések áttekintése egy kognitív keresési készségkészlet

Ebből a cikkből megtudhatja, hogyan hivatkozhat a megjegyzésekre a szaktudás-definíciókban, példákkal illusztrálva a különböző forgatókönyveket. Mivel a dokumentumok tartalma szaktudáson keresztül áramlik, a jegyzetek használatával gazdagítva lesz. A jegyzetek használhatók bemenetként a további alsóbb rétegbeli dúsításhoz, vagy egy index kimeneti mezőjéhez is hozzárendelve. 
 
A cikkben szereplő példák az [Azure Blob](search-howto-indexing-azure-blob-storage.md) -indexek által automatikusan generált *tartalom* mező alapján jelennek meg a dokumentum repedési fázisának részeként. Ha blob-tárolóból származó dokumentumokra hivatkozik, használjon például `"/document/content"`egy olyan formátumot, amelyben a *Content (tartalom* ) mező a *dokumentum*részét képezi. 

## <a name="background-concepts"></a>Háttérbeli fogalmak

A szintaxis felülvizsgálata előtt tekintse át a néhány fontos fogalmat, hogy jobban megértse a jelen cikk későbbi részében ismertetett példákat.

| Kifejezés | Leírás |
|------|-------------|
| Dúsított dokumentum | A dúsított dokumentumok egy belső struktúra, amelyet a folyamat hozott létre, és amely a dokumentumhoz kapcsolódó összes jegyzet tárolására szolgál. Tekintse meg a dúsított dokumentumokat jegyzetekkel ellátott faszerkezetként. Általában egy korábbi jegyzetből létrehozott jegyzet lesz a gyermeke.<p/>A dúsított dokumentumok csak a készségkészlet végrehajtásának időtartamára léteznek. Ha a tartalom a keresési indexre van leképezve, a bővített dokumentumra már nincs szükség. Bár a dúsított dokumentumokkal nem folytatja közvetlenül a kapcsolatot, a készségkészlet létrehozásakor hasznos lehet a dokumentumok mentális modellje. |
| Dúsítási környezet | Az a környezet, amelyben a gazdagodás zajlik, és amely alapján a rendszer kibővíti az elemet. Alapértelmezés szerint a dúsítási környezet az `"/document"` egyes dokumentumokra kiterjedő szinten van. A szaktudás futtatásakor a szaktudás kimenetei [a definiált környezet tulajdonságai](#example-2)lesznek.|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>1\. példa: Egyszerű jegyzet leírása

Tegyük fel, hogy az Azure Blob Storage-ban számos olyan fájl található, amely az Entity Recognition használatával kinyerni kívánt személyek nevére mutató hivatkozásokat tartalmaz. Az alábbi `"/document/content"` szakértelem-definícióban a teljes dokumentum szöveges ábrázolása, a "személyek" pedig teljes nevek kinyerése a személyként azonosított entitások számára.

Mivel az alapértelmezett környezet `"/document"`a, a felhasználók listája most már `"/document/people"`hivatkozhat. Ebben az esetben `"/document/people"` egy jegyzet, amely most már leképezhető egy index egy mezőjére, vagy egy másik, ugyanabban a készségkészlet lévő képességben.

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

## <a name="example-2-reference-an-array-within-a-document"></a>2\. példa Dokumentumon belüli tömb hivatkozása

Ez a példa az előzőre épül, amely azt mutatja be, hogyan hívhat meg egyszerre többször a dúsítási lépést ugyanazzal a dokumentummal. Tegyük fel, hogy az előző példa olyan karakterláncok tömbjét generálta, amelyek 10 személy névvel rendelkeznek egyetlen dokumentumból. Egy ésszerű következő lépés lehet egy második dúsítás, amely Kinyeri a vezetéknevet a teljes névben. Mivel a rendszer 10 nevet használ, ezt a lépést a jelen dokumentumban 10 alkalommal kell meghívni, minden személyre egyszer. 

A megfelelő számú iteráció meghívásához állítsa be a kontextust `"/document/people/*"`úgy, hogy a csillag`"*"`() a kibővített `"/document/people"`dokumentum összes csomópontját a következőként jelenítse meg. Bár ez a képesség csak egyszer van definiálva a szaktudás tömbben, a rendszer minden tag számára meghívja a dokumentumot, amíg az összes tagot fel nem dolgozza.

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

Ha a jegyzetek tömbök vagy sztringek gyűjteményei, előfordulhat, hogy a tömb egésze helyett meghatározott tagokat szeretne megcélozni. A fenti példa létrehoz egy jegyzetet, `"last"` amelyet a környezet által jelzett csomópontok alatt nevezünk. Ha ezt a családot szeretné megtekinteni a megjegyzések közül, használhatja a szintaxist `"/document/people/*/last"`. Ha egy adott jegyzetre szeretne hivatkozni, használhat egy explicit indexet: `"/document/people/1/last`"a dokumentumban azonosított első személy vezetéknevének hivatkozásához. Figyelje meg, hogy ebben a szintaxisban a tömbök "0 indexelt".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>3\. példa: Egy tömbön belüli hivatkozási tagok

Előfordulhat, hogy egy adott típushoz tartozó összes jegyzetet egy adott szaktudásba kell átadnia. Vegyünk egy feltételezett egyéni képességet, amely a 2. példában kinyert összes vezetéknevet azonosítja a leggyakoribb vezetéknevek közül. Ha csak az utolsó nevet szeretné megadni az egyéni szakértelem számára, adja meg a `"/document"` kontextust és a `"/document/people/*/lastname"`bemenetet a következőként:.

Figyelje meg, hogy a kardinális `"/document/people/*/lastname"` érték nagyobb, mint a dokumentum. A dokumentumhoz csak egy dokumentum-csomópont tartozhat. Ebben az esetben a rendszer automatikusan létrehoz egy tömböt `"/document/people/*/lastname"` , amely tartalmazza a dokumentum összes elemét.

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
+ [Egyéni szakértelem integrálása a dúsítási folyamatba](cognitive-search-custom-skill-interface.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [A dúsított mezők indexhez való leképezése](cognitive-search-output-field-mapping.md)
