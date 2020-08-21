---
title: Koncepció – Azure VMware-megoldás üzembe helyezésének integrálása egy sugaras architektúrával
description: Ismerje meg az Azure VMware-megoldások üzembe helyezését egy meglévő vagy egy új, az Azure-ban, az Azure-ban.
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684508"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Azure VMware-megoldás integrálása egy sugaras architektúrával

Ebben a cikkben javaslatot teszünk egy Azure VMware-megoldás üzembe helyezésének integrálására egy meglévő vagy egy új, az Azure [-ban küllő architektúrában](/azure/architecture/reference-architectures/hybrid-networking/shared-services) . 

A hub és küllős forgatókönyv hibrid felhőalapú környezetet feltételez a következő munkaterhelésekkel:

* Natív Azure a IaaS vagy a Pásti Services használatával
* Azure VMware Solution 
* helyszíni vSphere

## <a name="architecture"></a>Architektúra

A *hub* egy azure-Virtual Network, amely központi kapcsolódási pontként szolgál a helyszíni és az Azure VMware-megoldás privát felhőhöz. A *küllők* olyan virtuális hálózatok, amelyek a központtal együttműködve lehetővé teszik a virtuális hálózat közötti kommunikációt.

A helyszíni adatközpont, az Azure VMware Solution Private Cloud és a hub közötti forgalom ExpressRoute-kapcsolatokon keresztül halad. A küllős virtuális hálózatok általában IaaS-alapú számítási feladatokat tartalmaznak, de olyan Pásti-szolgáltatásokat is tartalmazhatnak, mint például a [app Service Environment](../app-service/environment/intro.md), amelyek közvetlen integrációját Virtual Network vagy más, az [Azure Private linktel](../private-link/index.yml) rendelkező Pásti szolgáltatásokkal együtt. 

Az ábrán egy példa látható az Azure-beli és a ExpressRoute-on keresztül a helyszíni és az Azure VMware-megoldáshoz kapcsolódó sugaras üzembe helyezésre.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Azure VMware Solution hub és küllős integrációs üzembe helyezés" border="false":::

Az architektúra a következő fő összetevőkből áll:

-   Helyszíni **hely:** A helyszíni adatközpont (ok) ExpressRoute-kapcsolaton keresztül csatlakozik az Azure-hoz.

-   **Azure VMware-megoldás saját felhő:** Az Azure VMware-megoldás SDDC egy vagy több vSphere-fürt alkotja, amelyek mindegyike legfeljebb 16 csomóponttal rendelkezik.

-   **ExpressRoute-átjáró:** Lehetővé teszi a kommunikációt az Azure VMware-megoldás saját felhője, a helyszíni hálózat, a hub virtuális hálózat megosztott szolgáltatásai és a küllős virtuális hálózatokon futó munkaterhelések között.

    > [!NOTE]
    > **S2S VPN-megfontolások:** Az Azure VMware megoldás éles környezetekben való üzembe helyezése esetén az Azure S2S a HCX hálózati követelményei miatt nem támogatott. Azonban a HCX-t nem igénylő PoC-vagy nem éles környezetekben is használható.

-   **Hub virtuális hálózata:** Központi kapcsolódási pontként működik a helyszíni hálózat és az Azure VMware-megoldás privát felhője számára.

-   **Küllős virtuális hálózat**

    -   **IaaS küllő:** Egy IaaS az Azure IaaS-alapú számítási feladatait üzemelteti, beleértve a virtuális gépek rendelkezésre állási készleteit és a virtuálisgép-méretezési csoportokat, valamint a megfelelő hálózati összetevőket.

    -   **Péter küllő:** A Péter beszélt a privát [végpontok](../private-link/private-endpoint-overview.md) és a [privát kapcsolatok](../private-link/private-link-overview.md)révén a magánjellegű címzést használó Azure Pásti-szolgáltatásokat.

-   **Azure Firewall:** Központi szerepet játszik a küllők, a helyszíni és az Azure VMware megoldás közötti adatforgalom szegmentálásához.

