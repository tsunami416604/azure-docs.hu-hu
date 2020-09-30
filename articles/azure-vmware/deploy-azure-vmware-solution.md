---
title: Azure VMware-megoldás üzembe helyezése és konfigurálása
description: Megtudhatja, hogyan használhatja a tervezési szakaszban összegyűjtött információkat az Azure VMware-megoldás saját felhőbe történő üzembe helyezéséhez.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 08d2d48820505dad9bba74fe3ac84f45525f4525
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583311"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware-megoldás üzembe helyezése és konfigurálása

Ebben a cikkben a [tervezési szakaszban](production-ready-deployment-steps.md) található információkat fogja használni az Azure VMware-megoldás üzembe helyezéséhez. Ha még nem adta meg az adatokat, lépjen vissza a [tervezés szakaszra](production-ready-deployment-steps.md) a folytatás előtt.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Azure VMware-megoldás üzembe helyezése

Használja az [Azure VMware megoldás üzembe helyezésének megtervezése](production-ready-deployment-steps.md) című cikkben összegyűjtött információkat:

>[!NOTE]
>Az Azure VMware-megoldás üzembe helyezéséhez az előfizetés minimális közreműködői szintjén kell lennie.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]



## <a name="create-the-jump-box"></a>A Jump Box létrehozása

>[!IMPORTANT]
>Ha a **saját felhő létrehozása** képernyő kezdeti kiépítési lépése során üresen hagyta a **Virtual Network** lehetőséget, a szakasz folytatása **előtt** fejezze be a [hálózatkezelés konfigurálása a VMware Private Cloud](tutorial-configure-networking.md) oktatóanyaghoz című témakört.  



Az Azure VMware-megoldás üzembe helyezése után létre kell hoznia a virtuális hálózat vCenter és NSX csatlakozó ugrási mezőjét. Miután konfigurálta a ExpressRoute áramköröket és a ExpressRoute Global Reach, a Jump Box nem szükséges.  Az Azure VMware-megoldásban azonban hasznos lehet a vCenter és a NSX elérése.  


:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Az Azure VMware-megoldás Jump Box létrehozása":::

Ha virtuális gépet (VM) szeretne létrehozni a [telepítési folyamat részeként azonosított vagy létrehozott](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution)virtuális hálózaton, kövesse az alábbi utasításokat: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Kapcsolódás virtuális hálózathoz a ExpressRoute használatával

Ha nem definiált virtuális hálózatot az üzembe helyezési lépésben, és a szándéka az Azure VMware-megoldás ExpressRoute meglévő ExpressRoute-átjáróhoz való összekötése, kövesse az alábbi lépéseket.

Ha már definiált egy virtuális hálózatot az Azure-beli üzembe helyezési képernyőn, ugorjon a következő szakaszra.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Meghirdetett hálózati útvonalak ellenőrzése

