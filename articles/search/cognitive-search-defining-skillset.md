---
title: Hozzon létre egy skillset kognitív keresési folyamat (Azure Search) |} Microsoft Docs
description: Adja meg az adatok kinyerése, feldolgozás, a természetes nyelvű, vagy kép elemzése lépéseket kiegészítése és strukturált információk kinyerése a adatait az Azure Search használja.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.openlocfilehash: 997b106f748a2f18e8141f77f3b9ff8bb6b9d971
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268233"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Egy skillset dúsító-feldolgozási folyamat létrehozása

Kognitív keresési kibontja, valamint a következőképpen színesíti adatokba, így az Azure Search kereshető. Kibontási és dúsító lépéseket nevezzük *kognitív képességek*, a kombinált egy *skillset* hivatkozott indexelés során. Egy skillset használhatja [képességek az előre megadott](cognitive-search-predefined-skills.md) vagy egyéni képességek (lásd: [példa: hozzon létre egy egyéni szakértelem](cognitive-search-create-custom-skill-example.md) olvashat).

Ebből a cikkből megismerheti, hogyan szeretné használni a képességek egy dúsító folyamat létrehozásához. Egy skillset csatolva van egy Azure Search [indexelő](search-indexer-overview.md). Adatcsatorna tervezés, a cikkben szereplő egyik részét képező van hozhat létre, a skillset magát. 

