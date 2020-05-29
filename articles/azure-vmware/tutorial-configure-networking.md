---
title: Oktatóanyag – a VMware Private Cloud hálózatkezelésének konfigurálása az Azure-ban
description: Megtudhatja, hogyan hozhatja létre és konfigurálhatja a privát felhő üzembe helyezéséhez szükséges hálózatkezelést az Azure-ban
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6cac420fb77526746dbdbfef5a88b071c007d555
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148106"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Oktatóanyag: az Azure-beli VMware Private-felhő hálózatkezelésének konfigurálása

Egy Azure VMware-megoldás (AVS) privát felhőhöz virtuális hálózat szükséges. Mivel az AVS nem támogatja a helyszíni vCenter az előzetes verzióban, szükség van a helyszíni környezettel való integráció további lépéseire. A ExpressRoute-áramkör és a Virtual Network átjáró beállítása is szükséges, és ebben az oktatóanyagban fog foglalkozni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Virtual Network átjáró létrehozása
> * A ExpressRoute-áramkör összekapcsolása az átjáróval
> * A vCenter és a NSX Manager URL-címeinek megkeresése

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Navigáljon az [előző oktatóanyagban](tutorial-create-private-cloud.md)létrehozott erőforráscsoporthoz, és válassza a **+ Hozzáadás** elemet egy új erőforrás definiálásához.

A **Keresés a piactér** szövegmezőbe írja be a következőt: **Virtual Network**. Keresse meg a Virtual Network erőforrást, és válassza ki.

A Virtual Network lapon válassza a **Létrehozás** lehetőséget, hogy beállítsa a virtuális hálózatot a saját felhőhöz.

A **Virtual Network létrehozása** lapon adja meg a virtuális hálózat vonatkozó részleteit, a tulajdonságok leírását a következő táblázat mutatja:

> [!IMPORTANT]
> Az előző oktatóanyagban olyan címtartományt kell használnia, amely **nem** fedi át a privát felhő létrehozásakor használt címterületet.

Az **alapvető beállítások** lapon adja meg a virtuális hálózat nevét, és válassza ki a megfelelő régiót, majd válassza a **tovább lehetőséget: IP-címek**

Az **IP-címek** lap IPv4- **címterület**területén adja meg az előző oktatóanyagban létrehozott címtartományt.

Válassza az **+ alhálózat hozzáadása**lehetőséget, majd az **alhálózat hozzáadása** lapon adja meg az alhálózat nevét és a megfelelő címtartományt. Amikor végzett, válassza a **Hozzáadás** elemet.

Válassza a **felülvizsgálat + létrehozás** lehetőséget

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="virtuális hálózat létrehozása" border="true":::

Ellenőrizze az adatokat, és válassza a **Létrehozás**lehetőséget. Miután az üzembe helyezés befejeződött, a virtuális hálózat megjelenik az erőforráscsoporthoz.

## <a name="create-a-virtual-network-gateway"></a>Virtual Network átjáró létrehozása

Az előző szakaszban létrehozott egy virtuális hálózatot, most létrehoz egy Virtual Network-átjárót.

Az erőforráscsoporthoz válassza a **+ Hozzáadás** lehetőséget egy új erőforrás hozzáadásához.

A **Keresés a piactér** szövegmezőbe írja be a **virtuális hálózati átjárót**. Keresse meg a Virtual Network erőforrást, és válassza ki.

A **Virtual Network átjáró** lapon válassza a **Létrehozás**lehetőséget.

A **virtuális hálózati átjáró létrehozása** lap alapok lapján adja meg a mezők értékeit. a mezők leírása a következő táblázatban látható:

