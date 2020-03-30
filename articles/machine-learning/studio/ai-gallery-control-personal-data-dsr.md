---
title: Az Azure AI-katalógus adatainak kezelése
titleSuffix: ML Studio (classic) - Azure
description: Exportálhatja és törölheti a terméken lévő felhasználói adatokat az Azure AI Gallery-ből a felület vagy az AI Katalógus API használatával. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204545"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Terméken lévő felhasználói adatok megtekintése és törlése az Azure AI-gyűjteményből

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Megtekintheti és törölheti a terméken lévő felhasználói adatokat az Azure AI Gallery-ből a felület vagy az AI-katalógus API használatával. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Az adatok megtekintése az AI-galériában a felhasználói felülettel

Megtekintheti az Azure AI Gallery webhely felhasználói felületén közzétett elemeket. A felhasználók nyilvános és nem nyilvános megoldásokat, projekteket, kísérleteket és egyéb közzétett elemeket is megtekinthetnek:

1.    Jelentkezzen be az [Azure AI-galériába.](https://gallery.azure.ai/)
2.    Kattintson a jobb felső sarokban lévő profilképre, majd a fiók nevére a profillap betöltéséhez.
3.    A profillapon a katalógusban közzétett összes elem megjelenik, beleértve a nem nyilvános bejegyzéseket is.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Az AI-katalógus API használata az adatok megtekintéséhez

Programozott módon megtekintheti az AI Gallery Katalógus API-n https://catalog.cortanaanalytics.com/entitieskeresztül gyűjtött adatokat, amely a helyen érhető el. Az adatok megtekintéséhez szüksége van a szerzői azonosítóra. A katalógus API-n keresztül a nem listázott entitások megtekintéséhez szükség van egy hozzáférési jogkivonatra.

A katalógusválaszok JSON formátumban kerülnek visszaadásra.

### <a name="get-an-author-id"></a>Szerzői azonosító beszereznie
A szerzői azonosító az Azure AI-galériában való közzétételkor használt e-mail-címalapján alapul. Ez nem változik:

1.    Jelentkezzen be az [Azure AI-galériába.](https://gallery.azure.ai/)
2.    Kattintson a jobb felső sarokban lévő profilképre, majd a fiók nevére a profillap betöltéséhez.
3.    A címsorban lévő URL-cím a következő `authorId=`alfanumerikus azonosítót jeleníti meg. Például az URL-címhez:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Szerző azonosítója:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>A hozzáférési jogkivonat beszerezése

A katalógus API-n keresztül a nem listázott entitások megtekintéséhez hozzáférési jogkivonatra van szükség. Hozzáférési jogkivonat nélkül a felhasználók továbbra is megtekinthetik a nyilvános entitásokat és más felhasználói adatokat.

A hozzáférési jogkivonat beszerzéséhez `DataLabAccessToken` meg kell vizsgálnia egy HTTP-kérelem fejlécét, amelyet a böngésző a katalógus API-ba küld, miközben bejelentkezett:

1.    Jelentkezzen be az [Azure AI-galériába.](https://gallery.azure.ai/)
2.    Kattintson a jobb felső sarokban lévő profilképre, majd a fiók nevére a profillap betöltéséhez.
3.    Nyissa meg a böngésző fejlesztői eszközei ablaktábláját az F12 billentyű lenyomásával, válassza a Hálózat lapot, és frissítse a lapot. 
4. A *karakterlánc-katalógusra* vonatkozó kérelmek szűrése a Szűrő szövegmezőbe beírással.
5.    Az URL-címre `https://catalog.cortanaanalytics.com/entities`irányuló kérelmekben keressen egy GET-kérelmet, *Request Headers* és válassza a *Fejlécek* lapot.
6.    A fejléc `DataLabAccessToken` alatt van az alfanumerikus token. Az adatok biztonságának megőrzéséhez ne ossza meg ezt a jogkivonatot.

### <a name="view-user-information"></a>Felhasználói adatok megtekintése
Az előző lépésekben megadott szerzői azonosító használatával tekintse meg a felhasználói `[AuthorId]` profilban szereplő információkat a következő URL-cím cseréjével:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Például ez az URL-kérelem:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

A következő választ adja eredményül:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Nyilvános entitások megtekintése

A katalógus API tárolja a közzétett entitások adatait az Azure AI-galériában, amelyeket közvetlenül az [AI-galéria webhelyén](https://gallery.azure.ai/)is megtekinthet. 

A közzétett entitások megtekintéséhez látogasson `[AuthorId]` el a következő URL-címre, amely a fenti [Szerzői azonosító beszerzése](#get-an-author-id) című részben található Szerzői azonosító t.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Példa:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Nem nyilvános és nyilvános entitások megtekintése

Ez a lekérdezés csak nyilvános entitásokat jelenít meg. Az összes entitás megtekintéséhez, beleértve a nem listázott is, adja meg az előző szakaszból beszerzett hozzáférési jogkivonatot.

1.    A [Postmanhez](https://www.getpostman.com)hasonló eszköz használatával hozzon létre egy HTTP GET-kérelmet a katalógus URL-címére [a Hozzáférési jogkivonat bekérése](#get-your-access-token)című részben leírtak szerint.
2.    Hozzon létre egy `DataLabAccessToken`HTTP-kérelem fejlécnevű , az érték a hozzáférési jogkivonatot.
3.    Küldje el a HTTP-kérelmet.

> [!TIP]
> Ha a nem listázott entitások nem jelennek meg a katalógus API-ból érkező válaszokban, előfordulhat, hogy a felhasználó érvénytelen vagy lejárt hozzáférési jogkivonattal rendelkezik. Jelentkezzen ki az Azure AI-galériából, majd ismételje meg a lépéseket [a hozzáférési jogkivonat beszerezése](#get-your-access-token) a jogkivonat megújításához. 
