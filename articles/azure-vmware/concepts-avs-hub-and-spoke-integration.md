---
title: Koncepció – az Azure VMware-megoldás (AVS) üzembe helyezése egy sugaras architektúrában
description: Ismerje meg az Azure VMware-megoldás (AVS) üzembe helyezésének javaslatait egy meglévő vagy egy új, az Azure-ban küllő architektúrában.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85375047"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Az Azure VMware-megoldás (AVS) integrálása egy sugaras architektúrába

Ebben a cikkben javaslatot teszünk az Azure VMware-megoldások (AVS) üzembe helyezésére egy meglévő vagy egy új, az Azure-ban működő [architektúrában](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) . 

A hub és küllős forgatókönyv hibrid felhőalapú környezetet feltételez a következő munkaterhelésekkel:

* Natív Azure a IaaS vagy a Pásti Services használatával
* AVS 
* helyszíni vSphere

## <a name="architecture"></a>Architektúra

A *hub* egy Azure-Virtual Network, amely központi kapcsolódási pontként működik a helyszíni és az AVS Private Cloud-hoz. A *küllők* olyan virtuális hálózatok, amelyek a központtal együttműködve lehetővé teszik a virtuális hálózat közötti kommunikációt.

A helyszíni adatközpont, az AVS Private Cloud és a hub közötti forgalom ExpressRoute-kapcsolatokon keresztül halad. A küllős virtuális hálózatok általában IaaS-alapú számítási feladatokat tartalmaznak, de olyan Pásti-szolgáltatásokat is tartalmazhatnak, mint például a [app Service Environment](../app-service/environment/intro.md), amelyek közvetlen integrációját Virtual Network vagy más, az [Azure Private linktel](https://docs.microsoft.com/azure/private-link/) rendelkező Pásti szolgáltatásokkal együtt. 

Az ábrán egy példa látható az Azure-ban a helyszíni és az AVS-alapú ExpressRoute-kapcsolaton keresztül a sugaras üzembe helyezésre.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Az AVS hub és a küllős integráció üzembe helyezése":::




Az architektúra a következő fő összetevőkből áll:

-   Helyszíni **hely:** A helyszíni adatközpont (ok) egy expressz útvonalon keresztül csatlakozik az Azure-hoz.

-   **AVS Private Cloud:** Az AVS-SDDC egy vagy több vSphere-fürtből lettek létrehozva, amelyek mindegyike legfeljebb 16 csomóponttal rendelkezik.

-   **ExpressRoute-átjáró:** Lehetővé teszi a kommunikációt az AVS Private Cloud, a helyszíni hálózat, a hub virtuális hálózat megosztott szolgáltatásai és a küllős virtuális hálózatokon futó munkaterhelések között.

    > [!NOTE]
    > **S2S VPN-megfontolások:** Az AVS éles környezetben történő üzembe helyezése esetén az Azure S2S a HCX hálózati követelményei miatt nem támogatott. Azonban a HCX-t nem igénylő PoC-vagy nem éles környezetekben is használható.

-   **Hub virtuális hálózata:** A helyszíni hálózathoz és az AVS privát felhőhöz való kapcsolódás központi pontja.

-   **Küllős virtuális hálózat**

    -   **IaaS küllő:** Egy küllős IaaS az Azure IaaS-alapú számítási feladatait fogja tárolni, beleértve a virtuális gépek rendelkezésre állási készleteit és a virtuálisgép-méretezési csoportokat, valamint a megfelelő hálózati összetevőket.

    -   **Péter küllő:** A Péter beszélt a privát [végpontok](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) és a [privát kapcsolatok](https://docs.microsoft.com/azure/private-link/private-link-overview)révén a magánjellegű címzést használó Azure Pásti-szolgáltatásokat.

-   **Azure Firewall:** Központi darabként működik, amely a küllők, a helyszíni és az AVS közötti adatforgalom szegmentálására szolgál.

-   **Application Gateway:** Az Azure IaaS-/Pásti-vagy AVS-alapú virtuális gépeken futtatott webalkalmazások közzététele és védelme. Integrálható más szolgáltatásokkal, például API Managementokkal.

## <a name="network-and-security-considerations"></a>Hálózati és biztonsági megfontolások

A ExpressRoute-kapcsolatok lehetővé teszik a helyszíni, az AVS és az Azure hálózati háló közötti forgalom áramlását. Az AVS a [ExpressRoute Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) használja a kapcsolat megvalósításához.

A helyszíni kapcsolat ExpressRoute Global Reach is használhat, de nem kötelező.

* **Helyszíni – AVS forgalom**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Helyszíni – AVS forgalom":::


* **AVS – hub VNET-forgalom**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="AVS – hub virtuális hálózati forgalom":::


Az AVS-hálózatkezeléssel és az összekapcsolással kapcsolatos fogalmakról az [AVS termékdokumentációjában](https://docs.microsoft.com/azure/azure-vmware/concepts-networking)talál további információt.

### <a name="traffic-segmentation"></a>Forgalom szegmentálása

[Azure Firewall](https://docs.microsoft.com/azure/firewall/) a hub virtuális hálózatán üzembe helyezett hub és küllős topológia központi eleme. Használjon Azure Firewall vagy egy másik Azure által támogatott hálózati virtuális készüléket a forgalmi szabályok létrehozásához és a különböző küllők, helyszíni és AVS számítási feladatok közötti kommunikáció szegmentálásához.

Hozzon létre útválasztási táblákat a Azure Firewall felé irányuló forgalom irányításához.  A küllős virtuális hálózatok esetében hozzon létre egy útvonalat, amely a Azure Firewall belső felületének alapértelmezett útvonalát állítja be, így ha a Virtual Network munkaterhelésének el kell érnie a tűzfal által kiértékelhető AVS-címtartományt, és alkalmaznia kell a megfelelő forgalmi szabályt, hogy engedélyezze vagy megtagadja.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Útválasztási táblázatok létrehozása a Azure Firewallhoz való közvetlen forgalomhoz":::


> [!IMPORTANT]
> A **GatewaySubnet** beállításban a 0.0.0.0/0 előtaggal rendelkező útvonal nem támogatott.

Adja meg a megfelelő útválasztási táblázatban megadott hálózatok útvonalait. Az olyan útvonalak például, amelyekkel az AVS-felügyelet és a számítási feladatok IP-előtagjai elérhetők a helyszíni környezetből, és fordítva, a helyszíni rendszerről az AVS Private Cloud felé irányuló összes forgalmat a Azure Firewall használatával irányíthatja.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Adott hálózatok útvonalának beállítása a megfelelő útválasztási táblázatban":::

Egy második szintű forgalmi szegmentálás a küllők és a hub hálózati biztonsági csoportjaival, hogy egy részletesebb forgalmi szabályzatot hozzon létre. 


### <a name="application-gateway"></a>Application Gateway

Az Azure Application Gateway v1-es és v2-es verziója az AVS-alapú virtuális gépeken futó webalkalmazásokkal lett tesztelve háttér-készletként. A Application Gateway jelenleg az egyetlen támogatott módszer az AVS-alapú virtuális gépeken futó webalkalmazások interneten való elérhetővé tétele érdekében. A belső felhasználók számára is biztonságosan teheti elérhetővé az alkalmazásokat.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="A forgalom szegmentálásának második szintje a hálózati biztonsági csoportok használatával":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox és az Azure Bastion

A Jumpbox-mel férhet hozzá az AVS-környezethez, amely egy olyan Windows 10 vagy Windows Server rendszerű virtuális gép, amelyet a hub virtuális hálózatán belül telepítettek a megosztott szolgáltatás alhálózatán.

Ajánlott biztonsági eljárásként a központi virtuális hálózaton belül üzembe helyezheti [Microsoft Azure megerősített](https://docs.microsoft.com/azure/bastion/) szolgáltatást. Az Azure Bastion zökkenőmentes RDP-és SSH-hozzáférést biztosít az Azure-on üzembe helyezett virtuális gépekhez anélkül, hogy nyilvános IP-címeket kellene kiépítenie ezekre az erőforrásokra. Az Azure Bastion szolgáltatás kiépítése után a Azure Portalból férhet hozzá a kiválasztott virtuális géphez. A kapcsolat létrehozása után megnyílik egy új lap, amely megjeleníti a Jumpbox asztalt, és ebből az asztalból elérheti az AVS Private Cloud Management Plant.

> [!IMPORTANT]
> Ne adjon meg nyilvános IP-címet a Jumpbox virtuális géphez, vagy tegye elérhetővé a 3389/TCP portot a nyilvános internethez. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion hub virtuális hálózat":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS megoldási megfontolások

Azure DNS felbontás esetén két lehetőség érhető el:

-   A központban üzembe helyezett Azure Active Directory-(Azure AD-) tartományvezérlőket használja névkiszolgálókként (lásd az [identitással kapcsolatos szempontokat](#identity-considerations)).

-   Azure DNS privát zóna üzembe helyezése és konfigurálása.

A legjobb megoldás az, hogy kombinálja mindkettőt, hogy megbízható névfeloldást biztosítson az AVS, a helyszíni és az Azure számára.

Általános tervezési javaslatként használja a meglévő Azure DNS infrastruktúrát (ebben az esetben Active Directory integrált DNS-t), amely legalább két, a hub virtuális hálózatban üzembe helyezett Azure-beli virtuális gépre van telepítve, és a küllős virtuális hálózatokban van konfigurálva, hogy a DNS-beállításokban ezeket a Azure DNS kiszolgálókat használják.

Az Azure saját DNS továbbra is használható, ahol az Azure saját DNS zóna a virtuális hálózatokhoz van társítva, a DNS-kiszolgálók pedig hibrid feloldóként használatosak, és feltételes továbbítással működnek a helyszíni/AVS-t futtató DNS-nevekre, és az ügyfél Azure saját DNS infrastruktúráját használják.

Több szempontot figyelembe kell venni a Azure DNS privát zónák esetében:

* Az automatikus regisztrációt engedélyezni kell a Azure DNS számára, hogy automatikusan kezelhesse a küllős virtuális hálózatokon belül üzembe helyezett virtuális gépek DNS-rekordjainak életciklusát.
* Az automatikus regisztrációval rendelkező virtuális hálózatok maximális száma csak egy lehet.
* Azon magánhálózati DNS-zónák maximális száma, amelyekhez a virtuális hálózat 1000, az automatikus regisztráció engedélyezése nélkül lehet kapcsolni.

A helyszíni és az AVS-kiszolgálókat feltételes továbbítókkal lehet konfigurálni az Azure-beli feloldó virtuális gépekhez az Azure saját DNS zónában.

## <a name="identity-considerations"></a>Identitással kapcsolatos megfontolások

A legjobb megoldás az, ha a központi telepítés során legalább egy AD-tartományvezérlőt üzembe helyez az elosztón a megosztott szolgáltatás alhálózatának használatával, ideális esetben kettőt a zóna által terjesztett módon vagy a virtuális gép rendelkezésre állási csoportján belül. A helyszíni AD-tartomány Azure-ra való kiterjesztését [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) tekintheti meg.

Emellett helyezzen üzembe egy másik tartományvezérlőt az AVS oldalon, hogy identitásként és DNS-forrásként működjön a vSphere-környezetben.

A vCenter és az SSO esetében állítsa be az identitás forrását a Azure Portalban az ** \> identitás- \> azonosító források kezelése**területen.

Ajánlott eljárásként [az ad-tartomány integrálása a Azure Active Directorysal](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