| Mező | Érték |
| --- | --- |
| **Előfizetés** | Ez az érték már fel van töltve azzal az előfizetéssel, amelyhez az erőforráscsoport tartozik. |
| **Erőforráscsoport** | Ez az érték már fel van töltve az aktuális erőforráscsoporthoz. Ez lehet az előző tesztben létrehozott erőforráscsoport. |
| **Név** | Adja meg a virtuális hálózati átjáró egyedi nevét. |
| **Régió** | Válassza ki a virtuális hálózati átjáró földrajzi helyét. |
| **Átjáró típusa** | Válassza a **ExpressRoute**lehetőséget. |
| **VPN-típus** | Válassza az **útvonal-alapú**lehetőséget. |
| **Termékváltozat** | Hagyja meg az alapértelmezett értéket: **standard**. |
| **Virtuális hálózat** | Válassza ki a korábban létrehozott virtuális hálózatot. Ha nem látja a virtuális hálózatot, győződjön meg arról, hogy az átjáró régiója megegyezik a virtuális hálózat régiójával. |
| **Átjáró alhálózati címtartomány** | Ez az érték a virtuális hálózat kiválasztásakor lesz feltöltve. Ne módosítsa az alapértelmezett értéket. |
| **Nyilvános IP-cím** | Válassza az **Új létrehozása** lehetőséget. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="átjáró létrehozása" border="true":::

Válassza a **felülvizsgálat + létrehozás**lehetőséget, a következő oldalon ellenőrizze, hogy helyesek-e a részletek, majd válassza a **Létrehozás** lehetőséget a virtuális hálózati átjáró üzembe helyezésének megkezdéséhez. Az üzembe helyezés befejezése után lépjen az oktatóanyag következő szakaszára, hogy összekapcsolja a ExpressRoute-kapcsolatot a saját felhőjét tartalmazó virtuális hálózattal.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>A ExpressRoute és a Virtual Network átjáró összekötése

Ez a szakasz végigvezeti a kapcsolat hozzáadásán az AVS Private Cloud és a létrehozott virtuális hálózati átjáró között.

Navigáljon az előző oktatóanyagban létrehozott saját felhőhöz, és válassza a **kezelés**alatt a **kapcsolat** lehetőséget, és válassza a **ExpressRoute** lapot.

Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, ehhez a Select **+ Request an Authorization Key**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="engedélyezési kulcs kérése" border="true":::

Lépjen az előző lépésben létrehozott Virtual Network átjáróra, és a **Beállítások**területen válassza a **kapcsolatok**lehetőséget. A **kapcsolatok** lapon válassza a **+ Hozzáadás**lehetőséget.

A **kapcsolatok hozzáadása** lapon adja meg a mezők értékeit. A mezők leírása a következő táblázatban látható:

| Mező | Érték |
| --- | --- |
| **Név**  | Adjon egy nevet a kapcsolatnak.  |
| **Kapcsolat típusa**  | Válassza a **ExpressRoute**lehetőséget.  |
| **Engedély beváltása**  | Győződjön meg arról, hogy a jelölőnégyzet be van jelölve.  |
| **Virtuális hálózati átjáró** | A korábban létrehozott virtuális hálózati átjáró  |
| **Engedélyezési kulcs**  | Másolja és illessze be az engedélyezési kulcsot az erőforráscsoport ExpressRoute lapjáról. |
| **Társ áramkör URI-ja**  | Másolja és illessze be a ExpressRoute-azonosítót az erőforráscsoport ExpressRoute lapjáról.  |

Kattintson az **OK** gombra. Ez létrehozza a kapcsolatot a ExpressRoute-áramkör és a virtuális hálózat között.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="kapcsolatok hozzáadása" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>A vCenter és a NSX Manager URL-címeinek megkeresése

A vVenter és a NSX Manager alkalmazásba való bejelentkezéshez szüksége lesz az vCenter webes ügyfelének és a NSX-T Manager-hely URL-címeire. Az URL-címek megkeresése:

Navigáljon az AVS Private-felhőhöz, a **kezelés**alatt válassza az **identitás**lehetőséget, itt megtalálja a szükséges információkat.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="a vCenter URL-címeinek megkeresése" border="true":::

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Virtual Network átjáró létrehozása
> * A ExpressRoute-áramkör összekapcsolása az átjáróval
> * A vCenter és a NSX Manager URL-címeinek megkeresése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre a környezethez való kapcsolódáshoz használt Jump Box-t, hogy helyileg kezelhesse a saját felhőjét.

> [!div class="nextstepaction"]
> [Hozzáférés a magánfelhőhöz](tutorial-access-private-cloud.md)
