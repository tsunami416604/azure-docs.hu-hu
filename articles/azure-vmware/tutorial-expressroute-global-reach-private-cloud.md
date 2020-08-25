---
title: Helyszíni helyi környezetek saját felhőhöz
description: Ebben az Azure VMware-megoldás oktatóanyagában ExpressRoute-Global Reach-társítást hoz létre egy Azure VMware-megoldásban található privát felhőbe.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750451"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Oktatóanyag: helyszíni helyszíni környezetek egy privát felhőhöz

A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet a privát Felhőkkel. A ExpressRoute Global Reach kapcsolat létrejött egy privát felhőalapú ExpressRoute áramkör és egy meglévő ExpressRoute-kapcsolat között a helyszíni környezetekhez.  A ExpressRoute-Global Reach az Azure CLI-vel és a PowerShell-lel való konfigurálására vonatkozó utasítások, valamint a [CLI-parancsok](../expressroute/expressroute-howto-set-global-reach-cli.md) kibővítésével konkrét részleteket és példákat talál, amelyek segítségével konfigurálhatja a ExpressRoute Global REACH a helyszíni környezetek között egy Azure VMware-megoldás saját felhőbe.   

Mielőtt engedélyezi a kapcsolatot a két ExpressRoute-áramkör között a ExpressRoute Global Reach használatával, tekintse át a dokumentációt, amely bemutatja, hogyan [engedélyezheti a kapcsolatokat különböző Azure-előfizetésekben](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Az [Azure – Private felhő hálózatkezelésének konfigurálásakor](tutorial-configure-networking.md) használt ExpressRoute-áramkörhöz engedélyezési kulcsokat kell létrehoznia és használnia, amikor a ExpressRoute átjárókat vagy más ExpressRoute-áramköröket használ a Global REACH használatával. A ExpressRoute áramkör már használ egy engedélyezési kulcsot, és létrehoz egy másikat a helyszíni ExpressRoute áramkörrel.

> [!TIP]
> Ezen utasítások kontextusában a helyszíni ExpressRoute áramkör _1_, a saját felhőalapú ExpressRoute áramköre pedig egy másik előfizetésben és _2_. címkével ellátott áramkörben található. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Használja ki a ExpressRoute-áramkörök és a ExpressRoute Global Reach-társítások kezelésének meglévő utasításait
> * Engedélyezési kulcs létrehozása a _2. áramkörhöz_, a saját felhőalapú ExpressRoute áramköréhez
> * Használja az Azure CLI-t az _1. áramköri_ előfizetéshez tartozó Cloud Shellban, hogy lehetővé tegye a helyszíni – privát Felhőbeli ExpressRoute Global REACH

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:
- Egy privát felhő a ExpressRoute áramkörével, amely egy Azure-beli virtuális hálózatban (VNet) található ExpressRoute-átjáróval, ez a _2. áramköri_ eljárás.
- Egy különálló, működő ExpressRoute-áramkör a helyi környezetek az Azure-hoz való összekapcsolásához – ez az _1. áramköri_ eljárás a társítási eljárások szempontjából.
- Egy/29 nem átfedésben lévő [hálózati címterület](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) a ExpressRoute Global REACH-társításhoz.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>ExpressRoute-engedélyezési kulcs létrehozása az Azure VMware Solution Private Cloud-ban

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute > az engedélyezési kulcs igénylése**lehetőséget.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Válassza a kapcsolat > ExpressRoute > az új kérés indításához kérjen egy engedélyezési kulcsot.":::

2. Adja meg az engedélyezési kulcs nevét, majd válassza a **Létrehozás**lehetőséget. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Új engedélyezési kulcs létrehozásához kattintson a Létrehozás gombra. ":::

   A létrehozást követően az új kulcs megjelenik a privát felhőhöz tartozó engedélyezési kulcsok listájában. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Győződjön meg arról, hogy az új engedélyezési kulcs megjelenik a titkos felhőhöz tartozó kulcsok listájában. ":::

3. Jegyezze fel az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját, valamint a/29-címterület számát. A következő lépésben a társítás befejezéséhez fogja használni őket. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer Private Cloud a helyszíni hitelesítéshez engedélyezési kulcs használatával

Most, hogy létrehozott egy engedélyezési kulcsot a saját felhőalapú ExpressRoute áramkörhöz, azt a helyszíni ExpressRoute-áramkörrel is elvégezheti.  A társítás a helyszíni ExpressRoute-áramkör perspektívájában történik az Azure CLI-vel a saját felhőalapú ExpressRoute-áramkörének egy Cloud Shell-és erőforrás-azonosítója és engedélyezési kulcsa alapján, amely az előző lépésekben lett létrehozva.

> [!TIP]  
> Az Azure CLI-parancs kimenetének rövidített eléréséhez ezek az utasítások egy [ `–query` argumentumot használhatnak egy JMESPath-lekérdezés végrehajtásához, hogy csak a szükséges eredményeket jelenítsék meg](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Jelentkezzen be a Azure Portalba ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel, és nyisson meg egy Cloud Shell. Hagyja a shellt bash-ként.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Jelentkezzen be a Azure Portalba, és nyisson meg egy Cloud Shell.":::
 
2. A parancssorban adja meg az Azure CLI-parancsot a társítás létrehozásához az adott információ és erőforrás-azonosító, engedélyezési kulcs és/29 CIDR hálózati blokk használatával. 

   A következőkben egy példa látható a használni kívánt parancsra, valamint a sikeres társat jelző kimenetre. A példában szereplő parancs a ["ExpressRoute-áramkörök közötti kapcsolat engedélyezése különböző Azure-előfizetésekben" című rész 3. lépésében](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)használt parancson alapul.

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Hozzon létre ExpressRoute Global Reach-társítást az Azure CLI-paranccsal egy Cloud Shell.":::
 
   Most már képesnek kell lennie a helyszíni környezetek és a saját felhő közötti kapcsolódásra a ExpressRoute Global Reach-társításon keresztül.

> [!TIP]
> Az imént létrehozott társítást törölheti a helyszíni hálózatokra vonatkozó utasításokat követve a [kapcsolat letiltása](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) lehetőséggel.


## <a name="next-steps"></a>További lépések

Ha hibrid felhőalapú bővítményt (HCX) kíván használni a virtuális gépek számítási feladatainak saját felhőbe való áttelepítéséhez, használja az [Azure VMware megoldás telepítése HCX](hybrid-cloud-extension-installation.md) .


<!-- LINKS - external-->

<!-- LINKS - internal -->
