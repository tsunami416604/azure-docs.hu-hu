---
title: Skillset létrehozása (REST api-version = 2017-11-11 – előzetes verzió) – az Azure Search |} Microsoft Docs
description: Egy skillset egy dúsító csővezeték alkotó kognitív képességek gyűjteménye.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)

**A következőkre vonatkozik:** api-version-2017-11-11-Preview

Egy skillset használható a természetes nyelvű feldolgozására és egyéb átalakítások kognitív képességek gyűjteménye. Képességek közé tartoznak a névvel rendelkező entitás kinyerési, kulcs kifejezés kinyerési, szöveges adattömbösítő logikai oldalakra, többek között.

A skillset használatához Azure Search-indexelőt vonatkozó hivatkozást, és futtassa a az indexelő adatimportálás, átalakítások és dúsító meghívása és a kimeneti mezők leképezése az index. Egy skillset magas szintű erőforrás, de csak az indexelő feldolgozási belül működési. Magas szintű erőforrásként egy skillset egyszer tervezését, és több indexelők majd hivatkoznia. 

Egy skillset Azure Search HTTP PUT vagy POST kérelem keresztül van megadva. A PUT a kérelem törzse egy JSON-séma, amely meghatározza, melyik ismeretek kerül meghívásra. Ismeretek együtt kapcsolt bemeneti / kimeneti társítások, ahol egy átalakító kimenete válik adjon meg másik keresztül.

Egy skillset rendelkeznie kell legalább egy szakértelem. Nincs elméleti korlátja ismeretek maximális számát, de a konfiguráció 3-5.  


> [!NOTE]
> Nyilvános előzetes verzióban elérhető a Cognitive Search, a képességek alkalmazási lehetőségét pedig jelenleg ingyenesen kínáljuk. Ennek a funkciónak a díjszabását a későbbiekben jelentjük be.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Kérés  
 HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. A **létrehozása Skillset** kérelem egy PUT metódust használó skillset névvel, az URL-cím lehet létrehozni. Ha a skillset nem létezik, akkor jön létre. Ha már létezik, az új definition frissül. Figyelje meg, hogy csak HELYEZHET el egy skillset egyszerre.  

 A skillset nevét az alábbi követelményeknek kell megfelelniük:

- A nagybetűs kell
- Indítsa el, és betűvel vagy számmal végződhet
- Nincs perjeleket vagy pontokból
- Legfeljebb 128 karakterből állhat 

Után a skillset nevének betűvel vagy számmal verziótól kezdődően a nevét a többi tartalmazhatnak bármely betű, szám és kötőjel mindaddig, amíg a kötőjelek nincsenek egymást követő.  

A **api-version** paraméter megadása kötelező. A csak a rendelkezésre álló verzió `2017-11-11-Preview`. Lásd: [az Azure Search API-verziók](https://go.microsoft.com/fwlink/?linkid=834796) összes verziók listáját. 


### <a name="request-headers"></a>Kérelemfejlécek  

 A következő táblázat ismerteti a szükséges és választható kérelemfejlécekben.  

|Fejléc|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Állítsa ezt a beállítást `application/json`|  
|*API-kulcs:*|Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **létrehozása Skillset** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot).|  

Meg kell összeállítani a kérelem URL-CÍMÉT a szolgáltatás nevét is. A szolgáltatás nevet is kaphat és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.  

### <a name="request-body-syntax"></a>Törzs szintaxis  

A kérelem törzsében a skillset definition, amely egy vagy több teljesen megadott képességei, valamint nevet és leírást nem kötelező paraméterek.  

A szerkezetének kialakítása a kérelem hasznos szintaxisa a következő. Egy minta kérelem valósul meg a cikk későbbi részében, majd a is [egy skillset definiálása](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Kérelem – példa
 Az alábbi példa létrehoz egy pénzügyi dokumentumok gyűjteménye bővítéséhez használt skillset.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzse egy JSON-dokumentumhoz. Az adott skillset használ aszinkron módon történik, függetlenül az anyag feldolgozása két képességek a `contents` , két különböző átalakításokat. Alternatív megoldásként megadhatja, egy átalakítással kell egy másik bemeneti kimenetét. További információkért lásd: [egy skillset definiálása](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
  ]
}
```

## <a name="response"></a>Válasz  

 A kérelem sikeres megtekintheti az állapotkód: "201 Created".  

 Alapértelmezés szerint az adott válasz törzsének a skillset definíciójának létrehozott JSON fogja tartalmazni. Azonban ha Prefer kérelemfejléc vissza = minimális, az adott válasz törzse üres lesz, és a sikeres állapotkód lesz "204 Nincs tartalom" helyett "201 Created". Ez igaz a PUT vagy POST használatától függetlenül a skillset létrehozásához.   

## <a name="see-also"></a>Lásd még

+ [Kognitív keresési áttekintése](cognitive-search-concept-intro.md)
+ [Gyors üzembe helyezés: Próbálja kognitív keresése](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Az Azure-blobokat indexelése dúsított](cognitive-search-tutorial-blob.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hogyan mezők](cognitive-search-output-field-mapping.md)
+ [Egy egyéni felületet definiálása](cognitive-search-custom-skill-interface.md)
+ [Példa: egyéni szakértelem létrehozása](cognitive-search-create-custom-skill-example.md)
+ [Előre definiált sklls](cognitive-search-predefined-skills.md)