> [!NOTE]
> Feldolgozási sor kialakításának részét egy másik határozza meg az indexelő, lásd: a [tovább](#next-step). Az indexelő definition tartalmaz egy hivatkozást a skillset, valamint a bemenetek, kimenetek a célként megadott index a csatlakozáshoz használt mező leképezéseket.

Legfontosabb:

+ Csak egy skillset / indexelő lehet.
+ Egy skillset rendelkeznie kell legalább egy szakértelem.
+ Létrehozhat több, ugyanabba a típusba tartozik (például egy kép elemzés szakértelem változatának) képességek, de minden szakértelem csak egyszer használható az azonos skillset belül.

## <a name="begin-with-the-end-in-mind"></a>A záró szem előtt kezdődik

Ajánlott kezdeti lépés el kell döntenie, melyik adatok kinyerése a nyers adatokat, és hogyan kívánja az adatokat a keresési megoldás. Az ábra a teljes dúsító folyamatának létrehozása segítségével azonosíthatja, hogy a szükséges lépéseket.

Tegyük fel, hogy érdekli, pénzügyi elemzői megjegyzések készlete feldolgozásakor. Az összes fájl esetében ki szeretné nyerni a vállalat nevét és a megjegyzések általános véleményeket. Érdemes azt is, egy egyéni enricher, a Bing entitás keresési szolgáltatást használ a vállalat, például a vállalat folytat üzleti milyen kapcsolatban további információt írni. Alapvetően információkat, például a következő kiolvasni kívánt nyilvántartott egyes dokumentumok indexelt:

| rekord-szöveg | A vállalatok | Véleményeket | Vállalati leírása |
|--------|-----|-----|-----|
|a minta-rekord| ["Microsoft" vagy "LinkedIn"] | 0,99 | ["Microsoft Corporation által kiadott egy amerikai nemzetközi technológia vállalati...", "LinkedIn egy és alkalmazás-üzletorientált közösségi hálózati..."]

Az alábbi ábrán látható egy elméleti dúsító folyamatot:

![Egy képzeletbeli dúsító folyamat](media/cognitive-search-defining-skillset/sample-skillset.png "elméleti dúsító folyamat")


Ha már van valós meghatározni, hogy mit szeretne a folyamat akkor is, amely ezeket a lépéseket a skillset express. Működését tekintve a skillset van kifejezve a indexelő definition Azure Search való feltöltésekor. Az indexelő feltöltéséről kapcsolatos további tudnivalókért tekintse meg a [indexelő-dokumentáció](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Az ábrán a *dokumentum repedés* lépés automatikusan megtörténik. Alapvetően Azure Search tudja nyitni a jól ismert fájlokat, és létrehoz egy *tartalom* kibontani a dokumentum szövegét tartalmazó mező. A fehér mezőkbe beépített enrichers, és a pontozott "Bing entitás" keresőmezőbe a létrehozni kívánt egyéni enricher jelöli. Módon, akkor a skillset három ismeretek tartalmazza.

## <a name="skillset-definition-in-rest"></a>REST-Skillset definíciójában

Egy skillset képességek tömb típusúként van definiálva. Minden egyes szakértelem bemenet a forrás- és a állítanak nevét határozza meg. Használja a [Skillset REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset), megadhat egy skillset, amely megfelel az előző ábrának: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Egy skillset létrehozásakor megadhat egy leírást, hogy ellenőrizze a skillset önálló dokumentálása. A Leírás: a nem kötelező, de hasznos nyomon követése céljából egy skillset funkciója. Mivel skillset egy JSON-dokumentumában, amely nem engedélyezi a megjegyzéseket, használjon egy `description` elemen.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A következő darab a skillset a képességek egy tömb. Az eltolásokat tekintheti minden szakértelem, egy primitív dúsító a. Minden egyes szakértelem egy kis feladatot az dúsító adatcsatorna hajt végre. Mindegyik időt vesz igénybe a bemeneti (vagy egy bemeneti adatokat), és néhány kimenetek adja vissza. A következő néhány szakasz megadása az előre meghatározott és egyéni képességei, ismeretek együtt láncolás bemeneti és kimeneti hivatkozások útján összpontosítanak. Bemenetek forrásadatok vagy egy másik szakértelem származhatnak. Kimenetek egy keresési indexszel mező leképezve, vagy egy alárendelt szakértelem bemeneteként használja.

## <a name="add-predefined-skills"></a>Előre definiált képességek hozzáadása

Az első szakértelem, amely az előre meghatározott nézzük [nevű entitás felismerés szakértelem](cognitive-search-skill-named-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Minden előre meghatározott szakértelem rendelkezik `odata.type`, `input`, és `output` tulajdonságok. Szakértelem jellemző tulajdonságok adott szakértelem alkalmazandó további információkkal szolgálnak. Az entitás felismerés `categories` egy entitás készletét, amely a pretrained modell fel tudja ismerni entitástípusok között.

* Minden egyes szakértelem rendelkeznie kell egy ```"context"```. A környezetben, ahol zajlanak szintjét jelöli. A fenti szakértelem a környezet a teljes dokumentum, ami azt jelenti, hogy a nevesített entitás felismerés szakértelem meghívása egyszer dokumentum. Kimenetek is előállítása azon a szinten. Pontosabban ```"organizations"``` tagjaként jönnek létre ```"/document"```. Az alsóbb rétegbeli képességek, olvassa el az újonnan létrehozott adatok ```"/document/organizations"```.  Ha a ```"context"``` mező nincs megadva, az alapértelmezett környezetben a dokumentumot.

* A szakértelem van egy bemeneti hívása a "text", a bemeneti forrás vannak beállítva a ```"/document/content"```. A szakértelem (nevű entitás felismerés) működik a *tartalom* minden a dokumentumban a szabványos mező mező hozta létre az Azure blob indexelő. 

* A szakértelem rendelkezik egy kimeneti nevű ```"organizations"```. Kimenetek létezik csak a feldolgozás során. A tanúsítványlánc egy alsóbb rétegbeli szakértelem bemeneti a kimenetet, hivatkozzon a kimeneti adatok ```"/document/organizations"```.

* Egy adott dokumentum értékének ```"/document/organizations"``` kibontani a szöveget a szervezetek tömbje. Példa:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Bizonyos helyzetekben a tömb egyes elemei külön hivatkozik. Tegyük fel például, hogy egyes elemeinek átadni kívánt ```"/document/organizations"``` (például az egyéni Bing entitás keresési enricher) egy másik szakértelem az külön-külön. A tömb egyes elemei hivatkoznak csillag ad hozzá az elérési út: ```"/document/organizations/*"``` 

A második szakértelem véleményeket kiolvasásához, az első enricher azonos mintát követi. Tart ```"/document/content"``` bemeneti, valamint a céggel kapcsolatos véleményeket. az összes olyan tartalmat példány helyezett értéket ad vissza. Mivel nem állította a ```"context"``` mezőben explicit módon, a kimenet (mySentiment) már gyermeke ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Egyéni szakértelem hozzáadása

Az egyéni Bing entitás keresési enricher szerkezete visszahívása:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Ez a definíció egyéni szakértelem, amely behívja a webes API-k a dúsító folyamatban. Minden egyes szervezet névvel rendelkező entitás felismerés által azonosított szakértelem hívja a webes API-k, hogy a szervezet leírása található. A vezénylés a hívás a webes API-t és a kapott információkat flow belsőleg kezeli a dúsító motor. Azonban az inicializálás szükséges az egyéni API felület meghívásakor kell megadni (például uri, httpHeaders és a bemenetek várt) JSON-ban. Egyéni webes API-k a dúsító adatcsatorna létrehozásakor útmutatóért lásd: [hogyan adhat meg egy egyéni felületet](cognitive-search-custom-skill-interface.md).

Figyelje meg, hogy a "context" mező értéke ```"/document/organizations/*"``` csillaggal, azaz a dúsító lépés nevezik *minden* a szervezet ```"/document/organizations"```. 

Kimeneti, ebben az esetben vállalati leírását, létrejön egy szervezet azonosított. A Leírás (például a kulcs kifejezés kibontási) egy alárendelt lépésben kontextusban való megnevezésekor szeretné használni az elérési út ```"/document/organizations/*/description"``` ehhez. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Fokozatokká kívüli strukturálatlan adatok struktúra létrehozása

A skillset hozza létre a strukturálatlan adatok kívül strukturált adatokat. Tekintse meg a következő példát:

*"A negyedik negyedévi Microsoft naplózott $1.1 milliárd a bevételek a LinkedIn, a közösségi hálózati vállalati azt vásárolt, az elmúlt évben. A beszerzési engedélyezi a Microsoft számára a CRM LinkedIn képességeket és Office képességeit. Raktározója Izgatottan várja, a folyamat az eddigi."*

Valószínűleg eredménye a következő ábra hasonló létrehozott struktúrával néz ki:

![Minta kimenet struktúra](media/cognitive-search-defining-skillset/enriched-doc.png "minta kimenet struktúra")

A visszaírási, hogy ez a struktúra belső-e. Ez a diagram a kódban ténylegesen nem lehet beolvasni.

<a name="next-step"></a>
## <a name="next-steps"></a>További lépések

Most, hogy ismeri a dúsító feldolgozási sorban lévő és skillsets, folytassa a [hogyan hivatkozható széljegyzetet egy skillset](cognitive-search-concept-annotations-syntax.md) vagy [hogyan képezheti kimenetek index mezők](cognitive-search-output-field-mapping.md). 
