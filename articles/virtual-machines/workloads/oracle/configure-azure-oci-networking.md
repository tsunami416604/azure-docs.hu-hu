---
title: Az Azure ExpressRoute és az Oracle Cloud Infrastructure összekötése | Microsoft Docs
description: Az Azure ExpressRoute és az Oracle Cloud Infrastructure (OCI) FastConnect összekapcsolása a felhőalapú Oracle-alkalmazások megoldásának lehetővé tételéhez
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
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 0e2e16ccc04ff6df80597d646a00c40551e4cfd0
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302049"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Közvetlen kapcsolat beállítása az Azure és az Oracle Cloud Infrastructure között  

[Integrált többfelhős felhasználói élmény](oracle-oci-overview.md) (előzetes verzió) létrehozásához a Microsoft és az Oracle közvetlen kapcsolatot biztosít az Azure és az Oracle Cloud Infrastructure (OCI) között a [ExpressRoute](../../../expressroute/expressroute-introduction.md) és a [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)segítségével. A ExpressRoute és a FastConnect összekapcsolásával az ügyfelek alacsony késést, nagy átviteli sebességet és közvetlen kapcsolatot tapasztalhatnak a két felhő között.

> [!IMPORTANT]
> A Microsoft Azure és a OCI közötti kapcsolat az előzetes verziójú szakaszban található. Az Azure és a OCI közötti alacsony késésű kapcsolat létrehozásához először engedélyeznie kell az Azure-előfizetést ehhez a funkcióhoz. Az előzetes verzióban regisztrálnia kell ezt a rövid [kérdőívet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Az előfizetés regisztrációja után egy e-mailt fog kapni. Nem használhatja a funkciót, amíg meg nem kap egy megerősítő e-mailt. A Microsoft képviselőjének is felveheti a kapcsolatot az előzetes verzióra. Az előzetes verzióhoz való hozzáférés a rendelkezésre állástól függ, és a Microsoft saját belátása szerint korlátozva van. A felmérés befejezése nem garantálja a hozzáférést. Ez az előzetes verzió szolgáltatói szerződés nélkül van megadva, és nem használható éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részletekért tekintse meg a Microsoft Azure előzetes [verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) . A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

Az alábbi képen az összekapcsolással kapcsolatos magas szintű áttekintés látható:

![Felhő közötti hálózati kapcsolatok](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Előfeltételek

* Az Azure és a OCI közötti kapcsolat létesítéséhez aktív Azure-előfizetéssel és aktív OCI-bérlettel kell rendelkeznie.

* A kapcsolódás csak akkor lehetséges, ha egy Azure ExpressRoute-társítási hely közel van a OCI-FastConnect azonos egyenrangú helyéhez vagy ahhoz. Lásd: [régió elérhetősége](oracle-oci-overview.md#region-availability).

* Ehhez az előzetes verzióhoz engedélyezve kell lennie az Azure-előfizetésnek.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Közvetlen kapcsolat konfigurálása a ExpressRoute és a FastConnect között

1. Hozzon létre egy szabványos ExpressRoute-áramkört az Azure-előfizetésben egy erőforráscsoport alatt. 
    * A ExpressRoute létrehozásakor válassza az **Oracle Cloud FastConnect** szolgáltatást szolgáltatóként. ExpressRoute áramkör létrehozásához tekintse meg a [lépésenkénti útmutatót](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Az Azure ExpressRoute-áramkör részletes sávszélesség-beállításokat biztosít, míg a FastConnect 1, 2, 5 vagy 10 GB/s használatát támogatja. Ezért javasoljuk, hogy válasszon egyet a megfelelő sávszélesség-beállítások közül a ExpressRoute területen.

    ![ExpressRoute áramkör létrehozása](media/configure-azure-oci-networking/exr-create-new.png)
1. Jegyezze fel a ExpressRoute **szolgáltatás kulcsát**. A FastConnect-áramkör konfigurálása során meg kell adnia a kulcsot.

    ![ExpressRoute szolgáltatás kulcsa](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > A ExpressRoute díjait a ExpressRoute áramkör kiépítés után számoljuk fel (még akkor is, ha a **szolgáltató állapota** nincs **kiépítve**).

1. Két magánhálózati IP-címtartomány kifaragása, amelyek nincsenek átfedésben az Azure Virtual Network vagy a OCI Virtual Cloud Network IP-címtartomány esetében. A rendszer az első IP-címtartományt használja elsődleges címterületként, a második IP-címtartományt pedig másodlagos címterületként. Jegyezze fel a címeket, amelyekre szüksége van a FastConnect-áramkör konfigurálásakor.
1. Hozzon létre egy dinamikus útválasztási átjárót (DRG). Erre szüksége lesz a FastConnect-áramkör létrehozásakor. További információt a [dinamikus útválasztási átjáró](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) dokumentációjában talál.
1. Hozzon létre egy FastConnect áramkört az Oracle-bérlő alatt. További információkért tekintse meg az [Oracle dokumentációját](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * A FastConnect konfigurálása területen válassza a **Microsoft Azure: ExpressRoute** lehetőséget szolgáltatóként.
    * Válassza ki az előző lépésben kiépített dinamikus útválasztási átjárót.
    * Válassza ki a kiépíteni kívánt sávszélességet. Az optimális teljesítmény érdekében a sávszélességnek meg kell egyeznie a ExpressRoute áramkör létrehozásakor kiválasztott sávszélességgel.
    * A **szolgáltatói szolgáltatás kulcsában**illessze be a ExpressRoute szolgáltatás kulcsát.
    * Az első/30 magánhálózati IP-címtartomány kifaragva az **elsődleges BGP IP-címére** és a **másodlagos BGP IP** -címéhez tartozó második/30 magánhálózati IP-címtartomány használatára.
        * Rendelje hozzá a két tartomány első használható címét az Oracle BGP IP-címéhez (elsődleges és másodlagos) és a második címet az ügyfél BGP IP-címéhez (FastConnect perspektívából). Az első használható IP-cím a/30 címtartomány második IP-címe (az első IP-cím a Microsoft számára van fenntartva).
    * Kattintson a  **Create** (Létrehozás) gombra.
1. A FastConnect a dinamikus útválasztási átjárón keresztül, az útválasztási táblázat használatával kapcsolja össze a virtuális felhőalapú hálózattal.
1. Navigáljon az Azure-hoz, és ellenőrizze, hogy a ExpressRoute áramkör **szolgáltatói állapota** **kiépítve** értékre változott-e, és hogy van-e kiépítve egy **Azure Private** típusú társ. Ez a következő lépések előfeltétele.

    ![ExpressRoute-szolgáltató állapota](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kattintson az **Azure Private** -partnerre. A rendszer a FastConnect áramkör beállításakor megadott információk alapján automatikusan beállítja a társítás részleteit.

    ![Privát egyenrangú beállítások](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtuális hálózat összekötése a ExpressRoute

1. Ha még nem tette meg, hozzon létre egy virtuális hálózatot és egy virtuális hálózati átjárót. Részletekért tekintse meg a [lépésenkénti útmutatót](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Állítsa be a kapcsolatot a virtuális hálózati átjáró és a ExpressRoute áramkör között úgy, hogy végrehajtja a [Terraform parancsfájlt](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) , vagy a PowerShell-parancs végrehajtásával [konfigurálja a ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

A hálózati konfiguráció befejezését követően ellenőrizheti a konfiguráció érvényességét. ehhez kattintson az **ARP-rekordok beolvasása** elemre, majd a Azure Portal ExpressRoute privát társítás paneljén található **útválasztási táblázat lekérése** lehetőségre.

## <a name="automation"></a>Automatizálás

A Microsoft Terraform parancsfájlokat hozott létre a hálózati összekötő automatikus üzembe helyezésének engedélyezéséhez. A Terraform parancsfájloknak a végrehajtás előtt hitelesíteniük kell az Azure-ban, mert az Azure-előfizetéshez megfelelő engedélyekkel kell rendelkezniük. A hitelesítés [Azure Active Directory egyszerű szolgáltatásnév](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) vagy az Azure CLI használatával végezhető el. További információkért tekintse meg a [Terraform dokumentációját](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

A Terraform-parancsfájlok és a kapcsolódó dokumentáció a kapcsolaton keresztüli kapcsolódás központi telepítéséhez ebben a [GitHub-adattárban](https://aka.ms/azureociinterconnecttf)található.

## <a name="monitoring"></a>Figyelés

Ha az ügynököket mindkét felhőkre telepíti, használhatja az Azure [Network Performance monitor (NPM)](../../../expressroute/how-to-npm.md) szolgáltatást a végpontok közötti hálózat teljesítményének figyelésére. A NPM megkönnyíti a hálózati problémák azonosítását, és segít a hibák elhárításában.

## <a name="delete-the-interconnect-link"></a>Az összekötő hivatkozásának törlése

Az összekötő törléséhez a következő lépéseket kell követni a megadott sorrendben. Ha ezt nem teszi meg, a "sikertelen állapot" ExpressRoute áramkört eredményez.

1. Törölje a ExpressRoute-kapcsolatokat. Törölje a kapcsolatokat úgy, hogy a **Törlés** ikonra kattint a kapcsolatok lapján. További információkért tekintse meg a [ExpressRoute dokumentációját](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Törölje az Oracle-FastConnect az Oracle Cloud Console-ból.
1. Az Oracle FastConnect-áramkör törlése után törölheti az Azure ExpressRoute áramkört.

Ezen a ponton a törlési és megszüntetési folyamat befejeződött.

## <a name="next-steps"></a>Következő lépések

* A OCI és az Azure közötti Felhőbeli kapcsolattal kapcsolatos további információkért tekintse meg az [Oracle dokumentációját](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* A [Terraform-parancsfájlok](https://aka.ms/azureociinterconnecttf) használatával az Azure-on keresztül megcélozhatja az Oracle-alkalmazások infrastruktúráját, és konfigurálhatja a hálózati összekötőt. 
