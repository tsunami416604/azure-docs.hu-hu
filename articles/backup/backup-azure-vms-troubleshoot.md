---
title: Az Azure-beli virtuális gépek biztonsági mentési hibák elhárítása
description: Biztonsági mentés és visszaállítás Azure virtuális gépek hibaelhárítása
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: srinathv
ms.openlocfilehash: 6f10d8bc7f813245a66296988e4bb3792d898e08
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618192"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure-beli virtuális gépek biztonsági mentésének hibaelhárítása
Észlelt, miközben az adatokat az Azure Backup segítségével a következő táblázatban felsorolt hibák elhárítását:

## <a name="backup"></a>Backup

### <a name="copyingvhdsfrombackupvaulttakinglongtime--copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – másolás biztonsági másolat tárból túllépte az időkorlátot

Hibakód: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Hibaüzenet: Másolás biztonsági másolat tárból túllépte az időkorlátot

Ez átmeneti tárolási hibákat vagy nincs elegendő storage-fiók biztonsági mentési szolgáltatásban való adatátvitel a tároló az időkorláton belül IOPS miatt fordulhat elő. Konfigurálja a virtuális gép biztonsági mentésének ezeket [ajánlott eljárások](backup-azure-vms-introduction.md#best-practices) , majd próbálja megismételni a biztonsági mentési műveletet.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – virtuális gép nincs olyan állapotban, amely lehetővé teszi, hogy a biztonsági mentések.

Hibakód: UserErrorVmNotInDesirableState <br/>
Hibaüzenet: Virtuális gép olyan állapotban, amely lehetővé teszi a biztonsági mentések nem szerepel.<br/>

A biztonsági mentési művelet nem sikerült, mert a virtuális gép hibás állapotban van. A sikeres biztonsági mentés a virtuális gép állapota fut, leállítva vagy leállítva (felszabadítva) kell lennie.

* Ha a virtuális gép átmeneti állapotban közötti **futó** és **Leállítás**, várjon, amíg az állapot módosítása. Ezután aktiválja a biztonsági mentési feladatot.
*  Ha a virtuális gép Linux rendszerű virtuális gép, és az Security-Enhanced Linux-kernel modult használja, zárja ki az Azure Linux-ügynök elérési út **/var/lib/waagent** a biztonsági házirendet, és ellenőrizze, hogy a Backup bővítmény telepítve van.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – nem sikerült egy vagy több csatlakozási pontját a fájlrendszer konzisztens pillanatképének elkészítéséhez virtuális gép rögzítése

Hibakód: UserErrorFsFreezeFailed <br/>
Hibaüzenet: Nem sikerült befagyasztani egy vagy több csatlakozási pontját a virtuális Gépet, a fájlrendszer konzisztens pillanatképének elkészítéséhez.

* Ellenőrizze a fájlrendszer állapota az összes csatlakoztatott eszközök a **tune2fs** parancsot, például **tune2fs -l/dev/sdb1 \\** .\| grep **fájlrendszerállapota**.
* Válassza le az eszközöket, amelynek a fájlrendszer állapota nem lett eltávolítva, használja a **umount** parancsot.
* Egy fájl rendszer konzisztencia-ellenőrzést az eszközök használatával futtassa a **fsck** parancsot.
* Újra csatlakoztatni az eszközöket, és próbálkozzon újra a biztonsági mentési műveletet.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - bővítmény telepítése/művelet nem sikerült: a COM + hiba

Hibakód: ExtensionSnapshotFailedCOM <br/>
Hibaüzenet: Pillanatkép-készítési művelet a COM + hiba miatt nem sikerült

Hibakód: ExtensionInstallationFailedCOM  <br/>
Hibaüzenet: Bővítmény telepítési/művelet egy COM + hiba miatt nem sikerült

Hibakód: ExtensionInstallationFailedMDTC hibaüzenet jelenik meg: A bővítmény telepítése nem sikerült a "COM + nem tudta felvenni a kapcsolatot, a Microsoft Distributed Transaction Coordinator

Windows-szolgáltatással kapcsolatos probléma miatt sikertelen volt a biztonsági mentési művelet **COM + System** alkalmazás.  A probléma megoldásához kövesse az alábbi lépéseket:

* Windows-szolgáltatás indítása/indítsa újra **COM + System Application** (egy rendszergazda jogú parancssorból **-net start COMSysApp**).
* Győződjön meg, hogy **elosztott tranzakciók koordinátora** szolgáltatásokat futtató **hálózati szolgáltatás** fiókot. Ha nem, akkor módosítsa futtató **hálózati szolgáltatás** fiókját, és indítsa újra a **COM + System Application**.
* Ha nem sikerült újraindítani a szolgáltatást, majd telepítse újra **elosztott tranzakciók koordinátora** szolgáltatás a következő az alábbi lépéseket:
    * Állítsa le az MSDTC szolgáltatást
    * Nyisson meg egy parancssort (cmd)
    * A parancs futtatása "msdtc-eltávolítása"
    * a parancs futtatása "msdtc-telepítése"
    * Indítsa el az MSDTC szolgáltatást
* Indítsa el a Windows-szolgáltatást **COM + System Application**. Miután a **COM + System Application** elindul, aktiválja a biztonsági mentési feladatot az Azure Portalról.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - pillanatkép-készítési művelet sikertelen volt, mert a VSS-írók rossz állapotban volt

Hibakód: ExtensionFailedVssWriterInBadState <br/>
Hibaüzenet: Pillanatkép-készítési művelet nem sikerült, mert a VSS-írók rossz állapotban volt.

Indítsa újra a VSS-írók rossz állapotban lévő. Egy rendszergazda jogú parancssorból futtassa ```vssadmin list writers```. A kimenet tartalmazza az összes VSS-író és az állapotuk. A minden VSS-író nem állapotú **[1] Stable**, hogy indítsa újra a VSS-író, futtassa a következő parancsokat egy rendszergazda jogú parancssorból:

  * ```net stop serviceName```
  * ```net start serviceName```

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – hiba a biztonsági másolat bővítményének konfigurációelemzése

Hibakód: ExtensionConfigParsingFailure<br/>
Hibaüzenet: Hiba történt a biztonsági másolat bővítményének konfigurációelemzése során.

Ez a hiba a módosított engedélyek miatt történik, a **MachineKeys** könyvtár: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Futtassa a következő parancsot, és győződjön meg arról, hogy engedélyeket a **MachineKeys** könyvtár alapértelmezett is:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Alapértelmezett engedélyek a következők:
* Mindenki számára: (R, W)
* BUILTIN\Rendszergazdák: (F)

Ha látja az engedélyek a **MachineKeys** directory eltérnek az alapértelmezett értékeket, kövesse az alábbi lépéseket a megfelelő engedélyekkel, törölje a tanúsítványt, és aktiválja a biztonsági mentés:

1. Hárítsa el az engedélyek az a **MachineKeys** könyvtár. A címtárban Explorer biztonsági tulajdonságait és a speciális biztonsági beállítások használatával visszaállítása az engedélyek az alapértelmezett értékeket. Távolítsa el az összes felhasználói objektumok, kivéve a könyvtárból az alapértelmezett beállításokat, és ellenőrizze, hogy a **mindenki** engedéllyel rendelkezik a speciális hozzáférési módon:

    * Lista mappa adatok olvasása
    * Attribútumok olvasása
    * Kiterjesztett attribútumok olvasása
    * Hozzon létre fájlokat/adatok írása
    * Hozzon létre mappák/adatok hozzáfűzése
    * Attribútumok írása
    * Kiterjesztett attribútumok írása
    * Olvasási engedélyek
2. Törölje az összes tanúsítvány ahol **tulajdonos** a klasszikus üzemi modell vagy **Windows Azure CRP-tanúsítványkészítő**:
    * [Nyissa meg a helyi számítógép-konzolt a tanúsítványok](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * A **személyes** > **tanúsítványok**, törölje az összes tanúsítvány ahol **tulajdonos** a klasszikus üzemi modell vagy **Windows Azure CRP Tanúsítvány-generátor**.
3. Virtuális gép biztonsági mentési feladat aktiválása.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - bővítmény állapota nem támogatja a biztonsági mentés

Hibakód: ExtensionStuckInDeletionState <br/>
Hibaüzenet: Bővítmény állapota nem támogatja a biztonsági mentés

A biztonsági mentését a biztonsági mentési bővítmény inkonzisztens állapota miatt nem sikerült. A probléma megoldásához kövesse az alábbi lépéseket:

* Ellenőrizze, hogy a vendégügynök telepítve van-e és működik-e
* Az Azure Portalon lépjen a **Virtuális gép** > **Minden beállítás** > **Bővítmények** elemhez
* Válassza ki a VmSnapshot vagy a VmSnapshotLinux nevű biztonsági mentési bővítményt, és kattintson az **Eltávolítás** elemre
* A biztonsági mentési bővítmény törlése után próbálkozzon meg újra a biztonsági mentéssel
* Ez a biztonsági mentési művelet a kívánt állapotban fogja telepíteni az új bővítményt

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>Néhány csatlakoztatott lemezen ExtensionFailedSnapshotLimitReachedError - pillanatkép-készítési művelet sikertelen volt, mert a pillanatképekre vonatkozó korlát túllépésekor

Hibakód: ExtensionFailedSnapshotLimitReachedError  <br/>
Hibaüzenet: Pillanatkép-készítési művelet sikertelen volt, mert a pillanatképekre vonatkozó korlát néhány csatlakoztatott lemezen túllépte

A pillanatkép-művelet sikertelen volt, mert a pillanatképekre vonatkozó korlát néhány csatlakoztatott lemezen túllépte. Hajtsa végre az alábbi hibaelhárítási lépéseket, és ezután próbálja megismételni a műveletet.

* A blobpillanatképek a lemez – amelyek nem kötelezőek. Körültekintően járjon el, nem a lemez blob törlése, csak a pillanatkép-blobok törölni kell.
* Ha a helyreállítható törlés funkciójának engedélyezve van a virtuális gép disk Storage-fiókok, helyreállítható törlés megőrzésének konfigurálása, úgy, hogy a meglévő pillanatképeket kisebb, mint a megengedett időt bármely pontján.
* Ha az Azure Site Recovery a biztonsági másolat virtuális gépen engedélyezve van, majd hajtsa végre az alábbi:

    * Győződjön meg, hogy értékét **isanysnapshotfailed** false /etc/azure/vmbackup.conf van beállítva
    * Azure Site Recovery ütemezhet egy másik időpontra, úgy, hogy azt a biztonsági mentési művelet nem ütközik.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - pillanatkép-készítési művelet sikertelen volt, mert nincs elegendő VM-erőforrás.

Hibakód: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Hibaüzenet: Pillanatkép-készítési művelet nem megfelelő Virtuálisgép-erőforrások miatt nem sikerült.

A virtuális gép biztonsági mentési művelet a pillanatkép-készítési művelet végrehajtása közben a hálózati hívások késleltetése miatt nem sikerült. A probléma megoldásához hajtsa végre az 1. lépést. Ha a probléma továbbra is fennáll, próbálkozzon a 2. és a 3. lépéssel.

**1. lépés**: Pillanatkép létrehozása a gazdagépen keresztül

Egy emelt szintű (rendszergazdai) parancssorból futtassa a következő parancsot:

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Ez gondoskodik róla, hogy a pillanatképek a gazdagépen keresztül készüljenek a vendég helyett. Próbálkozzon újra a biztonsági mentési művelettel.

**2. lépés**: Próbálja meg módosítani a biztonsági mentési ütemezést egy időpontra, amikor a virtuális gép a less (kisebb CPU/IOps-stb.) terhelés alatt van

**3. lépés**: Próbálja ki [virtuális gép méretének növelését](https://azure.microsoft.com/blog/resize-virtual-machines/) , majd próbálja megismételni a műveletet

### <a name="common-vm-backup-errors"></a>Általános virtuális gép biztonsági mentési hibák

| A hiba részletei | Áthidaló megoldás |
| ------ | --- |
| Hibakód: 320001<br/> Hibaüzenet: Nem sikerült elvégezni a műveletet, mert a virtuális gép már nem létezik. <br/> <br/> Hibakód: 400094 <br/> Hibaüzenet: A virtuális gép nem létezik. <br/> <br/>  Az Azure virtuális gép nem található.  |Ez a hiba akkor fordul elő, amikor az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is figyeli a virtuális gép biztonsági mentése. Ez a hiba elhárításához tegye a következőket: <ol><li> Hozza létre újból az azonos nevű és azonos erőforráscsoport neve, a virtuális gép **felhőszolgáltatás neve**,<br>**vagy**</li><li> Állítsa le vagy anélkül, hogy a biztonsági mentési adatok törlése a virtuális gép védelmét. További információkért lásd: [állítsa le a virtuális gépek védelme](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| A virtuális gép kiépítési állapota sikertelen van: <br>Indítsa újra a virtuális Gépet, és győződjön meg arról, hogy a virtuális gép fut-e, vagy állítsa le. | Ez a hiba akkor fordul elő, ha a virtuális gép beteszi a bővítményekkel kapcsolatos hibák egyike üzembe helyezési állapota sikertelen. Nyissa meg a bővítmények listájához, ellenőrizze, ha van egy sikertelen bővítményt, távolítsa el, és próbálja meg újraindítani a virtuális gép. Ha az összes bővítmény futó állapotban van, ellenőrizze, hogy a Virtuálisgép-ügynök szolgáltatás fut-e. Ha nem, indítsa újra a Virtuálisgép-ügynök szolgáltatást. |
|Hibakód: UserErrorBCMPremiumStorageQuotaError<br/> Hibaüzenet: A pillanatkép a virtuális gép, a storage-fiókban nincs elég szabad hely miatt nem sikerült másolni | A prémium szintű virtuális gépek a virtuális gép biztonsági másolat verem V1 a storage-fiókot a pillanatkép másolja azt. Ebben a lépésben gondoskodik arról, hogy a biztonságimásolat-kezelési forgalom, amely működik a pillanatkép, nem korlátozza a prémium lemezek segítségével az alkalmazáshoz rendelkezésre álló IOPS számát. <br><br>Azt javasoljuk, hogy csak 50 %-os, 17.5 TB, a teljes tárterület fiók lefoglalni. Ezután az Azure Backup szolgáltatás másolhatja a pillanatkép a tárolási fiók és az átviteli adatok erről a helyről másolt a tárfiókban a tárolóba. |
| Nem sikerült telepíteni a Microsoft Recovery Services-bővítmény, a virtuális gép nem fut. <br>A Virtuálisgép-ügynök az Azure Recovery Services-bővítmény előfeltétele. Az Azure virtuálisgép-ügynök telepítése, és indítsa újra a regisztrációs művelet. |<ol> <li>Ellenőrizze, hogy ha a Virtuálisgép-ügynök megfelelően van-e telepítve. <li>Győződjön meg arról, hogy megfelelően van-e beállítva a virtuális gép konfigurációs jelölőjét.</ol> További információ a Virtuálisgép-ügynök és a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| A pillanatkép-készítési művelet sikertelen volt a kötet árnyékmásolata szolgáltatás (VSS) művelet hibája: **a meghajtó BitLocker meghajtótitkosítással zárolva van. Meg kell oldja fel a zárolást a Vezérlőpulton.** |Kapcsolja ki a BitLocker az összes meghajtón a virtuális gépen, és ellenőrizze, hogy megoldódott-e a VSS-hiba. |
| A virtuális gép olyan állapotban, amely lehetővé teszi, hogy a biztonsági másolatok nem. |<ul><li>Ha a virtuális gép átmeneti állapotban közötti **futó** és **Leállítás**, várjon, amíg az állapot módosítása. Ezután aktiválja a biztonsági mentési feladatot. <li> Ha a virtuális gép Linux rendszerű virtuális gép, és az Security-Enhanced Linux-kernel modult használja, zárja ki az Azure Linux-ügynök elérési út **/var/lib/waagent** a biztonsági házirendet, és ellenőrizze, hogy a Backup bővítmény telepítve van.  |
| A Virtuálisgép-ügynök nem található a virtuális gépen: <br>Bármely előfeltétel és a Virtuálisgép-ügynök telepítése. Ezután indítsa újra a műveletet. |Tudjon meg többet [Virtuálisgép-ügynök telepítésének és a Virtuálisgép-ügynök telepítésének ellenőrzése](#vm-agent). |
| Biztonsági mentés nem sikerült befagyasztani a virtuális gép egy rendszer alkalmazáskonzisztens pillanatképet egy vagy több csatlakoztatási pontot. | A következő lépésekkel: <ul><li>Ellenőrizze a fájlrendszer állapota az összes csatlakoztatott eszközök használatával a **"tune2fs"** parancsot. Például **tune2fs -l/dev/sdb1 \\** .\| grep **fájlrendszer állapota**. <li>Az eszközök, amelynek a fájlrendszer állapota nem tiszta használatával válassza le a **"umount"** parancsot. <li> Egy fájl rendszer konzisztencia-ellenőrzést az eszközök használatával futtassa a **"fsck"** parancsot. <li> Újra csatlakoztatni az eszközöket, és próbálja meg a biztonsági mentés.</ol> |
| A pillanatkép-készítési művelet nem sikerült létrehozni egy biztonságos hálózati kommunikációs csatorna hiba miatt. | <ol><li> Nyissa meg a Beállításszerkesztőt futtatásával **regedit.exe** egy emelt jogosultságszintű módban. <li> Azonosítsa a rendszerben található a .NET-keretrendszer összes verziójához. Azok a hierarchia beállításkulcs alatt található **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Minden .NET-keretrendszer a beállításkulcs megtalálható adja hozzá a következő kulcsot: <br> **SchUseStrongCrypto "= dword: 00000001**. </ol>|
| A pillanatkép-készítési művelet nem sikerült telepíteni a Visual C++ terjeszthető csomag Visual Studio 2012 miatt nem sikerült. | Navigáljon a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion, és telepítse a vcredist2012_x64.<br/>Győződjön meg arról, hogy a beállításkulcs-érték, amely lehetővé teszi, hogy a szolgáltatás telepítése a helyes értékre van állítva. Állítsa be a **Start** értékét **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** való **3** , és nem **4**. <br><br>Ha továbbra is problémákba ütközik, indítsa újra a telepítést szolgáltatást futtatásával **MSIEXEC /UNREGISTER** követ **MSIEXEC /REGISTER** egy rendszergazda jogú parancssorból.  |


## <a name="jobs"></a>Feladatok

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Lemondás esetén ez a feladattípus nem támogatott: <br>Várjon, amíg a feladat befejeződik. |None |
| A feladat nem visszavonható állapotú: <br>Várjon, amíg a feladat befejeződik. <br>**vagy**<br> A kiválasztott feladat nem visszavonható állapotú: <br>Várjon, amíg a feladat befejeződésére. |Valószínű, hogy a feladat majdnem befejeződött. Várjon, amíg a feladat befejeződött.|
| Biztonsági mentési feladat nem szakítható meg, mert nem szerepel a folyamat: <br>Törlés csak a folyamatban lévő feladatok esetében támogatott. Próbálja meg visszavonni egy folyamatban lévő feladat. |Ez a hiba akkor fordul elő, egy átmeneti állapot miatt. Várjon egy percet, és próbálja megismételni a megszakítási műveletet. |
| A biztonsági mentés nem sikerült megszakítani a feladatot: <br>Várjon, amíg a feladat befejeződik. |None |

## <a name="restore"></a>Visszaállítás

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Egy felhőalapú belső hiba miatt nem sikerült visszaállítani. |<ol><li>A felhőalapú szolgáltatás, amelyhez visszaállítani kívánt DNS-beállításokkal van konfigurálva. Ellenőrizheti: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Ha **cím** konfigurálva van, akkor a DNS-beállítások konfigurálása.<br> <li>A felhőalapú szolgáltatás, amelyhez a visszaállítani kívánt állítottak **fenntartott IP-cím**, és a felhőszolgáltatás virtuális gépeinek konfigurációjától leállított állapotban van. Ellenőrizheti, hogy egy felhőalapú szolgáltatás a következő PowerShell-parancsmagok használatával lefoglalt IP-címet: **$deployment = Get-AzureDeployment - ServiceName "szolgáltatásnév"-"Éles" $tárolóhely eszközökre. ReservedIPName**. <br><li>Szeretne egy virtuális gépet ugyanazon a felhőszolgáltatáson, a következő speciális hálózati konfigurációk visszaállításához: <ul><li>Terheléselosztó konfigurációját, belső és külső virtuális gépeket.<li>Virtuális gépek több fenntartott IP-címmel. <li>Több hálózati adapterrel rendelkező virtuális gépeket. </ul><li>Válassza ki egy új felhőszolgáltatást a felhasználói felületen vagy lásd [helyreállítási szempontjai](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) speciális hálózati konfigurációval rendelkező virtuális gépek számára.</ol> |
| A kiválasztott DNS-név már használatban van: <br>Adjon meg egy másik DNS-nevet, és próbálkozzon újra. |A DNS-név hivatkozik a felhőszolgáltatás neve, általában végződő **. cloudapp.net**. Ez a név egyedinek kell lennie. Ha ez a hibaüzenet kell egy másik virtuális gép nevet választ a visszaállítás során. <br><br> Ez a hiba csak a felhasználók számára az Azure Portalon látható. PowerShell-lel a visszaállítási művelet sikeres, mert csak a lemezek visszaállítása, és nem hoz létre a virtuális Gépet. A hiba lesz tapasztalható, a virtuális gép létrehozásakor explicit módon, a lemez a visszaállítási művelet után. |
| A megadott virtuális hálózati konfiguráció nem megfelelő: <br>Adjon meg egy másik virtuális hálózati konfigurációt, és próbálkozzon újra. |None |
| A megadott felhőszolgáltatás fenntartott IP-cím, amely nem felel meg a visszaállítandó virtuális gép konfigurációját használja: <br>Adjon meg egy másik felhőalapú szolgáltatás, amely egy fenntartott IP-címet nem használja. Vagy válasszon egy másik helyreállítási pontot a visszaállításhoz. |None |
| A felhőszolgáltatás elérte a bemeneti végpontok számának korlátját: <br>Adjon meg egy másik felhőszolgáltatást vagy egy meglévő végpontot, majd próbálja megismételni a műveletet. |None |
| A Recovery Services tár és a céloldali tárfiók két különböző régióban vannak: <br>Ellenőrizze, hogy a visszaállítási műveletben megadott tárfiók ugyanabban a régióban az Azure és a Recovery Services-tárolónak. |None |
| A visszaállítási művelethez megadott tárfiók nem támogatott: <br>Csak alapszintű vagy standard szintű storage-fiókok a helyileg redundáns vagy georedundáns tárolást replikációs beállítások támogatottak. Válasszon egy támogatott tárfiókot. |None |
| A visszaállítási művelethez megadott tárfiók típusa nem online: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott tárfiók online. |Ez a hiba akkor fordulhat elő, az Azure Storage-ban egy átmeneti hiba miatt, vagy leállás miatt. Válasszon ki egy másik tárfiókot. |
| Elérte az erőforráscsoport-kvótát: <br>Töröljön néhány erőforráscsoportot az Azure Portalról, vagy lépjen kapcsolatba az Azure ügyfélszolgálatától a határértékek növelését. |None |
| A kijelölt alhálózat nem létezik: <br>Válassza ki egy létező alhálózatot. |None |
| A biztonsági mentési szolgáltatás nem rendelkezik hozzáférési erőforrást az előfizetésében. |Ez a hiba elhárításához a lépéseket követve először állítsa vissza a lemezeket [készül lemezek visszaállítása](backup-azure-arm-restore-vms.md#restore-disks). Ezt követően használja a PowerShell-ben lépéseket [hozzon létre egy virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Biztonsági mentési vagy visszaállítási időt vesz igénybe
Ha a biztonsági mentés több mint 12 óra alatt, vagy a helyreállítás 6 óránál több vesz igénybe, tekintse át a [ajánlott eljárások](backup-azure-vms-introduction.md#best-practices) és [teljesítménnyel kapcsolatos szempontok](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>A Virtuálisgép-ügynök
### <a name="set-up-the-vm-agent"></a>A Virtuálisgép-ügynök telepítése
Általában a Virtuálisgép-ügynök már szerepel az Azure katalógusból létrehozott virtuális gépeket. De a helyszíni adatközpontokból áttelepített virtuális gépeket a Virtuálisgép-ügynök nem lesz. A virtuális gépeken a virtuális gép ügynökét explicit módon kell telepíteni.

#### <a name="windows-vms"></a>Windows rendszerű virtuális gépek

* Töltse le és telepítse az [ügynök MSI-t](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* A klasszikus üzemi modell használatával létrehozott virtuális gépek [frissítse a virtuális gép tulajdonságát](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) jelzi, hogy az ügynök telepítve van-e. Ez a lépés nem szükséges az Azure Resource Manager virtuális gépeken.

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

* Az ügynök legújabb verziójának telepítése a terjesztésipont-adattárból. A csomag nevét a részletekért lásd: a [Linux-ügynök tárház](https://github.com/Azure/WALinuxAgent).
* A klasszikus üzemi modell használatával létrehozott virtuális gépek [használni erre a blogra](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) frissítse a virtuális gép tulajdonságát, és győződjön meg arról, hogy az ügynök telepítve van-e. Ez a lépés nem szükséges a Resource Manager virtuális gépeken.

### <a name="update-the-vm-agent"></a>A Virtuálisgép-ügynök frissítése
#### <a name="windows-vms"></a>Windows rendszerű virtuális gépek

* A Virtuálisgép-ügynök frissítéséhez újra kell telepítenie a [Virtuálisgép-ügynök bináris fájljainak](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Az ügynök frissítése, előtt ellenőrizze, nincs biztonsági mentési műveleteket a rendszer a Virtuálisgép-ügynök frissítése közben.

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

* A Linux rendszerű Virtuálisgép-ügynök frissítéséhez kövesse a cikkben lévő utasítások [Linuxos Virtuálisgép-ügynök frissítése](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Mindig a terjesztésipont-tárház használatával frissítse az ügynököt.

    Az ügynökkód ne töltse le a Githubról. Ha a legújabb ügynök nem érhető el a terjesztés, lépjen kapcsolatba a beszerezni a legújabb ügynököt utasításokat terjesztési támogatása. Ellenőrizheti a legújabb [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) a GitHub-adattárban lévő információkat.

### <a name="validate-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése

Windows virtuális gépeken a Virtuálisgép-ügynök verziójának ellenőrzése:

1. Jelentkezzen be az Azure virtuális gépen, és lépjen abba a mappába **C:\WindowsAzure\Packages**. Keresse meg a **WaAppAgent.exe** fájlt.
2. Kattintson jobb gombbal a fájlra, majd **tulajdonságok**. Válassza ki a **részletek** fülre. A **termékverzió** mező lehet 2.6.1198.718 vagy újabb verziója.

## <a name="troubleshoot-vm-snapshot-issues"></a>Virtuális gép pillanatfelvételeivel kapcsolatos problémák elhárítása
Virtuális gép biztonsági mentése a pillanatkép-parancsok kiadása az alapul szolgáló tárolóról támaszkodik. Nem férhető hozzá storage vagy az késleltetések a pillanatkép-feladat futtatása okozhat a biztonsági mentési feladat sikertelen lesz. Az alábbi okok miatt jöhet pillanatkép-feladat sikertelensége:

- **Tárhely hálózati elérhetőségét le van tiltva, az NSG-t**. További információ a [hálózati hozzáférés létrehozása](backup-azure-arm-vms-prepare.md#establish-network-connectivity) Storage vagy az IP-címek vagy egy proxykiszolgálón keresztül engedélyezési használatával.
- **Az SQL Server biztonsági másolat konfigurált virtuális gépek okozhat a pillanatkép-feladat késleltetése**. Alapértelmezés szerint a virtuális gép biztonsági mentésének létrehoz egy VSS teljes biztonsági mentés Windows virtuális gépeken. Az SQL Server biztonsági mentés konfigurálva, SQL Servert futtató virtuális gépek pillanatkép késések tapasztalhatnak. Pillanatkép késések a biztonsági mentési hibákhoz vezethet, ha állítsa be a következő beállításkulcsot:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Virtuális gép állapota helytelenül van jelentenek, mivel a virtuális gép leállításakor az RDP**. Ha a távoli asztal a virtuális gép leállítása, győződjön meg arról, hogy helyesen szerepel-e a virtuális gép állapota a portálon. Az állapota nem megfelelő, ha a **leállítási** a virtuális gép leállítása a beállítás a virtuális gép portal irányítópultján.
- **Ha több mint négy virtuális gépek megosztás ugyanazon a felhőszolgáltatáson, elosztva a virtuális gépek több biztonsági mentési szabályzatok**. A biztonsági mentési szinkronizálások idejének eltolása, így nincs négynél több virtuális gép biztonsági mentései kezdenek egyszerre. Próbálja meg külön a kezdési idejének legalább egy órával azokban a házirendekben.
- **A virtuális gép fut, a magas CPU és memória**. Ha a virtuális gép fut magas memória és CPU-használat, több mint 90 %-os, a pillanatkép-feladat várólistára és késleltetett. Végül azt az időkorlátot. Ha a probléma történik, próbálja meg egy igény szerinti biztonsági mentést.

## <a name="networking"></a>Hálózat
Például az összes bővítmény Backup bővítmény működjön a nyilvános interneten hozzá kell férniük. Nem férhető hozzá a nyilvános interneten is manifest maga különböző módokon:

* A bővítmény telepítése sikertelen lehet.
* Biztonsági mentési műveleteket, például a lemez-pillanatképből sikertelen lehet.
* A biztonsági mentési művelet állapotának megjelenítésével sikertelen lehet.

Nyilvános internetes címeket feloldani a következő cikkben [Azure-támogatási blog](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Ellenőrizze a virtuális hálózat DNS-konfigurációját, és ellenőrizze, hogy az URI-azonosítókat Azure feloldható.

Miután névfeloldás megfelelően megtörtént, az Azure IP-címek elérését is kell megadni. Az Azure-infrastruktúra való hozzáférés feloldása, kövesse az alábbi lépéseket:

- Az Azure-adatközpont engedélyezett IP-címtartományok:
   1. Listájának [Azure datacenter IP-címek](https://www.microsoft.com/download/details.aspx?id=41653) az engedélyezéshez.
   1. Az IP-címek feloldása a használatával a [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) parancsmagot. Ez a parancsmag az Azure virtuális Gépen belül futtassa egy emelt szintű PowerShell-ablakban. Futtassa rendszergazdaként.
   1. Adja hozzá a szabályokat az NSG-hez, ha több helyen, az IP-címek hozzáférésének engedélyezéséhez.
- A HTTP-forgalom áramlását elérési utat hoz létre:
   1. Bizonyos hálózati korlátozás van érvényben, a forgalom a HTTP-proxykiszolgáló központi telepítését. Ilyen például, egy hálózati biztonsági csoportot. Tekintse meg a lépéseket a HTTP-proxykiszolgáló üzembe helyezéséhez [hálózati kapcsolatot](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Ha több helyen, a HTTP-proxy az internetes hozzáférést adhat szabályokat az NSG-t.

> [!NOTE]
> DHCP engedélyezve kell lennie a Vendég működéséhez az IaaS virtuális gépek biztonsági mentését. Statikus magánhálózati IP-cím van szüksége, ha a beállítást az Azure Portalon vagy a Powershellen keresztül. Győződjön meg arról, hogy a virtuális gép a DHCP-beállítás engedélyezve van.
> További információ a Powershellen keresztül statikus IP-cím beállítása:
> - [Egy meglévő virtuális gép statikus belső IP-cím hozzáadása](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [A kiosztási módszert a hozzárendelt hálózati adapter magánhálózati IP-cím](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
