---
title: Képességcsoport létrehozása
titleSuffix: Azure Cognitive Search
description: Definiálja az adatok kinyerését, a természetes nyelvi feldolgozást vagy a képelemzési lépéseket a strukturált adatok richalzításához és kinyeréséhez az Azure Cognitive Search ben való használatra.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754566"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Szakértelem-készlet létrehozása a ai-dúsítási folyamatban az Azure Cognitive Search szolgáltatásban 

A I dúsításkivonatok és gazdagítja az adatokat, hogy kereshető az Azure Cognitive Search. Nevezzük kitermelési és gazdagítási lépéseket *kognitív képességek,* kombinálva egy *skillset* hivatkozott indexelés során. A skillset használhat [beépített képességeket](cognitive-search-predefined-skills.md) vagy egyéni képességeket (további információkért [lásd: Példa: Egyéni szakértelem létrehozása AI-bővítési folyamatban).](cognitive-search-create-custom-skill-example.md)

Ebben a cikkben megtudhatja, hogyan hozhat létre egy dúsítási folyamat a használni kívánt szakértelemhez. Egy skillset csatlakozik egy Azure Cognitive Search [indexelő.](search-indexer-overview.md) A csővezeték-tervezés egy része, amelyet ez a cikk is magában a skillset-ben épít. 

