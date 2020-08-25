---
title: Oktatóanyag – a VMware Private Cloud hálózatkezelésének konfigurálása az Azure-ban
description: Megtudhatja, hogyan hozhatja létre és konfigurálhatja a privát felhő üzembe helyezéséhez szükséges hálózatkezelést az Azure-ban
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: ff071e0d6eaf1552634433a76e4eade530c603b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750502"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Oktatóanyag: az Azure-beli VMware Private-felhő hálózatkezelésének konfigurálása

Egy Azure VMware-megoldáshoz tartozó privát felhőhöz Azure-Virtual Network szükséges. Mivel az Azure VMware-megoldás nem támogatja a helyszíni vCenter az előzetes verzió használata során, a helyszíni környezettel való integráció további lépéseire is szükség van. A ExpressRoute áramkör és a virtuális hálózati átjáró beállítása is szükséges, és ez az oktatóanyag tartalmazza azt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Virtuális hálózati átjáró létrehozása
> * A ExpressRoute-áramkör összekapcsolása az átjáróval
> * A vCenter és a NSX Manager URL-címeinek megkeresése

## <a name="prerequisites"></a>Előfeltételek 
A virtuális hálózat létrehozása előtt győződjön meg arról, hogy létrehozta az [Azure VMware-megoldás saját felhőjét](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon a [saját felhő létrehozása oktatóanyagban](tutorial-create-private-cloud.md) létrehozott erőforráscsoporthoz, és válassza a **+ Hozzáadás** lehetőséget egy új erőforrás definiálásához. 

1. A **Keresés a piactéren** szövegmezőbe írja be a következőt: **Virtual Network**. Keresse meg a Virtual Network erőforrást, és válassza ki.

1. A **Virtual Network** lapon válassza a **Létrehozás** lehetőséget, hogy beállítsa a virtuális hálózatot a saját felhőhöz.

1. A **Virtual Network létrehozása** lapon adja meg a virtuális hálózat adatait.

1. Az **alapvető beállítások** lapon adja meg a virtuális hálózat nevét, és válassza ki a megfelelő régiót, majd válassza a **Tovább: IP-címek**lehetőséget.

1. Az **IP-címek** lap IPv4- **címterület**területén adja meg az előző oktatóanyagban létrehozott címtartományt.

   > [!IMPORTANT]
   > Az előző oktatóanyagban olyan címtartományt kell használnia, amely **nem** fedi át a privát felhő létrehozásakor használt címterületet.

1. Válassza az **+ alhálózat hozzáadása**lehetőséget, majd az **alhálózat hozzáadása** lapon adja meg az alhálózat nevét és a megfelelő címtartományt. Amikor végzett, válassza a **Hozzáadás** elemet.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Válassza a felülvizsgálat + létrehozás lehetőséget." border="true":::

1. Ellenőrizze az adatokat, és válassza a **Létrehozás**lehetőséget. Miután az üzembe helyezés befejeződött, a virtuális hálózat megjelenik az erőforráscsoporthoz.

## <a name="create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

Most, hogy létrehozott egy virtuális hálózatot, létre fog hozni egy virtuális hálózati átjárót.

1. Az erőforráscsoporthoz válassza a **+ Hozzáadás** lehetőséget egy új erőforrás hozzáadásához.

1. A **Keresés a piactér** szövegmezőbe írja be a **virtuális hálózati átjárót**. Keresse meg a Virtual Network erőforrást, és válassza ki.

1. A **Virtual Network átjáró** lapon válassza a **Létrehozás**lehetőséget.

1. A **virtuális hálózati átjáró létrehozása** lap alapok lapján adja meg a mezők értékeit, majd válassza a **felülvizsgálat + létrehozás**elemet. 

   | Mező | Érték |
   | --- | --- |
   | **Előfizetés** | Ez az érték már fel van töltve azzal az előfizetéssel, amelyhez az erőforráscsoport tartozik. |
   | **Erőforráscsoport** | Ez az érték már fel van töltve az aktuális erőforráscsoporthoz. Ez lehet az előző tesztben létrehozott erőforráscsoport. |
   | **Név** | Adja meg a virtuális hálózati átjáró egyedi nevét. |
   | **Régió** | Válassza ki a virtuális hálózati átjáró földrajzi helyét. |
   | **Átjáró típusa** | Válassza a **ExpressRoute**lehetőséget. |
   | **Termékváltozat** | Hagyja meg az alapértelmezett értéket: **standard**. |
   | **Virtuális hálózat** | Válassza ki a korábban létrehozott virtuális hálózatot. Ha nem látja a virtuális hálózatot, győződjön meg arról, hogy az átjáró régiója megegyezik a virtuális hálózat régiójával. |
   | **Átjáró alhálózati címtartomány** | Ez az érték a virtuális hálózat kiválasztásakor lesz feltöltve. Ne módosítsa az alapértelmezett értéket. |
   | **Nyilvános IP-cím** | Válassza az **Új létrehozása** lehetőséget. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="A virtuális hálózati átjáró létrehozása lap alapok lapján adja meg a mezők értékeit, majd válassza a felülvizsgálat + létrehozás elemet." border="true":::

1. Ellenőrizze, hogy helyesek-e a részletek, majd válassza a **Létrehozás** lehetőséget a virtuális hálózati átjáró üzembe helyezésének elindításához. 
1. Az üzembe helyezés befejezése után lépjen a következő szakaszra, hogy összekapcsolja a ExpressRoute-kapcsolatot a virtuális hálózati átjáróval, amely az Azure VMware-megoldás saját felhőjét tartalmazza.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>A ExpressRoute és a virtuális hálózati átjáró összekötése

Most, hogy telepített egy virtuális hálózati átjárót, hozzá fog adni egy kapcsolatot az IT és az Azure VMware-megoldás privát felhője között.

1. Navigáljon az előző oktatóanyagban létrehozott saját felhőhöz, és válassza a **kezelés**alatt a **kapcsolat** lehetőséget, és válassza a **ExpressRoute** lapot.

1. Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, ehhez válassza a Select **+ Authorization Key (engedélyezési kulcs kérése**) lehetőséget.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, ehhez válassza a Select + Authorization Key (engedélyezési kulcs kérése) lehetőséget." border="true":::

1. Lépjen az előző lépésben létrehozott Virtual Network átjáróra, és a **Beállítások**területen válassza a **kapcsolatok**lehetőséget. A **kapcsolatok** lapon válassza a **+ Hozzáadás**lehetőséget.

1. A **kapcsolatok hozzáadása** lapon adja meg a mezők értékeit, majd kattintson **az OK gombra**. 

   | Mező | Érték |
   | --- | --- |
   | **Név**  | Adjon egy nevet a kapcsolatnak.  |
   | **Kapcsolat típusa**  | Válassza a **ExpressRoute**lehetőséget.  |
   | **Engedély beváltása**  | Győződjön meg arról, hogy a jelölőnégyzet be van jelölve.  |
   | **Virtuális hálózati átjáró** | A korábban létrehozott Virtual Network átjáró.  |
   | **Engedélyezési kulcs**  | Másolja és illessze be az engedélyezési kulcsot az erőforráscsoport ExpressRoute lapjáról. |
   | **Társ áramkör URI-ja**  | Másolja és illessze be a ExpressRoute-azonosítót az erőforráscsoport ExpressRoute lapjáról.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="A kapcsolatok hozzáadása lapon adja meg a mezők értékeit, majd kattintson az OK gombra." border="true":::

Létrejön a ExpressRoute-áramkör és a Virtual Network közötti kapcsolat.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>A vCenter és a NSX Manager URL-címeinek megkeresése

A vCenter és a NSX Manager alkalmazásba való bejelentkezéshez szüksége lesz az vCenter webes ügyfelének és a NSX-T Manager-hely URL-címeire. 

Navigáljon az Azure VMware-megoldás saját felhőhöz, a **kezelés**alatt válassza az **identitás**lehetőséget, itt megtalálja a szükséges információkat.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Navigáljon az Azure VMware-megoldás saját felhőhöz, a kezelés alatt válassza az identitás lehetőséget, itt megtalálja a szükséges információkat." border="true":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Virtuális hálózati átjáró létrehozása
> * A ExpressRoute-áramkör összekapcsolása az átjáróval
> * A vCenter és a NSX Manager URL-címeinek megkeresése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre a környezethez való kapcsolódáshoz használt Jump Box-t, hogy helyileg kezelhesse a saját felhőjét.

> [!div class="nextstepaction"]
> [Hozzáférés a magánfelhőhöz](tutorial-access-private-cloud.md)
