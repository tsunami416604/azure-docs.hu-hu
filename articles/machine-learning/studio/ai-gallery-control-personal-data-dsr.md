---
title: Azure AI Gallery-adatkezelés
titleSuffix: ML Studio (classic) - Azure
description: Exportálhatja, és a terméken belüli felhasználói adatok törlése az Azure AI-katalógusban a felület vagy a AI katalógus katalógus API használatával. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 8406da7a429fb5b9429845628a00cdb6cc715540
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148692"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Megtekintheti és terméken belüli felhasználói adatok törlése az Azure AI-katalógusban

Megtekintheti, és a terméken belüli felhasználói adatok törlése az Azure AI-katalógusban a felület vagy a AI katalógus katalógus API használatával. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Adatok megtekintése az AI-katalógusban a felhasználói felületen keresztül

Az Azure AI-katalógusban webhelyen felhasználói felületén keresztül közzétett cikkek megtekintéséhez. Felhasználók megtekinthetik a nyilvános és a fel nem sorolt megoldások, a projektek, a kísérletek és a más közzétett elemeket:

1.  Jelentkezzen be a [Azure AI Galleryba](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  A profil lapra a katalógusban, beleértve a listán nem szereplő bejegyzések közzétett összes elemet jeleníti meg.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Az AI katalógus katalógus API használatával az adatok megtekintése

Programozott módon megtekintheti az AI Gallery Catalog API-n keresztül gyűjtött adatokat, amely https://catalog.cortanaanalytics.com/entitiesérhető el. Adatok megtekintéséhez van szüksége a szerző azonosítóját. A katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez, szüksége van egy hozzáférési jogkivonatot.

Katalógus válaszokat a rendszer JSON formátumban adja vissza.

### <a name="get-an-author-id"></a>Szerző azonosító beszerzése
A szerző azonosító az Azure AI-katalógusban való közzétételkor használt e-mail-cím alapján. Nem módosítja:

1.  Jelentkezzen be [Azure AI Galleryba](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  A címsorban található URL-cím a `authorId=`következő alfanumerikus azonosítót jeleníti meg. Például a következő URL-címhez: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Szerző azonosítója: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>A hozzáférési jogkivonat beszerzése

Szüksége lesz egy hozzáférési jogkivonatot a katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez. Egy hozzáférési jogkivonat nélküli felhasználók továbbra is megtekinthetik állami szervek és egyéb felhasználói adatok.

Hozzáférési jogkivonat beszerzéséhez meg kell vizsgálnia egy HTTP-kérelem `DataLabAccessToken` fejlécét, amelyet a böngésző a katalógus API-ba való bejelentkezéskor tesz elérhetővé:

1.  Jelentkezzen be a [Azure AI Galleryba](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  Nyissa meg a böngésző fejlesztői eszközök ablak F12 billentyű lenyomásával, válassza ki a hálózat lapot, és frissítse az oldalt. 
4. A karakterlánc- *katalógusban* lévő kérelmek szűréséhez írja be a szűrő szövegmezőbe.
5.  Az URL-címre `https://catalog.cortanaanalytics.com/entities`keresse meg a GET kérelmet, és válassza a *fejlécek* fület. görgessen le a *kérések fejlécek* szakaszhoz.
6.  A fejléc alatt `DataLabAccessToken` az alfanumerikus token. Az adatok biztonsága érdekében, ne ossza meg ezt a tokent.

### <a name="view-user-information"></a>Felhasználói adatok megtekintése
Az előző lépésekben kapott szerzői azonosító használatával megtekintheti a felhasználói profilban található információkat, ha lecseréli `[AuthorId]` a következő URL-címre:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Ha például a URL kérelmet:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Például adja vissza a választ:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Nyilvános entitások megtekintése

A katalógus API a közzétett entitásokkal kapcsolatos adatokat tárolja a Azure AI Gallery, amelyeket közvetlenül a [mesterséges intelligencia](https://gallery.azure.ai/)-katalógus webhelyén is megtekintheti. 

A közzétett entitások megtekintéséhez látogasson el a következő URL-címre, és cserélje le `[AuthorId]`t a szerző azonosítójának [lekérése a fenti Szerző azonosítója](#get-an-author-id) mezőben.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Például:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Nyilvános és a fel nem sorolt entitások megtekintéséhez

Ez a lekérdezés csak nyilvános entitásokat jeleníti meg. Minden az entitásokkal, többek között a listán nem szereplő eszközök megtekintéséhez adja meg a hozzáférési jogkivonat érkezett az előző szakaszban.

1.  Hozzon létre egy HTTP GET-kérést a katalógus [URL-](https://www.getpostman.com)címére a következő témakörben leírtak szerint: [hozzáférési token beszerzése](#get-your-access-token).
2.  Hozzon létre egy `DataLabAccessToken`nevű HTTP-kérési fejlécet a hozzáférési jogkivonathoz beállított értékkel.
3.  A HTTP-kérelem elküldéséhez.

> [!TIP]
> Listán nem szereplő entitások nem jelennek meg a válaszokat a katalógus API-ból, ha a felhasználó előfordulhat, hogy érvénytelen, vagy a hozzáférési jogkivonat lejárt. Jelentkezzen ki a Azure AI Galleryból, majd ismételje meg a [hozzáférési token beszerzése](#get-your-access-token) című témakör lépéseit a jogkivonat megújításához. 
