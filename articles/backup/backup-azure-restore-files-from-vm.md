---
title: Fájlok és mappák helyreállítása az Azure virtuális gép biztonsági mentéséből
description: Ebből a cikkből megtudhatja, hogyan állíthatja helyre a fájlokat és mappákat egy Azure virtuálisgép-helyreállítási pontból.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273305"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása az Azure virtuálisgép biztonsági másolatából

Az Azure Backup lehetővé teszi az [Azure virtuális gépek (VM-ek) és lemezek](./backup-azure-arm-restore-vms.md) visszaállítását az Azure virtuális gépek biztonsági mentéseiből, más néven helyreállítási pontokból. Ez a cikk bemutatja, hogyan állíthatja helyre a fájlokat és mappákat egy Azure virtuális gép biztonsági mentéséből. A fájlok és mappák visszaállítása csak az Erőforrás-kezelő modell használatával telepített és a Helyreállítási szolgáltatások tárolójában védett Azure virtuális gépek számára érhető el.

> [!NOTE]
> Ez a funkció az Erőforrás-kezelő modell használatával telepített és a Recovery Services-tárolóban védett Azure virtuális gépek számára érhető el.
> A titkosított virtuális gép biztonsági mentéséből származó fájlhelyreállítás nem támogatott.
>

## <a name="mount-the-volume-and-copy-files"></a>A kötet csatlakoztatása és fájlok másolása

Fájlok vagy mappák helyreállítási pontból történő visszaállításához lépjen a virtuális gépre, és válassza ki a kívánt helyreállítási pontot.

