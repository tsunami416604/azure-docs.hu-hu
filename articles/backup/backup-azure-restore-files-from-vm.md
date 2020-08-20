---
title: Fájlok és mappák helyreállítása az Azure virtuális gép biztonsági másolatából
description: Ebből a cikkből megtudhatja, hogyan állíthatja helyre a fájlokat és mappákat egy Azure-beli virtuális gép helyreállítási pontjából.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: ba97a5812359fc72e52d68e337762f7234aa3883
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611840"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából

Azure Backup lehetővé teszi az Azure-beli [virtuális gépek (VM-EK) és lemezek](./backup-azure-arm-restore-vms.md) visszaállítását az Azure VM biztonsági másolatokból, más néven helyreállítási pontokból. Ez a cikk azt ismerteti, hogyan lehet helyreállítani a fájlokat és mappákat egy Azure-beli virtuális gép biztonsági másolatából. A fájlok és mappák visszaállítása csak a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépekhez és a Recovery Services-tárolóhoz való védelemhez érhető el.

> [!NOTE]
> Ez a funkció a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépek számára érhető el, és Recovery Services-tárolóval védett.
> A titkosított virtuális gépek biztonsági másolatból történő helyreállítása nem támogatott.
>

## <a name="mount-the-volume-and-copy-files"></a>A kötet csatlakoztatása és a fájlok másolása

A fájlok vagy mappák helyreállítási pontról történő visszaállításához nyissa meg a virtuális gépet, és válassza ki a kívánt helyreállítási pontot.

