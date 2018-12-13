---
title: Cognitive Services csatlakoztatása a indexmezők – Azure Search
description: A Cognitive Services teljes körű előfizetéssel csatolása egy kognitív képességek alkalmazási lehetőségét utasításokat
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317236"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Cognitive Services-erőforrás társítani egy indexmezők 

> [!NOTE]
> December 21, 2018-as, lesz egy Cognitive Services-erőforrás társítása egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon is megkezdjük a dokumentumfeltörést fázis részeként a lemezkép kinyerési díjszabási. A szövegek dokumentumokból való kinyerése továbbra is ingyenesen használható.
>
> A végrehajtás beépített képességek díjat számítunk fel a meglévő [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás az előzetes verziók díjszabása díjat számítunk fel, és a leírt a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


Cognitive search ad eredményül, és bővíti, hogy az Azure Search a kereshető adatok. Kibontási-felderítési bővítést lépéseket nevezzük *kognitív képességeket*. A tartalom indexelés során nevű képességek halmaza vannak meghatározva egy *indexmezők*. A képességek alkalmazási lehetőségét használható [képességek az előre meghatározott](cognitive-search-predefined-skills.md) vagy egyéni képesség (lásd: [példa: hozzon létre egy egyéni ismeretek](cognitive-search-create-custom-skill-example.md) további információt).

Ebből a cikkből elsajátíthatja, hogyan társítsa a [Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) erőforrást, a kognitív képességek alkalmazási lehetőségét.

A Cognitive Services-erőforrás, válassza ki a beépített kognitív képességeket fog power. Ez az erőforrás lesz számlázás szempontjából is. Bármely hajt végre, a beépített kognitív képességek segítségével végrehajtott információbeolvasás választja a Cognitive Services-erőforrás terhére számlázzuk. Fog nekik számlázni megegyező módon, ha végrehajtotta a Cognitive Services-erőforrás használja ugyanazt a feladatot. Lásd: [a Cognitive Services díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Korlátok, ha nincs a Cognitive Services-erőforrás van kiválasztva
2019. február 1. Amennyiben nem társít egy Cognitive Services-előfizetés és a képességek alkalmazási lehetőségét kezdődően csak lesz feldúsítani kis számú dokumentumok ingyenes (20 dokumentumok / nap). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Cognitive Services-erőforrás társít egy új tudásával

1. Részeként a *adatimportálás* észlel, miután csatlakozott az adatforráshoz fog váltani a *hozzáadása a cognitive search* választható lépés. 

1. Bontsa ki a *csatolása a Cognitive Services* szakaszban. Ez megjeleníti az azonos régióban, a keresési szolgáltatás rendelkezik, a Cognitive Services erőforrásokat. 
![Bővített Cognitive Service csatolása](./media/cognitive-search-attach-cognitive-services/attach1.png "kibontva csatolása a Cognitive Services")

1. Válassza ki a Cognitive Services meglévő erőforrást, vagy *hozzon létre egy új Cognitive Services-erőforrás*. Ha a *(korlátozott végrehajtott Információbeolvasás) ingyenes erőforrás*, csak lesz bővítését kis számú dokumentumok ingyenes (20 dokumentumok / nap). Ha rákattintott a *hozzon létre egy új Cognitive Services-erőforrás*, egy új lapon nyílik meg, amely lehetővé teszi, hogy a Cognitive Services-erőforrás létrehozásához. 

1. Ha létrehozott egy új erőforrást, kattintson a *frissítése* frissítse a Cognitive Services-erőforrások listáját, és válassza ki az erőforrást. 
![A Cognitive Services-erőforrás kiválasztott](./media/cognitive-search-attach-cognitive-services/attach2.png "kiválasztott Cognitive Services-szolgáltatás-erőforrást")

1. Miután ezt elvégezte, akkor bontsa ki a *hozzáadása végrehajtott Információbeolvasás* válassza ki az adott kognitív képességeket, futtassa az adatokon, és folytassa a folyamatot a többi szakaszt.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Cognitive Services-erőforrás társít egy meglévő tudásával

1. A szolgáltatás áttekintése lapon válassza ki a *szakértelmével* fülre. ![Szakértelmével lapon](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "szakértelmével lap")

1. *Kattintson a* a módosítani kívánt készségeitől. Ekkor megnyílik egy panel, amely lehetővé teszi, hogy egy indexmezők szerkesztése.

1. Cognitive Services meglévő erőforrást akkor válasszon vagy *hozzon létre egy új Cognitive Services-erőforrás*. Ha a *(korlátozott végrehajtott Információbeolvasás) ingyenes erőforrás*, csak lesz bővítését kis számú dokumentumok ingyenes (20 dokumentumok / nap). Ha rákattintott a *hozzon létre egy új Cognitive Services-erőforrás*, egy új lapon nyílik meg, amely lehetővé teszi, hogy a Cognitive Services-erőforrás létrehozásához. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Ha létrehozott egy új erőforrást, kattintson a *frissítése* frissítse a Cognitive Services-erőforrások listáját, és válassza ki az erőforrást.
1. Kattintson a *OK* , hagyja jóvá a módosításokat

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Cognitive Services-erőforrás programozott módon társítása

Meghatározásakor készségeitől programozott módon, adjon hozzá egy `cognitiveServices` szakaszban. A szakasz a Cognitive Services-erőforrás, a képességek alkalmazási lehetőségét társítani szeretné a kulcsot kell tartalmaznia, valamint a @odata.type, amely "#Microsoft.Azure.Search.CognitiveServicesByKey" értékre kell állítani. Ez a minta az alábbi példában látható.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Példa: Költségeinek dokumentumfeltörést és Adatbővítés
Ha meg szeretné becsülni az IP-címek fenntartási költségei feldúsítani a dokumentum egy adott típusú érdemes. Az alábbi gyakorlat csak egy példa, de Ön számára hasznos lehet.

Tegyük fel, hogy rendelkezünk 1000 PDF-fájlokat, és tudjuk megbecsülni, hogy ezeket a dokumentumokat mindegyike rendelkezik átlagosan 6 lapok minden. Tegyük fel, hogy mindegyik rendelkezik-e ehhez a gyakorlathoz laponként egy rendszerképet. Nézzük is tegyük fel, hogy átlagosan körülbelül 3000 karakterek vannak egy-egy lapon. 

Most tegyük fel, hogy szeretnénk a Adatbővítés folyamat részeként a következő lépésekkel:
1. Dokumentumfeltörést részeként bontsa ki a tartalom és a képek a dokumentumot.
1. Adatbővítés részeként OCR egyes oldalak ki kell olvasni, kombinálja az összes szövegét, és bontsa ki a kombinált szöveget az összes rendszerkép a szervezetek mindegyike.

Nézzük becsülje meg kellene mennyibe, hogy ezeket a dokumentumokat, lépésről lépésre.

Az 1000 dokumentumok:

1. A dokumentum repedés, hogy lenne kinyerése 6000 lemezképek összesített száma. Feltéve, hogy 1 dolláros minden ki kell olvasni, 1000 képeket szeretne követelnek egyéb fontos nekünk $6,00.

2. A szöveg kinyerése azt szeretné, az egyes 6000 rendszerképeket. Angol optikai Karakterfelismerés cognitive szakértelem a legjobb algoritmust használja (DescribeText). Ha egy 2,50 $ / 1000 lemezképek elemezni költségét, azt kell $15,00 ezt a lépést.

3. Az entitások kinyeréséhez hogy kellene összesen 3 szöveg rekordok száma oldalanként (minden rekordot a 1000 karakter). 3 szöveg rekordok/oldala * 6000 oldalak = 18,000 szöveg rögzíti. Feltéve, hogy 2,00 $ / 1000 szöveges rekord, ebben a lépésben lenne költség USA $36.00.

A teljes kép, hogy kell 57.00 $ leírt készségeitől az ilyen jellegű 1000 pdf-dokumentumok betöltését.  Ebben a gyakorlatban azt feltételezi a legköltségesebb tranzakciónkénti árral, akkor lehetett volna alacsonyabb díjszabás integráljuk miatt. Lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>További lépések
+ [Az Azure Search-díjszabás](https://azure.microsoft.com/pricing/details/search/)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
