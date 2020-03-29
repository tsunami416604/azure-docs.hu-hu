---
title: Az Avere vFXT-fürt kezelése - Azure
description: Az Avere-fürt kezelése - csomópontok hozzáadása vagy eltávolítása, újraindítás, leállítás vagy a vFXT-fürt megsemmisítése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153479"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt felügyelete

Az Avere vFXT azure-fürthöz való életciklusának egy bizonyos pontján előfordulhat, hogy fürtcsomópontokat kell hozzáadnia, vagy el kell indítania vagy újra kell indítania vagy újra kell indítania a fürtöt. Amikor a projekt befejeződött, tudnia kell, hogyan állíthatja le és távolíthatja el véglegesen a fürtöt.

Ez a cikk a fürtcsomópontok és más alapvető fürtműveletek hozzáadását és eltávolítását ismerteti. Ha módosítania kell a fürt beállításait, vagy figyelnie kell annak működését, használja az [Avere vezérlőpultot.](avere-vfxt-cluster-gui.md)

A felügyeleti feladattól függően előfordulhat, hogy három különböző eszköz egyikét kell használnia: Avere Vezérlőpult, a vfxt.py parancssori fürtfelügyeleti parancsfájl és az Azure Portal.

Ez a táblázat áttekintést nyújt abból, hogy mely eszközök használhatók az egyes feladatokhoz.

| Műveletek | Avere vezérlőpult | vfxt.py  | Azure portál |
| --- | --- | --- | --- |
| Fürtcsomópontok hozzáadása | nem | igen | nem |
| Fürtcsomópontok eltávolítása | igen | nem | nem |
| Fürtcsomópont leállítása | igen (is újraindíthatja a szolgáltatásokat, vagy indítsa újra) | nem | A portálról származó virtuális csomópont lekapcsolása csomóponthibaként lesz értelmezve |
| Leállított csomópont indítása | nem | nem | igen |
| Egyetlen fürtcsomópont megsemmisítése | nem | nem | igen |
| A fürt újraindítása |  |  |  |
| A fürt biztonságos leállítása vagy leállítása | igen | igen | nem |
| A fürt megsemmisítése  | nem | igen | Igen, de az adatok integritása nem garantált. |

Az egyes eszközökrészletes utasításait az alábbiakban találja.

## <a name="about-stopped-instances-in-azure"></a>Leállított példányok az Azure-ban

Ha leállít vagy leállít egy Azure-virtuális gép, leállítja a számítási díjak at, de továbbra is fizetnie kell a tárhelyért. Ha leállít egy vFXT-csomópontot vagy a teljes virtuális gépfürtöt, és nem kívánja újraindítani, az Azure Portalon kell törölni a kapcsolódó virtuális gépeket.

Az Azure Portalon egy *leállított* csomópont (amely újraindítható) az Azure Portalon **leállított** állapotot jeleníti meg. A *törölt* csomópont **állapotleállítva (felszabadított)** állapotot jelenít meg, és már nem merül fel számítási vagy tárolási költségek.

A virtuális gép törlése előtt győződjön meg arról, hogy az összes módosított adat a gyorsítótárból a háttértárolóba lett írva az Avere vezérlőpult használatával, vagy vfxt.py lehetőségeket a fürt leállításához vagy leállításához.

## <a name="manage-the-cluster-with-avere-control-panel"></a>A fürt kezelése az Avere vezérlőpultjával

Az Avere vezérlőpult a következő feladatokhoz használható:

* Egyes csomópontok leállítása vagy újraindítása
* Csomópont eltávolítása a fürtből
* A teljes fürt leállítása vagy újraindítása

Az Avere Vezérlőpult prioritásként kezeli az adatok integritását, ezért megkísérli a módosított adatokat háttértárolóvá írni egy esetlegesen romboló művelet előtt. Ez biztonságosabbá teszi, mint az Azure Portal.

Az Avere Vezérlőpult webböngészőből való elérése. Ha segítségre van szüksége, kövesse [az Access the vFXT cluster (Hozzáférés a vFXT-fürt)](avere-vfxt-cluster-gui.md) című témakör utasításait.

### <a name="manage-nodes-with-avere-control-panel"></a>Csomópontok kezelése az Avere vezérlőpultjával

Az **FXT-csomópontok beállítási** lapja az egyes csomópontok kezeléséhez szolgáló vezérlőkkel rendelkezik.

A csomópont leállításához, újraindításához vagy eltávolításához keresse meg a csomópontot az **FXT-csomópontok** lapján, és kattintson a megfelelő gombra a **Műveletek** oszlopban.

