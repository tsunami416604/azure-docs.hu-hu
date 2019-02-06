---
title: Fürtfelügyeleti Avere vFXT – Azure
description: Hogyan Avere fürt kezelése – adjon hozzá vagy távolíthat el csomópontokat, újraindítás, állítsa le vagy a vFXT fürt megszüntetése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: bc91b052d3d69924af9afeb012c0ebb5be01dfbf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745554"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt felügyelete

A fürt létrehozása után szükség lehet a fürtcsomópontok hozzáadása vagy leállítani, vagy indítsa újra a fürtöt. És amikor befejeződött a projekt leállítása és a fürt eltávolításához véglegesen ismernie kell. 

A fürt felügyeleti feladat függően előfordulhat, hogy kell Avere Vezérlőpultján, a vfxt.py parancssori fürt létrehozási parancsprogrammal vagy az Azure portal használatával teheti meg. 

Ez a táblázat áttekintést nyújt, amelyek az egyes feladatokhoz használható eszközök. 

| Műveletek | Avere Vezérlőpult | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Fürtcsomópontok hozzáadása | nem | igen | nem |
| Fürtcsomópontok eltávolítása | igen | nem | nem |
| Fürt csomópont leállítása | Igen (is szolgáltatások újraindítása vagy újraindítás) | nem | kockázatszámító le a csomópont virtuális Gépet a portálon egy Csomóponthiba kerül értelmezésre |
| Indítsa el egy leállított csomópont | nem | nem | igen |
| Szüntesse meg egyetlen fürtcsomópont | nem | nem | igen |
| Indítsa újra a fürt |  |  |  |
| Állítsa le, vagy állítsa le a fürtöt biztonságos | igen | igen | nem |
| A fürt megszüntetése  | nem | igen | Igen, azonban nem garantált az adatok integritásának megőrzése |

Az egyes eszközök részletes útmutatást az alábbiakban találhatók.

## <a name="about-stopped-instances-in-azure"></a>Tudnivalók a leállított példány az Azure-ban

Állítsa le, vagy minden olyan Azure virtuális gép leállítása, számításidíj leállítja, de továbbra is külön kell fizetni a tárhely. Ha leállítja a vFXT csomópont vagy a teljes vFXT fürt, és nem kívánja újraindítani, a kapcsolódó virtuális gépek törléséhez használjon az Azure Portalon. 

Az Azure Portalon egy *leállt* (amely lehet újraindítani) csomópont állapotát megjeleníti **leállt** az Azure Portalon; egy *törölt* csomópont állapotát megjeleníti **leállt (felszabadítva)**  és azt már nem tekintetében számítási és tárolási számítunk fel díjat.

Mielőtt törölné a virtuális Gépet, ellenőrizze, hogy minden módosított adat lett írva a gyorsítótárból háttér-tároló Avere Vezérlőpult vagy vfxt.py lehetőségeinek használatával állítsa le vagy a fürt leállításához.

## <a name="manage-the-cluster-with-avere-control-panel"></a>A fürt Avere Vezérlőpultjának kezelése 

Ezeket a feladatokat a Avere Vezérlőpult használható: 

* Állítsa le és indítsa újra az egyes csomópontok
* Eltávolít egy csomópontot a fürtből
* Állítsa le, vagy indítsa újra a teljes fürt

Avere Vezérlőpult priorizálja az adatok integritásának megőrzése, így a módosított adatokat írni az háttérrendszer tárolási valószínűleg felülíró művelet előtt megkísérli. Így a biztonságosabb megoldás, mint a Avere portálon. 

Avere a Vezérlőpult elérése webböngészőből. Kövesse a [a vFXT fürt eléréséhez](avere-vfxt-cluster-gui.md) Ha segítségre van szüksége.

### <a name="manage-nodes-with-avere-control-panel"></a>Kezel a csomópontokat Avere Vezérlőpult

A **FXT csomópontok** beállítások lapon a vezérlők az egyes csomópontok kezeléséhez.

Állítsa le, indítsa újra, vagy eltávolít egy csomópontot, keresse meg a csomópont a listában a a **FXT csomópontok** lapon, és kattintson a megfelelő gombra annak **műveletek** oszlop.

> [!NOTE] 
> IP-címek előfordulhat, hogy helyezze át a fürt csomópontjai között, ha az aktív csomópontok számát.

Olvasási [fürt > csomópontok FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) a Avere fürt beállítások útmutató további információt.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Állítsa le és indítsa újra a fürtöt a következővel Avere Vezérlőpult

