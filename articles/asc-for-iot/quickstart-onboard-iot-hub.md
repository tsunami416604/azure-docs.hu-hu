---
title: 'Rövid útmutató: Az Azure Security Center engedélyezése az IoT-szolgáltatáshoz az IoT Hubban'
description: Ismerje meg, hogyan lehet az Azure Security Center for IoT biztonsági szolgáltatás az Azure IoT Hub on-t.
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
ms.openlocfilehash: 0b1f09cfaf107802e1ce6586b3f96b14269aaced
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74664861"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Rövid útmutató: Az IoT Hub onboard Azure Security Center szolgáltatása az IoT Hubban

Ez a cikk ismerteti, hogyan engedélyezheti az Azure Security Center for IoT szolgáltatás a meglévő IoT Hub. Ha jelenleg nem rendelkezik Egy IoT Hub, [lásd: Hozzon létre egy IoT Hub az Azure Portalon az](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) első lépésekhez. 

> [!NOTE]
> Az Azure Security Center for IoT jelenleg csak a standard szintű IoT Hubs támogatja.


## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezésének előfeltételei

- Log Analytics-munkaterület
  - Az Azure Security Center for IoT alapértelmezés szerint kétféle információt tárol a Log Analytics-munkaterületen; **biztonsági riasztásokat** és **ajánlásokat.** 
  - Választhat, hogy hozzá tárolási egy további információtípus, **nyers események**. Vegye figyelembe, hogy **a nyers események** log analytics-ben tárolása további tárolási költségeket hordoz. 
- IoT Hub (standard szint)
- Az összes [szolgáltatásfeltétel](service-prerequisites.md) teljesítése 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Az Azure Security Center engedélyezése az IoT-hez az IoT Hubon 

Az IoT Hub biztonságának engedélyezése: 

1. Nyissa meg az **IoT Hubot** az Azure Portalon. 
1. A **Biztonság** menüben kattintson **az IoT-megoldás biztonságossá tétele parancsra.**    


Gratulálunk! Befejezte az Azure Security Center ioT-hoz való engedélyezését az IoT Hubon. 

### <a name="geolocation-and-ip-address-handling"></a>Földrajzi helymeghatározás és IP-címkezelés

Az IoT-megoldás biztonságossá tétele érdekében a bejövő és kimenő kapcsolatok IP-címeit az IoT-eszközök, az IoT Edge és az IoT Hub(ok) gyűjtik és tárolják alapértelmezés szerint. Ez az információ elengedhetetlen a gyanús IP-forrásokból származó rendellenes kapcsolat észleléséhez. Ha például megkísérelnek kapcsolatokat létesíteni egy ismert botnet IP-forrásából vagy a földrajzi helyen kívüli IP-forrásból. Az Azure Security Center for IoT szolgáltatás rugalmasságot biztosít az IP-címadatok gyűjtésének engedélyezéséhez és letiltásához. 

Az IP-címadatok gyűjtésének engedélyezése vagy letiltása: 

1. Nyissa meg az IoT Hubot, és válassza a **Biztonság** menü **Áttekintés parancsát.** 
2. Válassza ki a **Beállítások** képernyőt, és tetszés szerint módosítsa a földrajzi helymeghatározási és/vagy IP-kezelési beállításokat.

### <a name="log-analytics-creation"></a>A Log Analytics létrehozása

Ha az Azure Security Center for IoT be van kapcsolva, egy alapértelmezett Azure Log Analytics-munkaterület jön létre a nyers biztonsági események, riasztások és javaslatok tárolására az IoT-eszközök, az IoT Edge és az IoT Hub tárolására. Minden hónapban az első öt (5) GB-os adatok ügyfélenként az Azure Log Analytics szolgáltatás ingyenes. Az Azure Log Analytics-munkaterületbe bevitt összes GB-adat az első 31 napban díjmentesen megmarad. További információ a [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) díjszabásáról.

A Log Analytics munkaterület-konfigurációjának módosítása:

1. Nyissa meg az IoT Hubot, és válassza a **Biztonság** menü **Áttekintés parancsát.** 
2. Válassza ki a **Beállítások** képernyőt, és módosítsa a Naplóelemzési beállítások munkaterületi konfigurációját, ahogy szeretné.

### <a name="customize-your-iot-security-solution"></a>Az IoT biztonsági megoldásának testreszabása
Alapértelmezés szerint az Azure Security Center for IoT-megoldás bekapcsolása automatikusan biztosítja az Azure-előfizetésben lévő összes IoT Hub-ot. 

Az Azure Security Center for IoT-szolgáltatás be- és kikapcsolása egy adott IoT Hubon: 

1. Nyissa meg az IoT Hubot, és válassza a **Biztonság** menü **Áttekintés parancsát.** 
2. Válassza ki a **Beállítások** képernyőt, és módosítsa az Azure-előfizetés bármely IoT-hub biztonsági beállításait, ahogy szeretné.


## <a name="next-steps"></a>További lépések

A megoldás konfigurálásához a következő cikkre lépve...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)