-   **Application Gateway:** Az Azure IaaS/Pásti vagy az Azure VMware megoldású virtuális gépeken futó webalkalmazások közzététele és védelme. Integrálható más szolgáltatásokkal, például API Managementokkal.

## <a name="network-and-security-considerations"></a>Hálózati és biztonsági megfontolások

A ExpressRoute-kapcsolatok lehetővé teszik a helyszíni, az Azure VMware-megoldás és az Azure hálózati háló közötti forgalom áramlását. Az Azure VMware-megoldás [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) használ a kapcsolat megvalósításához.

A helyszíni kapcsolat ExpressRoute Global Reach is használhat, de nem kötelező.

* **Helyszíni Azure-beli VMware-megoldás forgalmának folyamata**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Helyszíni Azure-beli VMware-megoldás forgalmának folyamata" border="false":::


* **Azure VMware-megoldás a VNET hub-forgalomhoz**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware-megoldás a hub virtuális hálózati forgalmához" border="false":::


Az Azure VMware megoldás hálózatkezeléséről és az összekapcsolási fogalmakról az [Azure VMware megoldás termékdokumentációjában](./concepts-networking.md)talál további információt.

### <a name="traffic-segmentation"></a>Forgalom szegmentálása

[Azure Firewall](../firewall/index.yml) a hub virtuális hálózatán üzembe helyezett hub és küllős topológia központi eleme. Használjon Azure Firewall vagy egy másik Azure által támogatott hálózati virtuális készüléket a forgalmi szabályok létrehozásához és a különböző küllők, a helyszíni és az Azure VMware megoldás munkaterhelések közötti kommunikáció szegmentálásához.

Hozzon létre útválasztási táblákat a Azure Firewall felé irányuló forgalom irányításához.  A küllős virtuális hálózatok esetében hozzon létre egy útvonalat, amely a Azure Firewall belső felületének alapértelmezett útvonalát állítja be, így ha a Virtual Network munkaterhelésének el kell érnie az Azure VMware-megoldási címtartomány elérését, a tűzfal kiértékelheti azt, és alkalmazhatja a megfelelő forgalmi szabályt, hogy az engedélyezze vagy megtagadja.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Útválasztási táblázatok létrehozása a Azure Firewallhoz való közvetlen forgalomhoz":::


> [!IMPORTANT]
> A **GatewaySubnet** beállításban a 0.0.0.0/0 előtaggal rendelkező útvonal nem támogatott.

Adja meg a megfelelő útválasztási táblázatban megadott hálózatok útvonalait. Például az Azure VMware-megoldás felügyeletéhez és a helyszíni és fordítva elérhető IP-előtagokhoz tartozó útvonalakat átirányíthatja a helyszínről az Azure VMware megoldás saját felhőbe Azure Firewall keresztül.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Adott hálózatok útvonalának beállítása a megfelelő útválasztási táblázatban":::

Egy második szintű forgalmi szegmentálás a küllők és a hub hálózati biztonsági csoportjaival, hogy egy részletesebb forgalmi szabályzatot hozzon létre. 


### <a name="application-gateway"></a>Application Gateway

Az Azure Application Gateway v1-es és v2-es verziója olyan webalkalmazásokkal lett tesztelve, amelyek az Azure VMware megoldás virtuális gépein futnak háttér-készletként. A Application Gateway jelenleg az egyetlen támogatott módszer az Azure VMware-alapú virtuális gépeken futó webalkalmazások interneten keresztüli elérhetővé tétele érdekében. A belső felhasználók számára is biztonságosan teheti elérhetővé az alkalmazásokat.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="A forgalom szegmentálásának második szintje a hálózati biztonsági csoportok használatával" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox és az Azure Bastion

Hozzáférés az Azure VMware megoldási környezethez a Jumpbox, amely egy olyan Windows 10 vagy Windows Server rendszerű virtuális gép, amelyet a hub virtuális hálózaton belül, a megosztott szolgáltatás alhálózatán helyeztek üzembe.

