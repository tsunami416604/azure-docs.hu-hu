---
title: Csatlakozás az Azure ExpressRoute az Oracle-felhő-infrastruktúra |} A Microsoft Docs
description: Az Azure ExpressRoute összekapcsolása Oracle Felhőbeli infrastruktúrát (OCI) FastConnect engedélyezéséhez a több felhőre kiterjedő Oracle alkalmazási megoldások
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: ce1f86f7594692c797aaca5008b211b96ba81fbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453150"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>A közvetlen összekapcsolása az Azure és az Oracle-felhő-infrastruktúra beállítása  

Hozhat létre egy [integrált többfelhős felhasználói felület](oracle-oci-overview.md) (előzetes verzió), a Microsoft és Oracle kínálnak az Azure és az Oracle Felhőbeli infrastruktúrát (OCI) keresztül közvetlen összekapcsolása [ExpressRoute](../../../expressroute/expressroute-introduction.md) és [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Az ExpressRoute- és FastConnect összekapcsolása révén ügyfeleink tapasztalhatnak, alacsony késleltetésű, nagy átviteli sebességet, a két felhők közötti privát közvetlen kapcsolódás.

> [!IMPORTANT]
> Az előzetes fázisban van a Microsoft Azure és a OCI közötti kapcsolat. Ahhoz, hogy az Azure és a OCI közel valós idejű összekapcsolását, az Azure-előfizetés és OCI bérlős kell ezt a képességet az engedélyezési listán.

Az alábbi képen látható, az összekapcsolás magas szintű áttekintését:

![A felhőbe irányuló hálózati kapcsolat](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-ban és OCI közötti kapcsolatot létesíteni, rendelkeznie kell egy aktív Azure-előfizetéssel, és a egy aktív OCI bérlős.

* Kapcsolat csak akkor lehetséges, ahol az Azure ExpressRoute-társviszony-létesítési helyszínen közel kerülhet a vagy a a OCI FastConnect társviszony-létesítési ugyanazon a helyen van. Lásd: [előzetes verzió korlátozásai](oracle-oci-overview.md#preview-limitations).

* Az Azure-előfizetés szerepel az engedélyezési listán előzetes képességhez kell lennie. A Microsoft képviselőjétől engedélyezze ezt a funkciót, az előfizetéséhez.

* A OCI bérlős szerepel az engedélyezési listán előzetes képességhez kell lennie. Részletek az Oracle képviselőjétől kaphat.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>ExpressRoute- és FastConnect közötti közvetlen kapcsolat konfigurálása

1. Hozzon létre egy standard ExpressRoute-kapcsolatcsoport egy erőforráscsoportba tartozó Azure-előfizetéséhez. 
    * Az ExpressRoute létrehozásakor válassza ki a **Oracle Felhőbeli FastConnect** a szolgáltató. Hozzon létre egy ExpressRoute-kapcsolatcsoportot, tekintse meg a [lépésenkénti útmutató](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Az Azure ExpressRoute-kapcsolatcsoport részletes sávszélesség beállításokat biztosít, mivel a FastConnect támogatja az 1, 2, 5 vagy 10 GB/s. Ezért ajánlott ezek alatt ExpressRoute egyező sávszélesség-lehetőségek közül.

    ![ExpressRoute-kapcsolatcsoport létrehozása](media/configure-azure-oci-networking/exr-create-new.png)
1. Jegyezze fel az ExpressRoute **Szolgáltatáskulcs**. Meg kell adnia a kulcsot a FastConnect kapcsolatcsoport konfigurálása közben.

    ![Az ExpressRoute szolgáltatás kulcsa](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Díjköteles az ExpressRoute költségeire, amint az ExpressRoute-kapcsolatcsoport ki van építve (akkor is, ha a **szolgáltató állapota** van **nincs kiépítve**).

1. Különítsen el két magánhálózati IP-címterek / 30 minden egyes, amely nincs átfedésben az Azure virtuális hálózat vagy OCI felhőalapú virtuális hálózati IP-címtér. Hivatkozik az első IP-címtér elsődleges címteret, és a második IP-címtér, másodlagos címtér. Jegyezze fel a címeket, mert szüksége lesz a FastConnect kapcsolatcsoport konfigurálásakor.
1. Hozzon létre egy dinamikus útválasztású átjárót (DARB). Szüksége lesz a FastConnect kapcsolatcsoport létrehozása során. További információkért lásd: a [dinamikus útválasztási átjárót](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) dokumentációját.
1. Hozzon létre egy FastConnect kapcsolatcsoportot, az Oracle-bérlőhöz. További információkért lásd: a [Oracle-dokumentáció](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * FastConnect beállítás csoportban **Microsoft Azure: Az ExpressRoute** szolgáltatója.
    * Válassza ki a dinamikus útválasztási átjárót, amely az Ön által üzembe helyezett az előző lépésben.
    * Válassza ki a sávszélesség, ki kell építeni. Az optimális teljesítmény érdekében a sávszélesség egyeznie kell a sávszélesség, az ExpressRoute-kapcsolatcsoport létrehozása során kiválasztott.
    * A **szolgáltató Szolgáltatáskulcs**, illessze be az ExpressRoute szolgáltatás kulcsot.
    * Az előző lépésben faragottnak privát IP-címteret használja az első/30 a **elsődleges BGP IP-cím** és a második/30 a privát IP-címteret a **másodlagos BGP IP-Címét** címet.
        * A első gyakorlatot cím hozzárendelése a két tartomány Oracle BGP IP-cím (elsődleges és másodlagos) és a második címet az ügyfél BGP IP-címét (a FastConnect szempontjából). Első gyakorlatot IP-címe, a második IP-cím a/30 címtér (az első IP-címét a Microsoft számára van fenntartva).
    * Kattintson a **Create** (Létrehozás) gombra.
1. Fejezze be a FastConnect összekapcsolása a felhőbeli virtuális hálózati dinamikus útválasztási átjárót, útválasztási táblázatot használja az Oracle-bérlőből alatt.
1. Keresse meg az Azure-ba, és ellenőrizze, hogy a **szolgáltató állapota** az expressroute kapcsolatcsoport változott **kiépített** , és hogy típusú társviszony-létesítés **Azure-beli privát** lett kiépítve. Ez a előfeltétele a következő lépéseket.

    ![Az ExpressRoute-szolgáltató állapota](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kattintson a **Azure-beli privát** társviszony-létesítés. Látni fogja a társviszony-létesítés részleteinek beállításakor a FastConnect kapcsolatcsoport mentése megadott információk alapján automatikusan konfigurálva.

    ![Magánhálózati társviszony beállításai](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtuális hálózat csatlakoztatása ExpressRoute

1. Hozzon létre egy virtuális hálózatot és virtuális hálózati átjáró, ha még nem tette. További információkért lásd: a [lépésenkénti útmutató](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Állítsa be a kapcsolatot a virtuális hálózati átjáró és az ExpressRoute-kapcsolatcsoport között úgy, hogy végrehajtja a [Terraform parancsfájl](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) vagy a PowerShell-parancs végrehajtásával [konfigurálása ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Miután végzett a hálózati konfigurációt, a konfiguráció érvényességét kattintva ellenőrizheti **ARP-Rekodok első** és **Get útvonaltábla** alapján az ExpressRoute privát társviszony-létesítési panelen az Azure Portalon.

## <a name="automation"></a>Automation

A Microsoft hozott létre ahhoz, hogy a hálózati memóriamodulok közötti automatikus telepítése a Terraform parancsfájlokat. A Terraform parancsfájlok végrehajtási, mielőtt Azure-hitelesítésre van szükség, mert azok megkövetelik, hogy az Azure-előfizetést a megfelelő engedélyekkel. Hitelesítés használatával végezheti el egy [Azure Active Directory egyszerű szolgáltatás](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) vagy az Azure CLI használatával. További információkért lásd: a [Terraform dokumentáció](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

A Terraform parancsfájlok és üzembe helyezéséhez a inter-connect kapcsolódó dokumentáció megtalálható ez [GitHub-adattár](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Figyelés

Telepítse az ügynököket mindkét a felhőben, kihasználhatja az Azure [Network Performance monitort (NPM)](../../../expressroute/how-to-npm.md) a végpontok közötti hálózati teljesítményének figyeléséhez. Az NPM révén könnyen azonosíthatja a hálózati problémákat, és azok elhárításához nyújt segítséget.

## <a name="delete-the-interconnect-link"></a>A memóriamodulok közötti kapcsolat törlése

A memóriamodulok közötti törléséhez a következő lépéseket kell követnie, a megadott meghatározott sorrendben. Ezt egy "hibás állapotban" ExpressRoute-kapcsolatcsoport eredményez.

1. Az ExpressRoute-kapcsolat törlése. A kapcsolat törlése gombra kattintva a **törlése** ikonra a kapcsolat az oldalon. További információkért lásd: a [az ExpressRoute dokumentációja](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Az Oracle FastConnect törölje az Oracle Cloud konzoljáról.
1. Az Oracle FastConnect-kapcsolatcsoport törlése után törölheti az Azure ExpressRoute-kapcsolatcsoporthoz.

Ezen a ponton a Törlés és -megszüntetés folyamat befejeződött.

## <a name="next-steps"></a>További lépések

* A több felhőre kiterjedő kapcsolat OCI és az Azure közötti kapcsolatos további információkért lásd: a [Oracle-dokumentáció](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Használat [Terraform parancsfájlok](https://aka.ms/azureociinterconnecttf) központi telepítése a célzott Oracle-alkalmazásokhoz tartozó infrastruktúra Azure keresztül, és a hálózati összekötöje konfigurálása. 