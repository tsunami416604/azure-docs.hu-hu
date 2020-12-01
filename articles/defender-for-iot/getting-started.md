---
title: Üzembe helyezési beállítások
description: Ismerkedjen meg a Defender alapszintű munkafolyamatával a IoT szolgáltatásainak és szolgáltatásának megismeréséhez.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340016"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Ismerkedés az Azure Defender IoT

Ez a cikk azokat az üzembe helyezési és előkészítési folyamatokat ismerteti, amelyek szükségesek ahhoz, hogy az Azure Defender IoT fusson. További lépések is szükségesek. Javasoljuk, hogy Ismerje meg ezeket a lépéseket, és ismerkedjen meg a kapcsolódó dokumentumokkal kapcsolatos információkkal.

Az összes lépés elvégzése után a IoT-érzékelőkhöz készült Azure Defender a hálózatot fogja figyelni. A megoldás beállításának módjától függően az észlelések a helyszíni felügyeleti konzolra vagy a IoT Hubra is küldhetők.

Az Azure Defender IoT való üzembe helyezéséhez hajtsa végre az alábbi lépéseket.

## <a name="1-set-up-azure"></a>1. az Azure beállítása

- Hozzon létre egy Azure-fiókot. További információ: Azure- [fiók létrehozása](/learn/modules/create-an-azure-account/).

- Tűzfal vagy proxy: Ha tűzfallal vagy hasonló beavatkozó hálózati eszközzel rendelkezik, amely lehetővé teszi a megadott kapcsolatok engedélyezését, ellenőrizze, hogy a *. azure-devices.net:443 meg van-e nyitva a tűzfal vagy a proxy számára. Ha a helyettesítő karakterek nem támogatottak, vagy ha több vezérlőt szeretne használni, akkor az adott IoT Hub teljes tartománynevet meg kell nyitni az FW-ben vagy a proxyban. További információ: [Reference-IoT hub végpontok](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. hardverek, szoftverek és bevezetések üzembe helyezése az érzékelőn

- Sensor-hardver vásárlása és szoftver telepítése. Kövesse az itt ismertetett lépéseket, és további információkért tekintse meg ezt a cikket és a [Defender for IoT útmutatót](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) és a [telepítési útmutatót](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

  - Az érzékelő telepítése után biztonságosan rögzítheti az érzékelő bejelentkezési hitelesítő adatait. Szüksége lesz a hitelesítő adatokra az aktiválási fájlnak az érzékelőre való feltöltéséhez.

  - Ha helyileg felügyelt érzékelőkkel dolgozik, biztonságosan rögzítse az érzékelő IP-címét vagy a telepítésben definiált érzékelő nevét. A IoT-portál Defender-regisztrációja során érdemes lehet használni az érzékelő nevének létrehozásakor. Később az Azure Defender for IoT portál regisztrációs neve és az érzékelő konzolon megjelenő üzembe helyezett érzékelő IP-címe között könnyebben nyomon követheti és konzisztens elnevezéseket biztosíthat.

- Regisztrálja az érzékelőt a Defender for IoT portálon, és töltse le az érzékelő aktiválási fájlját.

- Töltse fel az aktiválási fájlt az érzékelőbe.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. az érzékelő figyelésének és felügyeletének hálózati beállítása

- Kapcsolja össze az érzékelőt a hálózattal. A [hálózati telepítési útmutatóban](https://aka.ms/AzureDefenderForIoTNetworkSetup)olvasható.

## <a name="4-start-discovering-your-network"></a>4. a hálózat felfedezésének megkezdése

- A rendszerbeállítások csípése az érzékelő konzolon.

- Szenzorok összekapcsolása egy helyszíni felügyeleti konzolon.

További információ: az [Azure Defender for IoT szenzor felhasználói útmutatója](https://aka.ms/AzureDefenderforIoTUserGuide) és a [IoT helyszíni felügyeleti konzol felhasználói útmutatója](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. az Azure Sentinel feltöltése riasztási információkkal

- Ha riasztási információkat szeretne küldeni az Azure Sentinelnek, konfigurálja az Azure Sentinelt: [az adatok összekapcsolását a Defendertől az Azure Sentinel IoT](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>További lépések

- [A Defender engedélyezése a IoT](quickstart-onboard-iot-hub.md)
- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások](quickstart-create-custom-alerts.md) konfigurálása
- [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)