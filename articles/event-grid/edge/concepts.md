---
title: Fogalmak – Azure Event Grid IoT Edge | Microsoft Docs
description: A IoT Edge Event Grid kapcsolatos fogalmak.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992547"
---
# <a name="event-grid-concepts"></a>Az Event Griddel kapcsolatos fogalmak

Ez a cikk a Azure Event Grid főbb fogalmait ismerteti.

## <a name="events"></a>Események

Az esemény az a legkisebb mennyiségű információ, amely teljes mértékben leírja a rendszeren történteket. Minden eseményhez olyan általános információk tartoznak, mint például: az esemény forrása, az esemény időpontja és egyedi azonosító. Minden eseményhez konkrét információ is tartozik, amely csak az adott típusú eseményre vonatkozik. A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el.

Az események által tartalmazott tulajdonságokért lásd: [Azure Event Grid Event Schema](event-schemas.md).

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, aki úgy dönt, hogy elküldi az eseményeket Event Gridba. Az eseményeket a saját alkalmazásból teheti közzé.

## <a name="event-sources"></a>Eseményforrások

Az esemény forrása az esemény. Minden eseményforrás egy vagy több eseménytípus kapcsolódik. Az Azure Storage például a blob által létrehozott események eseményforrás. Az alkalmazás a definiált egyéni események eseményforrás. Az esemény-források felelősek az események Event Grid való elküldéséhez.

## <a name="topics"></a>Üzenettémák

Az Event Grid-témakör olyan végpontot biztosít, amelyben a forrás eseményeket küld. A közzétevő létrehozza az Event Grid-témakört, és eldönti, hogy egy adott eseményforrás egy vagy több témakörre van-e szüksége. A témakörök a kapcsolódó események gyűjteményéhez használatosak. Bizonyos típusú események megválaszolásához az előfizetők eldönthetik, hogy mely témakörökre kell előfizetni.

Az alkalmazás tervezésekor rugalmasan dönthet arról, hogy hány témakört kell létrehoznia. Nagyméretű megoldások esetében hozzon létre egy egyéni témakört a kapcsolódó események egyes kategóriáira. Vegyünk például egy olyan alkalmazást, amely a felhasználói fiókok és a feldolgozási rendelések módosításával kapcsolatos eseményeket küld. Nem valószínű, hogy az eseménykezelő mindkét típusú eseményt szeretné használni. Hozzon létre két egyéni témakört, és engedje, hogy az eseménykezelők előfizessenek az őket érdeklő személyre. Kis megoldások esetében érdemes lehet az összes eseményt egyetlen témakörbe elküldeni. Az esemény-előfizetők szűrhetik a kívánt típusú eseményeket.

A Event Grid témaköreinek kezeléséről [REST API dokumentációban](api.md) talál további információt.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés azt mutatja be, Event Grid hogy mely események érdeklik egy adott témakörben. Az előfizetés létrehozásakor meg kell adnia egy végpontot az esemény kezelésére. A végpontnak elküldett eseményeket szűrheti. 

A Event Grid előfizetések kezelésével kapcsolatban [REST API dokumentációban](api.md) talál további információt.

## <a name="event-handlers"></a>Eseménykezelők

Egy Event Grid perspektívából az eseménykezelő az a hely, ahol az eseményt elküldjék. A kezelő további műveletet hajt végre az esemény feldolgozásához. Event Grid számos kezelő típust támogat. Használhat egy támogatott Azure-szolgáltatást vagy a saját webhookot kezelőként. A kezelő típusától függően a Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. Ha a cél eseménykezelő egy HTTP-webhook, az eseményt a rendszer újrapróbálkozik, amikor a kezelő a `200 – OK`állapotkódot adja vissza. Az Edge hub esetében, ha az esemény kivétel nélkül kerül kézbesítésre, akkor azt a rendszer sikeresnek tekinti.

## <a name="security"></a>Biztonság

Event Grid biztosít biztonságot a témakörökre való feliratkozáshoz és a témakörök közzétételéhez. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

## <a name="event-delivery"></a>Esemény kézbesítése

Ha Event Grid nem tudja megerősíteni, hogy az előfizető végpontja megkapta-e az eseményt, az visszaadja az eseményt. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](delivery-retry.md).

## <a name="batching"></a>Kötegelés

Egyéni témakör használatakor az eseményeket mindig közzé kell tenni egy tömbben. Alacsony átviteli sebesség esetén a tömbnek csak egy értéke lesz. Nagy mennyiségű használati eset esetén javasoljuk, hogy a nagyobb hatékonyság érdekében a közzététel során egyszerre több eseményt is hozzon létre. A kötegek legfeljebb 1 MB méretűek lehetnek. Minden eseménynek még nem lehet 1 MB-nál nagyobbnak lennie (előzetes verzió).