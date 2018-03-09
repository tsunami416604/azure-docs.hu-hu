---
title: "Az Azure Backup: Fájlok és mappák helyreállítása Azure virtuális gép biztonsági másolaton |} Microsoft Docs"
description: "Egy Azure virtuális gép helyreállítási pontból állítsa helyre a fájlok"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "elemszintű helyreállítás; a fájlok helyreállítása Azure virtuális gép biztonsági másolatból; Azure virtuális gép fájlok visszaállítása"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: pullabhk;markgal
ms.openlocfilehash: 0fce38db010a77ac61145ef63f616e0e466c95e2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása Azure virtuális gép biztonsági mentése

Azure biztonsági mentés lehetővé teszi a visszaállítás [Azure virtuális gépek (VM) és a lemezek](./backup-azure-arm-restore-vms.md) Azure virtuális gép biztonsági másolatból, más néven visszaállítási pontok. Ez a cikk azt ismerteti, fájlok és mappák helyreállítása Azure virtuális gép biztonsági másolaton. Fájlok és mappák visszaállítása csak a Azure virtuális gépeken telepített, a Resource Manager modellt használja, és a Recovery services-tároló védelme alatt álló érhető el.

> [!Note]
> Ez a szolgáltatás Azure virtuális gépeken telepített, a Resource Manager modellt használja, és a Recovery Services-tároló protected érhető el.
> A fájlok helyreállítása titkosított virtuális gép biztonsági másolaton nem támogatott.
>

## <a name="mount-the-volume-and-copy-files"></a>Csatlakoztassa a kötet, és másolja a fájlokat

Fájlok és mappák visszaállítása a visszaállítási pontról, nyissa meg a virtuális géphez, és válassza a visszaállítási pontot. 