A Jump Box abban a virtuális hálózaton található, ahol az Azure VMware megoldás a ExpressRoute áramkörén keresztül csatlakozik.  Az Azure-ban nyissa meg a Jump Box hálózati adapterét, és [tekintse meg a hatályos útvonalakat](../virtual-network/manage-route-table.md#view-effective-routes).

Az érvényben lévő útvonalak listájában az Azure VMware-megoldás üzembe helyezésének részeként létrehozott hálózatokat kell látnia. [A jelen cikk korábbi](#deploy-azure-vmware-solution) részében [ `/22` megadott hálózatról](production-ready-deployment-steps.md#ip-address-segment) származó több hálózat is megjelenik.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Az Azure VMware-megoldás Jump Box létrehozása":::

Ebben a példában az üzembe helyezés során a 10.74.72.0/22 hálózat bemenete a/24 hálózatot eredményezi.  Ha valami hasonlót lát, csatlakozhat a vCenter-hez az Azure VMware megoldásban.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Kapcsolódás és bejelentkezés a vCenter és a NSX-T-re

Jelentkezzen be az előző lépésben létrehozott Jump Box-ba. Miután bejelentkezett, nyisson meg egy webböngészőt, és lépjen be, és jelentkezzen be a vCenter és a NSX-T felügyeleti konzolon.  

A Azure Portal azonosíthatja a vCenter, valamint a NSX-T felügyeleti konzol IP-címeit és hitelesítő adatait.  Válassza ki saját felhőjét, majd az **Áttekintés** nézetben válassza az **identitás > alapértelmezett**lehetőséget. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Hálózati szegmens létrehozása Azure VMware-megoldással

Az NSX-T használatával új hálózati szegmenseket hozhat létre az Azure VMware-megoldási környezetben.  Definiálta a [tervezés szakaszban](production-ready-deployment-steps.md)létrehozni kívánt hálózat (oka) t.  Ha még nem definiálta őket, lépjen vissza a [tervezés szakaszra](production-ready-deployment-steps.md) a továbblépés előtt.

>[!IMPORTANT]
>Győződjön meg arról, hogy a megadott CIDR hálózati címterület nem fedi át az Azure-ban vagy a helyszíni környezetekben található bármit.  

Kövesse az [NSX-t hálózati szegmens létrehozása az Azure VMware-megoldás](tutorial-nsx-t-network-segment.md) oktatóanyagban NSX-t hálózati szegmens létrehozásához az Azure VMware megoldásban című témakört.

## <a name="verify-advertised-nsx-t-segment"></a>Meghirdetett NSX-T szegmens ellenőrzése

Lépjen vissza a [meghirdetett hálózati útvonalak ellenőrzése](#verify-network-routes-advertised) lépésre. Az előző lépésben létrehozott hálózati szegmens (ek) et jelölő listában egy további útvonal (ok) látható.  

A virtuális gépek esetében a [meghirdetett NSX-T szegmens ellenőrzése](#verify-advertised-nsx-t-segment) lépésben létrehozott szegmens (eke) t hozzá kell rendelnie.  

Mivel a DNS szükséges, azonosítsa a használni kívánt DNS-kiszolgálót.  

- Ha a ExpressRoute Global Reach konfigurálva van, használja a helyileg használni kívánt DNS-kiszolgálót.  
- Ha rendelkezik Azure-beli DNS-kiszolgálóval, használja azt.  
- Ha még nem rendelkezik a szolgáltatással, akkor a Jump Box által használt tetszőleges DNS-kiszolgálót használja.

>[!NOTE]
>Ez a lépés a DNS-kiszolgáló azonosítására szolgál, és ebben a lépésben nem végeznek konfigurációkat.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Választható DHCP-szolgáltatások biztosítása NSX-T hálózati szegmenshez

Ha azt tervezi, hogy DHCP-T használ a NSX-T szegmens (ek) n, folytassa ezt a szakaszt. Ellenkező esetben ugorjon a [virtuális gép hozzáadása elemre a NSX-T hálózati szegmens](#add-a-vm-on-the-nsx-t-network-segment) szakaszban.  

Most, hogy létrehozta a NSX-T hálózati szegmenst, a következő módokon végezheti el az alábbiak EGYIKét:

* Használja a NSX-T DHCP-kiszolgálóként a létrehozott szegmens (ek) hez. Ehhez a beállításhoz [létre kell hoznia egy DHCP-kiszolgálót a NSX-T-ben](manage-dhcp.md#create-dhcp-server) , és [továbbítania kell](manage-dhcp.md#create-dhcp-relay-service)erre a kiszolgálóra.
* DHCP-kérések továbbítása a NSX-T szegmensből a környezetében máshol található DHCP-kiszolgálóra. Ehhez a beállításhoz [csak a továbbítási konfigurációt kell végrehajtani](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Virtuális gép felvétele a NSX-T hálózati szegmensbe

Az Azure VMware-megoldás vCenter helyezzen üzembe egy virtuális gépet, és használja az Azure VMware-megoldási hálózat (ok) kapcsolatának ellenőrzéséhez a következőre:

- Az Internet
- Azure virtuális hálózatok
- Helyszíni.  

A virtuális gépet bármilyen vSphere-környezetben üzembe helyezheti.  Csatlakoztassa a virtuális gépet a korábban a NSX-T-ben létrehozott hálózati szegmens (ek) egyikéhez.  

>[!NOTE]
>DHCP-kiszolgáló beállítása esetén a virtuális gép hálózati konfigurációja (ne felejtse el beállítani a hatókört).  Ha statikusan konfigurálja a konfigurálást, akkor a szokásos módon konfigurálja a konfigurálást.

## <a name="test-the-nsx-t-segment-connectivity"></a>A NSX-T szegmens kapcsolat tesztelése

Jelentkezzen be az előző lépésben létrehozott virtuális gépre, és ellenőrizze a kapcsolatot;

1. Egy IP-cím pingelése az interneten.
2. Nyissa meg az internetes webhelyet egy webböngészőn keresztül.
3. Pingelje az Azure Virtual Network található Jump Box-t.

>[!IMPORTANT]
>Ezen a ponton az Azure VMware-megoldás működik, és sikeresen létesített kapcsolatot az Azure Virtual Network és az internet között.

## <a name="next-steps"></a>További lépések

A következő szakaszban az Azure VMware-megoldást a helyszíni hálózathoz a ExpressRoute-on keresztül kapcsolhatja össze.
> [!div class="nextstepaction"]
> [Azure VMware-megoldás összekapcsolása a helyszíni környezettel](azure-vmware-solution-on-premises.md)
