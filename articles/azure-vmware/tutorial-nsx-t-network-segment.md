---
title: Oktatóanyag – NSX-T hálózati szegmens létrehozása az Azure VMware-megoldásban
description: Ismerje meg, hogyan hozhatja létre a virtuális gépekhez használt NSX-T hálózati szegmenseket a vCenter-ben
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367669"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Oktatóanyag: NSX-T hálózati szegmens létrehozása az Azure VMware-megoldásban

A vCenter-ben létrehozott virtuális gépek (VM-EK) az NSX-T-ben létrehozott hálózati szegmensekre kerülnek, és a vCenter láthatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati szegmensek hozzáadásához navigáljon a NSX-T Managerben
> * Új hálózati szegmens hozzáadása
> * Figyelje meg az új hálózati szegmenst a vCenter-ben

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy, a vCenter és a NSX-T kezelő felügyeleti felületek elérését biztosító Azure VMware-megoldás saját felhővel. További információt a [hálózatkezelés konfigurálása](tutorial-configure-networking.md) oktatóanyagban talál.

## <a name="provision-a-network-segment-in-nsx-t"></a>Hálózati szegmens kiépítése a NSX-T-ben

1. A privát felhő vCenter válassza a **SDDC-Datacenter > hálózatok** lehetőséget, és figyelje meg, hogy még nincsenek hálózatok.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. A privát felhőhöz tartozó NSX-T Managerben válassza a **hálózatkezelés**lehetőséget.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Válassza a **szegmensek**lehetőséget.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. A NSX-T szegmensek – Áttekintés lapon válassza a **szegmens hozzáadása**elemet. Három szegmens jön létre a saját felhőalapú kiépítés részeként, és nem használható virtuális gépekhez.  Ehhez a célhoz új hálózati szegmenst kell hozzáadnia.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Nevezze el a szegmenst, válassza ki az előre konfigurált Tier1-átjárót (TNTxx-T1) **csatlakoztatott átjáróként**, hagyja meg a **típust** rugalmasként, válassza az előre konfigurált átfedéses **átviteli zónát** (TNTxx-overlay-TZ), majd válassza az alhálózatok beállítása lehetőséget. Az ebben a szakaszban szereplő összes egyéb beállítás, valamint a **portok** és **szegmensek profiljai** az alapértelmezett konfigurációban maradhatnak.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Állítsa be az átjáró IP-címét az új szegmenshez, majd válassza a **Hozzáadás**lehetőséget. Az Ön által használt IP-címnek egy nem átfedésben lévő RFC1918 kell lennie, amely biztosítja, hogy az új szegmensben lévő virtuális gépekhez is csatlakozhat.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Alkalmazza az új hálózati szegmenst az **alkalmaz** gombra kattintva, majd mentse a **konfigurációt mentéssel.**

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Ekkor létrejött az új hálózati szegmens, és a **nem**lehetőség kiválasztásával elutasítja a szegmens konfigurálásának folytatását.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Erősítse meg az új hálózati szegmenst a NSX-T-ben a **hálózatkezelés > szegmensek** lehetőség kiválasztásával és az új szegmens megjelenítésével (ebben az esetben "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

1. Erősítse meg, hogy az új hálózati szegmens megtalálható a vCenter-ben: válassza a **hálózatkezelés > SDDC-Datacenter** lehetőséget, és figyelje meg, hogy az új szegmens szerepel-e a listán (ebben az esetben "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="A privát felhő vCenter válassza a SDDC-Datacenter > Networks lehetőséget, és vegye figyelembe, hogy még nincsenek hálózatok.":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozta a virtuális gépekhez használt NSX-T hálózati szegmenseket a vCenter-ben. Mostantól [létrehozhat egy tartalomkezelőt a virtuális gépek Azure VMware-megoldásban való üzembe helyezéséhez](deploy-vm-content-library.md). Az oktatóanyagban létrehozott hálózaton is üzembe helyezhet egy virtuális gépet.

Ha nem, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre ExpressRoute-Global Reach a privát felhőhöz egy Azure VMware-megoldásban.

> [!div class="nextstepaction"]
> [Helyszíni helyi környezetek saját felhőhöz](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
