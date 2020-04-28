---
title: Mik azok a Azure Firewall Manager megbízható biztonsági partnerei (előzetes verzió)
description: Ismerje meg Azure Firewall Manager megbízható biztonsági partnereit
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436768"
---
# <a name="what-are-trusted-security-partners-preview"></a>Mik azok a megbízható biztonsági partnerek (előzetes verzió)?

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Firewall Manager *megbízható biztonsági partnerei (előzetes verzió)* lehetővé teszik az ismerős, legjobb, harmadik féltől származó biztonsági szolgáltatásként (SECaaS) kínált ajánlatok használatát a felhasználók internet-hozzáférésének védelme érdekében.

A gyors konfigurálással biztonságossá teheti a hubot egy támogatott biztonsági partnerrel, és átirányíthatja és szűrheti az internetes forgalmat a virtuális hálózatok (virtuális hálózatok) vagy az adott régión belüli fiókirodák között. Ez az automatikus útválasztási felügyelettel végezhető el a felhasználó által megadott útvonalak (UDR-EK) beállítása és kezelése nélkül.

Az Ön által választott biztonsági partnerrel konfigurált biztonságos hubokat több Azure-régióban is üzembe helyezheti, így a felhasználók bárhonnan és biztonságosan érhetik el a felhasználókat a különböző régiókban. A biztonsági partnernek az internetes/SaaS-alkalmazások forgalmára való használatának, valamint a biztonságos hubokon Azure Firewall a privát forgalomra való felépítése lehetővé teszi, hogy a globálisan elosztott felhasználókhoz és alkalmazásokhoz közeledő Azure-beli biztonsági szegélyt fejlesszen fel.

Ebben az előzetes verzióban a támogatott biztonsági partnerek a **ZScaler** és a **iboss**. A támogatott régiók a következők: WestCentralUS, NorthCentralUS, WestUS, WestUS2 és EastUS.

![Megbízható biztonsági partnerek](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Főbb forgatókönyvek

A biztonsági partnerek az internetes forgalom szűrésére használhatók a következő helyzetekben:

- Virtual Network (VNet) az internetre

   Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó NSaaS-szűrést a fiókirodában az internetes forgatókönyvekhez. Az Azure Virtual WAN használatával felépítheti a globális átviteli hálózatot és a biztonsági szegélyt.

A következő forgatókönyvek támogatottak:
-   VNet egy harmadik féltől származó partneri ajánlaton keresztül.
-   Harmadik féltől származó partneri ajánlattal az interneten keresztül.
-   Egy harmadik féltől származó partneri ajánlaton keresztül, a privát forgalom (küllő – küllő, küllők, ágak és a küllők) között Azure Firewallon keresztül.

A következő forgatókönyv nem támogatott:

- A partneri ajánlaton keresztüli VNet nem kombinálható a privát forgalom Azure Firewallával. Tekintse meg a következő korlátozásokat.

## <a name="current-limitations"></a>Aktuális korlátozások

- Az internetre való VNet nem keverheti össze a Azure Firewallt a privát forgalomhoz, és egy partneri ajánlatot az internetes forgalomhoz. Az internetes forgalmat Azure Firewall vagy harmadik féltől származó biztonsági partneri ajánlatként is elküldheti a biztonságos virtuális központban, de mindkettőt nem. 
- Virtuális hubhoz legfeljebb egy biztonsági partner helyezhető üzembe. Ha módosítania kell a szolgáltatót, el kell távolítania a meglévő partnert, és hozzá kell adnia egy újat.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Ajánlott eljárások az internetes forgalom szűréséhez a biztonságos virtuális hubokban

Az internetes forgalom jellemzően webes forgalmat tartalmaz. Emellett magában foglalja az olyan SaaS-alkalmazásokra irányuló forgalmat is, mint az Office 365 (O365) és az Azure nyilvános Pásti-szolgáltatások, mint például az Azure Storage, az Azure SQL stb. A következő ajánlott eljárásokat ajánljuk a szolgáltatások forgalmának kezelésére:

### <a name="handling-azure-paas-traffic"></a>Az Azure Pásti-forgalom feldolgozása
 
- A védelem Azure Firewall használata, ha a forgalom főleg az Azure Pástiből áll, és az alkalmazások erőforrás-hozzáférése az IP-címek, a teljes tartománynevek, a szolgáltatási címkék vagy a FQDN-címkék használatával szűrhető.

- Használjon harmadik féltől származó partneri megoldást a hubokban, ha a forgalom SaaS-alkalmazás-hozzáférésből áll, vagy ha a felhasználó által használható szűrésre van szüksége (például a virtuális asztali infrastruktúra (VDI) számítási feladataihoz), vagy speciális Internet-szűrési képességekre van szüksége.

![A Azure Firewall Manager összes forgatókönyve](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365-(O365-) forgalom kezelésére

Globálisan elosztott fiókirodai helyzetekben az Office 365 forgalmat közvetlenül a fiókirodában kell átirányítani, mielőtt elküldi az Azure-beli védett központ fennmaradó internetes forgalmát.

Az Office 365 esetében a hálózati késés és a teljesítmény kritikus fontosságú a sikeres felhasználói élmény érdekében. Az optimális teljesítmény-és felhasználói élmény érdekében az ügyfeleknek az Azure-on keresztül az internetes forgalom további forgalmának megfontolása előtt be kell vezetniük az Office 365 Direct és a local Escape szolgáltatást.

Az [office 365 hálózati kapcsolati alapelveket](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) hívja a key Office 365 hálózati kapcsolatainak helyi továbbítására a felhasználói ágban vagy a mobileszközön, és közvetlenül az interneten keresztül a legközelebbi Microsoft hálózati pontra.

Emellett az Office 365-kapcsolatok szigorúan titkosítva vannak az adatvédelem szempontjából, és hatékony, szabadalmaztatott protokollokat használnak a teljesítmény szempontjából. Ez a hagyományos hálózati szintű biztonsági megoldásokhoz való csatlakozást nem teszi lehetővé. Ezeknek az okoknak az az oka, hogy az ügyfelek közvetlenül az ágakból küldik el az Office 365 forgalmat, mielőtt elküldik a forgalmat az Azure-on keresztül. A Microsoft több, az Azure-ral és az Office 365-nal integrált SD-WAN-megoldási szolgáltatóval együttműködik, és megkönnyíti az ügyfelek számára, hogy az Office 365 Direct és a helyi internetes breakout szolgáltatást is engedélyezzék. További részletek: [Hogyan saját O365-szabályzatok beállítása virtuális WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview) -kapcsolaton keresztül?


## <a name="next-steps"></a>További lépések

[Helyezzen üzembe egy megbízható biztonsági ajánlatot egy biztonságos központban a Azure Firewall Manager használatával](deploy-trusted-security-partner.md).
