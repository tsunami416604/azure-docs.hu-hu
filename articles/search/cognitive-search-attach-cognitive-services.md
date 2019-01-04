---
title: Cognitive Services-erőforrás társítása az Azure Search egy indexmezők
description: Utasítások a kognitív képességek alkalmazási lehetőségét az Azure Search egy teljes körű Cognitive Services előfizetési csatolása
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000155"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Cognitive Services-erőforrás társítása az Azure Search egy indexmezők 

Cognitive search ad eredményül, és bővíti, hogy az Azure Search a kereshető adatok. Kibontási-felderítési bővítést lépéseket nevezzük *kognitív képességeket*. A tartalom indexelés során nevű képességek halmaza vannak meghatározva egy *indexmezők*. A képességek alkalmazási lehetőségét használható [képességek az előre meghatározott](cognitive-search-predefined-skills.md) vagy egyéni képesség. További információkért lásd: [példa: hozzon létre egy egyéni ismeretek](cognitive-search-create-custom-skill-example.md).

Ebből a cikkből elsajátíthatja, hogyan társítsa a [Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) erőforrás a kognitív képességek alkalmazási lehetőségét.

A Cognitive Services-erőforrás kiválasztott fog power a beépített kognitív képességeket. Ez az erőforrás lesz számlázás szempontjából is. Bármely, a beépített kognitív képességek használatával végrehajtott információbeolvasás a Cognitive Services erőforráson, választhat, számlázunk ki. Fog nekik számlázni megegyező módon, ha végrehajtotta ugyanazt a feladatot a Cognitive Services-erőforrás. Lásd: [a Cognitive Services díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> 2018. December 21. Cognitive Services-erőforrás is társíthat egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi számunkra, hogy indexmezők végrehajtási számítunk fel díjat. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> A beépített képességek végrehajtás díja a [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). Kép kinyerési díjszabás díja az előzetes verzió díjszabása, és a leírt a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Korlátok, ha nincs a Cognitive Services-erőforrás van kiválasztva
Ha nem a Cognitive Services-előfizetés társítása a képességek alkalmazási lehetőségét 2019. február 1-től kezdődően lesz feldúsítani csak egy kisméretű dokumentumok száma (naponta 20 dokumentumok) ingyenes. 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Rendelje hozzá egy új tudásával Cognitive Services-erőforrás

1. Részeként a [adatimportálás](search-import-data-portal.md) Ugrás a varázsló használatával az adatforrásokhoz való csatlakozás után a **hozzáadása a cognitive search** választható lépés. Ez a varázsló második lépése.

1. Bontsa ki a **csatolása a Cognitive Services** szakaszban. Ez a lépés bemutatja, bármely Cognitive Services-erőforrások, mint az Azure Search szolgáltatás az azonos régióban található.

   ![Cognitive Services csatolása szakasz kibontva](./media/cognitive-search-attach-cognitive-services/attach1.png "Cognitive Services csatolása kibontva")

1. Cognitive Services meglévő erőforrást akkor válasszon, vagy válasszon **új Cognitive Services-erőforrás létrehozása**. Ha **(korlátozott végrehajtott Információbeolvasás) ingyenes**, bővítését, csak egy kisméretű dokumentumok száma (naponta 20 dokumentumok) ingyenes lesz. Ha **új Cognitive Services-erőforrás létrehozása**, egy új lapon nyílik meg, az erőforrás hozható létre. 

1. Ha létrehozott egy új erőforrást, válassza ki a **frissítése** a Cognitive Services-erőforrások listájának frissítése, és válassza ki az erőforrást. 

   ![Cognitive Services-erőforrás kiválasztott](./media/cognitive-search-attach-cognitive-services/attach2.png "kiválasztott Cognitive Service erőforrás")

1. Bontsa ki a **hozzáadása végrehajtott Információbeolvasás** válassza ki az adott kognitív képességekkel, amelyek az adatok futtatni kívánt szakaszt, és a többi folyamat folytatásához.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Rendelje hozzá egy meglévő tudásával Cognitive Services-erőforrás

1. Az a **szolgáltatás áttekintése** lapon válassza ki a **szakértelmével** fülre.

   ![Szakértelmével lapon](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "szakértelmével lap")

1. Válassza ki a módosítani kívánt képességek alkalmazási lehetőségét. Ekkor megnyílik egy panel, ahol szerkesztheti a képességek alkalmazási lehetőségét.

1. Cognitive Services meglévő erőforrást akkor válasszon, vagy válasszon **új Cognitive Services-erőforrás létrehozása**. Ha **(korlátozott végrehajtott Információbeolvasás) ingyenes**, bővítését, csak egy kisméretű dokumentumok száma (naponta 20 dokumentumok) ingyenes lesz. Ha **új Cognitive Services-erőforrás létrehozása**, egy új lapon nyílik meg, az erőforrás hozható létre.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Ha létrehozott egy új erőforrást, válassza ki a **frissítése** a Cognitive Services-erőforrások listájának frissítése, és válassza ki az erőforrást.

1. Válassza ki **OK** , hagyja jóvá a módosításokat.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Programozott módon társíthatja a Cognitive Services-erőforrás

Definiálásakor készségeitől programozott módon, adjon hozzá egy `cognitiveServices` szakaszban. A szakaszban a kulcsot a Cognitive Services-erőforrás készségeitől társítani kívánt tartalmazza. Is `@odata.type`, és állítsa be `#Microsoft.Azure.Search.CognitiveServicesByKey`. Az alábbi példa bemutatja ennek a mintának.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Példa: Becslés dokumentumfeltörést és Adatbővítés
IP-címek fenntartási költségei dokumentumtípus feldúsítani becslése érdekében érdemes. Az alábbi gyakorlat csak egy példa, de Ön számára hasznos lehet.

Tegyük fel, hogy rendelkezik-e 1000 PDF-fájlok. Becsülje meg, hogy átlagosan ezeket a dokumentumokat mindegyike 6 oldalból áll. Minden oldalnak van 1 kép. Átlagosan körülbelül 3000 karakterek egy-egy lapon találhatók. 

Most tegyük fel, hogy szeretné-e a Adatbővítés folyamat részeként a következő lépésekkel:
1. Dokumentumfeltörést részeként bontsa ki a tartalom és a képek a dokumentumot.
1. Adatbővítés részeként optikai karakterfelismerés (OCR) használja minden kinyert oldalak, kombinálhatja a szöveg minden lap, és bontsa ki a szervezet a egyesített szövegben az összes rendszerkép mindegyike.

Nézzük becslése lenne mennyibe, hogy ezek 1000 dokumentum, lépésről lépésre:

1. A dokumentumfeltörést, 6000 lemezképek összesített száma lenne csomagolja ki. Feltéve, hogy 1 dolláros minden ki kell olvasni, 1000 képeket szeretne követelnek egyéb fontos $6,00.

2. Az egyes 6000 rendszerképeket lenne bontsa ki a szöveget. Angol optikai Karakterfelismerés cognitive szakértelem a legjobb algoritmust használja (DescribeText). Ha egy 2,50 $ / 1000 lemezképek elemezni költségét, ehhez a lépéshez $15,00 lenne fizet.

3. Az entitások kinyeréséhez 3 szöveg rekordok száma oldalanként összesen kell. (Minden rekordot a 1000 karakter). Három szöveg rekordok száma oldalanként * 6000 oldalak = 18,000 szöveg rögzíti. 2,00 $ / 1000 szöveg rekordok, feltéve, hogy ezt a lépést akkor költség $36.00.

A teljes kép, fizetjük, hogy az itt ismertetett készségeitől ilyen jellegű 1000 PDF-dokumentumok 57.00 $. Ebben a gyakorlatban tudjuk a legköltségesebb tranzakciónkénti árral veszi alapul. Azt is lehetett volna alacsonyabb integráljuk díjszabása miatt. Lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>További lépések
+ [Az Azure Search-díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/search/)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
