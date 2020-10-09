---
title: Data Science Virtual Machine frissítése Ubuntu 18,04-re
titleSuffix: Azure Data Science Virtual Machine
description: Megtudhatja, hogyan frissíthet a CentOS és az Ubuntu 16,04 rendszerről a legújabb Ubuntu 18,04 Data Science Virtual Machinera.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, csoportos adatelemzési folyamat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: d57de4d52ccf3a029a8dd1350635fb65dd3ac829
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828567"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Data Science Virtual Machine frissítése Ubuntu 18,04-re

Ha olyan Data Science Virtual Machine fut, amely egy régebbi kiadást futtat, például Ubuntu 16,04 vagy CentOS, akkor telepítse át a DSVM Ubuntu 18,04-ra. Az áttelepítés során a rendszer a legújabb operációsrendszer-javításokat, illesztőprogramokat, előre telepített szoftvereket és a könyvtár verzióit fogja beszerezni. Ebből a dokumentumból megtudhatja, hogyan telepítheti át az Ubuntu régebbi verzióiról vagy a CentOS-ről. 

## <a name="prerequisites"></a>Előfeltételek

- Az SSH és a Linux parancssor ismerete

## <a name="overview"></a>Áttekintés

Kétféle módon lehet áttelepíteni:

- Helyben történő áttelepítés, más néven "ugyanaz a kiszolgáló" áttelepítés. Az áttelepítés új virtuális gép létrehozása nélkül frissíti a meglévő virtuális gépet. A helyben történő áttelepítés az Ubuntu 16,04-ról Ubuntu 18,04-re való áttelepítésének egyszerűbb módja.
- Párhuzamos áttelepítés, más néven "kiszolgálók közötti" áttelepítés. Ez az áttelepítés átviszi az adatait a meglévő virtuális gépről egy újonnan létrehozott virtuális GÉPRE. A párhuzamos áttelepítés a CentOS-ről Ubuntu 18,04-re való Migrálás módja. Érdemes lehet párhuzamosan áttelepíteni az Ubuntu-verziók közötti frissítést, ha úgy érzi, hogy a régi telepítése feleslegesen zsúfolt.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>A virtuális gép pillanatképének elkészítése, ha vissza kell állítania

A Azure Portalban keresse meg a **Pillanatképek** funkciót a keresősáv használatával. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

1. Válassza a **Hozzáadás**lehetőséget, amely a **pillanatkép létrehozása** lapra kerül. Válassza ki a virtuális gép előfizetését és erőforrás-csoportját. A **régió**mezőben válassza ki azt a régiót, ahol a cél tároló létezik. Válassza ki a DSVM és a további biztonsági mentési beállításokat. A **standard HDD** megfelelő tárolási típus ehhez a biztonsági mentési forgatókönyvhöz.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

2. Az összes adat kitöltése és érvényesítése után válassza a **felülvizsgálat + létrehozás** lehetőséget a pillanatkép ellenőrzéséhez és létrehozásához. A pillanatkép sikeres befejeződése után megjelenik egy üzenet, amely közli, hogy a telepítés befejeződött.

## <a name="in-place-migration"></a>Helyben történő áttelepítés