1. Jelentkezzen be a [Azure Portalba](https://portal.Azure.com) , és a bal oldali ablaktáblán válassza a **virtuális gépek**lehetőséget. A virtuális gépek listájából válassza ki a virtuális gépet a virtuális gép irányítópultjának megnyitásához.

2. A virtuális gép menüjében válassza a **biztonsági** mentés lehetőséget a biztonsági mentés irányítópultjának megnyitásához.

    ![Recovery Services tároló biztonsági másolati elemeinek megnyitása](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. A biztonsági mentés irányítópult menüjében válassza a **fájl-helyreállítás**lehetőséget.

    ![Fájl helyreállításának kiválasztása](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Megnyílik a **Fájl-helyreállítási** menü.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A **helyreállítási pont kiválasztása** legördülő menüben válassza ki a kívánt fájlokat birtokló helyreállítási pontot. Alapértelmezés szerint a legújabb helyreállítási pont már ki van választva.

5. A fájlok helyreállítási pontról történő másolásához használt szoftver letöltéséhez válassza a **végrehajtható fájl letöltése** (Windows Azure-beli virtuális gépek esetén) vagy a **parancsfájl letöltése** (Linux Azure-beli virtuális gépekhez, a Python-szkriptek létrehozása) lehetőséget.

    ![Végrehajtható fájl letöltése](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Az Azure letölti a végrehajtható fájlt vagy a parancsfájlt a helyi számítógépre.

    ![üzenet letöltése a végrehajtható fájlhoz vagy parancsfájlhoz](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ha a végrehajtható fájlt vagy a parancsfájlt rendszergazdaként szeretné futtatni, akkor azt javasoljuk, hogy mentse a letöltött fájlt a számítógépre.

6. A végrehajtható fájl vagy a szkript jelszavas védelemmel van ellátva, és jelszót igényel. A **Fájl-helyreállítási** menüben a Másolás gombra kattintva töltse be a jelszót a memóriába.

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Győződjön [meg arról, hogy rendelkezik a megfelelő géppel](#selecting-the-right-machine-to-run-the-script) a parancsfájl végrehajtásához. Ha a megfelelő gép ugyanazon a gépen van, ahol letöltötte a parancsfájlt, akkor folytathatja a letöltési szakaszt. A letöltési helyről (általában a *letöltések* mappából) kattintson a jobb gombbal a végrehajtható fájlra vagy a parancsfájlra, és futtassa a rendszergazdai hitelesítő adatokkal. Ha a rendszer kéri, írja be a jelszót, vagy illessze be a jelszót a memóriából, és nyomja le az **ENTER**billentyűt. Az érvényes jelszó megadása után a parancsfájl csatlakozik a helyreállítási ponthoz.

    ![Végrehajtható fájl kimenete](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Linux rendszerű gépeken Python-szkript jön létre. Az egyiknek le kell töltenie a szkriptet, és át kell másolnia a megfelelő/kompatibilis Linux-kiszolgálóra. Előfordulhat, hogy módosítania kell az engedélyeket a futtatásához ```chmod +x <python file name>``` . Ezután futtassa a Python-fájlt a rel ```./<python file name>``` .

Tekintse meg a [hozzáférési követelmények](#access-requirements) szakaszt, és győződjön meg arról, hogy a parancsfájl sikeresen fut.

### <a name="identifying-volumes"></a>Kötetek azonosítása

#### <a name="for-windows"></a>Windows esetén

A végrehajtható fájl futtatásakor az operációs rendszer csatlakoztatja az új köteteket, és hozzárendeli a meghajtóbetűjeleket. A meghajtók tallózásához használhatja a Windows Intézőt vagy a fájlkezelőt is. Előfordulhat, hogy a kötetekhez rendelt meghajtóbetűjelek nem azonosak az eredeti virtuális géppel. A kötet neve azonban megmarad. Ha például az eredeti virtuális gép kötete "adatlemez (E: `\` )" volt, akkor a kötet a helyi számítógépen "Adatlemezként" ("bármely levél":) csatolható `\` . Tallózással keresse meg a parancsfájl kimenetében említett összes kötetet, amíg meg nem találja a fájlokat vagy a mappát.  

   ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux esetén

A Linux rendszerben a helyreállítási pont kötetei ahhoz a mappához vannak csatlakoztatva, amelyben a parancsfájl fut. Ennek megfelelően megjelennek a csatolt lemezek, kötetek és a hozzájuk tartozó csatlakoztatási útvonalak. Ezek a csatlakoztatási útvonalak a root szintű hozzáféréssel rendelkező felhasználók számára láthatók. Tallózzon a szkript kimenetében említett kötetek között.

  ![Linux-fájl helyreállítási menüje](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>A kapcsolatok bezárása

A fájlok azonosítása és a helyi tárolóhelyre való másolása után távolítsa el (vagy válassza le) a további meghajtókat. A meghajtók leválasztásához a Azure Portal **Fájl-helyreállítási** menüjében válassza a **lemezek**leválasztása lehetőséget.

![Lemezek leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

A lemezek leválasztása után üzenet jelenik meg. A kapcsolatok frissítése eltarthat néhány percig, hogy el lehessen távolítani a lemezeket.

A Linux rendszerben a helyreállítási ponttal létesített csatlakozás letelte után az operációs rendszer nem távolítja el automatikusan a megfelelő csatlakoztatási útvonalakat. A csatlakoztatási útvonalak "árva" kötetekként léteznek, és láthatók, de hiba történt a fájlok elérése/írása közben. Ezeket manuálisan is el lehet távolítani. A parancsfájl futtatásakor a rendszer a korábbi helyreállítási pontokból származó összes ilyen kötetet azonosítja, és jóváhagyja őket.

> [!NOTE]
> Győződjön meg arról, hogy a szükséges fájlok visszaállítása után a kapcsolatok be vannak zárva. Ez fontos, különösen abban az esetben, ha a gép, amelyben a parancsfájlt futtatja, a biztonsági mentésre is konfigurálva van. Ha a kapcsolatok továbbra is nyitva vannak, a következő biztonsági mentés sikertelen lehet, hiba: "UserErrorUnableToOpenMount". Ez azért történik, mert a csatlakoztatott meghajtók/kötetek elérhetőnek kell lenniük, és a hozzáférésük sikertelen lehet, mert a mögöttes tároló, azaz az iSCSI-célkiszolgáló nem érhető el. A kapcsolatok tisztítása eltávolítja ezeket a meghajtókat/köteteket, így azok nem lesznek elérhetők a biztonsági mentés során.

## <a name="selecting-the-right-machine-to-run-the-script"></a>A megfelelő gép kiválasztása a parancsfájl futtatásához

Ha a parancsfájl letöltése sikeresen megtörtént, a következő lépés annak ellenőrzése, hogy a gép, amelyre a szkriptet kívánja végrehajtani, a megfelelő gép-e. A következő követelményeket kell teljesíteni a gépen.

### <a name="original-backed-up-machine-versus-another-machine"></a>Eredeti biztonsági másolat készítése a gépről egy másik géppel szemben

1. Ha a biztonsági másolattal rendelkező gép nagyméretű lemezes virtuális gép – vagyis a lemezek száma meghaladja a 16 lemezt, vagy az egyes lemezek 4 TB-nál nagyobb méretűek, akkor a szkriptet **egy másik gépen kell végrehajtani** , és [ezeknek a követelményeknek](#file-recovery-from-virtual-machine-backups-having-large-disks) teljesülnie kell.
1. Akkor is, ha a biztonsági másolatban szereplő gép nem nagyméretű virtuális gép, [ezekben a forgatókönyvekben](#special-configurations) a parancsfájl nem futtatható ugyanazon a biztonsági másolattal rendelkező virtuális gépen.

### <a name="os-requirements-on-the-machine"></a>Operációs rendszerre vonatkozó követelmények a gépen

A parancsfájl végrehajtásához szükséges számítógépeknek meg kell felelniük az [operációs rendszer követelményeinek](#system-requirements).

### <a name="access-requirements-for-the-machine"></a>A gép hozzáférési követelményei

A szkript végrehajtásához szükséges számítógépnek meg kell felelnie a [hozzáférési követelményeknek](#access-requirements).

## <a name="special-configurations"></a>Speciális konfigurációk

### <a name="dynamic-disks"></a>Dinamikus lemezek

Ha a védett Azure-beli virtuális gép a következő jellemzők egyikével vagy mindkettővel rendelkezik kötetekkel, nem futtathatja ugyanazt a virtuális gépen a végrehajtható parancsfájlt.

- Több lemezre kiterjedő kötetek (átfedő és csíkozott kötetek)
- Hibatűrő kötetek (tükrözött és RAID-5 kötetek) dinamikus lemezeken

Ehelyett futtassa a végrehajtható parancsfájlt minden olyan számítógépen, amely kompatibilis operációs rendszerrel rendelkezik.

### <a name="windows-storage-spaces"></a>Windows-tárhelyek

A Windows tárolóhelyek olyan Windows-technológia, amely lehetővé teszi a tárterület virtualizálása. A Windows tárolóhelyek szolgáltatással az iparági szabványnak megfelelő lemezeket csoportosíthatja a Storage-készletekbe. Ezután az ezekben a tárolóhelyeken elérhető tárterületet használja a tárolóhelyek nevű virtuális lemezek létrehozásához.

Ha a védett Azure-beli virtuális gép a Windows Storage Spaces szolgáltatást használja, a végrehajtható parancsfájl nem futtatható ugyanarra a virtuális gépre. Ehelyett futtassa a végrehajtható parancsfájlt minden olyan gépen, amely kompatibilis operációs rendszerrel rendelkezik.

### <a name="lvmraid-arrays"></a>LVM/RAID-tömbök

A Linuxban a logikai kötetek kezelője (LVM) és/vagy a szoftveres RAID tömbök használatával több lemezen is kezelhetők a logikai kötetek. Ha a védett linuxos virtuális gép LVM és/vagy RAID tömböket használ, nem futtathatja ugyanazon a virtuális gépen a parancsfájlt. Ehelyett futtassa a parancsfájlt bármely más olyan gépen, amely kompatibilis operációs rendszerrel rendelkezik, és amely támogatja a védett virtuális gép fájlrendszerét.

A következő parancsfájl kimenete az LVM és/vagy RAID tömbök lemezeit, valamint a partíció típusát tartalmazó köteteket jeleníti meg.

   ![Linux LVM kimeneti menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

A partíciók online állapotba helyezéséhez futtassa a következő szakaszban található parancsokat.

#### <a name="for-lvm-partitions"></a>LVM-partíciók esetén

A szkript futtatása után az LVM-partíciók a szkript kimenetében megadott fizikai kötet (ek)/Disk vannak csatlakoztatva. A folyamat

1. A mennyiségi csoportok neveinek egyedi listájának beolvasása a fizikai kötetekből vagy lemezekről
2. Ezután sorolja fel a kötetek logikai köteteit.
3. Ezután csatlakoztassa a logikai köteteket a kívánt elérési úthoz.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Mennyiségi csoportok neveinek listázása fizikai kötetekről

A kötetek csoportjai neveinek listázása:

```bash
pvs -o +vguuid
```

Ez a parancs felsorolja az összes fizikai kötetet (beleértve a parancsfájl futtatása előtt is), a hozzájuk tartozó kötetek csoportjának nevét és a kötet csoport egyedi felhasználói azonosítóit (UUID). Alább látható a parancs mintájának kimenete.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

Az első oszlop (PV) megjeleníti a fizikai kötetet, az azt követő oszlopok a megfelelő kötet csoport nevét, formátumát, attribútumait, méretét, szabad területét és a kötet egyedi AZONOSÍTÓját jelenítik meg. A parancs kimenete az összes fizikai kötetet megjeleníti. Tekintse át a parancsfájl kimenetét, és azonosítsa a biztonsági mentéshez kapcsolódó köteteket. A fenti példában a szkript kimenete/dev/SDF és/dev/SDD. Így a *datavg_db* kötet csoport a parancsfájlhoz tartozik, és a *Appvg_new* kötet csoport a géphez tartozik. Az utolsó ötlet az, hogy egy egyedi kötet csoport nevének egy egyedi AZONOSÍTÓval kell rendelkeznie.

###### <a name="duplicate-volume-groups"></a>Duplikált kötetek csoportjai

Vannak olyan forgatókönyvek, ahol a kötetek nevei a szkript futtatása után 2 UUID-t tartalmazhatnak. Ez azt jelenti, hogy a kötet azon neve, amelyben a parancsfájlt futtatják, és a biztonsági másolatban szereplő virtuális gépen ugyanazok vannak. Ezt követően át kell neveznie a virtuális gépek biztonsági másolatának kötetei csoportot. Vessen egy pillantást az alábbi példára.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

A szkript kimenete a/dev/SDG, a/dev/SDH, a/dev/sdm2 és a csatolt módon jelenik meg. Így a megfelelő VG-nevek Appvg_new és rootvg. De ugyanezek a nevek is szerepelnek a gép VG listájában. Ellenőrizheti, hogy egy VG-név két UUID-val rendelkezik-e.

Most át kell neveznie a parancsfájl-alapú kötetek VG-nevét, például:/dev/SDG,/dev/SDH,/dev/sdm2. A kötet csoport átnevezéséhez használja a következő parancsot.

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Most már minden, egyedi azonosítóval rendelkező VG-név szerepel.

###### <a name="active-volume-groups"></a>Aktív kötetek csoportjai

Győződjön meg arról, hogy a parancsfájl köteteinek megfelelő kötetek aktívak. Az alábbi parancs az aktív mennyiségi csoportok megjelenítésére szolgál. Győződjön meg arról, hogy a parancsfájl kapcsolódó kötetei szerepelnek-e a listában.

```bash
vgdisplay -a
```  

Ellenkező esetben aktiválja a kötet csoportot az alábbi parancs használatával.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>A mennyiségi csoportokban található logikai kötetek listázása

Miután beolvasta a parancsfájlhoz kapcsolódó VGs egyedi, aktív listáját, az ezekben a kötetekben található logikai kötetek az alábbi paranccsal szerepelhetnek.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Ez a parancs az egyes logikai kötetek elérési útját jeleníti meg "LV Path" néven.

##### <a name="mounting-logical-volumes"></a>Logikai kötetek csatlakoztatása

A logikai kötetek csatlakoztatása a választott útvonalhoz:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Ne használja a "Mount-a" kulcsszót. Ezzel a paranccsal az "/etc/fstab"-ben leírt összes eszköz csatlakoztatható. Ez azt jelentheti, hogy duplikált eszközök csatlakoztatása is lehetséges. Az adatelemek átirányíthatók a parancsfájl által létrehozott eszközökre, amelyek nem őrzik meg az adatmegőrzést, ezért adatvesztést okozhatnak.

#### <a name="for-raid-arrays"></a>RAID-tömbök esetén

A következő parancs az összes RAID-lemez részleteit jeleníti meg:

```bash
#!/bin/bash
mdadm –detail –scan
```

 A megfelelő RAID-lemez a következőképpen jelenik meg `/dev/mdm/<RAID array name in the protected VM>`

Ha a RAID-lemez fizikai kötetekkel rendelkezik, használja a csatlakoztatási parancsot:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemezen van egy másik LVM konfigurálva, akkor használja a fenti eljárást az LVM-partíciók esetében, de használja a kötet nevét a RAID-lemez nevének helyén.

## <a name="system-requirements"></a>Rendszerkövetelmények

### <a name="for-windows-os"></a>Windows operációs rendszer esetén

A következő táblázat a kiszolgáló és a számítógép operációs rendszerének kompatibilitását mutatja be. A fájlok helyreállításakor nem állíthatja vissza a fájlokat egy korábbi vagy későbbi verziójú operációs rendszerre. Nem lehet például visszaállítani egy fájlt egy Windows Server 2016 rendszerű virtuális gépről a Windows Server 2012-re vagy egy Windows 8 rendszerű számítógépre. A virtuális gépek fájljait visszaállíthatja ugyanarra a kiszolgálói operációs rendszerre vagy a kompatibilis ügyfél operációs rendszerre.

|Kiszolgáló operációs rendszere | Kompatibilis ügyfél operációs rendszer  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux operációs rendszer esetén

A Linux rendszerben a fájlok visszaállítására használt számítógép operációs rendszerének támogatnia kell a védett virtuális gép fájlrendszerét. Amikor kijelöl egy számítógépet a parancsfájl futtatásához, győződjön meg arról, hogy a számítógép kompatibilis operációs rendszerrel rendelkezik, és az alábbi táblázatban felsorolt verziók egyikét használja:

|Linux operációs rendszer | Verziók  |
| --------------- | ---- |
| Ubuntu | 12,04 és újabb verziók |
| CentOS | 6,5 és újabb verziók  |
| RHEL | 6,7 és újabb verziók |
| Debian | 7 és újabb verziók |
| Oracle Linux | 6,4 és újabb verziók |
| SLES | 12 és újabb |
| openSUSE | 42,2 és újabb verziók |

> [!NOTE]
> Találtunk néhány problémát a fájl-helyreállítási szkript futtatásához a SLES 12 SP4 operációs rendszert futtató gépeken, és a SLES csapatot vizsgáljuk.
> Jelenleg a fájl-helyreállítási parancsfájl futtatása a SLES 12 SP2 és SP3 operációsrendszer-verzióval rendelkező gépeken működik.
>

A parancsfájlnak a Python és a bash összetevők futtatására is szükség van, és biztonságosan csatlakozhat a helyreállítási ponthoz.

|Összetevő | Verzió  |
| --------------- | ---- |
| bash | 4 és újabb verziók |
| python | 2.6.6 és újabb verziók  |
| TLS | a 1,2 támogatottnak kell lennie  |

## <a name="access-requirements"></a>Hozzáférési követelmények

Ha korlátozott hozzáféréssel rendelkező számítógépen futtatja a parancsfájlt, ellenőrizze, hogy van-e hozzáférése a következőhöz:

- `download.microsoft.com`
- Helyreállítási szolgáltatás URL-címei (a Geo-név arra a régióra utal, ahol a helyreállítási tár található)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Azure-beli nyilvános régiókban)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` (Az Azure China 21Vianet esetében)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (Azure USA kormánya)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (Az Azure Germany esetében)
- Kimenő portok 53 (DNS), 443, 3260

> [!NOTE]
>
> - A letöltött parancsfájl neve lesz az URL-címben kitöltendő **geo-név** . Például: a letöltött parancsfájl neve a \' VMname \' \_ \' geoname \' _ \' GUID azonosítóval kezdődik \' , például *ContosoVM_wcus_12345678*
> - Az URL-cím a következő lenne: <https://pod01-rec2.wcus.backup.windowsazure.com> "
>

Linux esetén a parancsfájl "Open-iSCSI" és "lshw" összetevőket igényel a helyreállítási ponthoz való kapcsolódáshoz. Ha az összetevők nem léteznek azon a számítógépen, amelyen a parancsfájl fut, a parancsfájl engedélyt kér az összetevők telepítésére. Adja meg a szükséges összetevők telepítésének jóváhagyását.

A hozzáférés `download.microsoft.com` szükséges a biztonságos csatorna létrehozásához használt összetevők letöltéséhez, amely a parancsfájl futtatására szolgáló gép és a helyreállítási pontban lévő információ között található.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>A fájlok helyreállítása a nagyméretű lemezekkel rendelkező virtuális gépekről

Ez a szakasz azt ismerteti, hogyan hajtható végre a fájlok helyreállítása a több mint 16 lemezzel rendelkező Azure-beli virtuális gépekről, vagy az egyes lemezek mérete meghaladja a 4 TB-ot.

Mivel a fájl-helyreállítási folyamat az összes lemezt csatlakoztatja a biztonsági mentésből, amikor nagy számú lemez (>16) vagy nagyméretű lemez (> 4 TB) van használatban, a következő műveleti pontok ajánlottak:

- A fájlok helyreállításához külön helyreállítási kiszolgálót (Azure VM D2v3 virtuális gépeket) kell megőrizni. Ezt csak a fájl-helyreállításhoz használhatja, majd leállíthatja, ha nincs rá szükség. Az eredeti gépen való visszaállítás nem ajánlott, mert jelentős hatással lesz a virtuális gépre.
- Ezután futtassa egyszer a parancsfájlt annak vizsgálatához, hogy a fájl-helyreállítási művelet sikeres-e.
- Ha a fájl-helyreállítási folyamat lefagy (a lemezek soha nincsenek csatlakoztatva, vagy csatlakoztatva vannak, de nem jelennek meg a kötetek), hajtsa végre a következő lépéseket.
  - Ha a visszaállítási kiszolgáló egy Windows rendszerű virtuális gép:
    - Győződjön meg arról, hogy az operációs rendszer WS 2012 vagy újabb.
    - Győződjön meg arról, hogy a beállításjegyzék-kulcsok az alábbi módon vannak beállítva a visszaállítási kiszolgálón, és indítsa újra a kiszolgálót. A GUID melletti szám a 0001-0005-tól terjedhet. A következő példában ez a 0004. A parameters (paraméterek) szakaszig navigáljon a beállításkulcs elérési útjára.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Ha a visszaállítási kiszolgáló egy Linux rendszerű virtuális gép:
  - A fájl/etc/iSCSI/iscsid.conf módosítsa a beállítást a következőről:
    - Node. Conn [0]. Timeo. noop_out_timeout = 5 – Node. Conn [0]. Timeo. noop_out_timeout = 30
- A fenti módosítás után futtassa újra a parancsfájlt. Ezekkel a változásokkal nagyon valószínű, hogy a fájl helyreállítása sikeres lesz.
- Minden alkalommal, amikor a felhasználó letölt egy parancsfájlt, Azure Backup kezdeményezi a letöltéshez a helyreállítási pont előkészítésének folyamatát. A nagyméretű lemezek esetében ez a folyamat jelentős időt vesz igénybe. Ha egymást követő kérések fordultak elő, a cél-előkészítés egy letöltés spirálba kerül. Ezért javasoljuk, hogy töltsön le egy parancsfájlt a portálról, a PowerShellből vagy a CLI-ből, várjon 20-30 percet (egy heurisztikus), majd futtassa. Ez idő alatt a cél várhatóan készen áll a parancsfájlból való kapcsolódásra.
- A fájlok helyreállítása után lépjen vissza a portálra, és válassza a **lemezek leválasztása** lehetőséget a helyreállítási pontokhoz, ahol nem tudta csatlakoztatni a köteteket. Ez a lépés lényegében törli a meglévő folyamatokat/munkameneteket, és növeli a helyreállítás esélyét.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémák merülnek fel a virtuális gépek fájljainak helyreállítása közben, további információkért tekintse meg a következő táblázatot.

| Hibaüzenet/forgatókönyv | Lehetséges ok | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Exe-kimenet: *kivétel történt a célhoz való csatlakozás közben* . | A parancsfájl nem fér hozzá a helyreállítási ponthoz    | Győződjön meg arról, hogy a gép megfelel-e az [előző hozzáférési követelményeknek](#access-requirements). |  
| Exe-kimenet: *a cél már be van jelentkezve iSCSI-munkameneten keresztül.* | A parancsfájl már végre lett hajtva ugyanazon a gépen, és a meghajtók csatlakoztatva lettek | A helyreállítási pont kötetei már csatolva vannak. Előfordulhat, hogy a rendszer nem csatlakoztatja az eredeti virtuális gép ugyanazzal a meghajtóbetűjelével. Tallózással keresse meg a fájlhoz tartozó fájlkezelőben elérhető összes kötetet. |
| Exe-kimenet: *Ez a parancsfájl érvénytelen, mert a lemezek a portálon keresztül lettek leválasztva, vagy túllépte a 12 HR-korlátot. Töltsön le egy új parancsfájlt a portálról.* |    A lemezek le lettek választva a portálról, vagy túllépte a 12 órás korlátot. | Ez az adott exe már érvénytelen, és nem futtatható. Ha szeretné elérni a helyreállítási pont fájljait, látogasson el az új exe-portálra.|
| Azon a gépen, amelyen az exe fut: az új kötetek nem vannak leválasztva a Leválasztás gombra kattintás után | A gépen lévő iSCSI-kezdeményező nem válaszol, és nem frissíti a megcélzott kapcsolatát, és megtartja a gyorsítótárat. |  A **Leválasztás**gombra kattintva várjon néhány percet. Ha az új kötetek nem vannak leválasztva, böngésszen végig az összes köteten. Az összes kötet böngészésével a kezdeményező frissíti a kapcsolódást, és a kötet le van választva, és a lemez nem érhető el.|
| Exe-kimenet: a parancsfájl sikeresen fut, de az "új kötetek csatolva" nem jelenik meg a parancsfájl kimenetén. |    Ez egy átmeneti hiba    | A kötetek már csatolva lesznek. Nyissa meg a Explorert a tallózáshoz. Ha ugyanazt a gépet használja a parancsfájlok futtatásához, érdemes megfontolni a gép újraindítását, és a listát a következő exe-futtatásokban kell megjeleníteni. |
| Linux-specifikus: nem lehet megtekinteni a kívánt köteteket | Előfordulhat, hogy a parancsfájlt futtató gép operációs rendszere nem ismeri fel a védett virtuális gép mögöttes fájlrendszerét | Győződjön meg arról, hogy a helyreállítási pont összeomlás-konzisztens vagy fájl-konzisztens. Ha a fájl konzisztens, futtassa a parancsfájlt egy másik gépen, amelynek operációs rendszere felismeri a védett virtuális gép fájlrendszerét. |
| Windows-specifikus: nem lehet megtekinteni a kívánt köteteket | Lehet, hogy a lemezek csatlakoztatva lettek, de a kötetek nincsenek konfigurálva | A Lemezkezelés képernyőn azonosítsa a helyreállítási ponthoz kapcsolódó további lemezeket. Ha a lemezek bármelyike offline állapotban van, próbálja meg online állapotba helyezni, majd kattintson a jobb gombbal a lemezre, és válassza az **online**lehetőséget.|

## <a name="security"></a>Biztonság

Ez a szakasz az Azure-beli virtuális gépek biztonsági másolatainak fájl-helyreállításának megvalósítására tett különböző biztonsági intézkedéseket ismerteti.

### <a name="feature-flow"></a>Szolgáltatás folyamata

Ez a funkció úgy lett felépítve, hogy hozzáférjen a virtuálisgép-információhoz anélkül, hogy vissza kellene állítania a teljes virtuális gépet vagy a virtuálisgép-lemezeket, és a minimális számú lépést. A virtuális gépekhez való hozzáférést egy parancsfájl adja meg (amely az alább látható módon csatlakoztatja a helyreállítási kötetet), és az összes biztonsági implementáció sarokköveként szolgál:

  ![Biztonsági szolgáltatás folyamata](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Biztonsági megvalósítások

#### <a name="select-recovery-point-who-can-generate-script"></a>Válassza ki a helyreállítási pontot (ki tud parancsfájlt előállítani)

A parancsfájl hozzáférést biztosít a virtuális gépekhez, ezért fontos annak szabályozása, hogy ki hozhatja elő az első helyen. Be kell jelentkeznie a Azure Portalba, és engedélyezni kell a [RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a szkript létrehozásához.

A fájl-helyreállításhoz a virtuális gépek visszaállításához és a lemezek visszaállításához azonos szintű engedélyezési szint szükséges. Más szóval csak a jogosultsággal rendelkező felhasználók tekinthetik meg a virtuális gépeket, és létrehozhatják a parancsfájlt.

A generált parancsfájl a Azure Backup szolgáltatáshoz tartozó hivatalos Microsoft-tanúsítvánnyal van aláírva. A szkripttel való illetéktelen módosítás azt jelenti, hogy az aláírás megszakadt, és a szkript futtatására tett bármilyen kísérlet az operációs rendszer potenciális kockázataként van kiemelve.

#### <a name="mount-recovery-volume-who-can-run-script"></a>A helyreállítási kötet csatlakoztatása (ki futtathat parancsfájlt)

Csak egy rendszergazda futtathatja a szkriptet, és emelt szintű módban kell futnia. A parancsfájl csak előre létrehozott lépéseket hajt végre, és nem fogadja el a külső forrásból érkező adatokat.

A parancsfájl futtatásához jelszó szükséges, amely csak a Azure Portal vagy a PowerShell/CLI parancsfájl generálásának időpontjában jelenik meg a felhatalmazott felhasználó számára. Ezzel biztosíthatja, hogy a parancsfájlt futtató jogosult felhasználó a parancsfájl futtatásához is felelős legyen.

#### <a name="browse-files-and-folders"></a>Fájlok és mappák tallózása

A fájlok és mappák tallózásához a parancsfájl az iSCSI-kezdeményezőt használja a gépen, és csatlakozik ahhoz a helyreállítási ponthoz, amely iSCSI-célként van konfigurálva. Itt képzelheti el, hogy az egyik a vagy az összes összetevőt próbálja utánozni/hamisítani.

Kölcsönös CHAP-hitelesítési mechanizmust használunk, hogy minden összetevő hitelesítse a másikat. Ez azt jelenti, hogy a hamis kezdeményezők rendkívül nehéz csatlakozni az iSCSI-tárolóhoz, és ahhoz, hogy hamis célt lehessen csatlakoztatni ahhoz a géphez, amelyen a parancsfájl fut.

A helyreállítási szolgáltatás és a gép közötti adatforgalom védelme a biztonságos TLS-alagút TCP-kapcsolaton keresztüli létrehozásával történik (a TLS 1,2-et a parancsfájl futtatására szolgáló gépen[kell támogatni](#system-requirements) ).

A szülő/biztonsági mentés alatt lévő virtuális gépen található összes fájl Access Control lista (ACL) a csatlakoztatott fájlrendszerben is megmarad.

A parancsfájl csak olvasási hozzáférést biztosít egy helyreállítási ponthoz, és csak 12 órára érvényes. Ha korábban el szeretné távolítani a hozzáférést, jelentkezzen be az Azure Portalra, a PowerShellbe vagy a CLI-be, és válassza le az adott helyreállítási ponthoz a **leválasztott lemezeket** . A parancsfájl azonnal érvénytelenítve lesz.

## <a name="next-steps"></a>Következő lépések

- A fájlok visszaállítása során felmerülő problémákért tekintse meg a [hibaelhárítási](#troubleshooting) szakaszt.
- Ismerje meg, hogyan [állíthatja vissza a fájlokat a PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) használatával
- Ismerje meg, hogyan [állíthatja vissza a fájlokat az Azure CLI-n keresztül](./tutorial-restore-files.md)
- A virtuális gép visszaállítása után megtudhatja, hogyan [kezelheti a biztonsági mentéseket](./backup-azure-manage-vms.md)
