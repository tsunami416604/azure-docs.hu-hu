---
title: A Microsoft Azure integrálása az Oracle Cloud Infrastructure szolgáltatással | Microsoft dokumentumok
description: Ismerje meg azokat a megoldásokat, amelyek a Microsoft Azure-on futó Oracle-alkalmazásokat integrálják az Oracle Cloud Infrastructure (OCI) adatbázisaival.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 178fb805f4bfc0762ce7e4a320752e5afa00cef5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271862"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>A Microsoft Azure-t és az Oracle Cloud Infrastructure-t integráló Oracle alkalmazásmegoldások

A Microsoft és az Oracle partneri kapcsolatban részesül az alacsony késleltetésű, nagy áteresztőképességű felhőközi kapcsolat biztosításában, lehetővé téve, hogy mindkét felhő ből a legjobbat kihasználhassa. 

Ezzel a felhőközi kapcsolattal particionálhat egy többrétegű alkalmazást az adatbázisréteg oracle cloud infrastructure (OCI), valamint az alkalmazás és más szintek futtatásához a Microsoft Azure-ban. A felhasználói élmény hasonló a teljes megoldásverem egyetlen felhőben való futtatásához. 

Ha teljes mértékben az Azure-infrastruktúrán szeretné telepíteni az Oracle-megoldásokat, olvassa el az [Oracle virtuálisgép-lemezképek és azok Microsoft Azure-beli telepítését.](oracle-vm-solutions.md)

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A felhőközi kapcsolat megoldást kínál az Oracle iparágvezető alkalmazásai és saját egyéni alkalmazásai Azure virtuális gépeken való futtatására, miközben élvezi az OCI üzemeltetett adatbázis-szolgáltatásainak előnyeit. 

> [!IMPORTANT]
> Az Oracle tanúsítja, hogy ezek az alkalmazások 2020 májusig futnak az Azure/Oracle Cloud interconnect megoldás használatával.

A felhőközi konfigurációban futtatható alkalmazások a következők:

* E-Business lakosztály
* JD Edwards Vállalat
* Peoplesoft
* Oracle kiskereskedelmi alkalmazások
* Oracle Hyperion pénzügyi menedzsment

Az alábbi ábra a csatlakoztatott megoldás magas szintű áttekintését. Az egyszerűség kedvéért az ábrán csak egy alkalmazásszint és egy adatréteg látható. Az alkalmazás architektúrától függően a megoldás tartalmazhat további rétegeket, például egy webes réteget az Azure-ban. További információt a következő szakaszokban talál.

![Azure OCI-megoldás – áttekintés](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Régió elérhetősége 

A felhőközi kapcsolat a következő régiókra korlátozódik:
* Az Azure East US (eastus) & OCI Ashburn (USA-beli kelet)
* Azure UK South (uksouth) & OCI London (UK South)
* Az Azure Canada Central (canadacentral) & OCI Toronto (Délkelet-Kanada)
* Azure Nyugat-Európa (nyugat-európa) & OCI Amsterdam (Hollandia északnyugat)
* Az Azure Japan East (japaneast) & OCI Tokyo (Kelet-Japán)

## <a name="networking"></a>Hálózat

A vállalati ügyfelek gyakran úgy döntenek, hogy különböző üzleti és üzemeltetési okokból több felhőn keresztül diverzifikálják és üzembe helyezik a számítási feladatokat. A diverzifikáció érdekében az ügyfelek összekapcsolják a felhőhálózatokat az interneten, az IPSec VPN-en keresztül, vagy a felhőszolgáltató közvetlen kapcsolódási megoldását a helyszíni hálózaton keresztül. A felhőhálózatok összekapcsolásával jelentős beruházásokra van szükség időben, pénzben, tervezésben, beszerzésben, telepítésben, tesztelésben és műveletekben. 

Az ügyfelek előtt álló kihívások kezelése érdekében az Oracle és a Microsoft integrált többfelhős élményt tett lehetővé. A felhőközi hálózatkezelés a Microsoft Azure [ExpressRoute-áramkörének](../../../expressroute/expressroute-introduction.md) az OCI-ben lévő [FastConnect-kapcsolattal](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) való összekapcsolásával jön létre. Ez a kapcsolat akkor lehetséges, ha egy Azure ExpressRoute-társviszony-létesítési hely az OCI FastConnect-el azonos társviszony-létesítési hely közelében vagy ugyanazon a társviszony-létesítési helyen található. Ez a beállítás lehetővé teszi a biztonságos, gyors kapcsolatot a két felhő között anélkül, hogy közbenső szolgáltatóra lenne szükség.

Az ExpressRoute és a FastConnect használatával az ügyfelek az OCI-ben virtuális felhőhálózattal rendelkező virtuális hálózatot létesíthetnek az Azure-ban, feltéve, hogy a privát IP-címtér nem fedi át egymást. A két hálózat társviszony-létesítése lehetővé teszi, hogy a virtuális hálózat egyik erőforrása úgy kommunikáljon egy erőforrással az OCI virtuális felhőhálózatában, mintha mindkettő ugyanabban a hálózatban lenne.

## <a name="network-security"></a>Hálózati biztonság

A hálózati biztonság minden vállalati alkalmazás kulcsfontosságú eleme, és központi szerepet játszik ebben a többfelhős megoldásban. Az ExpressRoute-on és a FastConnect-en áthaladó bármely forgalom magánhálózaton halad át. Ez a konfiguráció lehetővé teszi az Azure virtuális hálózat és az Oracle virtuális felhőhálózat közötti biztonságos kommunikációt. Az Azure-ban nem kell nyilvános IP-címet megadnia egyetlen virtuális gépnek sem. Hasonlóképpen nincs szükség internetes átjáróra az OCI-ben. Minden kommunikáció a gépek privát IP-címén keresztül történik.

Ezenkívül [biztonsági listákat](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) állíthat be az OCI virtuális felhőhálózati hálózatán és biztonsági szabályaiban (az Azure [hálózati biztonsági csoportjaihoz](../../../virtual-network/security-overview.md)csatolva). Ezekkel a szabályokkal szabályozhatja a virtuális hálózatok ban lévő gépek közötti forgalmat. A hálózati biztonsági szabályok gépszinten, alhálózati szinten és a virtuális hálózat szintjén is hozzáadhatók.
 
## <a name="identity"></a>Identitás

Az identitás a Microsoft és az Oracle közötti partnerség egyik alappillére. Jelentős munkát végeztek az [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) és az Azure Active [Directory](../../../active-directory/index.yml) (Azure AD) integrálása érdekében. Az Azure AD a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása. Ez segít a felhasználóknak a bejelentkezésben és a különböző erőforrások elérésében. Az Azure AD lehetővé teszi a felhasználók és azok engedélyei kezelését is.

Jelenleg ez az integráció lehetővé teszi, hogy egy központi helyen, amely az Azure Active Directory. Az Azure AD szinkronizálja a címtárban bekövetkező változásokat a megfelelő Oracle-könyvtárral, és egyszeri bejelentkezésre szolgál a felhőközi Oracle-megoldásokhoz.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure és az OCI közötti [felhőközi hálózattal.](configure-azure-oci-networking.md) 

Az OCI-ről további információt és tanulmányt az [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentációjában talál.
