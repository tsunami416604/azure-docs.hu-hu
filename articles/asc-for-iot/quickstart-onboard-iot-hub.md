---
title: 'Gyors útmutató: Azure Security Center engedélyezése a IoT szolgáltatáshoz a IoT Hub'
description: Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT Hubban.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 641ba4c8d3b0e54132c19a493e1e4bf17bb28e13
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904108"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Gyors útmutató: Azure Security Center a IoT Service-ben IoT Hub

Ez a cikk azt ismerteti, hogyan engedélyezhető a IoT szolgáltatás Azure Security Center a meglévő IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) című témakört. 

> [!NOTE]
> A IoT Azure Security Center jelenleg csak a standard szintű IoT hubokat támogatja.


## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezésének előfeltételei

- Log Analytics munkaterület
  - A Log Analytics munkaterület alapértelmezés szerint két típusú információt tárol a IoT számára Azure Security Center alapján. **biztonsági riasztások** és **javaslatok**. 
  - Dönthet úgy is, hogy hozzáad egy további adattípust, **nyers eseményeket**. Vegye figyelembe, hogy a Log Analytics **nyers események** tárolása további tárolási költségekkel jár. 
- IoT Hub (standard szint)
- Az összes [szolgáltatás előfeltételeinek](service-prerequisites.md) teljesítése 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Azure Security Center engedélyezése a IoT a IoT Hub 

A IoT Hub biztonságának engedélyezése: 

1. Nyissa meg a **IoT Hubt** Azure Portal. 
1. A **Biztonság** menüben kattintson a **IoT-megoldás biztonságossá tétele**lehetőségre.    


Gratulálunk! Befejezte a IoT Azure Security Centerének engedélyezését a IoT Hub. 

### <a name="geolocation-and-ip-address-handling"></a>Térinformatikai és IP-címek kezelése

A IoT-megoldás biztonságossá tételéhez a IoT-eszközökről, IoT Edge és IoT Hub a bejövő és kimenő kapcsolatainak IP-címeit a rendszer alapértelmezés szerint összegyűjti és tárolja. Ez az információ elengedhetetlen a gyanús IP-forrásokkal való rendellenes kapcsolódás észleléséhez. Ha például kísérlet történik a kapcsolat létrehozására egy ismert botnet IP-forrásával vagy egy, a földrajzi helyen kívüli IP-forrással. A IoT szolgáltatás Azure Security Center a rugalmasságot biztosít az IP-címek adatgyűjtésének bármikori engedélyezéséhez és letiltásához. 

Az IP-címek adatgyűjtésének engedélyezése vagy letiltása: 

1. Nyissa meg a IoT Hub, majd válassza az **Áttekintés** lehetőséget a **Biztonság** menüben. 
2. Válassza a **Beállítások** képernyőt, és módosítsa a térinformatikai és/vagy IP-kezelési beállításokat, ahogy szeretné.

### <a name="log-analytics-creation"></a>Log Analytics létrehozása

Ha a IoT Azure Security Center be van kapcsolva, a rendszer létrehoz egy alapértelmezett Azure Log Analytics-munkaterületet, amely a nyers biztonsági események, riasztások és javaslatok tárolására szolgál a IoT-eszközök, a IoT Edge és a IoT Hub számára. Minden hónapban az Azure Log Analytics-szolgáltatásba betöltött első öt (5) GB adat ingyenes. Az első 31 nap alatt az Azure Log Analytics-munkaterületbe betöltött minden GB adatot díjmentesen megtartunk. További információ a [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) díjszabásáról.

Log Analytics munkaterület-konfigurációjának módosítása:

1. Nyissa meg a IoT Hub, majd válassza az **Áttekintés** lehetőséget a **Biztonság** menüben. 
2. Válassza a **Beállítások** képernyőt, és módosítsa a log Analytics beállítások munkaterület-konfigurációját, ahogy szeretné.

### <a name="customize-your-iot-security-solution"></a>A IoT biztonsági megoldás testreszabása
Alapértelmezés szerint a IoT-megoldás Azure Security Center bekapcsolása automatikusan biztonságossá teszi az Azure-előfizetéshez tartozó összes IoT-elosztót. 

A IoT szolgáltatás Azure Security Centerának bekapcsolása adott IoT Hub be-vagy kikapcsolva: 

1. Nyissa meg a IoT Hub, majd válassza az **Áttekintés** lehetőséget a **Biztonság** menüben. 
2. Válassza a **Beállítások** képernyőt, és módosítsa az Azure-előfizetésében található bármely IoT hub biztonsági beállításait.


## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásához...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)
