---
title: Fájlok és mappák helyreállítása az Azure virtuális gép biztonsági másolatából
description: Ebből a cikkből megtudhatja, hogyan állíthatja helyre a fájlokat és mappákat egy Azure-beli virtuális gép helyreállítási pontjából.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: d80fb1060eca766305ecbfffe151d975472f8b3c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660920"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából

Azure Backup lehetővé teszi az Azure-beli [virtuális gépek (VM-EK) és lemezek](./backup-azure-arm-restore-vms.md) visszaállítását az Azure VM biztonsági másolatokból, más néven helyreállítási pontokból. Ez a cikk azt ismerteti, hogyan lehet helyreállítani a fájlokat és mappákat egy Azure-beli virtuális gép biztonsági másolatából. A fájlok és mappák visszaállítása csak a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépekhez és a Recovery Services-tárolóhoz való védelemhez érhető el.

> [!NOTE]
> Ez a funkció a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépek számára érhető el, és Recovery Services-tárolóval védett.
> A titkosított virtuális gépek biztonsági másolatból történő helyreállítása nem támogatott.
>

## <a name="mount-the-volume-and-copy-files"></a>A kötet csatlakoztatása és a fájlok másolása

A fájlok vagy mappák helyreállítási pontról történő visszaállításához nyissa meg a virtuális gépet, és válassza ki a kívánt helyreállítási pontot.

