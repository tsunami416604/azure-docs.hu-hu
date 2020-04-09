---
title: Csatlakoztassa az Azure ExpressRoute-ot az Oracle Cloud Infrastructure-hez | Microsoft dokumentumok
description: Csatlakoztassa az Azure ExpressRoute-ot az Oracle Cloud Infrastructure (OCI) FastConnect szolgáltatással a felhőközi Oracle alkalmazásmegoldások engedélyezéséhez
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: rogirdh
ms.openlocfilehash: cd0b8a50d25cd8d1a66a8eb98e54ec231aa2c62f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878714"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Közvetlen kapcsolat beállítása az Azure és az Oracle Cloud Infrastructure között  

Az [integrált többfelhős élmény](oracle-oci-overview.md)létrehozásához a Microsoft és az Oracle közvetlen kapcsolatot kínál az Azure és az Oracle Cloud Infrastructure (OCI) között az [ExpressRoute](../../../expressroute/expressroute-introduction.md) és a [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)segítségével. Az ExpressRoute és a FastConnect összekapcsoláson keresztül az ügyfelek alacsony késleltetést, nagy átviteli sebességű, privát közvetlen kapcsolatot tapasztalhatnak a két felhő között.

> [!IMPORTANT]
> Az Oracle tanúsítja, hogy ezek az alkalmazások 2020 májusig futnak az Azure/Oracle Cloud interconnect megoldás használatával.
> * E-Business lakosztály
> * JD Edwards Vállalat
> * Peoplesoft
> * Oracle kiskereskedelmi alkalmazások
> * Oracle Hyperion pénzügyi menedzsment

Az alábbi képen az összekapcsolás magas szintű áttekintése látható:

