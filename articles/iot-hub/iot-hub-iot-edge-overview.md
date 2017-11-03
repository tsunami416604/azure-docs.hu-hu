---
title: "Az Azure IoT peremhálózati áttekintése |} Microsoft Docs"
description: "Ismerteti az alapvető architekturális fogalmakat az Azure IoT Edge például átjárók, a modulok és a brókerek."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Az Azure IoT peremhálózati architekturális fogalmak

Mielőtt bármely mintakód vizsgálja meg, vagy hozzon létre saját IoT Edge használata mező, tisztában kell lennie az alapvető fogalmakat, amelyek az IoT-biztonsági architektúrája.

## <a name="iot-edge-modules"></a>Az IoT-Edge-modulok

Átjáró használjon létrehozásával és összeállításakor Azure IoT peremhálózati *IoT peremhálózati modulok*. A modulok *üzenetek* használatával cserélnek adatokat egymással. Az egyes modulok üzeneteket fogadnak, végrehajtanak valamilyen műveletet rajtuk, esetleg átalakítják őket új üzenetekké, majd közzéteszik azokat más modulok számára feldolgozásra. Egyes modulok esetleg kizárólag új üzeneteket állítanak elő, és nem dolgoznak fel beérkező üzeneteket. Modulok láncba rendezésével egy adatfeldolgozó folyamat hozható létre, amelynek minden pontján valamely modul valamilyen módon átalakítja az adatokat.

![Az átjáró moduljainak láncba rendezése az Azure IoT Edge szolgáltatással][1]

Az IoT-Edge a következő összetevőkből áll:

* Előzetes írásbeli modulokat, amelyek a közös átjáró.
* Felületek, amelyek segítségével a fejlesztők egyedi modulokat hozhatnak létre.
* A modulok üzembe helyezéséhez és futtatásához szükséges infrastruktúra.

Az SDK-t, amely lehetővé teszi, és különböző operációs rendszeren futó átjárók létrehozására absztrakciós réteget biztosít.

![Az Azure IoT Edge absztrakciós rétege][2]

## <a name="messages"></a>Üzenetek

Ha úgy képzeljük el, hogy a modulok egymásnak küldözgetnek üzeneteket, könnyen megragadható az átjáró működése mögött rejlő elv, azonban ez nem pontosan így történik. Az IoT-Edge modulok egy broker használatával kommunikálnak egymással. Modulok üzenetek közzététele az átvitelszervező (például a bus, vagy a közzétételi/előfizetési üzenetkezelési mintát használ), és engedélyezze a broker továbbítani az üzenetet csatlakozik hozzá modulok.

A modul a **Broker_Publish** függvény használatával teszi közzé az üzeneteket a közvetítőn. A közvetítő egy visszahívási függvény használatával továbbítja az üzeneteket az egyes moduloknak. Az üzenetek kulcs/érték tulajdonságokból és tartalmakból állnak, amelyek memóriablokként vannak továbbítva.

![A közvetítő szerepe az Azure IoT Edge-ben][3]

## <a name="message-routing-and-filtering"></a>Üzenettovábbítás és -szűrés

A megfelelő IoT Edge-modulok üzenetek közvetlen két módja van:

* Az átvitelszervező, a broker tudja a forrás és a fogadó minden modul átadhatók hivatkozásokat tartalmaz.
* A modul az üzenet tulajdonságai alapján szűrhet.

Az egyes moduloknak azonban csak akkor kell foglalkozniuk egy üzenettel, ha azt nekik szánták. Hivatkozások és a hatékony szűrési message üzenet folyamatokat létrehozni.

## <a name="next-steps"></a>Következő lépések

Ezek a fogalmak alkalmazott minta futtatása, olvassa el [felfedezés Azure IoT peremhálózati architektúra][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