1. Jelentkezzen be a [Azure Portalba](https://portal.Azure.com) , és a bal oldali ablaktáblán kattintson a **virtuális gépek**elemre. A virtuális gépek listájából válassza ki a virtuális gépet a virtuális gép irányítópultjának megnyitásához.

2. A virtuális gép menüjében kattintson a **biztonsági mentés** elemre a biztonsági mentés irányítópultjának megnyitásához.

    ![Recovery Services tároló biztonsági másolati elemeinek megnyitása](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. A biztonsági mentés irányítópult menüjében kattintson a **fájl-helyreállítás**elemre.

    ![Fájl helyreállítása gomb](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Megnyílik a **Fájl-helyreállítási** menü.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A **helyreállítási pont kiválasztása** legördülő menüben válassza ki a kívánt fájlokat birtokló helyreállítási pontot. Alapértelmezés szerint a legújabb helyreállítási pont már ki van választva.

5. A fájlok helyreállítási pontról történő másolásához használt szoftver letöltéséhez kattintson a **végrehajtható fájl letöltése** (Windows Azure-beli virtuális gépek esetén) vagy a **parancsfájl letöltése** (Linux Azure-beli virtuális gépek esetén a Python-szkript létrehozása) elemre.

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Az Azure letölti a végrehajtható fájlt vagy a parancsfájlt a helyi számítógépre.

    ![üzenet letöltése a végrehajtható fájlhoz vagy parancsfájlhoz](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ha a végrehajtható fájlt vagy a parancsfájlt rendszergazdaként szeretné futtatni, akkor azt javasoljuk, hogy mentse a letöltött fájlt a számítógépre.

6. A végrehajtható fájl vagy a szkript jelszavas védelemmel van ellátva, és jelszót igényel. A **Fájl-helyreállítási** menüben kattintson a Másolás gombra a jelszó memóriába való betöltéséhez.

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A letöltési helyről (általában a letöltések mappából) kattintson a jobb gombbal a végrehajtható fájlra vagy a parancsfájlra, és futtassa a rendszergazdai hitelesítő adatokkal. Ha a rendszer kéri, írja be a jelszót, vagy illessze be a jelszót a memóriából, és nyomja le az **ENTER**billentyűt. Az érvényes jelszó megadása után a parancsfájl csatlakozik a helyreállítási ponthoz.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Linux rendszerű gépeken Python-szkript jön létre. Az egyiknek le kell töltenie a szkriptet, és át kell másolnia a megfelelő/kompatibilis Linux-kiszolgálóra. Előfordulhat, hogy módosítania kell az engedélyeket a ```chmod +x <python file name>```való végrehajtásához. Ezután futtassa a Python-fájlt ```./<python file name>```.

Tekintse meg a [hozzáférési követelmények](#access-requirements) szakaszt, és győződjön meg arról, hogy a parancsfájl sikeresen fut.

### <a name="identifying-volumes"></a>Kötetek azonosítása

#### <a name="for-windows"></a>Windows esetén

A végrehajtható fájl futtatásakor az operációs rendszer csatlakoztatja az új köteteket, és hozzárendeli a meghajtóbetűjeleket. A meghajtók tallózásához használhatja a Windows Intézőt vagy a fájlkezelőt is. Előfordulhat, hogy a kötetekhez rendelt meghajtóbetűjelek nem azonosak az eredeti virtuális géppel. A kötet neve azonban megmarad. Ha például az eredeti virtuális gép kötete "adatlemez (E:`\`)" volt, akkor a kötet a helyi számítógépen "adatlemezként" ("bármely levél":`\`) csatlakoztatható. Tallózással keresse meg a parancsfájl kimenetében említett összes kötetet, amíg meg nem találja a fájlokat vagy a mappát.  

   ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux esetén

A Linux rendszerben a helyreállítási pont kötetei ahhoz a mappához vannak csatlakoztatva, amelyben a parancsfájl fut. Ennek megfelelően megjelennek a csatolt lemezek, kötetek és a hozzájuk tartozó csatlakoztatási útvonalak. Ezek a csatlakoztatási útvonalak a root szintű hozzáféréssel rendelkező felhasználók számára láthatók. Tallózzon a szkript kimenetében említett kötetek között.

  ![Linux-fájl helyreállítási menüje](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>A kapcsolatok bezárása

A fájlok azonosítása és a helyi tárolóhelyre való másolása után távolítsa el (vagy válassza le) a további meghajtókat. A meghajtók leválasztásához a Azure Portal **Fájl-helyreállítási** menüjében kattintson a **lemezek leválasztása**elemre.

![Lemezek leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

A lemezek leválasztása után üzenet jelenik meg. A kapcsolatok frissítése eltarthat néhány percig, hogy el lehessen távolítani a lemezeket.

A Linux rendszerben a helyreállítási ponttal létesített csatlakozás letelte után az operációs rendszer nem távolítja el automatikusan a megfelelő csatlakoztatási útvonalakat. A csatlakoztatási útvonalak "árva" kötetekként léteznek, és láthatók, de hiba történt a fájlok elérése/írása közben. Ezeket manuálisan is el lehet távolítani. A parancsfájl futtatásakor a rendszer a korábbi helyreállítási pontokból származó összes ilyen kötetet azonosítja, és jóváhagyja őket.

## <a name="special-configurations"></a>Speciális konfigurációk

### <a name="dynamic-disks"></a>Dinamikus lemezek

Ha a védett Azure-beli virtuális gép a következő jellemzők egyikével vagy mindkettővel rendelkezik kötetekkel, nem futtathatja ugyanazt a virtuális gépen a végrehajtható parancsfájlt.

- Több lemezre kiterjedő kötetek (átfedő és csíkozott kötetek)
- Hibatűrő kötetek (tükrözött és RAID-5 kötetek) dinamikus lemezeken

Ehelyett futtassa a végrehajtható parancsfájlt minden olyan számítógépen, amely kompatibilis operációs rendszerrel rendelkezik.

### <a name="windows-storage-spaces"></a>Windows-tárolóhelyek

A Windows tárolóhelyek olyan Windows-technológia, amely lehetővé teszi a tárterület virtualizálása. A Windows tárolóhelyek szolgáltatással az iparági szabványnak megfelelő lemezeket csoportosíthatja a Storage-készletekbe. Ezután az ezekben a tárolóhelyeken elérhető tárterületet használja a tárolóhelyek nevű virtuális lemezek létrehozásához.

Ha a védett Azure-beli virtuális gép a Windows Storage Spaces szolgáltatást használja, a végrehajtható parancsfájl nem futtatható ugyanarra a virtuális gépre. Ehelyett futtassa a végrehajtható parancsfájlt minden olyan gépen, amely kompatibilis operációs rendszerrel rendelkezik.

### <a name="lvmraid-arrays"></a>LVM/RAID-tömbök

A Linuxban a logikai kötetek kezelője (LVM) és/vagy a szoftveres RAID tömbök használatával több lemezen is kezelhetők a logikai kötetek. Ha a védett linuxos virtuális gép LVM és/vagy RAID tömböket használ, nem futtathatja ugyanazon a virtuális gépen a parancsfájlt. Ehelyett futtassa a parancsfájlt bármely más olyan gépen, amely kompatibilis operációs rendszerrel rendelkezik, és amely támogatja a védett virtuális gép fájlrendszerét.

A következő parancsfájl kimenete az LVM és/vagy RAID tömbök lemezeit, valamint a partíció típusát tartalmazó köteteket jeleníti meg.

   ![Linux LVM kimeneti menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

A partíciók online állapotba helyezéséhez futtassa a következő szakaszban található parancsokat.

#### <a name="for-lvm-partitions"></a>LVM-partíciók esetén

A mennyiségi csoportok neveinek listázása fizikai kötet alatt:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Az összes logikai kötet, név és elérési út listázása egy kötet csoportban:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command’s results>
```

A logikai kötetek csatlakoztatása a választott útvonalhoz:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>RAID-tömbök esetén

A következő parancs az összes RAID-lemez részleteit jeleníti meg:

```bash
#!/bin/bash
mdadm –detail –scan
```

 A megfelelő RAID-lemez `/dev/mdm/<RAID array name in the protected VM>`ként jelenik meg

Ha a RAID-lemez fizikai kötetekkel rendelkezik, használja a csatlakoztatási parancsot:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemezen van egy másik LVM konfigurálva, akkor használja a fenti eljárást az LVM-partíciók esetében, de használja a kötet nevét a RAID-lemez nevének helyén.

## <a name="system-requirements"></a>Rendszerkövetelmények

### <a name="for-windows-os"></a>Windows operációs rendszer esetén

A következő táblázat a kiszolgáló és a számítógép operációs rendszerének kompatibilitását mutatja be. A fájlok helyreállításakor nem állíthatja vissza a fájlokat egy korábbi vagy későbbi verziójú operációs rendszerre. Nem lehet például visszaállítani egy fájlt egy Windows Server 2016 rendszerű virtuális gépről a Windows Server 2012-re vagy egy Windows 8 rendszerű számítógépre. A virtuális gépek fájljait visszaállíthatja ugyanarra a kiszolgálói operációs rendszerre vagy a kompatibilis ügyfél operációs rendszerre.

|Server OS | Kompatibilis ügyfél operációs rendszer  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
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
  - <https://pod01-rec2.geo-name.backup.windowsazure.com> (az Azure nyilvános térségek)
  - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (az Azure China 21Vianet esetében)
  - <https://pod01-rec2.geo-name.backup.windowsazure.us> (az USA kormánya számára)
  - <https://pod01-rec2.geo-name.backup.windowsazure.de> (az Azure Germany esetében)
- Kimenő portok 53 (DNS), 443, 3260

> [!NOTE]
>
> - A letöltött parancsfájl neve lesz az URL-címben kitöltendő **geo-név** . Exampple esetén: a letöltött parancsfájl neve \'VMname\'\_\'geoname\'_\'GUID\', például *ContosoVM_wcus_12345678*
> - Az URL-cím <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

Linux esetén a parancsfájl "Open-iSCSI" és "lshw" összetevőket igényel a helyreállítási ponthoz való kapcsolódáshoz. Ha az összetevők nem léteznek azon a számítógépen, amelyen a parancsfájl fut, a parancsfájl engedélyt kér az összetevők telepítésére. Adja meg a szükséges összetevők telepítésének jóváhagyását.

A `download.microsoft.com`hoz való hozzáférés szükséges a biztonságos csatorna létrehozásához használt összetevők letöltéséhez a parancsfájlt futtató gép és a helyreállítási pontban lévő információ között.

A parancsfájlt bármely olyan gépen futtathatja, amely azonos (vagy kompatibilis) operációs rendszerrel rendelkezik, mint a biztonsági másolattal ellátható virtuális gép. Tekintse meg a kompatibilis operációs rendszerekhez használható operációsrendszer- [táblázatot](backup-azure-restore-files-from-vm.md#system-requirements) . Ha a védett Azure-beli virtuális gép Windows-tárolóhelyeket (Windows Azure-beli virtuális gépek esetén) vagy LVM/RAID-tömböket használ (linuxos virtuális gépek esetén), akkor nem futtathatja a végrehajtható fájlt vagy a parancsfájlt ugyanazon a virtuális gépen. Ehelyett futtassa a végrehajtható fájlt vagy a parancsfájlt bármely más, kompatibilis operációs rendszerrel rendelkező gépen.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>A fájlok helyreállítása a nagyméretű lemezekkel rendelkező virtuális gépekről

Ez a szakasz azt ismerteti, hogyan hajtható végre a fájlok helyreállítása a több mint 16 lemezzel rendelkező Azure-beli virtuális gépekről, és az egyes lemezek mérete meghaladja a 32 TB-ot.

Mivel a fájl-helyreállítási folyamat az összes lemezt csatlakoztatja a biztonsági mentésből, amikor nagy számú lemez (> 16) vagy nagyméretű lemez (> 32 TB) van használatban, a következő műveleti pontok ajánlottak:

- A fájlok helyreállításához külön helyreállítási kiszolgálót (Azure VM D2v3 virtuális gépeket) kell megőrizni. Ezt csak a fájl-helyreállításhoz használhatja, majd leállíthatja, ha nincs rá szükség. Az eredeti gépen való visszaállítás nem ajánlott, mert jelentős hatással lesz a virtuális gépre.
- Ezután futtassa egyszer a parancsfájlt annak vizsgálatához, hogy a fájl-helyreállítási művelet sikeres-e.
- Ha a fájl-helyreállítási folyamat lefagy (a lemezek soha nincsenek csatlakoztatva, vagy csatlakoztatva vannak, de nem jelennek meg a kötetek), hajtsa végre a következő lépéseket.
  - Ha a visszaállítási kiszolgáló egy Windows rendszerű virtuális gép:
    - Győződjön meg arról, hogy az operációs rendszer WS 2012 vagy újabb.
    - Győződjön meg arról, hogy a beállításjegyzék-kulcsok az alábbi módon vannak beállítva a visszaállítási kiszolgálón, és indítsa újra a kiszolgálót. A GUID melletti szám a 0001-0005-tól terjedhet. A következő példában ez a 0004. A parameters (paraméterek) szakaszig navigáljon a beállításkulcs elérési útjára.

    ![iSCSI-reg-Key-Changes. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

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
- Minden alkalommal, amikor a felhasználó letölt egy parancsfájlt, Azure Backup kezdeményezi a letöltéshez a helyreállítási pont előkészítésének folyamatát. A nagyméretű lemezek esetében ez a folyamat jelentős időt vesz igénybe. Ha egymást követő kérések fordultak elő, a cél-előkészítés egy letöltés spirálba kerül. Ezért javasoljuk, hogy töltsön le egy parancsfájlt a portálról, a Powershellből vagy a CLI-ből, várjon 20-30 percet (egy heurisztikus), majd futtassa. Ez idő alatt a cél várhatóan készen áll a parancsfájlból való kapcsolódásra.
- A fájl helyreállítása után lépjen vissza a portálra, és kattintson a **lemezek leválasztása** lehetőségre a helyreállítási pontokhoz, ahol nem tudta csatlakoztatni a köteteket. Ez a lépés lényegében törli a meglévő folyamatokat/munkameneteket, és növeli a helyreállítás esélyét.

## <a name="troubleshooting"></a>Hibakeresés

Ha problémák merülnek fel a virtuális gépek fájljainak helyreállítása közben, további információkért tekintse meg a következő táblázatot.

| Hibaüzenet/forgatókönyv | Lehetséges ok | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Exe-kimenet: *kivétel történt a célhoz való csatlakozás közben* . | A parancsfájl nem fér hozzá a helyreállítási ponthoz    | Győződjön meg arról, hogy a gép megfelel-e az [előző hozzáférési követelményeknek](#access-requirements). |  
| Exe-kimenet: *a cél már be van jelentkezve iSCSI-munkameneten keresztül.* | A parancsfájl már végre lett hajtva ugyanazon a gépen, és a meghajtók csatlakoztatva lettek | A helyreállítási pont kötetei már csatolva vannak. Előfordulhat, hogy a rendszer nem csatlakoztatja az eredeti virtuális gép ugyanazzal a meghajtóbetűjelével. Tallózással keresse meg a fájlhoz tartozó fájlkezelőben elérhető összes kötetet. |
| Exe-kimenet: *Ez a parancsfájl érvénytelen, mert a lemezek a portálon keresztül lettek leválasztva, vagy túllépte a 12 HR-korlátot. Töltsön le egy új parancsfájlt a portálról.* |    A lemezek le lettek választva a portálról, vagy túllépte a 12 órás korlátot. | Ez az adott exe már érvénytelen, és nem futtatható. Ha szeretné elérni a helyreállítási pont fájljait, látogasson el az új exe-portálra.|
| Azon a gépen, amelyen az exe fut: az új kötetek nem vannak leválasztva a Leválasztás gombra kattintás után | A gépen lévő iSCSI-kezdeményező nem válaszol, és nem frissíti a megcélzott kapcsolatát, és megtartja a gyorsítótárat. |  A **Leválasztás**gombra kattintva várjon néhány percet. Ha az új kötetek nem vannak leválasztva, böngésszen végig az összes köteten. Az összes kötet böngészésével a kezdeményező frissíti a kapcsolódást, és a kötet le van választva, és a lemez nem érhető el.|
| Exe-kimenet: a parancsfájl sikeresen fut, de az "új kötetek csatolva" nem jelenik meg a parancsfájl kimenetén. |    Ez egy átmeneti hiba    | A kötetek már csatolva lesznek. Nyissa meg a Explorert a tallózáshoz. Ha ugyanazt a gépet használja a parancsfájlok futtatásához, érdemes megfontolni a gép újraindítását, és a listát a következő exe-futtatásokban kell megjeleníteni. |
| Linux-specifikus: nem lehet megtekinteni a kívánt köteteket | Előfordulhat, hogy a parancsfájlt futtató gép operációs rendszere nem ismeri fel a védett virtuális gép mögöttes fájlrendszerét | Győződjön meg arról, hogy a helyreállítási pont összeomlás-konzisztens vagy fájl-konzisztens. Ha a fájl konzisztens, futtassa a parancsfájlt egy másik gépen, amelynek operációs rendszere felismeri a védett virtuális gép fájlrendszerét. |
| Windows-specifikus: nem lehet megtekinteni a kívánt köteteket | Lehet, hogy a lemezek csatlakoztatva lettek, de a kötetek nincsenek konfigurálva | A Lemezkezelés képernyőn azonosítsa a helyreállítási ponthoz kapcsolódó további lemezeket. Ha a lemezek bármelyike offline állapotban van, próbálja meg online állapotba helyezni, ha a jobb gombbal a lemezre kattint, és az **online**lehetőségre kattint.|

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

A parancsfájl futtatásához jelszó szükséges, amely csak a Azure Portal vagy a PowerShell/CLI parancsfájl generálásának időpontjában jelenik meg a felhatalmazott felhasználó számára. Ezzel biztosíthatja, hogy a parancsfájlt letöltő jogosult felhasználó a parancsfájl futtatásához is felelős legyen.

#### <a name="browse-files-and-folders"></a>Fájlok és mappák tallózása

A fájlok és mappák tallózásához a parancsfájl az iSCSI-kezdeményezőt használja a gépen, és csatlakozik ahhoz a helyreállítási ponthoz, amely iSCSI-célként van konfigurálva. Itt képzelheti el, hogy az egyik a vagy az összes összetevőt próbálja utánozni/hamisítani.

Kölcsönös CHAP-hitelesítési mechanizmust használunk, hogy minden összetevő hitelesítse a másikat. Ez azt jelenti, hogy a hamis kezdeményezők rendkívül nehéz csatlakozni az iSCSI-tárolóhoz, és ahhoz, hogy hamis célt lehessen csatlakoztatni ahhoz a géphez, amelyen a parancsfájl fut.

A helyreállítási szolgáltatás és a gép közötti adatforgalom védelme a biztonságos SSL-alagút TCP-kapcsolaton keresztüli létrehozásával történik (a TLS 1,2-et a parancsfájl futtatására szolgáló gépen[kell támogatni](#system-requirements) ).

A szülő/biztonsági mentés alatt lévő virtuális gépen található összes fájl Access Control lista (ACL) a csatlakoztatott fájlrendszerben is megmarad.

A parancsfájl csak olvasási hozzáférést biztosít egy helyreállítási ponthoz, és csak 12 órára érvényes. Ha korábban el szeretné távolítani a hozzáférést, jelentkezzen be az Azure Portalra, a PowerShellbe vagy a CLI-be, és válassza le az adott helyreállítási ponthoz a **leválasztott lemezeket** . A parancsfájl azonnal érvénytelenítve lesz.

## <a name="next-steps"></a>Következő lépések

- A fájlok visszaállítása során felmerülő problémákért tekintse meg a [hibaelhárítási](#troubleshooting) szakaszt.
- Ismerje meg, hogyan [állíthatja vissza a fájlokat a PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup) használatával
- Ismerje meg, hogyan [állíthatja vissza a fájlokat az Azure CLI-n keresztül](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- A virtuális gép visszaállítása után megtudhatja, hogyan [kezelheti a biztonsági mentéseket](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
