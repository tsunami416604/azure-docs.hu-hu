---
title: Az Azure Firewall Manager előzetes telepítése – áttekintés
description: Ismerje meg az Azure Firewall Manager előzetes verziójához szükséges magas szintű telepítési lépéseket
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443125"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Az Azure Firewall Manager előzetes telepítése – áttekintés

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Az Azure Firewall Manager előzetes verziójának üzembe helyezésére több módon is telepíthetők, de a következő általános folyamat ajánlott.

## <a name="general-deployment-process"></a>Az üzembe helyezés általános folyamata

### <a name="hub-virtual-networks"></a>Virtuális központi hálózatok

1.  Tűzfalházirend létrehozása

    - Új szabályzat létrehozása
<br>*Vagy*<br>
    - Alapházirend levezetni és testre szabni a helyi házirendet
<br>*Vagy*<br>
    - Szabályok importálása egy meglévő Azure-tűzfalról. Győződjön meg arról, hogy eltávolítja a NAT-szabályokat a több tűzfalon alkalmazandó házirendekből
1. A hub és a küllős architektúra létrehozása
   - Hub virtuális hálózat létrehozása az Azure Firewall Manager és a társküllővirtuális hálózatok használatával a virtuális hálózati társviszony-létesítés használatával
<br>*Vagy*<br>
    - Hozzon létre egy virtuális hálózatot, és adjon hozzá virtuális hálózati kapcsolatokat és társküllős virtuális hálózatokat a virtuális hálózati társviszony-létesítés használatával

3. Válassza ki a biztonsági szolgáltatókat, és társítsa a tűzfalházirendet. Jelenleg csak az Azure Firewall támogatott szolgáltató.

   - Ez akkor történik meg, amikor hub virtuális hálózatot hoz létre
<br>*Vagy*<br>
    - Meglévő virtuális hálózat átalakítása központi virtuális hálózattá. Az is lehetséges, hogy konvertálni több virtuális hálózatok.

4. A Felhasználói definiálási útvonalak konfigurálása a forgalom nak a központi virtuális hálózati tűzfalra való irányításához.


### <a name="secured-virtual-hubs"></a>Biztonságos virtuális elosztók

1. A hub és a küllős architektúra létrehozása

   - Hozzon létre egy biztonságos virtuális központot az Azure Firewall Manager használatával, és adjon hozzá virtuális hálózati kapcsolatokat.<br>*Vagy*<br>
   - Hozzon létre egy Virtuális WAN Hubot, és adjon hozzá virtuális hálózati kapcsolatokat.
2. Biztonsági szolgáltatók kiválasztása

   - Biztonságos virtuális elosztó létrehozása közben végzett.<br>*Vagy*<br>
   - Meglévő virtuális WAN-elosztó átalakítása biztonságos virtuális központtá.
3. Tűzfalházirend létrehozása és társítása a központtal

   - Csak akkor alkalmazható, ha az Azure Firewall használatával.
   - A külső biztonsági szolgáltatások (SECaaS) házirendek a partnerek felügyeleti élményén keresztül konfigurálva vannak.
4. Útvonalbeállítások konfigurálása a forgalom nak a biztonságos hubra való irányításához

   - A biztonságos elosztóra irányíthatja a forgalmat a felhasználó által definiált útvonalak (UDR) nélküli szűréshez és naplózáshoz a küllős virtuális hálózatokon a Biztonságos virtuális központ útvonalbeállítási lapján.

> [!NOTE]
> - Nem rendelkezhet egynél több hub/virtual wan régiónként. De a régióban több virtuális WAN-t is hozzáadhat ennek eléréséhez.
> - A vWAN hubjaihoz nem lehetnek egymást átfedő IP-tárolóhelyek.
> - A központi virtuális hálózat kapcsolatainak ugyanabban a régióban kell lenniük, mint a hubnak.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: A felhőhálózat biztonságossá tétele az Azure Firewall Manager előzetes verziójával az Azure Portal használatával](secure-cloud-network.md)