1. Jelentkezzen be a [Azure-portálon](http://portal.Azure.com) és a bal oldali menüben kattintson a **virtuális gépek**. A virtuális gépek listájának megtekintéséhez válassza ki a virtuális gépet, hogy a virtuális gép irányítópult megnyitásához. 

2. A virtuális gép menüben kattintson a **biztonsági mentés** a biztonsági mentés irányítópult megnyitásához.

    ![Biztonsági mentési elem megnyitása Recovery Services tároló](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. A biztonsági mentés irányítópult menüben kattintson a **fájlhelyreállítás** a menü megnyitásához.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Az a **válassza ki a helyreállítási pont** legördülő menüben válassza ki a kívánt fájlokat tartalmazó helyreállítási pontot. A legújabb helyreállítási pont alapértelmezés szerint ki van jelölve.

5. A fájlok másolását a helyreállítási pont használt szoftver letöltéséhez kattintson **végrehajtható fájl letöltése** (a Windows Azure virtuális gép) vagy **parancsfájl letöltése** (a Linux Azure virtuális gép). 

    ![Létrehozott jelszót](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure letölti a végrehajtható fájl vagy parancsfájl a helyi számítógépen.

    ![Töltse le a végrehajtható fájl vagy parancsfájl üzenet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Rendszergazdaként a végrehajtható fájl vagy parancsfájl futtatásához ajánlatos, mentse a letöltést a számítógépre.

6. A végrehajtható fájl vagy parancsfájl jelszóval védett, és jelszót igényel. Az a **fájlhelyreállítás** menüben kattintson a Másolás gombra a jelszó betölti a memóriába.

    ![Létrehozott jelszót](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A letöltési helyről (általában a Letöltések mappába) kattintson a jobb gombbal a végrehajtható fájl vagy parancsfájl, és futtassa rendszergazdai hitelesítő adataival. Amikor a rendszer kéri, írja be a jelszót vagy illessze be a jelszót a memóriából, és nyomja le az ENTER billentyűt. Az érvényes jelszót is meg kell adni, ha a parancsfájl a helyreállítási pont csatlakozik.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Ha a parancsfájl korlátozott hozzáféréssel rendelkező számítógépen futtatja, győződjön meg arról, érhető el:

    - download.microsoft.com
    - [Az Azure virtuális gép biztonsági mentések használt Azure-végpontok](backup-azure-arm-vms-prepare.md#establish-network-connectivity)
    - 3260-as kimenő port

    A Linux a parancsfájl működéséhez a "Megnyitás-iscsi" és "lshw" összetevőt, a helyreállítási ponthoz való csatlakozáshoz. Az összetevők nem létezik a számítógépen, ahol a parancsfájl futtatása, ha a parancsprogram kéri, az összetevők telepítését. Adja meg a hozzájárulási a szükséges összetevők telepítéséhez.
    
    A jövőben a Microsoft a hozzáférést a gépen, ahol a parancsfájl futtatása és a helyreállítási pont adatai között a biztonságos csatorna létrehozásához használt összetevők letöltése szükséges.         

    A parancsfájl minden számítógépen, amelyre a biztonsági másolat virtuális géppel azonos (vagy kompatibilis) operációs rendszer futtatható. Tekintse meg a [kompatibilis operációs rendszer tábla](backup-azure-restore-files-from-vm.md#system-requirements) kompatibilis operációs rendszerekhez. Ha védett virtuális gépet az Azure Windows tárolóhelyek (a Windows Azure virtuális gépeken) vagy LVM/RAID-tömböket (a Linux virtuális gépek) használ, a végrehajtható fájl vagy parancsfájl nem futtatható az azonos virtuális gépen. Ehelyett futtassa a végrehajtható fájl vagy parancsfájl egy kompatibilis operációs rendszer bármelyik olyan gépen.
 

### <a name="identifying-volumes"></a>Kötetek azonosítása

#### <a name="for-windows"></a>Windows rendszerhez

Ha a végrehajtható fájl futtatásához a az operációs rendszer csatlakoztatja az új kötetekre, és hozzárendeli a meghajtóbetűjeleket. A Windows Explorer vagy a fájlkezelő segítségével keresse meg azokat a meghajtókat. A kötetek hozzárendelt meghajtóbetűjelek nem lehet azonos betűje se az eredeti virtuális gépként, azonban a kötet neve megőrződik. Például, ha a kötet az eredeti virtuális gép "adatlemez (E:`\`)", hogy a kötet a helyi számítógépen csatolható "adatlemez (bármilyen betű:`\`). Tallózzon a parancsfájl kimenetében szerepel, amíg meg nem látja a fájl/mappa összes kötetet.  
       
   ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>A Linux rendszerhez

A Linux a helyreállítási pont csatlakoztatva vannak a mappára, ahol a parancsfájl futtatása. Ennek megfelelően a csatlakoztatott lemezek, kötetek és a megfelelő csatlakozási elérési utak láthatók. Ezek csatlakoztatási elérési utak gyökér szintű hozzáféréssel rendelkező felhasználók számára látható. Tallózzon a kötetek, a parancsfájl kimenetében szerepel.

  ![Linux-fájl helyreállítási menü](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>A kapcsolat bezárása

A fájlok azonosítása után másolja őket egy helyi tárhelyet, távolítsa el (vagy leválasztani) a további meghajtók. Leválasztani a meghajtókat, az a **fájlhelyreállítás** Azure-portál menüjében kattintson **lemez leválasztása**.

![Lemez leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Ha a lemezek fürtköteteiről van szó, kap egy üzenetet, jelezve, hogy sikeres volt. A kapcsolat frissítéséhez, hogy a lemezek eltávolíthat néhány percig is eltarthat.

A Linux után a kapcsolat és a helyreállítási pont daraboltak van, az operációs rendszer nem távolítja el a megfelelő csatlakozási elérési utak automatikusan. A csatlakoztatási elérési út nem létezik-e a "árva" kötetek és láthatók, de hibaüzenetet küldjön, amikor Ön hozzáférést/írás a fájlokat. Manuálisan eltávolíthatók. A parancsfájl futása közben ilyen bármely korábbi helyreállítási pontjait a meglévő köteteket azonosítja, és törli őket jóváhagyását követően.

## <a name="special-configurations"></a>Speciális konfigurációk

### <a name="dynamic-disks"></a>A dinamikus lemezek

Ha a védett Azure virtuális gép egyik vagy mindkét a következő jellemzőkkel rendelkező, az azonos virtuális gépen a végrehajtható parancsfájl nem futtatható. 

  - Kötetek, amely több lemezre (átnyúló és csíkozott kötetek)
  - A dinamikus lemezek hibatűrő kötetek (tükrözött vagy RAID-5 köteteket) 

Ehelyett futtassa a végrehajtható parancsfájlt bármely más kompatibilis operációs rendszerű számítógépre.

### <a name="windows-storage-spaces"></a>Windows tárolóhelyek

A tárolóhelyek a Windows egy Windows-technológia, amely lehetővé teszi, hogy a tároló virtualizálását, ha. A tárolóhelyek a Windows csoportosíthatja iparági szabványnak megfelelő lemezek tárolókészletbe csoportosítani. Virtuális lemezek, a tárolóhelyek létrehozása e tárolókészletek használja a rendelkezésre álló területet.

A védett Azure virtuális gép Windows tárolóhelyek szolgáltatást használja, ha a végrehajtható parancsfájl nem futtatható az azonos virtuális gépen. Ehelyett futtassa a végrehajtható parancsfájlt bármely más kompatibilis operációs rendszerrel rendelkező gép.

### <a name="lvmraid-arrays"></a>LVM/RAID tömbök

A Linux logikai kötetkezelő (LVM) és/vagy a szoftver RAID tömbök használhatók logikai kötetek kezelése több lemez keresztül. Ha a védett Linux virtuális gép LVM és/vagy RAID-tömbök használ, a parancsfájl nem futtatható az azonos virtuális gépen. Ehelyett futtassa a parancsfájlt egy kompatibilis operációs rendszer bármelyik olyan gépen, és amely támogatja a fájlrendszer a védett virtuális gép.

A következő parancsfájl kimenete a LVM és/vagy RAID-tömbök lemezek és a köteteket a partíció típusát jeleníti meg.

   ![Linux LVM kimeneti menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Ahhoz, hogy ezek a partíciók, futtassa a parancsokat az alábbi szakaszokban található. 

**LVM partíciók**

A kötet csoportnevek fizikai mennyiségi listázásához.
```
$ pvs <volume name as shown above in the script output> 
```
Az összes logikai kötetek, nevek és az elérési utak egy kötet csoportot a listában.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Csatlakoztassa azt az elérési utat a kiválasztott logikai kötetek.

```
$ mount <LV path> </mountpath>
```



**A RAID-tömbök**

A következő parancsot az összes raid-lemez adatainak megjelenítése.

```
$ mdadm –detail –scan
```
 A megfelelő RAID-lemez jelenik meg `/dev/mdm/<RAID array name in the protected VM>`

Akkor használja a csatlakoztatási parancs, ha a RAID-lemez van fizikai kötetekre.
```
$ mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemez van konfigurálva azt egy másik LVM, majd a fenti eljárással LVM partíciók, de használja a kötet neve helyett a RAID-lemez neve

## <a name="system-requirements"></a>Rendszerkövetelmények

### <a name="for-windows"></a>Windows rendszerhez

Az alábbi táblázat a kiszolgálóhoz és számítógépes operációs rendszerek közötti kompatibilitást. Ha a fájlok helyreállítása nem állítható vissza fájlok egy korábbi vagy későbbi operációs rendszer verziója. Például nem állítható vissza egy fájlt egy Windows Server 2016 virtuális gépről a Windows Server 2012 vagy a Windows 8 rendszerű számítógép. Fájlok állíthatja vissza a virtuális gépek azonos kiszolgálói operációs rendszer, illetve a kompatibilis ügyfél operációs rendszerét.   

|Server OS | Kompatibilis ügyfél OS  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux"></a>A Linux rendszerhez

A Linux az operációs rendszer, a fájlok visszaállítására használt számítógép támogatnia kell a fájlrendszer a védett virtuális gépek. Lehetőséget választva a parancsfájl futtatásához a számítógépen, győződjön meg arról, a számítógép egy kompatibilis operációs rendszer, és használja az alábbi táblázatban azonosított verziók egyikét:

|Linux operációs rendszer | Verziók  |
| --------------- | ---- |
| Ubuntu | 12.04 vagy újabb verzió |
| CentOS | 6.5-ös vagy újabb verzió  |
| RHEL | 6.7 vagy újabb verzió |
| Debian | 7 vagy újabb verzió |
| Oracle Linux | 6.4 vagy újabb verzió |
| SLES | 12 vagy újabb verzió |
| openSUSE | 42.2 vagy újabb verzió |

A parancsfájl a Python és bash összetevők hajtható végre, és a helyreállítási pont biztonságos kapcsolódás is szükséges.

|Összetevő | Verzió  |
| --------------- | ---- |
| Bash | 4 vagy újabb verzió |
| python | 2.6.6 vagy újabb verzió  |
| TLS | 1.2 támogatnia kell  |

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik a virtuális gépeket a fájlok helyreállítása során, ellenőrizze a következő táblázat további információt.

| Hibaüzenet / forgatókönyv | Lehetséges ok | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| A következő exe kimeneti: *csatlakozik a célként megadott kivétel* |Parancsfájl nincs érhetik el a helyreállítási pont | Ellenőrizze, hogy a gép megfelel a fenti hozzáférési követelmények. |  
|   A következő exe kimeneti: *a cél már naplózta keresztül az iSCSI-munkamenetből.* | A parancsfájl már végre lett hajtva ugyanazon a számítógépen, és a meghajtó csatlakoztatva van | A helyreállítási pont köteteinek már csatlakoztatva van. Előfordulhat, hogy nem felszerelt ugyanazokat a meghajtóbetűjeleket rendelje az eredeti virtuális gép. Tallózzon a fájlhoz a Fájlkezelőben az elérhető kötetek |
| A következő exe kimeneti: *ezt a parancsfájlt érvénytelen, mert a lemez portal/túllépte a 12-hr korlát keresztül is le lett választva. Új parancsfájl letöltéséről a portálon.* | A lemezek rendelkezik le lett választva, a portál vagy a 12-hr korlátja túllépve |    Ez különösen exe most már érvénytelen, ezért nem futtatható. Ha el szeretné érni a fájlokat az adott helyreállítási pont időponthoz kötött, látogasson el az új exe-portálon|
| A számítógépen, amelyen fut az exe: az új kötetekre nem leválaszthatja, a Leválasztás gombra történő kattintás után |    Az iSCSI-kezdeményezőt a számítógépen nem válaszol vagy frissíteni a kapcsolatot a cél- és a gyorsítótár karbantartása |    Néhány perc várakozás után a leválasztás művelet bekapcsolva. Ha az új kötetekre még nem leválasztották, keresse meg a kötetek keresztül. Ez arra kényszeríti a kapcsolat frissítése a kezdeményező és a kötet le van választva, egy hibaüzenet, hogy a lemez nem érhető el|
| A következő exe kimeneti: parancsfájl sikeresen fut, de az "Új kötetek csatlakoztatva" nem jelenik meg a parancsfájl kimenetében | Ez az egy átmeneti hiba   | Szeretné a kötetek már van csatlakoztatva. Nyissa meg a Explorert, hogy a Tallózás gombra. Ha ugyanaz a gép használ minden egyes parancsfájlok futtatásához, fontolja meg a számítógép újraindítása, és a következő exe futtatása kell megjelennie a listában. |
| Linux-specifikus: nem sikerült a kívánt kötetek megtekintése | A gép, amelyen fut a parancsfájl az operációs rendszer nem ismeri fel a védett virtuális gép a mögöttes fájlrendszer | Ellenőrizze, hogy a helyreállítási pont összeomlás-konzisztens vagy fájlkonzisztens. Ha a fájl egységes, futtassa a parancsfájlt egy másik számítógépre, amelynek operációs rendszer felismeri a védett virtuális fájlrendszer |
| Windows-specifikus: nem sikerült a kívánt kötetek megtekintése | A lemezek csatolt, de a kötetek nem voltak konfigurálva. | A lemez képernyőjén azonosítsa a helyreállítási ponttal kapcsolatos lemezt. Ezek a lemezek esetén a kapcsolat nélküli állapotban próbálja minősítené online kattintson a jobb gombbal a lemezen, és kattintson az "Online"|
