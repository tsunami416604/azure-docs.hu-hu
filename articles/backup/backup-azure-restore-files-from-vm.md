---
title: 'Az Azure Backup: Fájlok és mappák helyreállítása az Azure virtuális gép biztonsági mentése'
description: Fájlok helyreállítása az Azure virtuális gép helyreállítási pontot
services: backup
author: pvrk
manager: shivamg
keywords: elemszintű helyreállítás; az Azure virtuális gép biztonsági mentése; fájlok helyreállítása fájlok helyreállítása Azure virtuális gépből
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: pullabhk
ms.openlocfilehash: d38da87bae07dadb10894593dd41ded22f5f162d
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638307"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása Azure virtuális gépek biztonsági mentése

Az Azure Backup lehetővé teszi, hogy a visszaállítás [Azure-beli virtuális gépek (VM) és a lemezek](./backup-azure-arm-restore-vms.md) az Azure virtuális gép biztonsági mentése, más néven a helyreállítási pontokat. Ez a cikk ismerteti a fájlok és mappák helyreállítása az Azure virtuális gép biztonsági másolatából. Fájlok és mappák visszaállítása csak az Azure virtuális gépek, a Resource Manager modellel üzembe helyezett és a egy Recovery services-tárolót a védett érhető el.

> [!Note]
> Ez a funkció az Azure virtuális gépek a Resource Manager modellel üzembe helyezett és a egy Recovery Services-tárolót a védett érhető el.
> Fájlhelyreállítás egy titkosított virtuális gép biztonsági mentése nem támogatott.
>

## <a name="mount-the-volume-and-copy-files"></a>Csatlakoztassa a kötet, és átmásolja a fájlokat

A fájlok és mappák visszaállítása a helyreállítási pontot, nyissa meg a virtuális gép, és válassza ki a kívánt helyreállítási pontot.

