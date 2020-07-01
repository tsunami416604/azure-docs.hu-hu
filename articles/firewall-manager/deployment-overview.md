---
title: Azure Firewall Manager üzembe helyezésének áttekintése
description: A Azure Firewall Managerhez szükséges magas szintű üzembe helyezési lépések ismertetése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 90f817ac3bbd475d8a84df44bc284f09fcd19ce3
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565794"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Azure Firewall Manager üzembe helyezésének áttekintése

Azure Firewall Manager üzembe helyezésének több módja is van, de a következő általános folyamat ajánlott.

## <a name="general-deployment-process"></a>Az üzembe helyezés általános folyamata

### <a name="hub-virtual-networks"></a>Hub virtuális hálózatok

1.  Tűzfalszabály létrehozása

    - Új szabályzat létrehozása
<br>*vagy*<br>
    - Alapszintű házirend származtatása és helyi házirend testreszabása
<br>*vagy*<br>
    - Szabályok importálása meglévő Azure Firewallból. Győződjön meg arról, hogy eltávolítja a NAT-szabályokat a több tűzfalon alkalmazni kívánt házirendekről
1. A hub és a küllő architektúra létrehozása
   - Központi Virtual Network létrehozása a Azure Firewall Manager és a társ-küllős virtuális hálózatok használatával a virtuális hálózati kapcsolatok használatával
<br>*vagy*<br>
    - Virtuális hálózat létrehozása és virtuális hálózati kapcsolatok és társ-küllős virtuális hálózatok hozzáadása virtuális hálózati kapcsolaton keresztül

3. Válassza a biztonsági szolgáltatók lehetőséget, és rendeljen hozzá tűzfal-házirendet. Jelenleg csak Azure Firewall támogatott szolgáltató.

   - Ez egy központi Virtual Network létrehozásakor történik.
<br>*vagy*<br>
    - Meglévő virtuális hálózat konvertálása központi Virtual Networkra. Több virtuális hálózat is konvertálható.

4. Konfigurálja a felhasználói útvonalakat úgy, hogy átirányítsa a forgalmat a hubhoz Virtual Network tűzfalon.


### <a name="secured-virtual-hubs"></a>Biztonságos virtuális hubok

1. A hub és a küllő architektúra létrehozása

   - Hozzon létre egy biztonságos virtuális hubot a Azure Firewall Managerrel, és adja hozzá a virtuális hálózati kapcsolatokat.<br>*vagy*<br>
   - Hozzon létre egy virtuális WAN-hubot, és vegyen fel virtuális hálózati kapcsolatokat.
2. Biztonsági szolgáltatók kiválasztása

   - Biztonságos virtuális központ létrehozásakor történt.<br>*vagy*<br>
   - Meglévő virtuális WAN-központ konvertálása a virtuális központ biztonságossá tételéhez.
3. Tűzfalszabály létrehozása és hozzárendelése a hubhoz

   - Csak Azure Firewall használata esetén alkalmazható.
   - A harmadik féltől származó biztonsági szolgáltatások (SECaaS) házirendjei a partnerek felügyeleti felületén keresztül konfigurálhatók.
4. Útvonal-beállítások konfigurálása a forgalom biztonságos hubhoz való irányításához

   - A biztonságos hub útvonal-beállítási lapján könnyedén irányíthatja a forgalmat a biztonságos hubhoz a felhasználó által megadott útvonalak (UDR) nélküli szűréshez és naplózáshoz a küllős virtuális hálózatokon.

> [!NOTE]
> - Régiónként legfeljebb egy hub virtuális WAN-t használhat. Ennek eléréséhez azonban több virtuális WAN-t is hozzáadhat a régióhoz.
> - A vWAN lévő hubok esetében nem lehet átfedésben lévő IP-szóközök.
> - A hub-VNet kapcsolatainak ugyanabban a régióban kell lenniük, mint a hub-nak.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Managerrel a Azure Portal használatával](secure-cloud-network.md)