> [!NOTE]
> Az IP-címek a fürtcsomópontok között mozoghatnak, amikor az aktív csomópontok száma megváltozik.

További információért olvassa el [a fürt> FXT-csomópontokat](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) az Avere fürtbeállítási útmutatójában.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>A fürt leállítása vagy újraindítása az Avere vezérlőpultjával

A **Rendszerkarbantartás** beállításai lap parancsokat tartalmaz a fürtszolgáltatások újraindításához, a fürt újraindításához vagy a fürt biztonságos leállításához. A részletekért olvassa el [a Felügyeleti > a rendszer karbantartása](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) című részt (az Avere fürtbeállítási útmutatójában).

Amikor egy fürt leáll, állapotüzeneteket küld az **Irányítópult** lapra. Néhány pillanat múlva az üzenetek leállnak, és végül az Avere Vezérlőpult munkamenete nem válaszol, ami azt jelenti, hogy a fürt leállt.

## <a name="manage-the-cluster-with-vfxtpy"></a>A fürt kezelése vfxt.py

vfxt.py a fürt létrehozásának és kezelésének parancssori eszköze.

vfxt.py elő van telepítve a fürtvezérlő virtuális gépén. Ha egy másik rendszerre szeretné telepíteni, olvassa <https://github.com/Azure/AvereSDK>el a dokumentációt a.

A vfxt.py parancsfájl a fürtkezelési feladatokhoz használható:

* Új csomópontok hozzáadása fürthöz
* Fürt leállítása vagy indítása  
* Fürt megsemmisítése

Az Avere vezérlőpulthoz hasonlóan vfxt.py műveletek is igyekeznek biztosítani, hogy a módosított adatok véglegesen a háttértárolón legyenek tárolva, mielőtt leállítanák vagy megsemmisítenék a fürtöt vagy csomópontot. Ez biztonságosabbá teszi, mint az Azure Portal.

