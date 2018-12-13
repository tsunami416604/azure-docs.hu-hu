---
title: A kognitív keresés folyamat – Azure Search egy képességcsoport létrehozása
description: Adatok kinyerése, természetes nyelvi feldolgozás, definiálni, vagy kép elemzési lépések gazdagítják és az adatok a strukturált adatok kinyerését használják az Azure Search szolgáltatásban.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 091a165dacbf0e98532f343745e56c4acf765b84
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320795"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Egy képességcsoport létrehozása Adatbővítés folyamatban

Cognitive search ad eredményül, és bővíti, hogy az Azure Search a kereshető adatok. Kibontási-felderítési bővítést lépéseket nevezzük *kognitív képességeket*, kombinált be egy *indexmezők* hivatkozott az indexelés során. A képességek alkalmazási lehetőségét használható [képességek az előre meghatározott](cognitive-search-predefined-skills.md) vagy egyéni képesség (lásd: [példa: hozzon létre egy egyéni ismeretek](cognitive-search-create-custom-skill-example.md) további információt).

Ebből a cikkből elsajátíthatja, hogyan hozhat létre a használni kívánt képességek Adatbővítés folyamatot. A képességek alkalmazási lehetőségét csatolva van egy Azure Search [indexelő](search-indexer-overview.md). Folyamat tervezése, a cikkben szereplő egyik összetevője van hozhat létre, a képességek alkalmazási lehetőségét magát. 