Ajánlott biztonsági eljárásként a központi virtuális hálózaton belül üzembe helyezheti [Microsoft Azure megerősített](../bastion/index.yml) szolgáltatást. Az Azure Bastion zökkenőmentes RDP-és SSH-hozzáférést biztosít az Azure-on üzembe helyezett virtuális gépekhez anélkül, hogy nyilvános IP-címeket kellene kiépítenie ezekre az erőforrásokra. Az Azure Bastion szolgáltatás kiépítése után a Azure Portalból férhet hozzá a kiválasztott virtuális géphez. A kapcsolat létrehozása után megnyílik egy új lap, amely megjeleníti a Jumpbox asztalt, és ebből az asztalból elérheti az Azure VMware-megoldás saját Felhőbeli felügyeleti síkon.

> [!IMPORTANT]
> Ne adjon meg nyilvános IP-címet a Jumpbox virtuális géphez, vagy tegye elérhetővé a 3389/TCP portot a nyilvános internethez. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion hub virtuális hálózat" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS megoldási megfontolások

Azure DNS felbontás esetén két lehetőség érhető el:

-   A központban üzembe helyezett Azure Active Directory-(Azure AD-) tartományvezérlőket használja névkiszolgálókként (lásd az [identitással kapcsolatos szempontokat](#identity-considerations)).

-   Azure DNS privát zóna üzembe helyezése és konfigurálása.

A legjobb megoldás, ha az Azure VMware-megoldás, a helyszíni környezet és az Azure megbízható névfeloldását is lehetővé teszi.

Általános tervezési javaslatként használja a meglévő Azure DNS infrastruktúrát (ebben az esetben Active Directory integrált DNS-t), amely legalább két, a hub virtuális hálózatban üzembe helyezett Azure-beli virtuális gépre van telepítve, és a küllős virtuális hálózatokban van konfigurálva, hogy a DNS-beállításokban ezeket a Azure DNS kiszolgálókat használják.

Az Azure saját DNS továbbra is használható, ahol az Azure saját DNS zónája a virtuális hálózatokhoz van társítva, a DNS-kiszolgálók pedig hibrid feloldóként használhatók, feltételes továbbítással az Azure saját DNS-infrastruktúrát használó helyszíni/Azure VMware-megoldáshoz.

Több szempontot figyelembe kell venni a Azure DNS privát zónák esetében:

* Az automatikus regisztrációt engedélyezni kell a Azure DNS számára, hogy automatikusan kezelhesse a küllős virtuális hálózatokon belül üzembe helyezett virtuális gépek DNS-rekordjainak életciklusát.
* Az automatikus regisztrációval rendelkező virtuális hálózatok maximális száma csak egy lehet.
* Azon magánhálózati DNS-zónák maximális száma, amelyekhez a virtuális hálózat 1000, az automatikus regisztráció engedélyezése nélkül lehet kapcsolni.

A helyszíni és az Azure VMware megoldás-kiszolgálókat feltételes továbbítókkal lehet konfigurálni az Azure-beli feloldó virtuális gépekhez az Azure saját DNS zónában.

## <a name="identity-considerations"></a>Identitással kapcsolatos megfontolások

A legjobb megoldás az, ha a központi telepítés során legalább egy AD-tartományvezérlőt üzembe helyez az elosztón a megosztott szolgáltatás alhálózatának használatával, ideális esetben kettőt a zóna által terjesztett módon vagy a virtuális gép rendelkezésre állási csoportján belül. A helyszíni AD-tartomány Azure-ra való kiterjesztését [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain) tekintheti meg.

Emellett helyezzen üzembe egy másik tartományvezérlőt az Azure VMware megoldás oldalán, hogy identitásként és DNS-forrásként működjön a vSphere-környezetben.

A vCenter és az SSO esetében állítsa be az identitás forrását a Azure Portalban az ** \> identitás- \> azonosító források kezelése**területen.

Ajánlott eljárásként [az ad-tartomány integrálása a Azure Active Directorysal](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
