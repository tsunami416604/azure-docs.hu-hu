---
title: Megtekintése és törlése az adatok Azure AI gyűjteményből |} Microsoft Docs
description: Exportálni, és a terméken belüli felhasználói adatok törlése az illesztő vagy AI gyűjtemény Catalog API Azure AI gyűjteményből. Ez a cikk bemutatja, hogyan.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655156"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Megtekintése és törlése a terméken belüli felhasználói adatok Azure AI gyűjteményből

Megtekintheti és a terméken belüli felhasználói adatok törlése az illesztő vagy AI gyűjtemény Catalog API Azure AI gyűjteményből. Ez a cikk azt ismerteti, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Megtekintheti az adatokat a felhasználói felületen Eszközintelligencia-katalógus

Az Azure Eszközintelligencia-katalógus webhelyen felhasználói felületén keresztül közzétett cikkek tekintheti meg. Felhasználók megtekinthetik a nyilvános és a nem felsorolt megoldások, projektek, kísérletek és egyéb közzétett elemeket:

1.  Jelentkezzen be a [Azure AI gyűjtemény](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profilszerkesztési lap betöltése a profilkép.
3.  A profilszerkesztési lap a gyűjteményébe, beleértve a listában nem szereplő bejegyzések közzétett összes elemek megjelenítése.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Az Eszközintelligencia gyűjtemény katalógus API használatával megtekintheti az adatokat

Programozott módon megtekintheti az Eszközintelligencia gyűjteménye katalógus API-n keresztül, elérhető a gyűjtött adatok https://catalog.cortanaanalytics.com/entities. Adatok megtekintéséhez van szüksége a szerző azonosítóját. A katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez olyan hozzáférési jogkivonatot kell.

Katalógus válaszokat a rendszer JSON formátumban adja vissza.

### <a name="get-an-author-id"></a>Az Szerző azonosítót
A Szerző azonosítója az Azure Eszközintelligencia-katalógus való közzétételkor használatos e-mail cím alapul. Nem módosítja:

1.  Jelentkezzen be [Azure AI gyűjtemény](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profilszerkesztési lap betöltése a profilkép.
3.  Címsor URL-CÍMÉT jeleníti meg a alfanumerikus azonosító következő `authorId=`. Például ha az URL-címe: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Szerző azonosítója: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>A hozzáférési jogkivonat beszerzése

Szüksége van egy jogkivonatot a katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez. Egy hozzáférési jogkivonat nélküli felhasználók továbbra is megtekinthetik nyilvános entitásokat és más felhasználó adatait.

Ahhoz, hogy olyan hozzáférési jogkivonatot, meg kell megvizsgálniuk a `DataLabAccessToken` a böngésző hajt végre a katalógus API bejelentkezett HTTP-kérések fejléc:

1.  Jelentkezzen be a [Azure AI gyűjtemény](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profilszerkesztési lap betöltése a profilkép.
3.  A böngésző fejlesztői eszközök ablaktábla megnyitása F12 billentyű megnyomásával, válassza ki a hálózati lapot, és frissítse az oldalt. 
4. A karakterlánc-kérések szűrése *katalógus* a szűrő szövegmezőbe írja be.
5.  A kérelem URL-címet `https://catalog.cortanaanalytics.com/entities`, GET kérés található, és válassza ki a *fejlécek* fülre. Görgessen le a *Request típusú fejlécekkel együtt* szakasz.
6.  A fejléc alatt `DataLabAccessToken` alfanumerikus lexikális eleme. Az adatok biztonsága érdekében, ne ossza meg ezt a tokent.

### <a name="view-user-information"></a>Felhasználói adatok megtekintése
Az előző lépésben kapott Szerző Azonosítóját használja, tekintse meg a felhasználói profil tartományvezérlőkkel történő lecserélésével `[AuthorId]` az alábbi URL-cím:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Például az URL-cím kérésére:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Visszaadja a választ, mint:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Nézet nyilvános entitások

A katalógus API-t az Azure Eszközintelligencia-katalógus, amely közvetlenül is megtekintheti a közzétett entitásokat kapcsolatos információkat tárolja a [AI gyűjtemény webhely](https://gallery.azure.ai/). 

A közzétett entitások megtekintéséhez keresse fel a következő URL-címet, cseréje `[AuthorId]` beolvasott Szerző azonosítójú [az Szerző azonosítót](#get-an-author-ID) felett.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Példa:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Fel nem sorolt és nyilvános entitások megtekintése

Ez a lekérdezés csak nyilvános entitások jeleníti meg. Megtekintheti az összes az entitások, beleértve a listában nem szereplő is, adja meg a hozzáférési tokent az előző szakaszban beszerzett.

1.  Hasonló eszköz használatával [Postman](https://www.getpostman.com), hozzon létre egy HTTP GET kérést a katalógus URL-CÍMÉT, a [be a hozzáférési token](#get-your-access-token).
2.  Hozzon létre egy HTTP-kérés fejlécének nevű `DataLabAccessToken`, az érték a hozzáférési jogkivonat.
3.  A HTTP-kérelem küldése.

> [!TIP]
> Fel nem sorolt entitások nem jelennek meg a válaszok a katalógus API, ha a felhasználó esetleg érvénytelen, vagy a hozzáférési token lejárt. Jelentkezzen ki az Azure Eszközintelligencia-katalógus, és ismételje meg a a [be a hozzáférési token](#get-your-access-token) a token megújításához. 