A teljes vfxt.py használati útmutató elérhető a GitHubon: [Felhőalapú fürtkezelés vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Fürtcsomópontok hozzáadása vfxt.py

A fürtvezérlő egy mintaparancs-parancsfájlt tartalmaz a fürtcsomópontok hozzáadásához. Keresse ``./add-nodes`` meg a vezérlőt, és nyissa meg egy szerkesztőben, hogy testre szabhassa a fürtadatokkal.

A parancs használatához a fürtnek futnia kell.

Adja meg a következő értékeket:

* A fürt, valamint a hálózati és tárolási erőforrások erőforráscsoportneve, ha nem ugyanabban az erőforráscsoportban vannak, mint a fürt
* Fürt helye
* Fürthálózat és -alhálózat
* Fürtcsomópont-hozzáférési szerepkör (a beépített [Avere operátor](../role-based-access-control/built-in-roles.md#avere-operator)szerepkör használata)
* Fürtkezelés IP-címe és rendszergazdai jelszava
* Hozzáadandó csomópontok száma (1, 2 vagy 3)
* Csomópontpéldány típusa és gyorsítótárméret-értékei

Ha nem használja a prototípust, meg kell építeni egy parancsot, mint a következő, beleértve az összes fent leírt információt.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

További információért olvassa el a [Csomópontok hozzáadása a fürthöz](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) című vfxt.py használati útmutatóban című részt.

### <a name="stop-a-cluster-with-vfxtpy"></a>Fürt leállítása vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Leállított fürt indítása vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Mivel a fürt le van állítva, a fürtcsomópontok megadásához példányazonosítókat kell átadnia. Olvassa [el: További információ: További információ: Adja meg, hogy melyik fürtöt szeretné módosítani](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) a vfxt.py használati útmutatóban.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Pusztítson el egy fürtöt vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A ``--quick-destroy`` beállítás akkor használható, ha nem szeretné menteni a módosított adatokat a fürt gyorsítótárából.

További információkért olvassa el a [vfxt.py használati útmutatóját.](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Fürtvirtuális gépek kezelése az Azure Portalról

Az Azure Portalon lehet használni a fürt virtuális gépei külön-külön megsemmisítésére, de az adatok integritása nem garantált, ha a fürt nem áll le tisztán először.

Az Azure Portal a fürtkezelési feladatokhoz használható:

* Leállított vFXT-csomópont indítása
* Egy virtuális gép virtuális gép csomópontjának leállítása (a fürt ezt csomóponthibaként értelmezi)
* A vFXT-fürt megsemmisítése, *ha* nem kell biztosítania, hogy a fürtgyorsítótárban lévő módosított adatok a központi fájlkezelőbe legyenek írva
* A virtuális fxt-csomópontok és más fürterőforrások végleges eltávolítása a biztonságos leállításuk után

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>A vFXT-példányok újraindítása az Azure Portalról

Ha újra kell indítania egy leállított csomópontot, az Azure Portalt kell használnia. Válassza a **virtuális gépek** a bal oldali menüben, majd kattintson a virtuális gép nevét a listában, hogy nyissa meg az áttekintő lapot.

Kattintson a **Start** gombra az áttekintő lap tetején a virtuális gép újraaktiválásához.

![Az Azure Portal képernyője a leállított virtuális gép indításának lehetőségét jeleníti meg](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Fürtcsomópontok törlése

Ha törölni szeretne egy csomópontot a vFXT-fürtből, de meg szeretné tartani a fürt fennmaradó részét, először [távolítsa el a csomópontot a fürtből](#manage-nodes-with-avere-control-panel) az Avere vezérlőpultjával.

> [!CAUTION]
> Ha anélkül töröl egy csomópontot, hogy először eltávolítaná azt a vFXT-fürtből, az adatok elveszhetnek.

A vFXT-csomópontként használt egy vagy több példány végleges megsemmisítéséhez használja az Azure Portalt.
Válassza a **virtuális gépek** a bal oldali menüben, majd kattintson a virtuális gép nevét a listában, hogy nyissa meg az áttekintő lapot.

Kattintson a **Törlés** gombra az áttekintő lap tetején a virtuális gép végleges megsemmisítéséhez.

Ezzel a módszerrel véglegesen eltávolíthatja a fürtcsomópontokat, miután azok biztonságosan leálltak.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>A fürt megsemmisítése az Azure Portalról

> [!NOTE]
> Ha azt szeretné, hogy a gyorsítótárban fennmaradó ügyfélmódosításokat háttértárolóba `--destroy` írja, használja a vfxt.py beállítást, vagy az Avere vezérlőpultsegítségével állítsa le a fürtöt tiszta módon, mielőtt eltávolítaná a csomópontpéldányokat az Azure Portalon.

A csomópontpéldányokat véglegesen megsemmisítheti, ha az Azure Portalon leszeretné őket. Törölheti őket egyenként a fent leírtak szerint, vagy a **Virtuális gépek** lapon megkeresheti az összes fürtvirtuális gépet, kijelölheti őket a jelölőnégyzetekkel, és a **Törlés gombra** kattintva egyetlen műveletben eltávolíthatja őket.

![A portálon lévő virtuális gépek listája, a "fürt" kifejezés sel szűrve, a négy ből három ellenőrzött és kiemelve](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>További fürterőforrások törlése az Azure Portalról

Ha további erőforrásokat hozott létre kifejezetten a vFXT-fürthöz, érdemes lehet eltávolítani őket a fürt lebontásának részeként. Ne semmisítse meg a szükséges adatokat tartalmazó elemeket, illetve a más projektekkel megosztott elemeket.

A fürtcsomópontok törlése mellett érdemes lehet eltávolítani ezeket az összetevőket:

* A fürtvezérlő Virtuális gépe
* Fürtcsomópontokhoz társított adatlemezek
* Fürtösszetevőkhöz társított hálózati adapterek és nyilvános IP-k
* Virtuális hálózatok
* Tárolótárolók és tárolófiókok **(csak** akkor, ha nem tartalmaznak fontos adatokat)
* Rendelkezésre állási csoport

![Az Azure Portal "minden erőforrás" listája a tesztfürthöz létrehozott erőforrásokat tartalmazza](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Fürt erőforráscsoportjának törlése az Azure Portalról

Ha létrehozott egy erőforráscsoportot kifejezetten a fürt elhelyezésére, megsemmisítheti a fürt összes kapcsolódó erőforrását az erőforráscsoport megsemmisítésével.

> [!Caution]
> Csak akkor pusztítsa el az erőforráscsoportot, ha biztos abban, hogy semmi értékes nem található a csoportban. Győződjön meg például arról, hogy az erőforráscsoporton belül lévő tárolótárolókból áthelyezte a szükséges adatokat.  

Erőforráscsoport törléséhez kattintson a portál bal oldali menüjében az **Erőforráscsoportok** elemre, és szűrje az erőforráscsoportok listáját a vFXT-fürthöz létrehozott elem megkereséséhez. Jelölje ki az erőforráscsoportot, és kattintson a panel jobb oldalán található három pontra. Válassza az **Erőforráscsoport törlése** elemet. A portál kérni fogja, hogy erősítse meg a törlést, ami visszafordíthatatlan.

![Az "Erőforráscsoport törlése" műveletet megjelenítő erőforráscsoport](media/avere-vfxt-delete-resource-group.png)