Ha régebbi Ubuntu-kiadást telepít át, helyi áttelepítés mellett dönthet. Ez az áttelepítés nem hoz létre új virtuális gépet, és kevesebb lépésből áll, mint az egymás melletti áttelepítés.  Ha párhuzamos áttelepítést szeretne végezni, mert nagyobb mértékben szeretné vezérelni a vezérlést, vagy mert egy másik disztribúcióból, például a CentOS-ből kíván áttelepíteni, ugorjon a [párhuzamos áttelepítés](#side-by-side-migration) szakaszra. 

1. A Azure Portal indítsa el a DSVM, és jelentkezzen be az SSH használatával. Ehhez válassza a **Kapcsolódás** és az **SSH** lehetőséget, és kövesse a kapcsolódási utasításokat. 

1. Miután kapcsolódott egy terminál-munkamenethez a DSVM, futtassa a következő frissítési parancsot:

    ```bash
    sudo do-release-upgrade
    ```

A frissítési folyamat eltarthat egy ideig. Ha a rendszer átadja, a program engedélyt kér a virtuális gép újraindítására. Válasz **Igen**. A rendszer újraindításakor leválasztja az SSH-munkamenetet.

### <a name="if-necessary-regenerate-ssh-keys"></a>Ha szükséges, az SSH-kulcsok újragenerálása

> [!IMPORTANT] 
> Előfordulhat, hogy a frissítés és az újraindítás után újra kell generálnia az SSH-kulcsokat.

A virtuális gép frissítését és újraindítását követően próbálja meg újra elérni az SSH-n keresztül. Lehetséges, hogy az IP-cím módosult az újraindítás során, ezért a kapcsolódás megkísérlése előtt erősítse meg.

Ha a **távoli GAZDAGÉP azonosításának hibája módosult**, újra kell létrehoznia az SSH hitelesítő adatait.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve"
```

Most már képesnek kell lennie az SSH-val való kapcsolódásra. Ha továbbra is problémákat tapasztal, a **Csatlakozás** oldalon kövesse az **SSH-kapcsolattal kapcsolatos problémák elhárítására**szolgáló hivatkozást.

## <a name="side-by-side-migration"></a>Párhuzamos áttelepítés

Ha a CentOS-ről végez áttelepítést, vagy tiszta operációs rendszert szeretne telepíteni, párhuzamos áttelepítést is végezhet. Az ilyen típusú áttelepítés több lépésből áll, de lehetővé teszi, hogy pontosan milyen fájlokat hajtson végre.

A más rendszerekből származó, a felsőbb rétegbeli forrás csomagok alapján történő áttelepítésnek viszonylag egyszerűnek kell lennie, például: [GYIK/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Dönthet úgy, hogy a fájlrendszer operációs rendszerének részeit frissíti, és meghagyja a felhasználói könyvtárakat, például `/home` a helyén. Ha elhagyja a régi felhasználói kezdőkönyvtár helyét, a rendszer némi problémát vár a GNOME/KDE menüivel és az egyéb asztali elemekkel kapcsolatban. Lehet, hogy a legegyszerűbb új felhasználói fiókokat létrehozni és a régi könyvtárakat csatlakoztatni a fájlrendszerben, hogy az áttelepítést követően a felhasználók anyagát referenciával, másolással vagy összekapcsolja.

### <a name="migration-at-a-glance"></a>Áttelepítési áttekintés

1.  Hozzon létre egy pillanatképet a meglévő virtuális gépről a korábban leírtaknak megfelelően

1.  Lemez létrehozása a pillanatképből

1.  Új Ubuntu-Data Science Virtual Machine létrehozása

1.  Felhasználói fiók (ok) újbóli létrehozása az új virtuális gépen

1.  Csatlakoztassa a pillanatfelvétel virtuális gép lemezét adatlemezként az új Data Science Virtual Machine

1.  A kívánt Adatmásolás manuális másolása

### <a name="create-a-disk-from-your-vm-snapshot"></a>Lemez létrehozása a virtuális gép pillanatképéről

Ha még nem hozott létre virtuálisgép-pillanatképet az előzőekben leírtak szerint, tegye meg a következőt:. 

1. A Azure Portalban keressen **lemezeket** , és válassza a **Hozzáadás**lehetőséget, amely megnyitja a **lemez** lapot.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

2. Állítsa be az **előfizetést**, az **erőforráscsoportot**és a **régiót** a virtuális gép pillanatképének értékeire. Válassza ki a létrehozandó lemez **nevét** .

3. Válassza a **forrás típusa** **pillanatképként** lehetőséget, és válassza ki a virtuális gép pillanatképét a **forrás pillanatképként**. Tekintse át és hozza létre a lemezt. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Új Ubuntu-Data Science Virtual Machine létrehozása

Hozzon létre egy új Ubuntu-Data Science Virtual Machine a [Azure Portal](https://portal.azure.com) vagy egy [ARM-sablon](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-tutorial-resource-manager)használatával. 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Hozza létre újra a felhasználói fiók (oka) t az új Data Science Virtual Machine

Mivel csak a régi számítógépről másolja az adatait, újra létre kell hoznia azokat a felhasználói fiókokat és szoftveres környezeteket, amelyeket az új gépen használni szeretne.

A Linux elég rugalmas ahhoz, hogy testreszabja az új telepítéshez tartozó címtárakat és elérési utakat a régi gép követéséhez. Általánosságban elmondható azonban, hogy könnyebben használhatja a modern Ubuntu előnyben részesített elrendezését, és módosíthatja a felhasználói környezetet és a szkripteket az alkalmazkodáshoz.

További információ [: gyors útmutató: a Linux Data Science Virtual Machine beállítása (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Csatlakoztassa a pillanatfelvétel virtuális gép lemezét adatlemezként az új Data Science Virtual Machine

1. Győződjön meg arról, hogy a Data Science Virtual Machine fut a Azure Portal.

2. A Azure Portal nyissa meg a Data Science Virtual Machine lapját. Válassza a **lemezek** panelt a bal oldali vasúton. Válassza a **meglévő lemezek csatolása**lehetőséget.

3. A **lemez neve** legördülő menüben válassza ki a régi virtuális gép pillanatképéről létrehozott lemezt.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

4. A virtuális gép frissítéséhez válassza a **Mentés** lehetőséget.

> [!Important]
> Az adatlemez csatlakoztatásakor a virtuális gépnek futnia kell. Ha a virtuális gép nem fut, a lemezeket helytelen sorrendben lehet hozzáadni, ami egy zavaros és potenciálisan nem rendszerindító rendszerhez vezethet. Ha a virtuális géppel együtt adja hozzá az adatlemezt, válassza az **X-et** az adatlemez mellett, indítsa el a virtuális gépet, majd csatlakoztassa újra.

### <a name="manually-copy-the-wanted-data"></a>A kívánt Adatmásolás manuális másolása 

1. Jelentkezzen be a futó virtuális gépre SSH használatával.

2. A következő parancs futtatásával erősítse meg, hogy csatlakoztatta a régi virtuális gépről készült pillanatképből létrehozott lemezt:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Az eredményeknek az alábbi képhez hasonlóan kell kinézniük. A rendszerképben a lemez `sda1` a gyökérhez van csatlakoztatva, és `sdb2` Ez a lemez `/mnt` . A régi virtuális gépről készített pillanatképből létrehozott adatlemezt a rendszer a `sdc1` csatlakoztatási hely hiánya által jelzett módon azonosítja, de még nem érhető el. Előfordulhat, hogy az eredmények eltérő azonosítóval rendelkeznek, de hasonló mintát kell látnia.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::
    
3. Az adatmeghajtó eléréséhez hozzon létre egy helyet a számára, és csatlakoztassa azt. Cserélje le a `/dev/sdc1` értéket a megfelelő értékre `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. A most `/datadrive` a régi Data Science Virtual Machine könyvtárait és fájljait tartalmazza. Helyezze át vagy másolja át a kívánt könyvtárakat vagy fájlokat az adatmeghajtóról az új virtuális gépre, ahogy szeretné.

További információ: [adatlemez csatlakoztatása egy Linux rendszerű virtuális géphez a portál használatával](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>A verziófrissítés és a verziófrissítés megerősítése

Függetlenül attól, hogy helyben vagy egymás melletti áttelepítés történt, ellenőrizze, hogy sikeresen frissítette-e a frissítést. Egy terminál-munkamenetből futtassa a következőt: 

```bash
cat /etc/os-release
```

És látnia kell, hogy Ubuntu 18,04-et futtat.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

A verzió módosítása a Azure Portal is látható.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Képernyőkép a Azure Portal és a keresősáv megjelenítéséről, a * * pillanatképek * * kiemelve":::

## <a name="next-steps"></a>Következő lépések

- [Adatelemzés egy Ubuntu adatelemzési géppel az Azure-ban](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough)
- [Milyen eszközöket tartalmaz az Azure Data Science Virtual Machine?](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/tools-included)