> [!NOTE]
> A csővezeték-tervezés egy másik része egy indexelő megadása, amelyet a [következő lépés](#next-step)fed. Az indexelő definíciója tartalmaz egy hivatkozást a skillset, valamint a mező leképezések használt bemenetek a célindex kimenetek.

Legfontosabb pontok, hogy emlékezzen:

+ Indexelőnként csak egy skillset lehet.
+ A skillset nek legalább egy képességgel kell rendelkeznie.
+ Több azonos típusú készséget is létrehozhat (például egy képelemzési szakértelem változatait).

## <a name="begin-with-the-end-in-mind"></a>Kezdje a végét szem előtt tartva

Az ajánlott kezdeti lépés annak eldöntése, hogy mely adatokat kell kinyerni a nyers adatokból, és hogyan szeretné használni ezeket az adatokat egy keresési megoldásban. A teljes dúsítási folyamat szemléltetésének létrehozása segíthet a szükséges lépések azonosításában.

Tegyük fel, hogy pénzügyi elemzői megjegyzéseket szeretne feldolgozni. Minden fájlhoz ki szeretné vonni a vállalatneveket és a megjegyzések általános hangulatát. Előfordulhat, hogy olyan egyéni dúsítót is szeretne írni, amely a Bing entitáskeresési szolgáltatást használja a vállalattal kapcsolatos további információk, például a vállalat tevékenységének során folytatott további információk keresésére. Lényegében a következőhöz hasonló, indexelt információkat szeretne kinyerni az egyes dokumentumokhoz:

| rekord-szöveg | Vállalatok | Hangulat | vállalati leírások |
|--------|-----|-----|-----|
|mintarekord| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation egy amerikai multinacionális technológiai vállalat ..." , "LinkedIn egy üzleti- és foglalkoztatás-orientált szociális hálózatok ..."]

Az alábbi ábra egy hipotetikus dúsítási folyamatot mutat be:

![Egy hipotetikus dúsítási csővezeték](media/cognitive-search-defining-skillset/sample-skillset.png "Egy hipotetikus dúsítási csővezeték")


Ha már van valós elképzelése arról, hogy mit szeretne a folyamatban, kifejezheti a skillset, amely ezeket a lépéseket. Funkcionálisan a skillset kifejezve, amikor feltölti az indexelő definícióját az Azure Cognitive Search. Ha többet szeretne megtudni az indexelő feltöltéséről, olvassa el az [indexelő dokumentációját.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)


A diagramon a *dokumentum feltörése* lépés automatikusan megtörténik. Lényegében az Azure Cognitive Search tudja, hogyan *content* kell megnyitni a jól ismert fájlokat, és létrehoz egy tartalommezőt, amely tartalmazza az egyes dokumentumokból kinyert szöveget. A fehér mezők beépített dúsítók, a pontozott "Bing entitáskeresés" mező pedig a létrehozott egyéni dúsítót jelöli. Amint az azt mutatja, a skillset tartalmaz három készség.

## <a name="skillset-definition-in-rest"></a>Skillset definíció a REST-ben

A skillset a képességek tömbjeként van definiálva. Minden szakértelem határozza meg a bemenetek forrását és az előállított kimenetek nevét. A [Skillset REST létrehozása API használatával](https://docs.microsoft.com/rest/api/searchservice/create-skillset)az előző diagramnak megfelelő skillsetet határozhat meg: 

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

A skillset létrehozása közben leírást adhat meg a skillset öndokumentálásához. A leírás nem kötelező, de hasznos nyomon követni, hogy mit csinál egy skillset. Mivel a skillset egy JSON-dokumentum, amely nem `description` engedélyezi a megjegyzéseket, ehhez egy elemet kell használnia.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A következő darab a skillset egy sor készségek. Minden képességre úgy gondolhatsz, mint a gazdagodás primitívére. Minden szakértelem egy kis feladatot hajt végre ebben a dúsítási folyamatban. Mindegyik egy bemenetet (vagy bemenetek egy készletét) vesz igénybe, és néhány kimenetet ad vissza. A következő néhány szakasz a beépített és egyéni képességek megadására összpontosít, a képességek összeláncolása bemeneti és kimeneti referenciákon keresztül. A bemenetek származhatnak forrásadatokból vagy más szakértelemből. A kimenetek leképezhetők egy mezőre egy keresési indexben, vagy egy alsóbb rétegbeli szakértelem bemeneteként használhatók.

## <a name="add-built-in-skills"></a>Beépített képességek hozzáadása

Nézzük meg az első készség, amely a beépített [entitás felismerés készség:](cognitive-search-skill-entity-recognition.md)

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

* Minden beépített szakértelemnek `odata.type` `input`van `output` , és tulajdonságai vannak. A szakértelem-specifikus tulajdonságok további információkat nyújtanak az adott szakértelemre vonatkozóan. Az entitás `categories` kiismerése esetén az entitástípusok rögzített készletei közül az egyik entitás, amelyet az előre betanított modell felismer.

* Minden készségnek ```"context"```rendelkeznie kell egy . A környezet azt a szintet jelöli, amelyen a műveletek zajlanak. A fenti szakértelemben a környezet a teljes dokumentum, ami azt jelenti, hogy az entitás felismerési szakértelem dokumentumonként egyszer van megnevezve. A teljesítményeket ezen a szinten is előállítják. Pontosabban a ```"organizations"``` . ```"/document"``` Az alsóbb rétegbeli készségekben hivatkozhat ```"/document/organizations"```erre az újonnan létrehozott információra .  Ha ```"context"``` a mező nincs explicit módon beállítva, az alapértelmezett környezet a dokumentum.

* A szakértelem egy "szöveg" nevű bemenettel rendelkezik, és a forrásbemeneti készlete ```"/document/content"```a. A szakértelem (entitás felismerés) minden dokumentum *tartalommezőjén* működik, amely az Azure blob indexelő által létrehozott szabványos mező. 

* A szakértelem nek ```"organizations"```van egy kimenete, amelyet . A kimenetek csak a feldolgozás során léteznek. Ha ezt a kimenetet egy alsóbb rétegbeli ```"/document/organizations"```szakértelem bemenetéhez szeretné láncolni, hivatkozzon a kimenetre a .

* Egy adott dokumentum esetében ```"/document/organizations"``` az érték a szövegből kivont szervezetek tömbje. Példa:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Egyes helyzetekben a tömb egyes elemeire külön-külön lehet hivatkozni. Tegyük fel például, hogy ```"/document/organizations"``` minden egyes elemét külön-külön szeretné átadni egy másik szakértelemnek (például az egyéni Bing entitáskeresési dúsítónak). A tömb egyes elemeire csillag hozzáadásával hivatkozhat az elérési útra:```"/document/organizations/*"``` 

A második szakértelem a hangulat kinyerése követi ugyanazt a mintát, mint az első dúsító. Bemenetként ```"/document/content"``` kell, és minden egyes tartalompéldányhoz egy véleménypontszámot ad vissza. Mivel nem állította ```"context"``` be a mezőt kifejezetten, a kimenet ```"/document"```(mySentiment) mostantól a .

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

Az egyéni Bing-entitáskeresési tartalomra való visszaemlékezés:

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

Ez a definíció egy [egyéni szakértelem,](cognitive-search-custom-skill-web-api.md) amely meghívja a webes API-t a dúsítási folyamat részeként. Az entitásfelismerés által azonosított minden egyes szervezet esetében ez a szakértelem webes API-t hív meg a szervezet leírásának megkereséséhez. A web API-hívásának vezénylését és a kapott információk áramlásának módját a dúsító motor belsőleg kezeli. Az egyéni API-k hívásához szükséges inicializálást azonban a JSON-ban kell megadni (például uri, httpHeaders és a várt bemenetek). A dúsítási folyamat egyéni webes API-jának létrehozásához az [Egyéni felület definiálása](cognitive-search-custom-skill-interface.md)című témakörben talál útmutatást.

Figyelje meg, hogy a ```"/document/organizations/*"``` "context" mező csillaggal van beállítva, ami azt ```"/document/organizations"```jelenti, hogy a dúsítási lépés az *egyes szervezetekhez* a . 

Kimenet, ebben az esetben a vállalat leírása, jön létre minden egyes azonosított szervezethez. Amikor egy alsóbb rétegbeli lépésben (például a kulcskifejezések kinyerése) a leírásra hivatkozik, akkor az elérési utat ```"/document/organizations/*/description"``` kell használnia. 

## <a name="add-structure"></a>Struktúra hozzáadása

A skillset strukturált adatokat hoz létre strukturálatlan adatokból. Tekintse meg a következő példát:

*"-ban -a negyedik negyed, Mikroszkóp mocsaras $1.1 billión -ban állami jövedelem -ból LinkedIn, a társadalmi hálózat társaság ez vásárolt tavaly. Az akvizíció lehetővé teszi a Microsoft számára, hogy a LinkedIn-képességeket crm és Office képességeivel kombinálja. A részvényesek izgatottan várják az eddigi fejlődést."*

A valószínű eredmény a következő ábrához hasonló létrehozott struktúra lenne:

![Minta kimeneti szerkezete](media/cognitive-search-defining-skillset/enriched-doc.png "Minta kimeneti szerkezete")

Eddig ez a struktúra csak belső, csak memória, és csak az Azure Cognitive Search-indexek. A tudástároló hozzáadásával mentheti az alakzatos dúsításokat a keresésen kívüli használatra.

## <a name="add-a-knowledge-store"></a>Tudástár hozzáadása

[A Tudástároló](knowledge-store-concept-intro.md) az Azure Cognitive Search előzetes verziója a bővített dokumentum mentéséhez. Az Azure-tárfiókkal létrehozott tudástároló az a tárház, ahol a bővített adatok landolnak. 

A tudástároló definíciója hozzáadódik a skillsethez. A teljes folyamat forgatókönyvét a [Tudástároló létrehozása rest-ben](knowledge-store-create-rest.md)című témakörben található.

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

Választhat, hogy mentse a bővített dokumentumokat, mint a hierarchikus kapcsolatokat megőrzött táblák vagy JSON-dokumentumok blob storage. A skillset bármely készségből származó kimenet a kivetítés bemeneteként beszerezhető. Ha az adatokat egy adott alakzatba szeretné kivetíteni, a frissített [formázó-szakértelem](cognitive-search-skill-shaper.md) mostantól modellezheti a használható összetett típusokat. 

<a name="next-step"></a>

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a dúsítási folyamatot és a skillsets, folytassa a [Hogyan hivatkozhat a jegyzetek egy skillset](cognitive-search-concept-annotations-syntax.md) vagy [Hogyan lehet leképezni a kimeneteket mezők egy index](cognitive-search-output-field-mapping.md). 