![Felhőközi hálózati kapcsolat](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Előfeltételek

* Az Azure és az OCI közötti kapcsolat létrehozásához aktív Azure-előfizetéssel és aktív OCI-bérleti díjral kell rendelkeznie.

* A kapcsolat csak akkor lehetséges, ha egy Azure ExpressRoute-társviszony-létesítési hely az OCI FastConnect közelében vagy ugyanazon a társviszony-létesítési helyen található. Lásd: [A régió elérhetősége](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Közvetlen kapcsolat konfigurálása az ExpressRoute és a FastConnect között

1. Hozzon létre egy szabványos ExpressRoute-csoportot az Azure-előfizetésében egy erőforráscsoport alatt. 
    * Az ExpressRoute létrehozásakor válassza az **Oracle Cloud FastConnect** szolgáltatást szolgáltatóként. ExpressRoute-kapcsolat létrehozásához tekintse meg a [részletes útmutatót.](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)
    * Az Azure ExpressRoute-áramkör részletes sávszélesség-beállításokat biztosít, míg a FastConnect támogatja az 1, 2, 5 vagy 10 Gbps-t. Ezért ajánlott az ExpressRoute csoportban a megfelelő sávszélesség-beállítások egyikét választani.

    ![ExpressRoute-kapcsolat létrehozása](media/configure-azure-oci-networking/exr-create-new.png)
1. Jegyezze fel az **ExpressRoute-szolgáltatás kulcsát.** A kulcsnak meg kell adnia a kulcsot a FastConnect-kapcsolat konfigurálása közben.

    ![ExpressRoute-szolgáltatás kulcsa](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Az ExpressRoute-díjakért az ExpressRoute-kapcsolat kiépítése után kell fizetnie (még akkor is, ha a **szolgáltató állapota** **nincs kiépítve).**

1. Faragni két privát IP-címterek /30 mindegyik, amelyek nem fedik át az Azure virtuális hálózat vagy OCI virtuális hálózati IP-cím tér. Az első IP-címterületre elsődleges címtérként, a második IP-címterületet másodlagos címtérként fogjuk hivatkozni. Jegyezze fel a címeket, amelyekre a FastConnect-kapcsolat konfigurálásakor szüksége van.
1. Dinamikus útválasztási átjáró (DRG) létrehozása. Erre a FastConnect-kapcsolat létrehozásakor lesz szüksége. További információt a [Dinamikus útválasztási átjáró dokumentációjában talál.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Hozzon létre egy FastConnect-áramkört az Oracle-bérlő alatt. További információt az [Oracle dokumentációjában](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)talál.
  
    * A FastConnect-konfiguráció csoportban válassza a **Microsoft Azure: ExpressRoute** szolgáltatót.
    * Válassza ki az előző lépésben kiépített dinamikus útválasztási átjárót.
    * Válassza ki a kiépítendő sávszélességet. Az optimális teljesítmény érdekében a sávszélességnek meg kell egyeznie az ExpressRoute-kapcsolat létrehozásakor kiválasztott sávszélességgel.
    * A **Szolgáltatószolgáltatás-kulcsban**illessze be az ExpressRoute szolgáltatáskulcsot.
    * Használja az első /30 privát IP-címteret, amelyet az **elsődleges BGP IP-címhez** egy előző lépésben, a második /30 privát IP-címteret pedig a **másodlagos BGP IP-címhez** kell használni.
        * Rendelje hozzá az Oracle BGP IP-cím (elsődleges és másodlagos) két tartományának első használható címét, a második címet pedig az ügyfél BGP IP-címéhez (FastConnect perspektívából). Az első használható IP-cím a második IP-cím a /30 címterületen (az első IP-címet a Microsoft lefoglalta).
    * Kattintson **a Létrehozás gombra.**
1. A FastConnect teljes összekapcsolása az Oracle-bérlő n belüli virtuális felhőhálózattal a Dynamic Routing Gateway segítségével, az Útvonaltábla használatával.
1. Keresse meg az Azure-t, és győződjön meg arról, hogy az ExpressRoute-kapcsolat **szolgáltatói állapota** **kiépítettre** változott, és hogy egy **Azure private** típusú társviszony-létesítési mód lett kiépítve. Ez a következő lépések előfeltétele.

    ![ExpressRoute-szolgáltató állapota](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kattintson az **Azure privát** társviszony-létesítés. Látni fogja, hogy a társviszony-létesítési adatok automatikusan konfigurálva vannak a FastConnect-kapcsolat beállításakor megadott adatok alapján.

    ![Privát társviszony-létesítési beállítások](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtuális hálózat csatlakoztatása az ExpressRoute-hoz

1. Hozzon létre egy virtuális hálózati és virtuális hálózati átjáró, ha még nem tette meg. A részleteket a [részletes útmutatóban találja.](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
1. Állítsa be a kapcsolatot a virtuális hálózati átjáró és az ExpressRoute-kapcsolat között a [Terraform parancsfájl](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) végrehajtásával vagy a PowerShell parancs nak az [ExpressRoute FastPath konfigurálása](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)parancsával.

Miután befejezte a hálózati konfigurációt, ellenőrizheti a konfiguráció érvényességét az **ARP-rekordok leválasztása** és az **Útvonaltábla betöltése** az Azure Portal ExpressRoute privát társviszony-létesítési panel alatt.

## <a name="automation"></a>Automation

A Microsoft terraform parancsfájlokat hozott létre a hálózati összeköttetés automatikus telepítésének lehetővé tétele érdekében. A Terraform-parancsfájlok kell hitelesíteni az Azure-ral a végrehajtás előtt, mert megfelelő engedélyeket igényelnek az Azure-előfizetéshez. A hitelesítés az [Azure Active Directory egyszerű szolgáltatásvagy](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az Azure CLI használatával hajtható végre. További információt a [Terraform dokumentációjában](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)talál.

A Terraform parancsfájlok és a kapcsolódó dokumentáció az inter-connect telepítéséhez ebben a [GitHub-tárházban](https://aka.ms/azureociinterconnecttf)találhatók.

## <a name="monitoring"></a>Figyelés

Az ügynökök telepítése mindkét felhőre, az Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) segítségével figyelheti a végpontok nélküli hálózat teljesítményét. Az NPM segít a hálózati problémák gyors azonosításában, és segít azok kiküszöbölésben.

## <a name="delete-the-interconnect-link"></a>Az összekötő hivatkozás törlése

Az összeköttetés törléséhez a következő lépéseket kell követni, a megadott sorrendben. Ennek elmulasztása "sikertelen állapotú" ExpressRoute-áramkört eredményez.

1. Törölje az ExpressRoute-kapcsolatot. A kapcsolat törléséhez kattintson a kapcsolat lapjának **Törlés** ikonjára. További információt az [ExpressRoute dokumentációjában](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)talál.
1. Törölje az Oracle FastConnect szolgáltatást az Oracle Cloud Console konzolról.
1. Az Oracle FastConnect-kapcsolat törlése után törölheti az Azure ExpressRoute-áramkört.

Ezen a ponton a törlési és megszüntetési folyamat befejeződött.

## <a name="next-steps"></a>További lépések

* Az OCI és az Azure közötti felhőközi kapcsolatról az [Oracle dokumentációjában](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)olvashat bővebben.
* [Terraform parancsfájlok](https://aka.ms/azureociinterconnecttf) segítségével telepítheti az infrastruktúrát a célzott Oracle-alkalmazásokhoz az Azure-on keresztül, és konfigurálhatja a hálózati összeköttetést. 
