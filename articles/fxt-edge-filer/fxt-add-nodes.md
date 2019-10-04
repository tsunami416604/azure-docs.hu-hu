---
title: A Microsoft Azure FXT Edge Filer fürtkonfiguráció - csomópontok hozzáadása
description: Az Azure FXT Edge Filer szoftvertároló gyorsítótár csomópontok hozzáadása
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: d84b98b4ab936bbb6978144eb2e89b5e19df7069
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543220"
---
# <a name="tutorial-add-cluster-nodes"></a>Oktatóanyag: Fürtcsomópontok hozzáadása 

Új Azure FXT Edge Filer fürt csak egy csomópont jön létre. Érdemes legalább két további csomópontok hozzáadása és a magas rendelkezésre állás engedélyezése más konfigurálása előtt. 

Ez az oktatóanyag azt ismerteti, hogyan fürtcsomópontok hozzáadása és a magas rendelkezésre állású (HA) funkció engedélyezéséhez. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Hogyan adhat hozzá csomópontokat a FXT fürt
> * Magas rendelkezésre ÁLLÁS engedélyezése

A jelen oktatóanyagban szereplő lépések végrehajtásához körülbelül 45 percet igénybe vehet.

Ebben az oktatóanyagban a Kezdés előtt bekapcsolják a hozzáadni kívánt csomópontokat, és [állítsa be a kezdeti jelszavukat](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. A fürtcsomópontok lap betöltése

A fürt Vezérlőpult megnyitása egy webböngészőben, és jelentkezzen be rendszergazdaként. (Részletes utasításokat a rendszer az áttekintő cikkben [nyissa meg a beállításlapok](fxt-cluster-create.md#open-the-settings-pages).)

A Vezérlőpult azt mutatja be a **irányítópult** lap megnyitásakor. 

![Szabályozhatja a Panel irányítópult (először lap)](media/fxt-cluster-config/dashboard-1-node.png)

Ezen a képen az irányítópult egy újonnan létrehozott fürt, egy egyetlen csomóponttal.

## <a name="2-locate-the-node-to-add"></a>2. Keresse meg a csomópont hozzáadása

Csomópontok hozzáadásához kattintson a **beállítások** fülre, és válassza a **FXT csomópontok** lapját a **fürt** szakaszban.

![Vezérlőpult-beállítások (második lap) lapon, a fürt > FXT csomópontok betöltése](media/fxt-cluster-config/settings-fxt-nodes.png)

A **FXT csomópontok - választható le arról** listában jeleníti meg az összes hozzá nem rendelt FXT csomópontok (a legtöbb adatközpontban csak néhány kell. Keresse meg a FXT csomópontokat a fürthöz hozzáadni kívánt.

> [!Tip] 
> Ha a csomópont nem találja a kívánt a **Unjoined** listában, ellenőrizze, hogy megfelel-e ezek a követelmények:
> 
> * Van-e kapcsolva, és a volt-e egy [jelszóbeállítási kiváltó](fxt-node-password.md).
> * Elérheti hálózathoz kapcsolódik. Ha VLAN-okat használ, mint a fürtöt az ugyanazon VLAN-on kell lennie.
> * Érzékelhető a Bonjour protokollal. 
>
>   Bizonyos tűzfalbeállítások letiltása a Bonjour, amely megakadályozza a FXT operációs rendszer automatikusan észleli a csomópontok által használt TCP/UDP-portok.
> 
> Ha a hozzáadni kívánt csomópont nem található a listában, próbálja ki ezeket a megoldásokat: 
> 
> * Kattintson a **felderítése a manuális** gombbal is megkeresheti az IP-cím alapján.
> 
> * Manuálisan rendelje hozzá az ideiglenes IP-címet. Ez ritka, de akkor lehet szükség, ha a címkézett VLAN-okat használ, és a csomópontok nem érhetők a megfelelő hálózati, vagy a hálózat nem teszi lehetővé a helyi hozzárendelt IP-címek. Kövesse ezt a dokumentumot, a régi verzióját a [állítsa be kézzel a statikus IP-cím](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

A csomópont nevét, a IP-cím, a szoftver verziója és a megfelelőségi állapot jelennek meg a listában. Általában a **állapot** vagy szerint oszlop "Szeretne csatlakozni", vagy bemutat egy rendszer- vagy hardveres problémát, amely lehetővé teszi a csomópontot a fürthöz való csatlakoztatáshoz nem lehet áttelepíteni.

A **műveletek** oszlopnak gombok, amelyek lehetővé teszik a csomópont hozzáadása a fürthöz, vagy frissítse a szoftvert. A frissítés gomb automatikusan telepíti a szoftver verziója, amely megfelel a csomópontok a fürtben már.

A fürtben lévő csomópontok mindegyikének az operációs rendszer ugyanazon verzióját kell használnia, de nem kell szoftverek frissítése a csomópont hozzáadása előtt. Miután rákattintott a **való csatlakozás engedélyezése** gombra, a fürthöz való csatlakozás folyamat automatikusan ellenőrzi, és telepíti az operációs rendszer szoftver, amely megfelel a verziót a fürtön.

Az e lapon látható beállítások kapcsolatos további információkért olvassa el [ **fürt** > **FXT csomópontok** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) az útmutató a fürt konfiguráció.

## <a name="3-click-the-allow-to-join-button"></a>3. Kattintson a "Lehetővé teszik, hogy csatlakoztatás" gombra 

Kattintson a **csatlakozás engedélyezése*** gombra a **műveletek** a csomópont, amely a hozzáadni kívánt oszlop.

Után a gombra kattint, a csomópont állapota megváltozhat, előkészítése, hogyan adhat hozzá a fürt frissítésekor a szoftverek. 

Az alábbi képen látható, amely a (legnagyobb valószínűséggel, egy operációsrendszer-frissítés előtt felvett kezd) fürthöz való csatlakozás folyamatban van egy csomópont. Nincsenek gombok megjelennek a **műveletek** csomópontokat a fürthöz hozzáadni kívánt alatt álló oszlop.

![egy sort a csomópont tábla, egy csomópont nevét, IP címe, szoftver verziója, az "Engedélyezett csatlakozni" üzenet és egy üres utolsó oszlop megjelenítése](media/fxt-cluster-config/node-join-in-process.png)

Néhány pillanat múlva az új csomópont meg kell jelennie a lista tetején található fürtcsomópont a **FXT csomópontok** beállítások lapon. 

Ismételje meg a folyamatot a többi csomópont hozzáadása a fürthöz. Nem kell várni egy csomópontot a fürthöz való csatlakozás másik befejezéséhez.

## <a name="enable-high-availability"></a>Magas rendelkezésre állás engedélyezése

A második csomópont hozzáadása a fürthöz, után láthatja a figyelmeztető üzenetet a Vezérlőpult irányítópult, amely a magas rendelkezésre állású szolgáltatás nincs konfigurálva. 

Magas rendelkezésre állású (HA) lehetővé teszi, hogy a fürt csomópontjai esetében történő kompenzálás minden más, ha egy leáll. Alapértelmezés szerint nincs engedélyezve a magas rendelkezésre ÁLLÁS.

![A "egynél több csomóponttal rendelkező fürt, de HA nincs engedélyezve..." üzenetet az irányítópult lap a feltételek tábla](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Ne engedélyezze a magas rendelkezésre ÁLLÁSÚ mindaddig, amíg a fürt legalább három csomóponttal rendelkezik.

Kövesse az alábbi eljárás segítségével kapcsolja be a magas rendelkezésre ÁLLÁS: 

1. Betöltés a **magas rendelkezésre állású** lapját a **fürt** szakaszában a **beállítások** lapon.

   ![Magas rendelkezésre ÁLLÁSÚ konfiguráció lap (fürt > magas rendelkezésre állás). "Engedélyezése magas rendelkezésre ÁLLÁS" jelölőnégyzet felső és alsó van a Küldés gombra.](media/fxt-cluster-config/enable-ha.png)

2. Jelölje be a címkével ellátott **engedélyezése magas rendelkezésre ÁLLÁS** , és kattintson a **küldés** gombra. 

Egy riasztás jelenik meg a **irányítópult** annak ellenőrzéséhez, hogy engedélyezve van-e a magas rendelkezésre ÁLLÁS.

![Az üzenet megjelenítő irányítópult táblázat "Magas rendelkezésre ÁLLÁS teljes körűen konfigurálva"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>További lépések

A felvett összes csomópont a fürtben, úgy konfigurálja a fürt hosszú távú tárolás a a telepítés folytatásához.

> [!div class="nextstepaction"]
> [Adja hozzá a háttér tárolót, és állítsa be a virtuális névtér](fxt-add-storage.md)