---
title: 'Oktatóanyag: Csomópontok hozzáadása egy Azure FXT Edge Filer-fürthöz'
description: Csomópontok hozzáadása az Azure FXT Edge Filer tárológyorsítótárához
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551947"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Oktatóanyag: Fürtcsomópontok hozzáadása egy Azure FXT Edge Filer-fürthöz

Egy új Azure FXT Edge Filer-fürt jön létre csak egy csomó. Adjon hozzá legalább két további csomópontot, és engedélyezze a magas rendelkezésre állást, mielőtt más konfigurációt végezne. 

Ez az oktatóanyag bemutatja, hogyan adhat hozzá fürtcsomópontokat, és hogyan engedélyezheti a magas rendelkezésre állású (HA) szolgáltatást. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Csomópontok hozzáadása az FXT-fürthöz
> * Hogyan lehet engedélyezni a HA-t?

Az oktatóanyag lépései körülbelül 45 percet vesznek igénybe.

Az oktatóanyag megkezdése előtt kapcsolja be a hozzáadni kívánt csomópontokat, és [állítsa be a kezdeti jelszavakat.](fxt-node-password.md) 

## <a name="1-load-the-cluster-nodes-page"></a>1. A fürtcsomópontok lap betöltése

Nyissa meg a fürt Vezérlőpultját egy webböngészőben, és jelentkezzen be rendszergazdaként. (A részletes utasítások az áttekintő cikkben, [a Beállítások lapok megnyitása](fxt-cluster-create.md#open-the-settings-pages)csoportban találhatók.)

A Vezérlőpulton megjelenik az **Irányítópult** lap, amikor megnyílik. 

![Vezérlőpult irányítópultja (első lap)](media/fxt-cluster-config/dashboard-1-node.png)

Ez a kép egy újonnan létrehozott fürt irányítópultját jeleníti meg egyetlen csomópontossal.

## <a name="2-locate-the-node-to-add"></a>2. Keresse meg a hozzáadni szeretne csomópontot

Csomópontok hozzáadásához kattintson a **Beállítások** fülre, és válassza az **FXT-csomópontok** lapot a **Fürt** szakaszban.

![Vezérlőpult Beállítások lapja (második lap) a Fürt > FXT-csomópontok betöltésével](media/fxt-cluster-config/settings-fxt-nodes.png)

Az **FXT-csomópontok - Unjoined** lista az összes ki nem osztott FXT-csomópontot jeleníti meg (a legtöbb adatközpontban csak néhány található. Keresse meg a fürthöz hozzáadni kívánt FXT-csomópontokat.

> [!Tip] 
> Ha nem találja a kívánt csomópontot az **Unjoined** listán, ellenőrizze, hogy megfelel-e az alábbi követelményeknek:
> 
> * Ez be van kapcsolva, és volt egy [root jelszó készlet](fxt-node-password.md).
> * Olyan hálózathoz csatlakozik, amelyhez el lehet jutni. VlAN-ok használata esetén a fürttel azonos VLAN-on kell lennie.
> * A Bonjour protokollal kimutatható. 
>
>   Egyes tűzfalbeállítások blokkolják a Bonjour által használt TCP/UDP portokat, ami megakadályozza, hogy az FXT operációs rendszer automatikusan észlelje a csomópontokat.
> 
> Ha a hozzáadni kívánt csomópont nem szerepel a listán, próbálkozzon az alábbi megoldásokkal: 
> 
> * Kattintson a **Kézi felderítés** gombra, hogy megtalálja azt az IP-címet.
> 
> * Ideiglenes IP-címek manuális hozzárendelése. Ez ritka, de szükség lehet, ha címkézett VLAN-okat használ, és a csomópontok nem a megfelelő hálózaton vannak, vagy a hálózat nem engedélyezi az önkiszolgáló IP-címeket. A [statikus IP-cím manuális beállításához](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)kövesse a dokumentum régebbi verziójának utasításait.

A csomópont neve, IP-címe, szoftververziója és jogosultsági állapota megjelenik a listában. Az **Állapot** oszlop általában a "Csatlakozni szeretne" oszlopban, vagy olyan rendszer- vagy hardverproblémát ír le, amely miatt a csomópont nem jogosult a fürthöz való csatlakozásra.

A **Műveletek** oszlop ban található gombok, amelyek lehetővé teszik a csomópont hozzáadását a fürthöz vagy a szoftver frissítését. A frissítés gomb automatikusan telepíti a fürtben már meglévő csomópontoknak megfelelő szoftververziót.

A fürt összes csomópontjának az operációs rendszer azonos verzióját kell használnia, de nem kell frissítenie a szoftvert a csomópont hozzáadása előtt. Miután a **Csatlakozás engedélyezése** gombra kattintott, a fürtillesztési folyamat automatikusan ellenőrzi és telepíti a fürtön lévő verziónak megfelelő operációsrendszer-szoftvert.

Ha többet szeretne megtudni az ezen az oldalon található beállításokról, olvassa el a [ **Fürt** > FXT-csomópontjait](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) a Fürtkonfigurációs útmutatóban.

## <a name="3-click-the-allow-to-join-button"></a>3. Kattintson a "Csatlakozás engedélyezése" gombra 

Kattintson a hozzáadni kívánt csomópont **Műveletek** oszlopának **Csatlakozás engedélyezése*** gombjára.

Miután a gombra kattintott, a csomópont állapota megváltozhat, amikor a szoftver frissül a fürthöz való hozzáadásának előkészítéseként. 

Az alábbi képen egy csomópont, amely a fürthöz való csatlakozás folyamatában van (valószínűleg egy operációs rendszer frissítése a hozzáhozzáadása előtt). A **Műveletek** oszlopban nem jelennek meg gombok a fürthöz való hozzáadás folyamatban lévő csomópontok számára.

![a csomóponttábla egy sora, amely egy csomópont nevét, IP-címét, szoftververzióját, a "Csatlakozás engedélyezve" üzenetet és egy üres utolsó oszlopot jelenít meg](media/fxt-cluster-config/node-join-in-process.png)

Néhány pillanat múlva az új csomópontnak meg kell jelennie az **FXT-csomópontok** beállítási lapjának tetején található fürtcsomópontok listájában. 

Ismételje meg ezt a folyamatot a többi csomópont hozzáadásához a fürthöz. Nem kell megvárnia, hogy egy csomópont befejezze a fürthöz való csatlakozást, mielőtt egy másikat indítana.

## <a name="enable-high-availability"></a>Magas rendelkezésre állás engedélyezése

Miután hozzáadott egy második csomópontot a fürthöz, megjelenhet egy figyelmeztető üzenet a vezérlőpult irányítópultján, amely szerint a magas rendelkezésre állású szolgáltatás nincs konfigurálva. 

A magas rendelkezésre állás (HA) lehetővé teszi, hogy a fürtcsomópontok kompenzálják egymást, ha az egyik leáll. A HA alapértelmezés szerint nincs engedélyezve.

![Irányítópult lap a következő üzenettel: "A fürt egynél több csomóponttal rendelkezik, de a HA nincs engedélyezve ..." a Feltételek táblázatban](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Ne engedélyezze a HA-t, amíg legalább három csomópont nem található a fürtben.

A HA bekapcsolásához kövesse az alábbi eljárást: 

1. Töltse be a **Magas rendelkezésre állás** lapot a **Beállítások** lap **Fürt** csoportjában.

   ![HA konfigurációs lap (fürt > magas rendelkezésre állás). Az "Enable HA" jelölőnégyzet felül, a küldés gomb pedig alul található.](media/fxt-cluster-config/enable-ha.png)

2. Kattintson a **HA engedélyezése** feliratú mezőre, majd a **Küldés** gombra. 

Egy riasztás jelenik meg az **irányítópulton,** amely megerősíti, hogy a HA engedélyezve van.

![Irányítópult-táblázat a következő üzenettel: "A HA most már teljesen konfigurálva van"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>További lépések

Miután hozzáadja a fürt összes csomópontját, folytassa a telepítést a fürt hosszú távú tárolójának konfigurálásával.

> [!div class="nextstepaction"]
> [Háttértároló hozzáadása és a virtuális névtér beállítása](fxt-add-storage.md)