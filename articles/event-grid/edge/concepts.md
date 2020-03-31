---
title: Fogalmak – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Fogalmak az Event Grid ben az IoT Edge-en.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992547"
---
# <a name="event-grid-concepts"></a>Az Event Griddel kapcsolatos fogalmak

Ez a cikk ismerteti a fő fogalmak az Azure Event Gridben.

## <a name="events"></a>Események

Az esemény a legkisebb mennyiségű információ, amely teljes mértékben leírja, hogy mi történt a rendszerben. Minden eseménynek olyan közös információi vannak, mint például: az esemény forrása, az esemény időpontja és az egyedi azonosító. Minden esemény olyan konkrét információkkal is rendelkezik, amelyek csak az adott eseménytípusra vonatkoznak. Az 1 MB-os méretű események támogatása jelenleg előzetes verzióban érhető el.

Az eseményben szereplő tulajdonságokról az [Azure Event Grid eseményséma című témakörben](event-schemas.md)található.

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, amely úgy dönt, hogy eseményeket küld az Eseményrácsra. A saját alkalmazásából is tehet közzé eseményeket.

## <a name="event-sources"></a>Eseményforrások

Az eseményforrás az, ahol az esemény történik. Minden eseményforráshoz egy vagy több eseménytípus tartozik. Például a blob által létrehozott események tekintetében az Azure Storage az eseményforrás. A saját megadott egyéni eseményei tekintetében a saját alkalmazása az eseményforrás. Az eseményforrások feladata, hogy eseményeket küldjenek az Event Grid számára.

## <a name="topics"></a>Témakörök

Az eseményrács témakör egy végpontot biztosít, ahol a forrás eseményeket küld. A közzétevő létrehozza az eseményrács témakörét, és eldönti, hogy egy eseményforrásnak szüksége van-e egy vagy több témakörre. A témakör kapcsolódó események gyűjteményéhez használatos. Bizonyos típusú eseményekre való reagáláshoz az előfizetők eldöntik, hogy mely témákra iratkozzanak fel.

Az alkalmazás tervezésekor rugalmasan döntheti el, hogy hány témakört szeretne létrehozni. Nagy megoldásokhoz hozzon létre egy egyéni témakört a kapcsolódó események minden kategóriájához. Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetén előfordulhat, hogy az összes eseményt egyetlen témakörbe szeretné küldeni. Az esemény-előfizetők szűrhetik a kívánt eseménytípusokat.

Tekintse meg [a REST API dokumentációját](api.md) a témakörök Event Gridben való kezeléséről.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés közli az Event Griddel, hogy mely eseményeket szeretnéd fogadni egy olyan témában, amelyet szeretnél. Az előfizetés létrehozásakor egy végpontot biztosít az esemény kezeléséhez. Szűrheti a végpontra küldött eseményeket. 

Tekintse meg [a REST API dokumentációját](api.md) az előfizetések kezeléséről az Event Gridben.

## <a name="event-handlers"></a>Eseménykezelők

Az Event Grid szempontjából az eseménykezelő az a hely, ahol az esemény elküldésre kerül. A kezelő további lépéseket tesz az esemény feldolgozásához. Az Event Grid több kezelőtípust támogat. Használhatja a támogatott Azure-szolgáltatás vagy a saját web hook kezelőként. A kezelő típusától függően az Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. Ha a célesemény-kezelő HTTP-webhook, az esemény újra próbálkozik, `200 – OK`amikor a kezelő a. állapotkódját adja vissza. Peremhálózati hub esetén, ha az esemény kivétel nélkül kézbesíti, sikeresnek minősül.

## <a name="security"></a>Biztonság

Az Event Grid biztonságot nyújt a témakörökre való feliratkozáshoz és a közzétételi témakörökhöz. További információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.

## <a name="event-delivery"></a>Esemény kézbesítése

Ha az Event Grid nem tudja megerősíteni, hogy az előfizető végpontja eseményhez érkezett, akkor újra kézbesíti az eseményt. További információt az [Event Grid üzenetkézbesítése és újrapróbálkozása](delivery-retry.md)című témakörben talál.

## <a name="batching"></a>Kötegelés

Egyéni témakör használata esetén az eseményeket mindig egy tömbben kell közzétenni. Alacsony átviteli-átviteli forgatókönyvek esetén a tömb csak egy értékkel rendelkezik. Nagy mennyiségű használati esetek esetén azt javasoljuk, hogy a nagyobb hatékonyság elérése érdekében közzétételenként több eseményt kell kötegelni. A kötegek legfeljebb 1 MB méretűek lehetnek. Az egyes események nem lehetnek 1 MB-nál nagyobbak (előzetes verzió).