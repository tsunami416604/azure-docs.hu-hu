---
title: Mik azok az Azure Firewall Manager megbízható biztonsági partnerek (előzetes verzió)
description: További információ az Azure Firewall Manager megbízható biztonsági partnereiről
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436768"
---
# <a name="what-are-trusted-security-partners-preview"></a>Mik azok a megbízható biztonsági partnerek (előzetes verzió)?

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Firewall Manager *megbízható biztonsági partnerei (előzetes verzió)* lehetővé teszik, hogy az ismerős, legjobb fajtabeli, harmadik féltől származó biztonsági ajánlatokat szolgáltatásként (SECaaS) használja a felhasználók internet-hozzáférésének védelme érdekében.

A gyors konfiguráció, a támogatott biztonsági partnerrel rendelkező hub biztonságossá tétele, valamint a virtuális hálózatok (virtuális hálózatok) vagy a régión belüli elágazási helyekről történő útvonal- és szűrőforgalom. Ez automatizált útvonalkezeléssel történik, a felhasználó által definiált útvonalak (UDRs) beállítása és kezelése nélkül.

Az Ön által kiválasztott biztonsági partnerrel konfigurált biztonságos elosztókat több Azure-régióban is üzembe helyezheti, hogy a felhasználók számára kapcsolatot és biztonságot kapjon a világ bármely pontján, ezekben a régiókban. A biztonsági partner ajánlatának az Internet/SaaS alkalmazásforgalomhoz való használatával és az Azure Firewall a biztonságos csomópontok privát forgalmához való használatával most már megkezdheti a globálisan elosztott felhasználók és alkalmazások közelében lévő Azure biztonsági élének kiépítését.

Ehhez az előzetes verzióhoz a támogatott biztonsági partnerek a **ZScaler** és **a iboss.** A támogatott régiók a WestCentralUS, a NorthCentralUS, a WestUS, a WestUS2 és az EastUS.

![Megbízható biztonsági partnerek](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Főbb forgatókönyvek

A biztonsági partnerek segítségével szűrheti az internetes forgalmat a következő esetekben:

- Virtuális hálózat (VNet) az internethez

   Használja ki a fejlett, felhasználóbarát internetvédelmet az Azure-ban futó felhőbeli számítási feladatokhoz.

- Ág az internethez

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, és egyszerűen hozzáadhat külső NSaaS-szűrést az internetes forgatókönyvekhez. Az Azure Virtual WAN használatával globális tranzithálózatot és biztonsági éleket hozhat létre.

A következő forgatókönyvek támogatottak:
-   virtuális hálózattal az internethez egy külső partnerajánlaton keresztül.
-   Ág az interneten keresztül egy harmadik fél partner kínál.
-   Ág az internethez egy harmadik fél partner ajánlat, a többi a magánforgalom (küllős, küllős, ág-to-küllők) az Azure Tűzfalon keresztül.

A következő forgatókönyv nem támogatott:

- Virtuális hálózat az internethez egy partner ajánlat nem kombinálható az Azure Firewall a privát forgalom. Tekintse meg az alábbi korlátozásokat.

## <a name="current-limitations"></a>Aktuális korlátozások

- A virtuális hálózat az internethez, nem keverheti hozzá az Azure Firewall a privát forgalom és a partner kínál az internetes forgalom. Küldhet internetes forgalmat az Azure Tűzfalnak vagy egy külső biztonsági partnernek a biztonságos virtuális központban, de mindkettőt nem. 
- Virtuális központonként legbőlegfelségre is telepíthet egy biztonsági partnert. Ha meg kell változtatnia a szolgáltatót, el kell távolítania a meglévő partnert, és újat kell hozzáadnia.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Gyakorlati tanácsok az internetes forgalom biztonságos virtuális csomópontokban történő szűréséhez

Az internetes forgalom általában webes forgalmat is magában foglal. De magában foglalja a SaaS-alkalmazások, például az Office 365 (O365) és az Azure nyilvános PaaS-szolgáltatások, például az Azure Storage, az Azure Sql és így tovább szánt forgalmat is. Az alábbi ajánlott eljárásokat ajánlott eljárások at a szolgáltatások forgalma kezelésével kapcsolatban:

### <a name="handling-azure-paas-traffic"></a>Azure PaaS-forgalom kezelése
 
- Az Azure Firewall védelmet nyújt, ha a forgalom többnyire Azure PaaS-ból áll, és az alkalmazások erőforrás-hozzáférése szűrhető IP-címek, teljes tartománynevek, szolgáltatáscímkék vagy teljes qdn-címkék használatával.

- Használjon egy külső partnermegoldást a hubokon, ha a forgalom SaaS-alkalmazás-hozzáféréssel rendelkezik, vagy felhasználóbarát szűrésre van szüksége (például a virtuális asztali infrastruktúra (VDI) számítási feladatához), vagy speciális internetszűrési funkciókra van szüksége.

![Az Azure Firewall Manager összes forgatókönyve](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Az Office 365(O365) forgalmának kezelése

Globálisan elosztott ághely-forgatókönyvekben az Office 365-forgalmat közvetlenül az ágra kell átirányítania, mielőtt az Azure által védett hub fennmaradó internetes forgalmat küldene.

Az Office 365-ben a hálózati késés és a teljesítmény kritikus fontosságú a sikeres felhasználói élmény érdekében. Az optimális teljesítmény és felhasználói élmény eléréséhez az ügyfeleknek közvetlen és helyi műveletet kell végrehajtaniuk az Office 365-ön, mielőtt fontolóra vennék az internetes forgalom fennmaradó részének az Azure-on keresztül történő útválasztását.

[Az Office 365 hálózati kapcsolattal kapcsolatos alapelvei](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) szerint a legfontosabb Office 365-hálózati kapcsolatokhelyileg irányíthatók a felhasználói ágból vagy mobileszközről, illetve közvetlenül az interneten keresztül a legközelebbi Microsoft-hálózati jelenléti pontra.

Továbbá az Office 365-kapcsolatok erősen titkosítva vannak az adatvédelem és a hatékony, saját tulajdonú protokollok használata érdekében. Ez nem praktikussá és hatásossá teszi, ha ezeket a kapcsolatokat hagyományos hálózati szintű biztonsági megoldásoknak vetjük alá. Ezért javasoljuk, hogy az ügyfelek közvetlenül az ágakról küldjék el az Office 365-forgalmat, mielőtt az Azure-on keresztül küldenék a forgalom többi részét. A Microsoft számos SD-WAN megoldásszolgáltatóval működik együtt, akik integrálódnak az Azure-ral és az Office 365-tel, és megkönnyítik az ügyfelek számára az Office 365 közvetlen és helyi internetes kitörésének engedélyezését. További részletek: [Hogyan állíthatom be az O365-szabályzatokat a Virtual WAN-on keresztül?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>További lépések

[Az Azure Firewall Manager használatával megbízható biztonsági ajánlat telepítése biztonságos hubon.](deploy-trusted-security-partner.md)