1. Jelentkezzen be a [az Azure portal](http://portal.Azure.com) , és kattintson a bal oldali ablaktáblában **virtuális gépek**. Virtuális gépek listájából válassza ki a virtuális gépet, hogy a virtuális gép irányítópultjának megnyitásához.

2. A virtuális gép menüben kattintson **Backup** a biztonsági mentés irányítópultjának megnyitásához.

    ![Nyissa meg a Recovery Services vault biztonsági másolati elem](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. A biztonsági mentés irányítópultos menüjében kattintson **fájlhelyreállítás**.

    ![Helyreállítási szolgáló gomb](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    A **fájlhelyreállítás** menü megnyitása.

    ![A fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Az a **válassza ki a helyreállítási pont** legördülő menüben válassza ki a kívánt fájlokat tárolja a helyreállítási pont. Alapértelmezés szerint már be van jelölve a legutóbbi helyreállítási pontot.

5. Töltse le a szoftverfrissítési fájlokat másolhat a helyreállítási pontot, kattintson a **végrehajtható fájl letöltése** (a Windows Azure virtuális Gépen), vagy **szkript letöltése** (Linux rendszerű Azure virtuális gép, egy python-szkriptet jön létre).

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure a végrehajtható fájl vagy parancsfájl letöltése a helyi számítógépen.

    ![Töltse le a végrehajtható fájl vagy parancsfájl üzenet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    A rendszergazda a végrehajtható vagy parancsfájlokat futtat, javasolt a számítógépet, mentse a letöltést.

6. A végrehajtható fájl vagy parancsfájl jelszóval védett, és a jelszó szükséges. Az a **fájlhelyreállítás** menüben kattintson a Másolás gombra, hogy a jelszó betölti a memóriába.

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A letöltési helyről (általában a letöltések mappában) kattintson a jobb gombbal a végrehajtható fájlt vagy parancsprogramot, és rendszergazdai hitelesítő adatokkal futtathatja. Amikor a rendszer kéri, adja meg a jelszót, vagy illessze be a jelszót a memóriából, és nyomja le az Enter billentyűt. Az érvényes jelszót is meg kell adni, miután a szkript csatlakozik a helyreállítási pont.

    ![A fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Ha korlátozott hozzáféréssel rendelkező számítógépen futtatja a szkriptet, győződjön meg arról, érhető el:

    - download.microsoft.com
    - A helyreállítási szolgáltatás URL-címek (geo-név a régióval, ahol a recovery Services-tároló található vonatkozik)
        - <https://pod01-rec2.geo-name.backup.windowsazure.com> (Az Azure nyilvános helyeken)
        - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (Az Azure China)
        - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Az Azure US Government)
        - <https://pod01-rec2.geo-name.backup.windowsazure.de> (Az Azure Germany)
    - 3260-as kimenő portot

    Linux esetén a szkripthez "nyílt-iscsi" és "lshw" összetevők a helyreállítási ponthoz való csatlakozáshoz. Ha az összetevők nem létezik a számítógépen, amelyen a szkript fut, akkor a parancsprogram kéri engedélyt összetevőinek telepítését. Hozzájárulás megadása a szükséges összetevők telepítéséhez.

    A jövőben a Microsoft a hozzáférést a gép, ahol a parancsfájl futtatása és az adatokat a helyreállítási pont közötti biztonságos csatorna létrehozásához használt összetevők letöltéséhez szükséges.

    A szkriptet minden olyan gépen, amelyen a biztonsági másolat virtuális géppel azonos (vagy kompatibilis) operációs rendszer futtathatja. Tekintse meg a [kompatibilis operációs rendszer tábla](backup-azure-restore-files-from-vm.md#system-requirements) kompatibilis operációs rendszerekhez. Ha a védett Azure virtuális gépen Windows tárolóhelyek (Windows Azure virtuális gépek esetén) vagy LVM/RAID-tömbök (a Linux rendszerű virtuális gépek) használ, a végrehajtható fájl vagy parancsfájl nem futtatható az azonos virtuális gépen. Ehelyett futtassa a végrehajtható fájl vagy parancsfájl bármely gépen más kompatibilis operációs rendszerrel.

### <a name="identifying-volumes"></a>Kötetek azonosítása

#### <a name="for-windows"></a>Windows esetén

Futtassa a végrehajtható fájlt, amikor az operációs rendszer az új kötetek csatlakoztatja, és rendeli hozzá meghajtóbetűjelet. Windows Intéző vagy a fájlkezelő segítségével keresse meg az adott meghajtókat. A kötetek hozzárendelt meghajtóbetűjelek nem lehet azonos betűk az eredeti virtuális gépként, azonban a kötet neve megőrződik. Például, ha a kötet az eredeti virtuális gép "adatlemez (E:`\`)", hogy a kötet, a helyi számítógépen csatolható "adatlemez (minden betűjének:`\`). Böngésszen a parancsfájl kimenetében már említettük, amíg meg nem találja a fájlok/mappa összes kötetet.  

   ![A fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux esetén

A Linux a köteteket a helyreállítási pont csatlakoztatva vannak a mappára, ahol a szkript futása. Ennek megfelelően jelennek meg a csatlakoztatott lemezek, kötetek és a megfelelő csatlakoztatási elérési útjait. Ezek a csatlakoztatási elérési utak gyökér szintű hozzáféréssel rendelkező felhasználók számára látható. Böngésszen a köteteket, a parancsfájl kimenetében említett.

  ![Linux-fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  
## <a name="closing-the-connection"></a>A kapcsolat bezárása

Fájlok azonosítása, és másolja őket egy helyi helyre, miután eltávolítása (vagy válassza le) a további meghajtók. Leválasztani a meghajtókat, az a **fájlhelyreállítás** menüjében az Azure Portalon kattintson **lemezek leválasztása**.

![Lemezek leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Miután a lemezek le lett, kap egy üzenetet. A kapcsolat frissítéséhez, hogy a lemezek eltávolíthatja néhány percig is eltarthat.

A Linux Miután a kapcsolat és a helyreállítási pont daraboltak van, az operációs rendszer nem távolítja el a megfelelő csatlakoztatási elérési utak automatikusan. A csatlakoztatási elérési út létezik, és "árva" kötetek és láthatók, de a hibaüzenetet küldjön, amikor Ön hozzáférési írható a fájlokat. Manuálisan távolíthatja el. A parancsfájl végrehajtásakor, minden korábbi helyreállítási pontjait a meglévő kötetek azonosítja, és törli azokat a jóváhagyás után.

## <a name="special-configurations"></a>Speciális konfigurációk

### <a name="dynamic-disks"></a>A dinamikus lemezek

Ha a védett Azure virtuális gépek egyike vagy a következő jellemzőkkel rendelkező, ugyanazon a virtuális gépen a végrehajtható parancsfájl nem futtatható.

    - Több lemezre (felölelt és csíkozott kötetek) kötetek
    - A dinamikus lemezeket és hibatűrő kötetek (tükrözött vagy RAID-5 köteteket)

Ehelyett futtassa a végrehajtható parancsfájlt kompatibilis operációs rendszerrel rendelkező más számítógépre.

### <a name="windows-storage-spaces"></a>A Windows tárolóhelyek

A Windows tárolóhelyek egy Windows-technológia, amely lehetővé teszi, hogy csoportosításával. A Windows tárolóhelyek csoportosíthatja is az iparági szabványnak megfelelő lemezek tárolókészletbe. Majd a rendelkezésre álló területet a tárolókészleteket hozhat létre a virtuális lemezek, a tárolóhelyek.

A védett Azure VM a Windows tárolóhelyek szolgáltatást használja, ha a végrehajtható parancsfájlt az azonos virtuális gépen nem futtatható. Ehelyett futtassa a végrehajtható parancsfájlt bármely más gép kompatibilis operációs rendszerrel.

### <a name="lvmraid-arrays"></a>LVM/RAID-tömbök

A Linux Logikaikötet-kezelő (LVM) és/vagy szoftver RAID-tömbök használhatók logikai kötetek kezelése több lemez keresztül. Ha a védett Linuxos virtuális gép LVM és/vagy RAID-tömbök használ, az azonos virtuális gépen a parancsfájl nem futtatható. Ehelyett futtassa a szkriptet egy kompatibilis operációs bármelyik olyan gépen, és amely támogatja a fájlrendszer, a védett virtuális gép.

A következő parancsprogram kimenete megjeleníti a LVM és/vagy RAID-tömbök lemezek és a köteteket, a partíció típusa.

   ![Linux LVM kimeneti menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Ahhoz, hogy ezeket a partíciókat, futtassa a parancsokat az alábbi szakaszokban található.

#### <a name="for-lvm-partitions"></a>LVM partíciók

A fizikai kötetre csoport kötetnevek listáját.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Minden logikai kötetet, a neveket és a egy kötet csoportban az elérési listázásához.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Csatlakoztassa a logikai elérési útját a kiválasztott kötetek.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>A RAID-tömbök

A következő parancsot az összes raid-lemez adatainak megjelenítése.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Megjelent a megfelelő RAID-lemez `/dev/mdm/<RAID array name in the protected VM>`

Használja a csatlakoztatási parancsot, ha a RAID-lemez fizikai kötetekre.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemez van konfigurálva, egy másik LVM, majd a fenti eljárással LVM-partíciók, de használata a kötet neve helyett a RAID-lemez neve

## <a name="system-requirements"></a>Rendszerkövetelmények

### <a name="for-windows-os"></a>A Windows operációs rendszer

Az alábbi táblázat bemutatja a kiszolgáló és a számítógép operációs rendszerek közötti kompatibilitást. Ha a fájlok visszaállítása egy korábbi vagy későbbi operációs rendszer verziója fájlok nem állítható vissza. Például nem állítható vissza egy fájlt a Windows Server 2016 virtuális gép Windows Server 2012 vagy Windows 8-as számítógépen. Fájlok visszaállíthatja egy virtuális Gépet azonos kiszolgálói operációs rendszer, vagy a kompatibilis ügyfél típusú operációs rendszer.

|Server OS | Kompatibilis ügyfél operációs rendszere  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>A Linux operációs rendszer

A Linux a fájlok visszaállítására használt számítógép operációs rendszerének támogatnia kell a fájlrendszer, a védett virtuális gép. A parancsfájl futtatására kiválasztásakor ellenőrizze, hogy a számítógép egy kompatibilis operációs rendszer, és az alábbi táblázatban szereplő verziók egyikét használja:

|Linux operációs rendszer | Verziók  |
| --------------- | ---- |
| Ubuntu | 12.04-es vagy újabb verzió |
| CentOS | 6.5-ös vagy újabb verzió  |
| RHEL | 6.7 vagy újabb verzió |
| Debian | 7 vagy újabb verzió |
| Oracle Linux | 6.4 vagy újabb verzió |
| SLES | 12 vagy újabb verzió |
| openSUSE | 42.2 vagy újabb verzió |

A szkript végrehajtásához és a helyreállítási pont biztonságosan kapcsolódhat a Python és bash összetevőket is szükséges.

|Összetevő | Verzió  |
| --------------- | ---- |
| A bash | 4 és újabb verziók |
| python | 2.6.6 vagy újabb verzió  |
| TLS | 1.2 támogatnia kell  |

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik a virtuális gépekről származó fájlok helyreállítása során, ellenőrizze az alábbi táblázat további információkat.

| Chybová Zpráva / forgatókönyv | Lehetséges ok | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Exe-fájl kimeneti: *csatlakozik a cél kivétel* |Parancsfájl nem sikerül a helyreállítási pont eléréséhez    | Ellenőrizze, hogy a gép megfelel-e a fenti hozzáférési követelmények. |  
| Exe-fájl kimeneti: *a cél már bejelentkezett iSCSI-munkamenet-n keresztül.* | A szkript már végre lett hajtva ugyanarra a gépre, és a meghajtók rendelkeznek-e csatlakoztatva | A kötetek a helyreállítási pont már csatlakoztatva van. Előfordulhat, hogy nem kell csatlakoztatni a ugyanolyan meghajtóbetűjellel rendelkező meghajtót az eredeti virtuális gép. Böngésszen az elérhető kötetek a Fájlkezelőben, a fájl |
| Exe-fájl kimeneti: *Ez a szkript érvénytelen, mert a lemezek korlát portal/túllépte a 12-hr-n keresztül is le lett választva. Töltsön le egy új parancsfájlt a portálról.* |    A lemezek rendelkezik le lett választva, a portálról vagy a 12-hr túllépte a korlátot | Az adott exe nem érvényes, és nem futtatható. Ha azt szeretné, hogy a helyreállítási pont kötött fájljainak eléréséhez, látogasson el a portál egy új exe-fájl|
| A gépen, amelyen az exe fut: az új kötetek nem leválasztása, miután a Leválasztás gombra kattint | Az iSCSI-kezdeményező a számítógépen nem válaszol vagy frissíteni a kapcsolatot a cél- és a gyorsítótár karbantartása. |  Kattintás után **leválasztani**, várjon néhány percet. Ha az új kötetek nem le vannak választva, böngésszen összes kötetet. Böngészés az összes kötet kényszeríti a kezdeményező frissíteni a kapcsolatot, és a kötet le van választva, egy hibaüzenet, hogy a lemez nem érhető el.|
| Exe-fájl kimeneti: szkript sikeres futtatása, de az "Új kötetek csatlakoztatva" nem jelenik meg a parancsprogram kimenete |    Ez az átmeneti hiba    | Szeretné a kötetek már lett csatolva. Tallózással nyissa meg. Ha a parancsfájlok futtatásához minden alkalommal ugyanazt a gépet használ, fontolja meg a gép újraindítását, és az ezt követő exe fut megjelennie a listában. |
| Linux-specifikus: nem sikerült a kívánt kötetek megtekintése | A gép, ahol a parancsfájl futtatása az operációs rendszer nem ismeri fel az alapul szolgáló fájlrendszer, a védett virtuális gép | Ellenőrizze, hogy a helyreállítási pont összeomlás-konzisztens vagy fájlkonzisztens. Ha a fájl egységes, futtassa a szkriptet egy másik gép amelynek operációs rendszer felismeri a védett virtuális gép fájlrendszer |
| Windows-specifikus: nem sikerült a kívánt kötetek megtekintése | A lemezek csatolt, de a kötetek nem lettek konfigurálva. | A lemez felügyeleti képernyőről azonosítsa a további lemezek a helyreállítási ponttal kapcsolatos. Ha bármelyik ezeket a lemezeket a kapcsolat nélküli állapot próbálja ki, így azok online kattintson a jobb gombbal a lemezen, és kattintson az "Online"|
