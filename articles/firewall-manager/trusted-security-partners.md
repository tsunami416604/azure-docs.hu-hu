---
title: Mik azok a Azure Firewall Manager biztonsági partner-szolgáltatók?
description: Tudnivalók a Azure Firewall Manager biztonsági partnereinek szolgáltatói
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563611"
---
# <a name="what-are-security-partner-providers"></a>Mik azok a biztonságipartner-szolgáltatók?

A Azure Firewall Managerben található *biztonsági partner szolgáltatók* lehetővé teszik, hogy az ismerős, legjobb, harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) kínálja a felhasználók internet-hozzáférésének védelmére.

A gyors konfigurálással biztonságossá teheti a hubot egy támogatott biztonsági partnerrel, és átirányíthatja és szűrheti az internetes forgalmat a virtuális hálózatok (virtuális hálózatok) vagy az adott régión belüli fiókirodák között. Ezt az automatikus útválasztási felügyelettel teheti meg, a felhasználó által megadott útvonalak (UDR) beállítása és kezelése nélkül.

Az Ön által választott biztonsági partnerrel konfigurált biztonságos hubokat több Azure-régióban is üzembe helyezheti, így a felhasználók bárhonnan és biztonságosan érhetik el a felhasználókat a különböző régiókban. A biztonsági partnernek az internetes/SaaS-alkalmazások forgalmára való használatának, valamint a biztonságos hubokon Azure Firewall a privát forgalomra való felépítése lehetővé teszi, hogy a globálisan elosztott felhasználókhoz és alkalmazásokhoz közeledő Azure-beli biztonsági szegélyt fejlesszen fel.

A támogatott biztonsági partnerek a következők: **ZScaler**, **ellenőrzési pont** (előzetes verzió) és **iboss** (előzetes verzió).

![Biztonságipartner-szolgáltatók](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Főbb forgatókönyvek

A biztonsági partnerek az internetes forgalom szűrésére használhatók a következő helyzetekben:

- Virtual Network (VNet) az internetre

   Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó NSaaS-szűrést a fiókirodában az internetes forgatókönyvekhez. Az Azure Virtual WAN használatával felépítheti a globális átviteli hálózatot és a biztonsági szegélyt.

A következő forgatókönyvek támogatottak:
- VNet/ág az interneten keresztül a biztonsági partner szolgáltatója és a másik forgalom (küllő, küllő, ág, ág, hogy küllős) Azure Firewallon keresztül.
- VNet/ág az internethez a biztonsági partner szolgáltatóján keresztül

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

Emellett az Office 365-kapcsolatok titkosítva vannak, és hatékony, védett protokollokat használnak a teljesítmény szempontjából. Ez a hagyományos hálózati szintű biztonsági megoldásokhoz való csatlakozást nem teszi lehetővé. Ezeknek az okoknak az az oka, hogy az ügyfelek közvetlenül az ágakból küldik el az Office 365 forgalmat, mielőtt elküldik a forgalmat az Azure-on keresztül. A Microsoft több, az Azure-ral és az Office 365-nal integrált SD-WAN-megoldási szolgáltatóval együttműködik, és megkönnyíti az ügyfelek számára, hogy az Office 365 Direct és a helyi internetes breakout szolgáltatást is engedélyezzék. További részletek: [Hogyan saját O365-szabályzatok beállítása virtuális WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview) -kapcsolaton keresztül?

## <a name="next-steps"></a>További lépések

[Telepítsen egy biztonsági partneri ajánlatot egy biztonságos hubhoz a Azure Firewall Manager használatával](deploy-trusted-security-partner.md).