A **rendszer karbantartása** beállítások lapon a parancsok a fürt szolgáltatások újraindítása, a fürt újraindítása vagy biztonságosan tárolóház le a fürtöt. Olvasási [felügyelet > rendszer-karbantartási](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (a Avere a fürt beállítások útmutatója) részletekért.

Ha egy fürtöt leállítás fázisában van, az állapotjelző üzenetek tesznek közzé a **irányítópult** első lapján. Néhány pillanat múlva a Vezérlőpult Avere munkamenet nem válaszol, ami azt jelenti, hogy a fürt le lett állítva.

## <a name="manage-the-cluster-with-vfxtpy"></a>A fürtöt a következővel vfxt.py kezelése

vfxt.PY egy olyan parancssori eszköz a fürt létrehozása és kezelése. 

vfxt.PY előre telepítve van a virtuális gép fürt vezérlőn. Ha szeretné telepíteni azt egy másik rendszerre, tekintse meg a dokumentációban a <https://github.com/Azure/AvereSDK>.

A vfxt.py parancsfájlt a fürt felügyeleti feladatokhoz használható:

* Új csomópontok hozzáadása egy fürthöz
* A fürt indítása vagy leállítása  
* Egy fürt megszüntetése

Például Avere Vezérlőpulton vfxt.py operations próbálja meg ellenőrizze, hogy a módosított adatokat a háttér-tároló leáll, vagy a fürt vagy a csomópont megsemmisítése előtt véglegesen tárolja. Így a biztonságosabb megoldás, mint a Avere portálon.

Egy teljes vfxt.py használati útmutató a Githubon érhető el: [Fürt felhőfelügyeletet vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Vfxt.py a fürtcsomópontok hozzáadása

Egy mintaszkriptet parancsot a fürtcsomópontok hozzáadása a fürt vezérlőn tartalmazza. Keresse meg ``./add-nodes`` a vezérlőre, és nyissa meg egy szövegszerkesztőben a fürt adatokkal testre szabni. 

A fürt ezen parancs használatához futnia kell. 

Adja meg a következő értékeket: 

* Erőforráscsoport-nevet a fürt és a hálózati és tárolási erőforrásokat, ha azok nem ugyanaz, mint a fürt
* Fürt helye
* Fürt hálózatot és alhálózatot 
* Fürtszerepkört csomópont hozzáférés 
* Felügyeleti IP-címet és a rendszergazdai jelszó 
* Csomópontok hozzáadása (1, 2 vagy 3)
* Csomópont példány típusát és a gyorsítótár mérete értékei 

Ha nem használ a prototípust, kell hozhatnak létre a következő, beleértve a fent leírt adatokat összes hasonló parancsot. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role ROLE_NAME \
   --log ~/vfxt.log
```

További információkért olvassa el [csomópontok hozzáadása egy fürthöz](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) a vfxt.py használati útmutatójában.

### <a name="stop-a-cluster-with-vfxtpy"></a>Egy fürt vfxt.py leállítása

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>A leállított fürt kezdődnie vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

A fürt le van állítva, mert akkor meg kell adnia a példány azonosítók, adja meg a fürt csomópontjai. Olvasási [megadásával módosíthatja azon fürt](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) a további vfxt.py használati útmutatóban.

### <a name="destroy-a-cluster-with-vfxtpy"></a>A vfxt.py egy fürt megszüntetése

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A beállítás ``--quick-destroy`` is használható, ha nem szeretné, hogy a fürt gyorsítótárból módosított adatokat írni.

Olvassa el a [vfxt.py – használati útmutató](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) további információt.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Fürt virtuális gépek kezelése az Azure Portalról 

Az Azure portal segítségével semmisítse meg a fürt virtuális gépeket külön-külön, de az adatok integritásának megőrzése nem garantált, ha a fürt nem áll le áramtalanították első. 

Az Azure Portalon a fürt felügyeleti feladatokhoz használható: 

* Indítsa el egy leállított vFXT csomópont
* Állítsa le az egyes vFXT csomópont (a fürt értelmezi, mint a tárolócsomópont-hiba)
* Egy vFXT fürt megszüntetése *Ha* nem kell annak érdekében, hogy a fürt gyorsítótárban módosított adatokat a core filer írt
* Végleg eltávolítja a vFXT csomópontok és más fürterőforrások után azok álltak le biztonságosan

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Az Azure Portalról vFXT-példányok újraindítása

Indítsa újra a leállított csomópont van szüksége, ha az Azure Portalon kell használnia. Válassza ki **virtuális gépek** a bal oldali menüben és kattintson a virtuális gép nevét a listában, az Áttekintés lap megnyitásához.

Kattintson a **Start** gombra a virtuális gép újraaktiválásához az Áttekintés oldal tetején.

![Az Azure portal képernyőkép egy leállított virtuális gép elindítása](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Törlés a fürtcsomópontok

Ha szeretne törölni egy csomópontot a vFXT fürtről, de ne a fürt része, akkor először [eltávolíthatja a csomópontot a fürtről](#manage-nodes-with-avere-control-panel) a Avere Vezérlőpult.

> [!CAUTION]
> Ha egy csomópont első eltávolítaná a vFXT fürt nélkül törli, adat elveszhet.

Véglegesen megsemmisül egy vagy több példány vFXT csomópontként használni, használja az Azure Portalon.
Válassza ki **virtuális gépek** a bal oldali menüben és kattintson a virtuális gép nevét a listában, az Áttekintés lap megnyitásához.

Kattintson a **törlése** gomb kiválasztásával helyezheti a virtuális gép az Áttekintés oldal tetején.

Ez a módszer segítségével véglegesen a fürtcsomópontok eltávolítása után azok álltak le biztonságosan. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Semmisítse meg a fürtöt az Azure Portalról

> [!NOTE] 
> Ha azt szeretné, hogy a többi ügyfél módosításokat a gyorsítótárban való írásra háttér-tároló, vagy használjon-e a vfxt.py `--destroy` lehetőséget vagy a Vezérlőpultban a Avere leállíthatók a fürt az Azure Portalon a csomópont-példányok eltávolítása előtt.

Az Azure Portalon törlésével véglegesen megsemmisítheti csomópont példányok. Törölheti őket egyenként leírtaknak megfelelően a fenti, vagy használhatja a **virtuális gépek** minden szükséges a fürt virtuális gépeihez, jelölje ki azokat a jelölőnégyzeteket, majd kattintson a lap a **törlése** gombra kattintva távolítsa el őket az összes egy műveletet.

![Virtuális gépek listáját a portálon a "fürt", melyek közül három a négy be van jelölve, és a kiemelt kifejezés szerint szűrve](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Törölje a fürt további erőforrások az Azure Portalról

Ha hozta létre kimondottan a vFXT fürt további erőforrások, érdemes távolítsa el őket a fürt kell részeként. Nem kell megsemmisíteni a szükséges adatokat, vagy bármely más projektek megosztott elemeket tartalmazó elemeket.

Törlés a fürtcsomópontok, vegye figyelembe ezeket az összetevőket eltávolítása: 

* A fürt vezérlő VM
* Fürtcsomópontok hozzárendelt adatlemezek
* Hálózati adapterek és a kiszolgálófürt-összetevők társított nyilvános IP-címek
* Virtuális hálózatok
* Storage-fiókok (**csak** ha azok nem tartalmaznak fontos adatokat)
* Rendelkezésre állási csoport 

![Az Azure portal egy tesztfürt létrehozott "minden erőforrás" lista erőforrások megjelenítése](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Egy fürt erőforráscsoport törlése az Azure Portalról

Ha létrehozott egy erőforráscsoportot, kifejezetten a fürt üzemeltetheti, az erőforráscsoport megsemmisítése által is semmisítse meg a a fürt minden kapcsolódó erőforrás. 

> [!Caution] 
> Csak akkor szüntesse meg az erőforráscsoportot, ha biztos benne, hogy semmi érték a csoportban található. Például ellenőrizze, hogy minden szükséges adatokat helyeztünk át minden olyan storage-tárolók az erőforráscsoporton belül.  

Egy erőforráscsoport törléséhez kattintson **erőforráscsoportok** a portálon, és a szűrő a bal oldali menüben lévő keresése az erőforráscsoportok listájának létrehozott a vFXT fürt. Válassza ki az erőforráscsoportot, és kattintson a panel jobb oldalán a három pontra. Válassza az **Erőforráscsoport törlése** elemet. A portál ekkor megkérdezi, hogy erősítse meg a törlést, amely nem vonható vissza.  

![A "Erőforráscsoport törlése" művelethez erőforráscsoport](media/avere-vfxt-delete-resource-group.png)