> [!NOTE]
> A tervezési folyamat része egy másik határozza meg az indexelő tárgyalja a [következő lépésre](#next-step). Az indexelő meghatározását tartalmaz egy hivatkozást a képességek alkalmazási lehetőségét, valamint a bemeneti adatokat a célindex a kimenetek csatlakozáshoz használt mező-leképezések.

Ne feledje, hogy alapvető szempontokat:

+ Legfeljebb egy indexmezők egy indexelő.
+ A képességek alkalmazási lehetőségét rendelkeznie kell legalább egy ismeretek.
+ Létrehozhat több, azonos típusú (például egy kép elemzési szakértelem változatának) képességek.

## <a name="begin-with-the-end-in-mind"></a>A célból szem kezdődik

Ajánlott kezdeti lépés gondolkodik adatok kinyerése a nyers adatokat, és hogyan szeretné az adatokat a keresési megoldások. Az ábra a teljes felderítési bővítést folyamat létrehozása segítségével azonosíthatja a szükséges lépéseket.

Tegyük fel, hogy Önt érdeklő feldolgozása pénzügyi elemzői megjegyzések készletét. Egyes fájlok szeretne kinyerni a vállalat neve és az általános hangulatát hozzászólást. Érdemes azt is, egy egyéni enricher a vállalat például folytat a vállalat üzleti milyen további információkat találhat a Bing Entity Search Service szolgáltatást használó írni. Alapvetően az alábbihoz hasonló információkat vonhat ki a kívánt indexelt dokumentumok:

| rekord-szöveg | Vállalatok | vélemények | Vállalati leírása |
|--------|-----|-----|-----|
|minta-record| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation-Amerikai nemzetközi technológiai vállalat...", "LinkedIn egy üzleti és alkalmazás-jellegű közösségi hálózati..."]

A következő ábra szemlélteti egy képzeletbeli Adatbővítés folyamatot:

![Egy képzeletbeli Adatbővítés folyamat](media/cognitive-search-defining-skillset/sample-skillset.png "elméleti Adatbővítés folyamat")


Ha már van mit a folyamat, amely ezeket a lépéseket biztosít készségeitől fejezhető valós képet. A képességek alkalmazási lehetőségét funkcionálisan, amikor feltölti az indexelő definíciója az Azure Search van kifejezve. Az indexelő fel lehet kapcsolatos további információkért tekintse meg a [indexelő-dokumentáció](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Az ábrán a *dokumentumfeltörést* lépés automatikusan megtörténik. Alapvetően az Azure Search képes megnyitni a jól ismert fájlokat, és létrehoz egy *tartalom* minden egyes dokumentum kinyert szöveget tartalmazó mezőbe. A fehér mezők beépített enrichers, és a pontozott "A Bing Entity Search" mezőben a létrehozott egyéni enricher jelöli. Módon, a képességek alkalmazási lehetőségét tartalmazza három képességek.

## <a name="skillset-definition-in-rest"></a>Képességcsoport definíciója a REST-ben

A képességek alkalmazási lehetőségét képességek tömbként van definiálva. Minden képzettségi kimeneteinek forrását és a állítanak nevét határozza meg. Használatával a [indexmezők REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset), akkor is képességcsoport megadása, amely megfelel az előző ábrának: 

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

A képességek alkalmazási lehetőségét létrehozásakor megadhat egy leírást, hogy a képességek alkalmazási lehetőségét önálló dokumentálásáért. A leírás megadása nem kötelező, de hasznos, ha tartja a képességek alkalmazási lehetőségét funkciója nyomon követheti, nem. Képességcsoport egy JSON-dokumentum, amely nem engedélyezi a megjegyzéseket, mert kell használnia egy `description` elemen.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A következő darab készségeitől a képességek egy tömb. Mint egy primitívet felderítési bővítést az egyes szakértelem is felfoghatók. Minden képzettségi kis feladat ebben az adatcsatornában Adatbővítés hajt végre. Mindegyik veszi a bemenetnek (vagy egy bemeneteinek), és néhány kimenetek adja vissza. A következő néhány szakaszban koncentrálhat, előre meghatározott és egyéni ismeretek képességek együtt láncolási bemeneti és kimeneti hivatkozások útján megadása. Bemenetek forrásadatok vagy egy másik szakértelem származhatnak. Kimenetek lehet rendelve egy mezőt a keresési index vagy alsóbb rétegbeli szakértelem bemeneteként használja.

## <a name="add-predefined-skills"></a>Adja hozzá az előre megadott képesség

Nézzük meg az első szakértelem, amely az előre meghatározott [megnevezett entitások felismerése szakértelem](cognitive-search-skill-named-entity-recognition.md):

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

* Minden előre meghatározott szakértelem rendelkezik `odata.type`, `input`, és `output` tulajdonságait. Ismeretek jellemző tulajdonságok a képzettségi alkalmazható további információkkal szolgálnak. Az entitások felismerése `categories` egy entitás között, hogy felismerje a pretrained modell entitástípusok készletét.

* Minden képzettségi rendelkeznie kell egy ```"context"```. A környezet, amellyel műveletek egy szintjét jelöli. A fenti szakértelem összefüggésben a teljes dokumentum, ami azt jelenti, hogy a nevesített entitások felismerése szakértelem / dokumentum egyszer neve. Kimenetek is előállítása ezen a szinten. Pontosabban ```"organizations"``` jönnek létre, amelynek ```"/document"```. Az alsóbb rétegbeli képességeit, tekintse meg az újonnan létrehozott adatokat, mint ```"/document/organizations"```.  Ha a ```"context"``` mező értéke nem explicit módon, az alapértelmezett környezet a dokumentumot.

* A szakértelem rendelkezik úgynevezett "szöveg", a bemeneti forrás vannak beállítva egy bemeneti ```"/document/content"```. A szakértelem (megnevezett entitások felismerése) működik a *tartalom* az Azure blob indexelőjével által létrehozott minden egyes dokumentum, amely standard mezőket a mező. 

* A szakértelem rendelkezik egy kimeneti nevű ```"organizations"```. Kimenetek létezik csak a feldolgozás során. Ez a kimenet egy alsóbb rétegbeli szakértelem bemeneti összekapcsolja, hivatkozhat a kimenetben: ```"/document/organizations"```.

* Egy adott dokumentum értékét ```"/document/organizations"``` kinyert szöveget szervezetek tömbje. Példa:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Bizonyos helyzetekben külön-külön hivatkozik egy tömb összes eleme. Tegyük fel például, hogy egyes elemeinek átadni kívánt ```"/document/organizations"``` külön-külön egy másik szakértelem (például az egyéni a Bing entity search enricher). A tömb egyes elemei hivatkozhatunk csillag ad hozzá az elérési út: ```"/document/organizations/*"``` 

A második szakértelem véleményelemzését a, az első enricher ugyanazt a mintát követi. Vesz ```"/document/content"``` kimenetként, és a egy a véleménypontszám tartalom példányonként értéket ad vissza. Mivel nem állította a ```"context"``` explicit módon mezőt, a kimenet (mySentiment) már gyermeke ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Adjon hozzá egy egyéni szakértelem

Az egyéni a Bing entity search enricher szerkezete visszahívása:

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

Ez a definíció, amely meghívja a webes API-k a Adatbővítés folyamat részeként egyéni műveleteket. Minden egyes szervezet elnevezett entitásfelismeréssel által azonosított szakértelem meghívja a webes API-k az adott szervezet leírását találja. A vezénylés, hogy mikor hívja a webes API-t és a flow a kapott információkat a Adatbővítés motor belsőleg kezeli. Az inicializálás szükséges az egyéni API hívása azonban a JSON-(például uri, hiba a httpHeaders és a várt bemenet) kötelező megadni. Egyéni webes API-k a Adatbővítés folyamat létrehozásakor útmutatásért lásd: [egy egyéni felületen definiálása](cognitive-search-custom-skill-interface.md).

Figyelje meg, hogy a "környezet" mező értéke ```"/document/organizations/*"``` csillaggal, tehát a Adatbővítés lépést nevezzük *minden* a szervezet ```"/document/organizations"```. 

A kimenet, ebben az esetben egy cég leírása jön létre minden egyes szervezet azonosított. A leírást (például a kulcsfontosságú kifejezések kinyerése) egy alsóbb rétegbeli lépésben kontextusban való megnevezésekor az elérési utat használja ```"/document/organizations/*/description"``` ennek a végrehajtására. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Végrehajtott információbeolvasás kívül strukturálatlan adatokat struktúra létrehozása

A képességek alkalmazási lehetőségét strukturálatlan adatok strukturált adatokat állít elő. Vegye figyelembe az alábbi példában:

*"Az a negyedik negyedévi Microsoft jelentkezett 1.1 milliárd dolláros bevétel az a LinkedIn, a közösségi hálózati vállalat, a múlt évi vásárolt. A beszerzési lehetővé teszi a LinkedIn-képességek a CRM- és Office képességeket kombinálhatja a Microsoft. Raktározója izgatottak vagyunk a folyamat állapotát az eddigi."*

Valószínűleg eredménye az alábbi ábrához hasonló létrehozott struktúrát a következő lesz:

![Minta kimeneti struktúra](media/cognitive-search-defining-skillset/enriched-doc.png "minta kimeneti struktúra")

Ne felejtse el, hogy ez a struktúra történik. Ez a diagram a kód ténylegesen nem lehet lekérdezni.

<a name="next-step"></a>
## <a name="next-steps"></a>További lépések

Most, hogy Ön ismeri a Adatbővítés folyamat és szakértelmével, folytassa [való hivatkozáshoz egy indexmezők széljegyzetet hogyan](cognitive-search-concept-annotations-syntax.md) vagy [kimenetek leképezése az indexben lévő mezők](cognitive-search-output-field-mapping.md). 