1. Jelentkezzen be az [Azure Portalra,](https://portal.Azure.com) és a bal oldali ablaktáblában kattintson a **Virtuális gépek**elemre. A virtuális gépek listájából válassza ki a virtuális gépet a virtuális gép irányítópultjának megnyitásához.

2. A virtuális gép menüjében kattintson a **Biztonsági mentés** parancsra a Biztonsági másolat irányítópult megnyitásához.

    ![A Helyreállítási szolgáltatások tárolójának biztonsági másolata](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Kattintson a Biztonsági másolat irányítópult **menüjében a Fájlhelyreállítás parancsra.**

    ![Fájl-helyreállítás gomb](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Megnyílik **a Fájlhelyreállítás** menü.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A **Helyreállítási pont kiválasztása** legördülő menüben válassza ki a kívánt fájlokat tartalmazó helyreállítási pontot. Alapértelmezés szerint a legutóbbi helyreállítási pont már ki van jelölve.

5. A fájlok helyreállítási pontból történő másolásához használt szoftver letöltéséhez kattintson a **Végrehajtható fájl letöltése** (Windows Azure virtuális gépekhez) vagy **a Download Script** (Linux Azure virtuális gépekhez python parancsfájl létrehozása) lehetőségre.

    ![Létrehozott jelszó](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Az Azure letölti a végrehajtható vagy parancsfájlt a helyi számítógépre.

    ![üzenet letöltése a végrehajtható fájlhoz vagy parancsfájlhoz](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ha rendszergazdaként szeretné futtatni a végrehajtható fájlt vagy a parancsfájlt, javasoljuk, hogy mentse a letöltött fájlt a számítógépre.

6. A végrehajtható fájl vagy parancsfájl jelszóval védett, és jelszót igényel. A **Fájlhelyreállítás** menüben kattintson a másolás gombra a jelszó memóriába töltéséhez.

    ![Létrehozott jelszó](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A letöltési helyről (általában a Letöltések mappából) kattintson a jobb gombbal a végrehajtható fájlra vagy a parancsfájlra, és futtassa rendszergazdai hitelesítő adatokkal. Amikor a rendszer kéri, írja be a jelszót, vagy illessze be a jelszót a memóriából, majd nyomja le az **Enter billentyűt.** Az érvényes jelszó megadása után a parancsfájl csatlakozik a helyreállítási ponthoz.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Linux-gépek hez python-parancsfájl jön létre. Le kell tölteni a szkriptet, és másolni a megfelelő / kompatibilis Linux szerverre. Előfordulhat, hogy módosítania kell a ```chmod +x <python file name>```végrehajtáshoz szükséges engedélyeket. Ezután futtassa ```./<python file name>```a python fájlt a segítségével.

A parancsfájl sikeres futtatásának biztosításához tekintse meg az [Access követelményei](#access-requirements) szakaszt.

### <a name="identifying-volumes"></a>Kötetek azonosítása

#### <a name="for-windows"></a>Windows esetén

A végrehajtható fájl futtatásakor az operációs rendszer csatlakoztatja az új köteteket, és meghajtóbetűjeleket rendel hozzá. A Windows Intéző vagy a Fájlkezelő segítségével tallózhat ezeken a meghajtókon. Előfordulhat, hogy a kötetekhez rendelt meghajtóbetűjelek nem egyeznek meg az eredeti virtuális géppel. A kötet neve azonban megmarad. Ha például az eredeti virtuális gépen lévő kötet "Data Disk (E:`\`)", akkor a kötet a helyi`\`számítógépen "Adatlemez ("Bármilyen betű": ) néven csatolható. Böngésszen a parancsfájl kimenetében említett összes kötet között, amíg meg nem találja a fájlokat vagy a mappát.  

   ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux esetén

Linux alatt a helyreállítási pont kötetei ahhoz a mappához vannak csatlakoztatva, ahol a parancsfájl fut. A csatlakoztatott lemezek, kötetek és a megfelelő csatlakoztatási útvonalak ennek megfelelően jelennek meg. Ezek a csatlakoztatási útvonalak a gyökérszintű hozzáféréssel rendelkező felhasználók számára láthatók. Böngésszen a parancsfájl kimenetében említett kötetek között.

  ![Linux fájl helyreállítási menü](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>A kapcsolat lezárása

Miután azonosította a fájlokat, és átmásolta őket egy helyi tárolóhelyre, távolítsa el (vagy távolítsa el) a további meghajtókat. A meghajtók leválasztásához kattintson az Azure Portal **Fájl-helyreállítási** **menüjében a Lemezek leválasztása parancsra.**

![Lemezek leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

A lemezek leválasztása után egy üzenet jelenik meg. A kapcsolat frissítése eltarthat néhány percig, hogy eltávolíthassa a lemezeket.

Linux alatt, miután a kapcsolat a helyreállítási pont megszakadt, az operációs rendszer nem távolítja el automatikusan a megfelelő csatlakoztatási útvonalakat. A csatlakoztatási útvonalak léteznek "árva" kötetek és látható, de dobjon egy hiba, amikor a fájlok elérése / írása. Ezek manuálisan eltávolíthatók. A parancsfájl futtatásakor azonosítja az ilyen kötetek meglévő bármely korábbi helyreállítási pontok, és törli őket a hozzájárulás alapján.

## <a name="special-configurations"></a>Speciális konfigurációk

### <a name="dynamic-disks"></a>Dinamikus lemezek

Ha a védett Azure virtuális gép kötetek az alábbi jellemzők egyikével vagy mindkettővel rendelkezik, nem futtathatja a végrehajtható parancsfájlt ugyanazon a virtuális gépen.

- Több lemezre kiterjedő kötetek (átnyúló és csíkozott kötetek)
- Hibatűrő kötetek (tükrözött és RAID-5 kötetek) dinamikus lemezeken

Ehelyett futtassa a végrehajtható parancsfájlt bármely más, kompatibilis operációs rendszerrel rendelkező számítógépen.

### <a name="windows-storage-spaces"></a>Windows-tárhelyek

A Windows tárolóhelyek egy Windows-technológia, amely lehetővé teszi a tárhely virtualizálását. A Windows tárolóhelyek segítségével az iparági szabványnak megfelelő lemezeket tárolókészletekbe csoportosíthatja. Ezután használja a rendelkezésre álló helyet ezekben a tárolókészletekben, hogy hozzon létre virtuális lemezek, úgynevezett tárolóhelyek.

Ha a védett Azure virtuális gép windows storage spaces, nem futtathatja a végrehajtható parancsfájlt ugyanazon a virtuális gépen. Ehelyett futtassa a végrehajtható parancsfájlt bármely más, kompatibilis operációs rendszerrel rendelkező gépen.

### <a name="lvmraid-arrays"></a>LVM/RAID tömbök

Linux alatt a logikai kötetkezelő (LVM) és/vagy a szoftverRAID-tömbök logikai kötetek kezelésére szolgálnak több lemezen. Ha a védett Linux os virtuális gép LVM és/vagy RAID tömböket használ, nem futtathatja a parancsfájlt ugyanazon a virtuális rendszeren. Ehelyett futtassa a parancsfájlt bármely más gépen egy kompatibilis operációs rendszerrel, és amely támogatja a védett virtuális gép fájlrendszerét.

A következő parancsfájlkimenet az LVM és/vagy RAID Tömbök lemezeit és a partíciótípussal rendelkező köteteket jeleníti meg.

   ![Linux LVM kimenet menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

A partíciók online állapotba hozásához futtassa a parancsokat a következő szakaszokban.

#### <a name="for-lvm-partitions"></a>LVM-partíciók esetén

A kötetcsoport nevek felsorolása fizikai kötet alatt:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Az összes logikai kötet, név és elérési útjuk listázása kötetcsoportban:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

A logikai kötetek csatlakoztatása az Ön által választott útvonalhoz:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>RAID-tömbökhöz

A következő parancs az összes raid lemez részleteit jeleníti meg:

```bash
#!/bin/bash
mdadm –detail –scan
```

 A megfelelő RAID-lemez`/dev/mdm/<RAID array name in the protected VM>`

Használja a csatlakoztatási parancsot, ha a RAID-lemez fizikai kötetekkel rendelkezik:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemezben egy másik LVM van konfigurálva, akkor az lvm-partíciókhoz az előző eljárást használja, de használja a kötet nevét a RAID-lemez neve helyett.

## <a name="system-requirements"></a>Rendszerkövetelmények

### <a name="for-windows-os"></a>Windows operációs rendszer esetén

Az alábbi táblázat a kiszolgáló és a számítógép operációs rendszerei közötti kompatibilitást mutatja be. A fájlok helyreállításakor a fájlok nem állíthatók vissza az operációs rendszer korábbi vagy jövőbeli verziójára. Windows Server 2016 virtuális gépről például nem állítható vissza fájl Windows Server 2012 vagy Windows 8 rendszerű számítógépre. A virtuális gépről visszaállíthatja a fájlokat ugyanarra a kiszolgálói operációs rendszerre vagy a kompatibilis ügyféloperációs rendszerre.

|Kiszolgáló operációs rendszer | Kompatibilis ügyfél operációs rendszer  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux operációs rendszerhez

Linux alatt a fájlok visszaállításához használt számítógép operációs rendszerének támogatnia kell a védett virtuális gép fájlrendszerét. A parancsfájl futtatásához használt számítógép kiválasztásakor győződjön meg arról, hogy a számítógép kompatibilis operációs rendszerrel rendelkezik, és az alábbi táblázatban meghatározott verziók egyikét használja:

|Linux operációs rendszer | Verziók  |
| --------------- | ---- |
| Ubuntu | 12.04 és újabb |
| CentOS | 6.5 és újabb  |
| RHEL | 6.7 és újabb |
| Debian | 7 és újabb |
| Oracle Linux | 6.4 és újabb |
| SLES | 12 és újabb |
| openSUSE | 42.2 és újabb |

> [!NOTE]
> Találtunk néhány problémát a fájl-helyreállítási parancsfájl futtatásában az SLES 12 SP4 operációs rendszerrel rendelkező gépeken, és az SLES csapattal vizsgáljuk.
> Jelenleg a fájl-helyreállítási parancsfájl futtatása az SLES 12 SP2 és SP3 operációs rendszer verzióval rendelkező gépeken működik.
>

A parancsfájl python- és bash-összetevőket is igényel a helyreállítási ponthoz való biztonságos végrehajtáshoz és csatlakozáshoz.

|Összetevő | Verzió  |
| --------------- | ---- |
| bash | 4 és újabb |
| python | 2.6.6 és újabb  |
| TLS | 1.2 támogatni kell  |

## <a name="access-requirements"></a>Hozzáférési követelmények

Ha a parancsfájlt korlátozott hozzáférésű számítógépen futtatja, győződjön meg arról, hogy van hozzáférése:

- `download.microsoft.com`
- Helyreállítási szolgáltatás URL-címei (a földrajzi név arra a régióra utal, ahol a helyreállítási szolgáltatás tárolója található)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Nyilvános Azure-beli földrajzi szolgáltatások esetén)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Az Azure China 21Vianet esetén)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Az Azure US Government esetében)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Az Azure Germany esetében)
- Kimenő portok 53 (DNS), 443, 3260

> [!NOTE]
>
> - A letöltött parancsfájlneve az **URL-címben** kitöltendő földrajzi nevet fogja. A exampple: A letöltött \'szkript neve\'\_\'kezdődik\'\'VMname geoname _ GUID\', mint *ContosoVM_wcus_12345678*
> - Az URL <https://pod01-rec2.wcus.backup.windowsazure.com>lenne "
>

Linux esetén a szkripthez "open-iscsi" és "lshw" összetevők szükségesek a helyreállítási ponthoz való csatlakozáshoz. Ha az összetevők nem léteznek azon a számítógépen, amelyen a parancsfájl fut, a parancsfájl engedélyt kér az összetevők telepítéséhez. Adja meg a szükséges összetevők telepítéséhez való hozzájárulást.

A hozzáférés `download.microsoft.com` szükséges a biztonságos csatorna létrehozásához használt összetevők letöltéséhez a parancsfájlt futtató gép és a helyreállítási pontban lévő adatok között.

A parancsfájl talláin futtathatja, amely ugyanazt (vagy kompatibilis) operációs rendszert rendelkezik, mint a biztonsági másolatot készített virtuális gép. A kompatibilis operációs rendszerekről a [Kompatibilis operációs rendszer táblázatban](backup-azure-restore-files-from-vm.md#system-requirements) látható. Ha a védett Azure virtuális gép windowsos tárolóhelyeket (Windows Azure-beli virtuális gépekhez) vagy LVM/RAID tömböket (Linuxos virtuális gépekhez) használ, nem futtathatja a végrehajtható fájlt vagy a parancsfájlt ugyanazon a virtuális gépen. Ehelyett futtassa a végrehajtható fájlt vagy parancsfájlt bármely más, kompatibilis operációs rendszerrel rendelkező számítógépen.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Fájlhelyreállítás nagy lemezekkel rendelkező virtuálisgép-biztonsági másolatokból

Ez a szakasz bemutatja, hogyan hajthatja végre a fájl-helyreállítást az Azure virtuális gépek biztonsági mentéseiből, amelyek több mint 16 lemezzel és minden lemezméret nagyobb, mint 32 TB.

Mivel a fájl-helyreállítási folyamat a biztonsági másolatból származó összes lemezt csatlakoztatja, ha nagy számú lemezt (>16) vagy nagy lemezeket (egyenként > 32 TB-ot) használ, a következő cselekvési pontok ajánlottak:

- Tartsa meg egy külön visszaállítási kiszolgáló (Azure VM D2v3 virtuális gépek) a fájl-helyreállításhoz. Ezt csak a fájl-helyreállításhoz használhatja, majd leállíthatja, ha nem szükséges. Visszaállítása az eredeti gépen nem ajánlott, mivel jelentős hatással lesz a virtuális gép is.
- Ezután futtassa a parancsfájlt egyszer, és ellenőrizze, hogy a fájl-helyreállítási művelet sikeres-e.
- Ha a fájl-helyreállítási folyamat lefagy (a lemezek soha nem vannak csatlakoztatva, vagy csatlakoztatva vannak, de a kötetek nem jelennek meg), hajtsa végre a következő lépéseket.
  - Ha a visszaállítási kiszolgáló Windows virtuális gép:
    - Győződjön meg arról, hogy az operációs rendszer WS 2012 vagy újabb.
    - Győződjön meg arról, hogy a beállításkulcsok az alábbiakszerint vannak beállítva a visszaállítási kiszolgálón, és győződjön meg arról, hogy újraindítja a kiszolgálót. A GUID melletti szám 0001-0005 között lehet. A következő példában 0004. Navigáljon a beállításkulcs elérési útján a paraméterek szakaszig.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Ha a visszaállítási kiszolgáló Linux virtuális gép:
  - Az /etc/iscsi/iscsid.conf fájlban módosítsa a beállítást:
    - node.conn[0].timeo.noop_out_timeout = 5 a csomópontnak.conn[0].timeo.noop_out_timeout = 30
- A fenti módosítás után futtassa újra a parancsfájlt. Ezekkel a változásokkal nagyon valószínű, hogy a fájl helyreállítása sikeres lesz.
- Minden alkalommal, amikor a felhasználó letölt egy parancsfájlt, az Azure Backup elindítja a helyreállítási pont előkészítésének folyamatát a letöltéshez. Nagy lemezek esetén ez a folyamat jelentős időt vesz igénybe. Ha egymást követő kérelmek sorozatai vannak, a cél előkészítése egy letöltési spirálba kerül. Ezért javasoljuk, hogy töltsön le egy parancsfájlt a Portal/Powershell/CLI-ből, várjon 20-30 percet (heurisztika), majd futtassa azt. Ekkorra a cél várhatóan készen áll a parancsfájlból való kapcsolatra.
- A fájl helyreállítása után győződjön meg arról, hogy menjen vissza a portálra, és kattintson **a Lemezek leválasztása** gombra a helyreállítási pontokhoz, ahol nem sikerült köteteket csatlakoztatnia. Lényegében ez a lépés megtisztítja a meglévő folyamatokat/munkameneteket, és növeli a helyreállítás esélyét.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a virtuális gépekről való helyreállítás során problémákat tapasztal, további információt az alábbi táblázatban talál.

| Hibaüzenet / forgatókönyv | Valószínű ok | Javasolt művelet |
| ------------------------ | -------------- | ------------------ |
| Exe kimenet: *A célhoz való csatlakozás közben észlelt kivétel* | A parancsfájl nem tud hozzáférni a helyreállítási ponthoz    | Ellenőrizze, hogy a készülék megfelel-e a [korábbi hozzáférési követelményeknek.](#access-requirements) |  
| Exe kimenet: *A cél már bejelentkezett egy iSCSI-munkameneten keresztül.* | A parancsfájl már ugyanazon a gépen lett végrehajtva, és a meghajtók at csatolták | A helyreállítási pont kötetei már csatolva vannak. Előfordulhat, hogy nem az eredeti virtuális gép azonos meghajtóbetűjeleivel vannak felszerelve. Tallózzon a fájlkezelő összes rendelkezésre álló kötetében. |
| Kimenet kimenete: Ez a parancsfájl érvénytelen, mert a lemezek lelettek *választani a portálon keresztül/túllépte a 12 órás korlátot. Töltsön le egy új parancsfájlt a portálról.* |    A lemezeklelettek távolak a portálról, vagy a 12 órás korlátot túllépték | Ez az egyes exe érvénytelen, ezért nem futtatható. Ha szeretné elérni a fájlokat, hogy a helyreállítási pont-in-time, látogasson el a portálon egy új exe.|
| Azon a számítógépen, amelyen az exe fut: Az új kötetek nem lesznek leválasztásra a leválasztás gombjára való kattintás után | A számítógép iSCSI-kezdeményezője nem válaszol/nem frissíti a kapcsolatot a célhoz, és nem tartja karban a gyorsítótárat. |  Kattintás után **Leválasztás**, várjon néhány percet. Ha az új kötetek nincsenek leválasztása, tallózzon az összes kötet között. Az összes kötet böngészése arra kényszeríti a kezdeményezőt, hogy frissítse a kapcsolatot, és a kötet et egy hibaüzenet küldi el, amely szerint a lemez nem érhető el.|
| Exe kimenet: A parancsfájl sikeresen futott, de az "Új kötetek csatolva" nem jelenik meg a parancsfájl kimenetén |    Ez egy átmeneti hiba    | A kötetek már csatolva lesznek. Nyissa meg az Intézőt a böngészéshez. Ha ugyanazt a gépet használja a parancsfájlok futtatásához minden alkalommal, fontolja meg a számítógép újraindítását, és a lista jelenik meg a következő exe fut. |
| Linux-specifikus: Nem tudja megtekinteni a kívánt köteteket | Előfordulhat, hogy annak a számítógépnek az operációs rendszere, amelyen a parancsfájl fut, előfordulhat, hogy nem ismeri fel a védett virtuális gép alapjául szolgáló fájlrendszert | Ellenőrizze, hogy a helyreállítási pont összeomlás-konzisztens vagy fájlkonzisztens. Ha a fájl konzisztens, futtassa a parancsfájlt egy másik gépen, amelynek operációs rendszere felismeri a védett virtuális gép fájlrendszerét. |
| Windows-specifikus: Nem tudja megtekinteni a kívánt köteteket | Lehet, hogy a lemezek csatlakoztatva voltak, de a kötetek nincsenek konfigurálva | A lemezkezelés képernyőn azonosítsa a helyreállítási ponthoz kapcsolódó további lemezeket. Ha a lemezek bármelyike kapcsolat nélküli állapotban van, próbálja meg online állapotba hozni őket, kattintson a jobb gombbal a lemezre, majd kattintson az **Online**parancsra.|

## <a name="security"></a>Biztonság

Ez a szakasz ismerteti a különböző biztonsági intézkedéseket hozott az Azure virtuális gépek biztonsági mentések fájl-helyreállítás megvalósítása érdekében.

### <a name="feature-flow"></a>Jellemző folyamata

Ez a szolgáltatás a virtuális gép adatainak eléréséhez készült anélkül, hogy a teljes virtuális gép vagy virtuálisgép-lemezek visszaállítása, és a minimális számú lépést. A virtuális gép adataihoz való hozzáférést egy parancsfájl biztosítja (amely az alábbi módon futtatva csatlakoztatja a helyreállítási kötetet), és ez képezi az összes biztonsági implementáció sarokkövét:

  ![Biztonsági szolgáltatás folyamata](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Biztonsági megvalósítások

#### <a name="select-recovery-point-who-can-generate-script"></a>Válassza a Helyreállítási pont lehetőséget (aki parancsfájlt tud létrehozni)

A parancsfájl hozzáférést biztosít a virtuális gép adataihoz, ezért fontos szabályozni, hogy ki generálhatja azt az első helyen. Be kell jelentkeznie az Azure Portalon, és [rBAC jogosult](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a parancsfájl létrehozásához.

A fájl-helyreállításnak ugyanolyan szintű engedélyre van szüksége, mint a virtuális gépek visszaállításához és a lemezek visszaállításához. Más szóval csak a jogosult felhasználók megtekinthetik a virtuális gép adatait hozhat létre a parancsfájlt.

A létrehozott parancsfájl alá van írva az Azure Backup szolgáltatás hivatalos Microsoft-tanúsítványával. A parancsfájl bármilyen módosítása azt jelenti, hogy az aláírás megszakad, és a parancsfájl futtatására tett minden kísérlet az operációs rendszer potenciális kockázatként van kiemelve.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Mount Recovery kötet (aki futtathatja a parancsfájlt)

Csak egy rendszergazda futtathatja a parancsfájlt, és emelt szintű módban kell futnia. A parancsfájl csak egy előre létrehozott lépéskészletet futtat, és nem fogad el külső forrásból származó adatokat.

A parancsfájl futtatásához egy jelszó szükséges, amely csak akkor jelenik meg a jogosult felhasználó az Azure Portalon vagy a PowerShell/CLI parancsfájl létrehozásakor. Ez biztosítja, hogy a parancsfájlt letöltő jogosult felhasználó is felelős a parancsfájl futtatásáért.

#### <a name="browse-files-and-folders"></a>Fájlok és mappák tallózása

A fájlok és mappák tallózásához a parancsfájl a számítógép iSCSI-kezdeményezője, és csatlakozik az iSCSI-tárolóként konfigurált helyreállítási ponthoz. Itt el lehet képzelni forgatókönyvek, ahol az egyik próbál utánozni / svindló vagy / minden összetevő.

Kölcsönös CHAP hitelesítési mechanizmust használunk, hogy minden összetevő hitelesítse a másikat. Ez azt jelenti, hogy egy hamis kezdeményező rendkívül nehéz csatlakozni az iSCSI-tárolóhoz, és egy hamis cél csatlakozik a géphez, ahol a parancsfájl fut.

A helyreállítási szolgáltatás és a gép közötti adatfolyamot egy biztonságos TLS-alagút TCP-n keresztüli létrehozása védi[(a TLS 1.2-t támogatni kell](#system-requirements) abban a gépen, ahol a parancsfájl fut).

A szülő/biztonsági másolatot tartalmazó virtuális gépben található fájlhozzáférés-vezérlési lista (ACL) a csatlakoztatott fájlrendszerben is megmarad.

A parancsfájl csak olvasható hozzáférést biztosít a helyreállítási ponthoz, és csak 12 óráig érvényes. Ha korábban szeretné eltávolítani a hozzáférést, jelentkezzen be az Azure Portalon/PowerShell/CLI-be, és hajtsa végre az adott helyreállítási pont **leválasztási lemezeit.** A parancsfájl azonnal érvényteleníti.

## <a name="next-steps"></a>További lépések

- A fájlok visszaállítása során felmerülő problémákról a Hibaelhárítás című részben [tájékozódhat.](#troubleshooting)
- További információ a [fájlok Powershellen keresztüli visszaállításáról](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Ismerje meg, hogyan állíthatja vissza a fájlokat az [Azure CLI-n keresztül](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- A virtuális gép visszaállítása után ismerje meg, hogyan kezelheti a [biztonsági másolatokat](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
