---
title: Virtuális gép hálózati útválasztási problémájának diagnosztizálása – Oktatóanyag – Azure Portal | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan diagnosztizálhatja egy virtuális gép hálózati útválasztási problémáit az Azure Network Watcher következő ugrás funkciójával.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: ea64c93726c3bc5c5d60f35790bb337333d4d47a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Oktatóanyag: Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása az Azure Portal használatával

A virtuális gép (VM) telepítésekor az Azure számos alapértelmezett útvonalat hoz létre. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak miatt a virtuális gép nem tud kommunikálni más erőforrásokkal. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális gép létrehozása
> * Egy URL felé irányuló kommunikáció tesztelése a Network Watcher következő ugrás funkciójával
> * IP-cím felé irányuló kommunikáció tesztelése
> * Útválasztási probléma diagnosztizálása és a megoldás megismerése

Igény szerint az [Azure CLI](diagnose-vm-network-routing-problem-cli.md) vagy az [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md) használatával is diagnosztizálhatja egy virtuális gép hálózati útválasztási problémáit.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** vagy az **Ubuntu Server 17.10 VM** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a **myResourceGroup** nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások** területen fogadja el az összes alapértelmezett beállítást, majd kattintson az **OK** gombra.
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez. A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg a virtuális gép üzembe helyezése befejeződik, mielőtt folytatná a további lépésekkel.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A hálózati kommunikáció Network Watcherrel való teszteléséhez először legalább egy Azure-régióban engedélyezze a Network Watchert, majd a következő ugrás funkcióval segítségével tesztelje a kommunikációt.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezte a Network Watchert legalább egy régióban, ugorjon a [Következő ugrás használata](#use-next-hop) szakaszra.

1. A portálon válassza a **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik az eredmények között, válassza ki.
2. Válassza a **Régiók** elemet a kibontásához, majd válassza az **USA keleti régiója** elem jobb oldalán található **...** lehetőséget az alábbi ábrán látható módon:

    ![A Network Watcher engedélyezése](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. Válassza a **Network Watcher engedélyezése** lehetőséget.

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. A Network Watcher következő ugrás funkciójával meghatározhatja, hogy az Azure melyik útvonalra irányítja a forgalmat.

1. Az Azure Portalon **Network Watcher** területén válassza a **Következő ugrás** elemet.
2. Válassza ki előfizetését, adja meg vagy válassza ki az alábbi értékeket, majd válassza a **Következő ugrás** lehetőséget az alábbi képen látható módon:

    |Beállítás                  |Érték                                                   |
    |---------                |---------                                               |
    | Erőforráscsoport          | Válassza a myResourceGroup lehetőséget                                 |
    | Virtuális gép         | Válassza a myVm lehetőséget                                            |
    | Hálózati illesztő       | myvm – A hálózati adapter neve eltérő lehet.   |
    | Forrás IP-címe       | 10.0.0.4                                               |
    | Cél IP-cím  | 13.107.21.200 – A www.bing.com címhez tartozó egyik cím. |

    ![Következő ugrás](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Néhány másodperc múlva a rendszer tájékoztatja, hogy a következő ugrás típusa **Internet**, az **Útválasztási táblázat azonosítója** pedig **Rendszerútvonal**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a célhoz.

3. Módosítsa a **Cél IP-címét** *172.31.0.100* értékre, majd újból válassza ki a **Következő ugrás** lehetőséget. A visszaadott eredmény tájékoztatja, hogy a **Következő ugrás típusa** **Nincs**, és az **Útválasztási táblázat azonosítója** szintén a **Rendszerútvonal**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Az útvonal további elemzéséhez tekintse át a hálózati adapterhez tartozó érvényes útvonalakat. A portál tetején lévő keresőmezőbe írja be a *myvm* kifejezést (vagy az Ön által ellenőrzött hálózati adapter nevét). Amikor a **myvm** elem megjelenik a keresési eredmények között, válassza ki.
2. Válassza a **TÁMOGATÁS + HIBAELHÁRÍTÁS** menüpont **Érvényes útvonalak** elemét a következő képen látható módon:

    ![Érvényes útvonalak](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    Amikor a [Következő ugrás használata](#use-next-hop) lépésben a 13.107.21.200 címmel futtatta a tesztet, a rendszer a 0.0.0.0/0 címelőtaggal rendelkező útvonalon irányította a forgalmat a címhez, mert más útvonal nem tartalmazza a címet. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

    Amikor viszont a 172.31.0.100 címmel futtatta a tesztet, az eredmények azt jelezték, hogy nincs következő ugrási típus. Ahogyan azt az előző képen láthatja, bár létezik alapértelmezett útvonal a 172.16.0.0/12 előtaghoz, amely magában foglalja a 172.31.0.100 címet, a **KÖVETKEZŐ UGRÁS TÍPUSA** **Nincs**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címteréhez, akkor az Azure a **KÖVETKEZŐ UGRÁS TÍPUSÁT** **Virtuális hálózat** értékre módosítja az útvonalra vonatkozóan. Ha ellenőrzést végezne, a **Virtuális hálózat** érték jelenne meg a **KÖVETKEZŐ UGRÁS TÍPUSA** mezőben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy virtuális gépet, és diagnosztizálta a hálózati útválasztást a virtuális géptől. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuálisgép-kapcsolatok esetén ezenkívül meghatározhatja a késést, engedélyezheti vagy letilthatja a virtuális gép és a végpont közötti hálózati forgalmat és a végponthoz használt útvonalat a Network Watcher [kapcsolatok hibaelhárítása](network-watcher-connectivity-portal.md) funkciójával. Megtudhatja, hogyan monitorozhatja egy virtuális gép és egy végpont, például egy IP- vagy URL-cím közötti kommunikációt a Network Watcher kapcsolat monitorozása funkciójával.

> [!div class="nextstepaction"]
> [Hálózati kapcsolat monitorozása](connection-monitor.md)