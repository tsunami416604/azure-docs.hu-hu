---
title: Adatok kezelése az Azure AI-katalógusban
titleSuffix: Azure Machine Learning Studio
description: Exportálhatja, és a terméken belüli felhasználói adatok törlése az Azure AI-katalógusban a felület vagy a AI katalógus katalógus API használatával. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 44ff2a5b723c086604acf39e9f975deb53759ae1
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648109"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Megtekintheti és terméken belüli felhasználói adatok törlése az Azure AI-katalógusban

Megtekintheti, és a terméken belüli felhasználói adatok törlése az Azure AI-katalógusban a felület vagy a AI katalógus katalógus API használatával. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Adatok megtekintése az AI-katalógusban a felhasználói felületen keresztül

Az Azure AI-katalógusban webhelyen felhasználói felületén keresztül közzétett cikkek megtekintéséhez. Felhasználók megtekinthetik a nyilvános és a fel nem sorolt megoldások, a projektek, a kísérletek és a más közzétett elemeket:

1.  Jelentkezzen be a [Azure AI katalógus](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  A profil lapra a katalógusban, beleértve a listán nem szereplő bejegyzések közzétett összes elemet jeleníti meg.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Az AI katalógus katalógus API használatával az adatok megtekintése

Programozott módon megtekintheti a AI-katalógus katalógus API-címen elérhető gyűjtött https://catalog.cortanaanalytics.com/entities. Adatok megtekintéséhez van szüksége a szerző azonosítóját. A katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez, szüksége van egy hozzáférési jogkivonatot.

Katalógus válaszokat a rendszer JSON formátumban adja vissza.

### <a name="get-an-author-id"></a>Szerző azonosító beszerzése
A szerző azonosító az Azure AI-katalógusban való közzétételkor használt e-mail-cím alapján. Nem módosítja:

1.  Jelentkezzen be a [Azure AI katalógus](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  Az URL-címet a címsorba jeleníti meg a alfanumerikus azonosító következő `authorId=`. Például ha az URL-címe: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Szerző azonosítója: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>A hozzáférési jogkivonat beszerzése

Szüksége lesz egy hozzáférési jogkivonatot a katalógus API-n keresztül fel nem sorolt entitások megtekintéséhez. Egy hozzáférési jogkivonat nélküli felhasználók továbbra is megtekinthetik állami szervek és egyéb felhasználói adatok.

Vizsgálhatja meg a szükséges hozzáférési jogkivonatot kapjon a `DataLabAccessToken` bejelentkeztek a katalógus API megkönnyíti a böngésző HTTP-kérelem fejléce:

1.  Jelentkezzen be a [Azure AI katalógus](https://gallery.azure.ai/).
2.  Kattintson a jobb felső sarokban, majd a fiók nevét a profillapján betölteni a profilképét.
3.  Nyissa meg a böngésző fejlesztői eszközök ablak F12 billentyű lenyomásával, válassza ki a hálózat lapot, és frissítse az oldalt. 
4. A karakterlánc a kérelmek szűrése *katalógus* a szűrő szövegmezőbe írja be.
5.  Az URL-címre intézett kérésekben `https://catalog.cortanaanalytics.com/entities`, keressen egy GET kéréssel, majd válassza ki a *fejlécek* fülre. Görgessen le a *Request típusú fejlécekkel együtt* szakaszban.
6.  A fejléc alatt `DataLabAccessToken` az alfanumerikus biztonsági jogkivonat van. Az adatok biztonsága érdekében, ne ossza meg ezt a tokent.

### <a name="view-user-information"></a>Felhasználói adatok megtekintése
A szerző azonosító, az előző lépésben kapott használatával információk megtekintése a felhasználói profil lecserélésével `[AuthorId]` az alábbi URL-cím:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Ha például a URL kérelmet:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Például adja vissza a választ:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Nyilvános entitások megtekintése

A katalógus API-t az Azure AI-katalógus, amely közvetlenül is megtekintheti a közzétett entitásokkal kapcsolatos információkat tárolja a [AI-katalógusban webhely](https://gallery.azure.ai/). 

A közzétett entitások megtekintéséhez látogasson el a következő URL-címet, és cserélje le `[AuthorId]` beszerzett Szerző azonosítójú [egy szerző Azonosítójának lekéréséhez](#get-an-author-id) felett.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Példa:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Nyilvános és a fel nem sorolt entitások megtekintéséhez

Ez a lekérdezés csak nyilvános entitásokat jeleníti meg. Minden az entitásokkal, többek között a listán nem szereplő eszközök megtekintéséhez adja meg a hozzáférési jogkivonat érkezett az előző szakaszban.

1.  Hasonló eszköz használatával [Postman](https://www.getpostman.com), hozzon létre egy HTTP GET kérést, az alkalmazáskatalógus URL-címre leírtak [a hozzáférési jogkivonat lekérése](#get-your-access-token).
2.  Hozzon létre egy HTTP-kérelem fejléce nevű `DataLabAccessToken`, értékre van állítva, a hozzáférési jogkivonatot.
3.  A HTTP-kérelem elküldéséhez.

> [!TIP]
> Listán nem szereplő entitások nem jelennek meg a válaszokat a katalógus API-ból, ha a felhasználó előfordulhat, hogy érvénytelen, vagy a hozzáférési jogkivonat lejárt. Jelentkezzen ki az Azure AI-katalógusban, és ismételje meg a a [a hozzáférési jogkivonat lekérése](#get-your-access-token) a jogkivonat megújításához. 
