---
title: 'Gyors útmutató: a szolgáltatás engedélyezése'
description: Ismerje meg, hogyan hozhatja be és engedélyezheti a Defender for IoT biztonsági szolgáltatást az Azure IoT Hubban.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 3f84f3121d9982205ecf51ec64cfe332b6a5ad42
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947261"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Gyors útmutató: az Azure Defender beléptetése a IoT szolgáltatásba IoT Hub

Ez a cikk azt ismerteti, hogyan engedélyezheti a Defender for IoT szolgáltatást a meglévő IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) című témakört.

> [!NOTE]
> A IoT Defender jelenleg csak a standard szintű IoT hubokat támogatja.

## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezésének előfeltételei

- Log Analytics-munkaterület
  - Alapértelmezés szerint két típusú információt tárol a Log Analytics munkaterületen, a Defender for IoT; **biztonsági riasztások** és **javaslatok**.
  - Dönthet úgy is, hogy hozzáad egy további adattípust, **nyers eseményeket**. Vegye figyelembe, hogy a Log Analytics **nyers események** tárolása további tárolási költségekkel jár.
- IoT Hub (standard szint)
- Az összes [szolgáltatás előfeltételeinek](service-prerequisites.md) teljesítése

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>A Defender engedélyezése a IoT a IoT Hub

A IoT Hub biztonságának engedélyezése:

1. Nyissa meg a **IoT Hubt** Azure Portal.
1. A **Biztonság** menüben kattintson a **IoT-megoldás biztonságossá tétele**lehetőségre.

Gratulálunk! Végrehajtotta a Defender IoT való engedélyezését a IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Térinformatikai és IP-címek kezelése

A IoT-megoldás biztonságossá tételéhez a IoT-eszközökről, IoT Edge és IoT Hub a bejövő és kimenő kapcsolatainak IP-címeit a rendszer alapértelmezés szerint összegyűjti és tárolja. Ez az információ elengedhetetlen a gyanús IP-forrásokkal való rendellenes kapcsolódás észleléséhez. Ha például kísérlet történik a kapcsolat létrehozására egy ismert botnet IP-forrásával vagy egy, a földrajzi helyen kívüli IP-forrással. A Defender for IoT szolgáltatás lehetővé teszi, hogy bármikor engedélyezze és tiltsa le az IP-címekre vonatkozó adatgyűjtést.

Az IP-címek adatgyűjtésének engedélyezése vagy letiltása:

1. Nyissa meg a IoT Hub, majd válassza a **Beállítások** lehetőséget a **Biztonság** menüben.
1. Válassza ki az **adatgyűjtési** képernyőt, és módosítsa a térinformatikai és/vagy IP-kezelési beállításokat, ahogy szeretné.

### <a name="log-analytics-creation"></a>Log Analytics létrehozása

Ha a IoT Defender be van kapcsolva, a rendszer létrehoz egy alapértelmezett Azure Log Analytics-munkaterületet a nyers biztonsági események, riasztások és javaslatok tárolására a IoT-eszközökre, a IoT Edgera és a IoT Hub. Minden hónapban az Azure Log Analytics-szolgáltatásba betöltött első öt (5) GB adat ingyenes. Az Azure Log Analytics-munkaterületre betöltött minden GB-nyi adat díjmentesen megmarad az első 31 napban. További információ a [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) díjszabásáról.

Log Analytics munkaterület-konfigurációjának módosítása:

1. Nyissa meg a IoT Hub, majd válassza a **Beállítások** lehetőséget a **Biztonság** menüben.
1. Válassza ki az **adatgyűjtés** képernyőt, és módosítsa a log Analytics beállítások munkaterület-konfigurációját igény szerint.

### <a name="customize-your-iot-security-solution"></a>A IoT biztonsági megoldás testreszabása

Alapértelmezés szerint a Defender for IoT megoldás bekapcsolása automatikusan biztonságossá teszi az Azure-előfizetéshez tartozó összes IoT-elosztót.

A Defender bekapcsolása a IoT szolgáltatáshoz egy adott IoT Hub be-vagy kikapcsolva:

1. Nyissa meg a IoT Hub, majd válassza a **Beállítások** lehetőséget a **Biztonság** menüben.
1. Válassza ki az **adatgyűjtés** képernyőt, és módosítsa az Azure-előfizetésében lévő bármelyik IoT hub biztonsági beállításait.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásához...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)
