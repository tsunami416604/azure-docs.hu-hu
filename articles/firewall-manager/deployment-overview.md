---
title: Azure Firewall Manager előzetes verziójának üzembe helyezése – áttekintés
description: A Azure Firewall Manager előzetes verziójához szükséges magas szintű telepítési lépések ismertetése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77443125"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Firewall Manager előzetes verziójának üzembe helyezése – áttekintés

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager előzetes verzióját több módon is üzembe helyezheti, de a következő általános folyamat ajánlott.

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

- [Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Manager előzetes verziójával a Azure Portal használatával](secure-cloud-network.md)