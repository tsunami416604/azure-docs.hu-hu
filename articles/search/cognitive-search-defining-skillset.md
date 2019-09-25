---
title: Készségkészlet létrehozása kognitív keresési folyamatokban – Azure Search
description: Meghatározhatja az adatok kinyerését, a természetes nyelvi feldolgozást, illetve a képelemzési lépéseket az adatokból Azure Search-ban való használatra szánt strukturált adatok dúsításához és kinyeréséhez
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: f78b8c3b9619b7eea92b6a4f04ed4f6543916efe
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265516"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Készségkészlet létrehozása a dúsítási folyamatban

A kognitív keresés kinyeri és gazdagítja az adatgyűjtést Azure Searchban kereshetővé teszi. A kinyerési és bővítési lépéseket a *kognitív képességek*, az indexelés során hivatkozott *készségkészlet* együtt hívjuk. A készségkészlet [beépített képességeket](cognitive-search-predefined-skills.md) vagy egyéni képességeket használhatnak (lásd [A példát: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md) további információért.

Ebből a cikkből megtudhatja, hogyan hozhat létre a használni kívánt képességek bővítési folyamatát. A készségkészlet egy Azure Search indexelő [](search-indexer-overview.md)van csatolva. A folyamat kialakításának egyik része, amely ebben a cikkben is szerepel, a készségkészlet maga hozza létre. 

> [!NOTE]
> A folyamat kialakításának egy másik része a [következő lépésben](#next-step)lefedett indexelő megadása. Az indexelő definíciója a készségkészlet mutató hivatkozást, valamint a bemenetek a célként megadott indexben való csatlakoztatásához használt mező-hozzárendeléseket is tartalmazza.

Jegyezze fel a legfontosabb pontokat:

+ Indexelő esetében csak egy készségkészlet tartozhat.
+ A készségkészlet rendelkeznie kell legalább egy képességgel.
+ Több, azonos típusú szaktudást is létrehozhat (például egy képelemzési képesség változatai).

## <a name="begin-with-the-end-in-mind"></a>Kezdje a végét szem előtt tartva

Az ajánlott kezdeti lépés azt határozza meg, hogy mely adatok legyenek kinyerve a nyers adatokból, és hogyan szeretné használni ezeket az adatok egy keresési megoldásban. A teljes dúsítási folyamat illusztrációjának létrehozása segíthet a szükséges lépések azonosításában.

Tegyük fel, hogy több pénzügyi elemzői Megjegyzés feldolgozását érdekli. Minden fájlhoz ki kell bontania a vállalatok nevét és a megjegyzések általános hangulatát. Érdemes lehet olyan egyéni dúsítást is írni, amely a Bing Entity Search szolgáltatást használja a vállalattal kapcsolatos további információk megkereséséhez, például arról, hogy a vállalat milyen üzleti tevékenységet folytat. Lényegében a következőhöz hasonló adatokat szeretne kinyerni az egyes dokumentumok indexeléséhez:

| record-text | vállalatok | Hangulatelemzés | a cég leírása |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation egy amerikai multinacionális technológiai cég...", "a LinkedIn egy üzleti és foglalkoztatás-orientált közösségi hálózat..."]

Az alábbi ábrán egy feltételezett alkoholtartalom-növelési folyamat látható:

![Egy feltételezett alkoholtartalom-növelési folyamat](media/cognitive-search-defining-skillset/sample-skillset.png "Egy feltételezett alkoholtartalom-növelési folyamat")


Ha jó ötlete van arról, hogy mit szeretne a folyamaton belül, megadhatja a lépéseket készségkészlet. A készségkészlet az indexelő definíciójának Azure Search való feltöltésekor fejezi ki. Az indexelő feltöltésével kapcsolatos további tudnivalókért tekintse meg az [Indexelő dokumentációját](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


A diagramon a *repedések* megjelenő lépése automatikusan megtörténik. Lényegében Azure Search tudja, hogyan nyitható meg a jól ismert fájlok, és létrehoz egy olyan *tartalmi* mezőt, amely az egyes dokumentumokból kinyert szöveget tartalmazza. A fehér dobozok beépített gazdagítók, a pontozott "Bing Entity Search" mező pedig egy Ön által létrehozott egyéni dúsítást jelöl. Ahogy az ábrán látható, a készségkészlet három ismerettel rendelkezik.

## <a name="skillset-definition-in-rest"></a>Készségkészlet-definíció a REST-ben

A készségkészlet a szaktudás tömbje van meghatározva. Az egyes képességek a bemenetek forrását és a létrehozott kimenetek nevét határozzák meg. A [Készségkészlet létrehozása REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)segítségével megadhatja az előző diagramnak megfelelő készségkészlet: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
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
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Készségkészlet létrehozásakor megadhat egy leírást, amely lehetővé teszi a készségkészlet öndokumentálása. A leírás nem kötelező, de hasznos, ha nyomon szeretné követni, hogy mi a készségkészlet. Mivel a készségkészlet egy JSON-dokumentum, amely nem engedélyezi a megjegyzéseket, ezt a `description` elemet kell használnia.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A készségkészlet következő része a szaktudás tömbje. Minden egyes képességet megtalálhat a gazdagodás primitívebb részeként. Az egyes képességek egy kis feladatot hajtanak végre ebben a dúsítási folyamatban. Mindegyiknek van egy bemenete (vagy a bemenetek halmaza), és néhány kimenetet ad vissza. A következő néhány szakaszban a beépített és az egyéni képességek megadására, valamint a bemeneti és kimeneti referenciák használatával történő láncolására koncentrálunk. A bemeneti adatok származhatnak a forrásadatokből vagy más képességből is. A kimenetek leképezhetők egy keresési index egy mezőjére, vagy az alárendelt képességek bemenetként használhatók.

## <a name="add-built-in-skills"></a>Beépített szaktudás hozzáadása

Nézzük meg az első szakértelmet, amely a beépített [entitás-felismerési képesség](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
    }
```

* Minden beépített képesség a, `odata.type` `input`a és `output` a tulajdonságokkal rendelkezik. A szaktudás-specifikus tulajdonságok további információkat biztosítanak az adott szakértelmet illetően. Az entitások felismerése `categories` esetében az egyik entitás egy rögzített típusú entitás, amelyet az előre betanított modell képes felismerni.

* Minden egyes szaktudásnak ```"context"```rendelkeznie kell egy. A környezet a műveletek elvégzésének szintjét jelöli. A fenti szaktudásban a kontextus a teljes dokumentum, ami azt jelenti, hogy az entitás-felismerési képességet dokumentum szerint egyszer kell meghívni. A kimenetek ezen a szinten is előállíthatók. Pontosabban ```"/document"```a atagjaként```"organizations"``` jön létre. Az alárendelt szakismeretekben ezt az újonnan létrehozott információt ```"/document/organizations"```tekintheti meg.  Ha a ```"context"``` mező nincs explicit módon beállítva, az alapértelmezett környezet a dokumentum.

* A skill egy "text" nevű bemenettel rendelkezik, amelyhez forrás bemenet van beállítva ```"/document/content"```. A skill (Entity Recognition) minden dokumentum *tartalom* mezőjében működik, amely az Azure Blob indexelő által létrehozott szabványos mező. 

* A szakértelem egy kimenettel ```"organizations"```rendelkezik. A kimenetek csak a feldolgozás során léteznek. Ha ezt a kimenetet egy alsóbb rétegbeli képesség bemenetéhez szeretné felvenni ```"/document/organizations"```, hivatkozzon a kimenetre a következőre:.

* Egy adott dokumentum esetében az értéke ```"/document/organizations"``` a szövegből kinyert szervezetek tömbje. Példa:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Bizonyos helyzetekben a tömb egyes elemeinek külön való hivatkozását kell meghívni. Tegyük fel például, hogy az egyes elemeket ```"/document/organizations"``` különállóan szeretné átadni egy másik képességre (például az egyéni Bing Entity Search-gazdagabbá). A tömb egyes elemeire úgy tekintheti meg, hogy egy csillagot ad hozzá az elérési úthoz:```"/document/organizations/*"``` 

Az érzelmek kinyerésének második szaktudása ugyanaz a minta, mint az első gazdagabbá. Bemenetként ```"/document/content"``` fog megjelenni, és az egyes tartalom-példányok esetében a hangulati pontszámot adja vissza. Mivel a ```"context"``` mező explicit módon nem lett beállítva, a kimenet (mySentiment) már ```"/document"```gyermeke.

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

## <a name="add-a-custom-skill"></a>Egyéni képesség hozzáadása

Hívja fel az egyéni Bing Entity Search gazdagabbáer struktúráját:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
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
```

Ez a definíció egy [Egyéni képesség](cognitive-search-custom-skill-web-api.md) , amely webes API-t hív meg a dúsítási folyamat részeként. Az entitások felismerése által azonosított minden szervezet számára ez a képesség meghívja a webes API-t, hogy megkeresse a szervezet leírását. A webes API meghívásának, valamint a kapott információk folyamatának előkészítését a dúsítási motor belsőleg kezeli. Azonban az egyéni API meghívásához szükséges inicializálást meg kell adni a JSON-ban (például URI, httpHeaders és a várt bemenetek). Az egyéni webes API-k bővítési folyamathoz való létrehozásával kapcsolatos útmutatásért lásd: [Egyéni felület definiálása](cognitive-search-custom-skill-interface.md).

Figyelje meg, hogy a "Context" mező csillaggal van beállítva, ami azt jelenti, hogy a dúsítási lépést ```"/document/organizations/*"``` ```"/document/organizations"``` *minden egyes* szervezethez meg kell hívni. 

A kimenet, ebben az esetben a vállalat leírása minden azonosított szervezethez létrejön. Ha egy alsóbb rétegbeli lépésben (például a Key kifejezés kibontásakor) hivatkozik a leírásra, az elérési utat ```"/document/organizations/*/description"``` kell használnia. 

## <a name="add-structure"></a>Struktúra hozzáadása

A készségkészlet strukturált adatokat hoz létre strukturálatlan adatokból. Vegye figyelembe a következő példát:

*"A negyedik negyedévében a Microsoft naplózott $1 100 000 000 a LinkedIn bevételeiből származik, az előző évben vásárolt közösségi hálózati vállalatnál. Az akvizíció lehetővé teszi, hogy a Microsoft a LinkedIn képességeit a CRM és az Office képességeivel kombinálja. Az eddigi előrehaladást a részvényesek is izgatottak. "*

A valószínű eredmény az alábbi ábrához hasonló generált struktúra lehet:

![Minta kimeneti szerkezete](media/cognitive-search-defining-skillset/enriched-doc.png "Minta kimeneti szerkezete")

Eddig ez a struktúra csak belső, csak a memóriában, és csak Azure Search indexekben használható. A Tudásbázis hozzáadása lehetővé teszi, hogy a keresésen kívüli használatra is mentse az alakzatokat.

## <a name="add-a-knowledge-store"></a>Knowledge Store hozzáadása

A [Knowledge Store](knowledge-store-concept-intro.md) a Azure Search előzetes verziójának funkciója a dúsított dokumentumok mentéséhez. A létrehozott, Azure Storage-fiókkal támogatott adattár az a tárház, amelyben a dúsított adatterületek szerepelnek. 

A rendszer egy Knowledge Store-definíciót ad hozzá egy készségkészlet. A teljes folyamatról a [Ismerkedés a Knowledge Store](knowledge-store-howto.md)szolgáltatással című témakörben olvashat bővebben.

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Dönthet úgy, hogy a dúsított dokumentumokat táblázatként vagy a blob Storage-ban található JSON-dokumentumként menti. A készségkészlet bármely szakismeretének kimenete a leképezés bemenetének forrásaként is megadható. Ha egy adott alakzatba kívánja feltervezni az adott alakzatot, a frissített [formáló képesség](cognitive-search-skill-shaper.md) mostantól a használni kívánt összetett típusok modellezésére is képes. 

<a name="next-step"></a>

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a dúsítási folyamatot és a szakértelmével, folytassa a [jegyzetek készségkészlet való hivatkozását](cognitive-search-concept-annotations-syntax.md) , illetve a [kimeneteknek az index mezőire való leképezését](cognitive-search-output-field-mapping.md). 
