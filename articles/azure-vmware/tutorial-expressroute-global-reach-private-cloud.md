---
title: Oktatóanyag – helyszíni környezetek saját felhőbe
description: Megtudhatja, hogyan hozhat létre ExpressRoute Global Reach-társítást egy Azure VMware-megoldásban található privát felhőbe.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 679887e1998a534001e72ddff7a1a184a84bd831
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254717"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Oktatóanyag: helyszíni helyszíni környezetek egy privát felhőhöz

A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet az Azure VMware-megoldás saját felhővel. A ExpressRoute Global Reach kapcsolat létrejött a saját felhőalapú ExpressRoute áramkör és egy meglévő ExpressRoute-kapcsolat között a helyszíni környezetekhez. 

Az [Azure – Private felhő hálózatkezelésének konfigurálásakor](tutorial-configure-networking.md) használt ExpressRoute-áramkörhöz engedélyezési kulcsokat kell létrehoznia és használnia, amikor a ExpressRoute átjárókat vagy más ExpressRoute-áramköröket használ a Global REACH használatával. A ExpressRoute áramkör már használ egy engedélyezési kulcsot, és ebben az oktatóanyagban egy másodikat fog létrehozni a helyszíni ExpressRoute áramkörrel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Második engedélyezési kulcs létrehozása a _Circuit 2_, a Private Cloud ExpressRoute áramkörhöz
> * Használja a [Azure Portal](#azure-portal-method) vagy az Azure CLI-t az _1. áramkör_ -előfizetés egyik [Cloud Shell metódusában](#azure-cli-in-a-cloud-shell-method) , hogy lehetővé tegye a helyszíni – privát Felhőbeli ExpressRoute Global REACH


## <a name="before-you-begin"></a>Előkészületek

Mielőtt engedélyezi a kapcsolatot a két ExpressRoute-áramkör között a ExpressRoute Global Reach használatával, tekintse át a dokumentációt, amely bemutatja, hogyan [engedélyezheti a kapcsolatokat különböző Azure-előfizetésekben](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Előfeltételek

1. Kapcsolat létesítése egy Azure VMware-megoldás saját ExpressRoute-áramkörével és egy Azure-beli virtuális hálózatban (VNet) található ExpressRoute-átjáróval, amely a _2. áramköri_ eljárásokból áll.  
1. Egy különálló, működő ExpressRoute-áramkör, amely a helyszíni környezetek Azure-hoz való összekapcsolására szolgál – amely az _1. áramköri_ eljárás perspektívája.
1. Egy/29 nem átfedésben lévő [hálózati címterület](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) a ExpressRoute Global REACH-társításhoz.

> [!TIP]
> Ezeknek az előfeltételeknek a kontextusában a helyszíni ExpressRoute áramkör _1_, a saját Felhőbeli ExpressRoute áramkör pedig egy másik előfizetésben és _2_. címkével ellátott áramkörben található. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>ExpressRoute-engedélyezési kulcs létrehozása a privát felhőben

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute > az engedélyezési kulcs igénylése**lehetőséget.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Válassza a kapcsolat > ExpressRoute > az új kérés indításához kérjen egy engedélyezési kulcsot.":::

2. Adja meg az engedélyezési kulcs nevét, majd válassza a **Létrehozás**lehetőséget. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Új engedélyezési kulcs létrehozásához kattintson a Létrehozás gombra. ":::

   A létrehozást követően az új kulcs megjelenik a privát felhőhöz tartozó engedélyezési kulcsok listájában. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Győződjön meg arról, hogy az új engedélyezési kulcs megjelenik a titkos felhőhöz tartozó kulcsok listájában. ":::

3. Jegyezze fel az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját, valamint a/29-címterület számát. A következő lépésben a társítás befejezéséhez fogja használni őket. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer Private Cloud a helyszíni hitelesítéshez engedélyezési kulcs használatával

Most, hogy létrehozott egy engedélyezési kulcsot a saját felhőalapú ExpressRoute áramkörhöz, azt a helyszíni ExpressRoute-áramkörrel is elvégezheti.  A társítás a helyszíni ExpressRoute áramkör szempontjából a [Azure Portal](#azure-portal-method) vagy az Azure parancssori felületének a [Cloud Shellban](#azure-cli-in-a-cloud-shell-method)történő használatával történik. Mindkét módszerrel használhatja a saját felhőalapú ExpressRoute áramkör erőforrás-AZONOSÍTÓját és engedélyezési kulcsát, amelyet az előző lépésekben hozott létre a társítás befejezéséhez.

### <a name="azure-portal-method"></a>Azure Portal metódus

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel.

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute Global REACH > Hozzáadás**lehetőséget.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="A menüben válassza a kapcsolat, a ExpressRoute Global Reach fület, és adja hozzá a következőt:.":::

1. A helyszíni felhőalapú kapcsolatok a következők egyikével hozhatók létre:

   - Válassza ki a ExpressRoute áramkört a listából.
   - Ha rendelkezik egy áramkör-AZONOSÍTÓval, másolja ki és illessze be.

1. Válassza a **Kapcsolódás** lehetőséget. Az új kapcsolat a helyszíni felhőalapú kapcsolatok listájában jelenik meg.  

>[!TIP]
>A listából törölheti vagy leválaszthatja a kapcsolatot a **továbbiak**lehetőség kiválasztásával.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Helyszíni kapcsolat leválasztása vagy törlése":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI Cloud Shell metódusban

A [CLI-parancsokat](../expressroute/expressroute-howto-set-global-reach-cli.md) speciális részletekkel és példákkal bővítettük, így könnyebben konfigurálható a ExpressRoute Global REACH a helyszíni környezetek között egy Azure VMware-megoldás saját felhőbe.  

> [!TIP]  
> Az Azure CLI-parancs kimenetének rövidített eléréséhez ezek az utasítások egy [ `–query` argumentumot használhatnak egy JMESPath-lekérdezés végrehajtásához, hogy csak a szükséges eredményeket jelenítsék meg](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Jelentkezzen be a Azure Portalba ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel, és nyisson meg egy Cloud Shell. Hagyja a shellt bash-ként.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Jelentkezzen be a Azure Portalba, és nyisson meg egy Cloud Shell.":::
 
2. A parancssorban adja meg az Azure CLI-parancsot a társítás létrehozásához az adott információ és erőforrás-azonosító, engedélyezési kulcs és/29 CIDR hálózati blokk használatával. 

   A következőkben egy példa látható a használni kívánt parancsra, valamint a sikeres társat jelző kimenetre. A példában szereplő parancs a ["ExpressRoute-áramkörök közötti kapcsolat engedélyezése különböző Azure-előfizetésekben" című rész 3. lépésében](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)használt parancson alapul.

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Hozzon létre ExpressRoute Global Reach-társítást az Azure CLI-paranccsal egy Cloud Shell.":::
 
   Most már képesnek kell lennie a helyszíni környezetek és a saját felhő közötti kapcsolódásra a ExpressRoute Global Reach-társításon keresztül.

> [!TIP]
> Az imént létrehozott társítást törölheti a helyszíni hálózatokra vonatkozó utasításokat követve a [kapcsolat letiltása](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) lehetőséggel.


## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre egy második engedélyezési kulcsot a privát felhőalapú ExpressRoute áramkör számára, és hogyan engedélyezheti a helyszíni – Private Felhőbeli ExpressRoute Global Reach. 

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti és konfigurálhatja a VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.

> [!div class="nextstepaction"]
> [VMware HCX üzembe helyezése és konfigurálása](hybrid-cloud-extension-installation.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
