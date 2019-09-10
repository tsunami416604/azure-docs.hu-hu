---
title: Az Azure Virtual Machines szolgáltatással kapcsolatos biztonsági mentési hibák elhárítása
description: Azure-beli virtuális gépek biztonsági mentésének és visszaállításának hibája
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 1b3d02d5cfdae2f196f2f35f075dd8c250b5ece1
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860343"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Biztonsági mentési hibák elhárítása Azure-beli virtuális gépeken

A Azure Backup használata során észlelt hibák elhárítása az alábbi információkkal végezhető el:

## <a name="backup"></a>Tartalék

Ez a szakasz az Azure-beli virtuális gép biztonsági mentési műveletének hibáját ismerteti.

### <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

* Győződjön meg arról, hogy a VM-ügynök (WA-ügynök) a [legújabb verzió](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Győződjön meg arról, hogy a Windows vagy Linux rendszerű virtuális gép operációsrendszer-verziója támogatott, tekintse meg a [IaaS virtuális gép biztonsági mentésének támogatási mátrixát](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Ellenőrizze, hogy egy másik biztonsági mentési szolgáltatás nem fut-e.
   * Annak érdekében, hogy a pillanatképek kiterjesztésével kapcsolatos hibák ne legyenek elérhetők, [távolítsa el a bővítményeket a kényszerített újratöltéshez](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load)
* Győződjön meg arról, hogy a virtuális gép rendelkezik internetkapcsolattal.
   * Győződjön meg arról, hogy egy másik biztonsági mentési szolgáltatás nem fut.
* A verzióban ellenőrizze ,hogyfut-eaWindowsAzureGuestAgentügynök`Services.msc`szolgáltatás. Ha a **Windows Azure Guest Agent ügynök** szolgáltatás hiányzik, telepítse az [Azure-beli virtuális gépek biztonsági mentése egy Recovery Services-](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)tárolóban.
* Az **Eseménynapló** tartalmazhat olyan biztonsági mentési hibákat, amelyek más biztonsági mentési termékekből származnak, például a Windows Server biztonsági másolatból, és nem az Azure Backup miatt. A következő lépések végrehajtásával megállapíthatja, hogy a probléma Azure Backup-e:
   * Ha hiba történt egy bejegyzés **biztonsági mentésével** kapcsolatban az eseményforrás vagy az üzenet esetében, ellenőrizze, hogy az Azure IaaS virtuális gépek biztonsági másolatai sikeresek voltak-e, és hogy egy visszaállítási pont lett-e létrehozva a kívánt pillanatkép-típussal.
    * Ha Azure Backup működik, akkor a probléma valószínűleg egy másik biztonsági mentési megoldás.
    * Íme egy példa egy eseménynapló-hibára, amelyben az Azure Backup megfelelően működik, de a "Windows Server biztonsági másolat" sikertelen volt:<br>
    ![Windows Server biztonsági másolat sikertelen](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
    * Ha Azure Backup sikertelen, akkor keresse meg a megfelelő hibakódot a jelen cikkben található általános virtuális gép biztonsági mentési hibái című szakaszban.

## <a name="common-issues"></a>Gyakori problémák

Az alábbiakban az Azure-beli virtuális gépek biztonsági mentési hibáival kapcsolatos gyakori problémákat ismertetjük.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – a tárból érkező biztonsági másolatok adatainak másolása időtúllépéssel leállt

Hibakód: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Hibaüzenet: A biztonsági másolatból származó adatok másolása a tárból időtúllépéssel leállt

Ez az átmeneti tárolási hibák vagy a nem elegendő Storage-fiók IOPS miatt fordulhat elő a Backup szolgáltatás számára az időtúllépési időszakon belül az adatoknak a tárolóba történő átviteléhez. Konfigurálja a virtuális gépek biztonsági mentését az [ajánlott eljárásokkal](backup-azure-vms-introduction.md#best-practices) , majd próbálja megismételni a biztonsági mentési műveletet.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – a virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket.

Hibakód: UserErrorVmNotInDesirableState <br/>
Hibaüzenet: A virtuális gép állapota nem teszi lehetővé a biztonsági mentés végrehajtását.<br/>

A biztonsági mentési művelet meghiúsult, mert a virtuális gép hibás állapotban van. A sikeres biztonsági mentéshez a virtuális gép állapotának futnia, leállítottnak vagy leállítottnak kell lennie (lefoglalva).

* Ha a virtuális gép átmeneti állapotban van a **Futtatás** és a **Leállítás**között, várjon, amíg az állapot megváltozhat. Ezután aktiválja a biztonsági mentési feladatot.
* Ha a virtuális gép Linux rendszerű virtuális gép, és a fokozott biztonságú Linux kernel-modult használja, zárja ki az Azure Linux-ügynök elérési útját a biztonsági szabályzatból, és győződjön meg arról, hogy a biztonsági **/var/lib/waagent** telepítve van.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – nem sikerült befagyasztani a virtuális gép egy vagy több csatlakoztatási pontját, hogy egy fájlrendszerrel konzisztens pillanatképet készítsen

Hibakód: UserErrorFsFreezeFailed <br/>
Hibaüzenet: Nem sikerült a virtuális gép egy vagy több rögzítési pontjának rögzítése a fájlrendszerrel konzisztens pillanatkép készítéséhez.

* A **tune2fs** parancs használatával vizsgálja meg az összes csatlakoztatott eszköz fájlrendszerbeli állapotát, például **tune2fs-l \\/dev/sdb1** .\| grep **fájlrendszer állapota**.
* A **umount** parancs használatával válassza le azokat az eszközöket, amelyek esetében a fájlrendszer állapota nem lett megtisztítva.
* Futtasson fájlrendszer-konzisztencia-ellenőrzéseket ezeken az eszközökön a **fsck** parancs használatával.
* Csatlakoztassa újra az eszközöket, és próbálkozzon újra a biztonsági mentési művelettel.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC – a bővítmény telepítése/végrehajtása COM+ hiba miatt meghiúsult

Hibakód: ExtensionSnapshotFailedCOM <br/>
Hibaüzenet: COM+ hiba miatt nem sikerült a pillanatkép-művelet

Hibakód: ExtensionInstallationFailedCOM  <br/>
Hibaüzenet: COM+ hiba miatt nem sikerült telepíteni a bővítményt/műveletet

Hibakód: ExtensionInstallationFailedMDTC <br/>
Hibaüzenet: A bővítmény telepítése a következő hibával meghiúsult: "a COM+ nem tudott kommunikálni a Microsoft Elosztott tranzakciók koordinátora <br/>

A biztonsági mentési művelet a Windows Service **com+** rendszeralkalmazás hibája miatt meghiúsult.  A probléma megoldásához kövesse az alábbi lépéseket:

* Próbálja meg elindítani vagy újraindítani a Windows Service **com+ Rendszeralkalmazást** (egy rendszergazda jogú parancssorból **– net start COMSysApp**).
* Győződjön meg arról, **Elosztott tranzakciók koordinátora** szolgáltatások **hálózati szolgáltatás** fiókként futnak. Ha nem, módosítsa a futtató **hálózati szolgáltatás** fiókját, és indítsa újra a **com+ rendszeralkalmazást**.
* Ha a szolgáltatás nem indítható újra, akkor a következő lépések végrehajtásával telepítse újra **Elosztott tranzakciók koordinátora** szolgáltatást:
    * Állítsa le az MSDTC szolgáltatást
    * Nyisson meg egy parancssort (cmd)
    * Az "MSDTC-uninstall" parancs futtatása
    * un parancs "MSDTC-install"
    * Indítsa el az MSDTC szolgáltatást
* Indítsa el a Windows Service **com+ Rendszeralkalmazást**. A **com+ rendszeralkalmazás** elindítása után indítson el egy biztonsági mentési feladatot a Azure Portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – a pillanatkép-készítési művelet nem sikerült, mert a VSS-írók rossz állapotban voltak

Hibakód: ExtensionFailedVssWriterInBadState <br/>
Hibaüzenet: A pillanatkép-művelet nem sikerült, mert a VSS-írók rossz állapotban voltak.

Indítsa újra a VSS-írókat, amelyek helytelen állapotban vannak. Egy rendszergazda jogú parancssorból futtassa ```vssadmin list writers```a parancsot. A kimenet tartalmazza az összes VSS-írót és azok állapotát. Minden olyan VSS-író esetében, amely nem **[1] stabil**, a VSS-író újraindításához futtassa a következő parancsokat egy emelt szintű parancssorból:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – hiba történt a biztonsági mentési bővítmény konfigurációjának elemzésekor

Hibakód: ExtensionConfigParsingFailure<br/>
Hibaüzenet: Hiba történt a biztonsági másolat bővítményének konfigurációelemzése során.

Ez a hiba a **következő** könyvtárának módosított engedélyei miatt fordul elő: **%SYSTEMDRIVE%\programdata\microsoft\crypto\rsa\machinekeys**.
Futtassa az alábbi parancsot, és ellenőrizze, hogy az **következő** könyvtár engedélyei alapértelmezettek-e:**icacls%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Az alapértelmezett engedélyek a következők:
* Mindenki (R, W)
* Builtin\rendszergazda F

Ha a **következő** címtárban az alapértelmezetttől eltérő engedélyek jelennek meg, kövesse az alábbi lépéseket az engedélyek megadásához, a tanúsítvány törléséhez és a biztonsági mentés elindításához:

1. Javítsa az engedélyeket a **következő** könyvtárában. Az Explorer biztonsági tulajdonságainak és a speciális biztonsági beállításoknak a címtárban való használatával állítsa vissza az engedélyeket az alapértelmezett értékekre. Távolítsa el az összes felhasználói objektumot, kivéve az alapértelmezett beállításokat a címtárból, és győződjön meg arról, hogy a **mindenki** engedély speciális hozzáféréssel rendelkezik a következő módon:

    * Mappa listázása/az adatolvasás
    * Attribútumok olvasása
    * Kiterjesztett attribútumok olvasása
    * Fájlok létrehozása/az adatírás
    * Mappák létrehozása/az adathozzáfűzés
    * Attribútumok írása
    * Kiterjesztett attribútumok írása
    * Olvasási engedélyek
2. Törölje az összes olyan tanúsítványt, ahol a **kiadás** a klasszikus üzemi modell vagy a **Windows Azure-beli CRP-tanúsítvány generátora**:
    * [Nyisson meg tanúsítványokat egy helyi számítógép-konzolon](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * A **személyes** > **tanúsítványok**területen törölje az összes olyan tanúsítványt, amely **számára a kiadás** a klasszikus üzemi modell vagy a **Windows Azure CRP-tanúsítvány generátora**.
3. Virtuális gép biztonsági mentési feladatainak elindítása.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>A ExtensionStuckInDeletionState-bővítmény állapota nem támogatja a biztonsági mentési műveletet

Hibakód: ExtensionStuckInDeletionState <br/>
Hibaüzenet: A bővítmény állapota nem támogatja a biztonsági mentési műveletet

A biztonsági mentési művelet nem sikerült, mert inkonzisztens állapotú a biztonsági mentési bővítmény. A probléma megoldásához kövesse az alábbi lépéseket:

* Ellenőrizze, hogy a vendégügynök telepítve van-e és működik-e
* Az Azure Portalon lépjen a **Virtuális gép** > **Minden beállítás** > **Bővítmények** elemhez
* Válassza ki a VmSnapshot vagy a VmSnapshotLinux nevű biztonsági mentési bővítményt, és kattintson az **Eltávolítás** elemre
* A biztonsági mentési bővítmény törlése után próbálkozzon meg újra a biztonsági mentéssel
* Ez a biztonsági mentési művelet a kívánt állapotban fogja telepíteni az új bővítményt

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError – a pillanatkép-készítési művelet nem sikerült, mert a csatolt lemezek némelyike túllépte a pillanatkép-korlátot

Hibakód: ExtensionFailedSnapshotLimitReachedError  <br/>
Hibaüzenet: A pillanatkép-művelet nem sikerült, mert a csatolt lemezek némelyike túllépte a pillanatkép-korlátot

A pillanatkép-művelet meghiúsult, mert a csatolt lemezek némelyike túllépte a pillanatkép-korlátot. Végezze el az alábbi hibaelhárítási lépéseket, majd próbálja megismételni a műveletet.

* Törölje a nem szükséges lemezes blob-pillanatképeket. Legyen óvatos a lemez blobjának törléséhez, csak a pillanatkép-blobokat kell törölni.
* Ha a Soft-delete engedélyezve van a virtuális gép lemezének Storage-fiókjaiban, konfigurálja a helyreállítható törlési adatmegőrzést úgy, hogy a meglévő Pillanatképek kevesebbek legyenek, mint a maximálisan megengedett idő.
* Ha Azure Site Recovery engedélyezve van a biztonsági másolatban szereplő virtuális gépen, hajtsa végre az alábbi műveleteket:

    * Győződjön meg arról, hogy a **isanysnapshotfailed** értéke hamis a/etc/Azure/vmbackup.conf
    * Az ütemezett Azure Site Recovery eltérő időpontban, például nem ütköznek a biztonsági mentési művelettel.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive – a nem megfelelő virtuálisgép-erőforrások miatt sikertelen volt a pillanatkép-készítési művelet.

Hibakód: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Hibaüzenet: A nem megfelelő virtuálisgép-erőforrások miatt nem sikerült a pillanatkép-művelet.

A virtuális gépen a biztonsági mentési művelet sikertelen volt, mert a hálózati hívások késése a pillanatkép-művelet végrehajtása közben történt. A probléma megoldásához hajtsa végre az 1. lépést. Ha a probléma továbbra is fennáll, próbálkozzon a 2. és a 3. lépéssel.

**1. lépés**: Pillanatkép létrehozása a gazdagépen keresztül

Egy emelt szintű (rendszergazdai) parancssorból futtassa a következő parancsot:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Ez gondoskodik róla, hogy a pillanatképek a gazdagépen keresztül készüljenek a vendég helyett. Próbálkozzon újra a biztonsági mentési művelettel.

**2. lépés**: Próbálja meg módosítani a biztonsági mentési ütemtervet olyan időpontra, amikor a virtuális gép kevesebb terhelés alatt van (kevesebb CPU/IOps stb.)

**3. lépés**: Próbálja meg [növelni a virtuális gép méretét](https://azure.microsoft.com/blog/resize-virtual-machines/) , és ismételje meg a műveletet.

## <a name="common-vm-backup-errors"></a>Gyakori virtuális gépek biztonsági mentésével kapcsolatos hibák

| Hibaüzenet részletei | Áthidaló megoldás |
| ------ | --- |
| **Hibakód**: 320001<br/> **Hibaüzenet**: Nem sikerült elvégezni a műveletet, mert a virtuális gép már nem létezik. <br/> <br/> **Hibakód**: 400094 <br/> **Hibaüzenet**: A virtuális gép nem létezik <br/> <br/>  Nem található Azure-beli virtuális gép.  |Ez a hiba akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is a virtuális gép biztonsági mentését keresi. A hiba elhárításához hajtsa végre a következő lépéseket: <ol><li> Hozza létre újra a virtuális gépet ugyanazzal a névvel és ugyanazzal az erőforráscsoport-névvel, a **Cloud Service-névvel**,<br>**or**</li><li> Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül vagy anélkül. További információ: a [virtuális gépek védelmének leállítása](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| A virtuális gép sikertelen kiépítési állapotban van: <br>Indítsa újra a virtuális gépet, és győződjön meg arról, hogy a virtuális gép fut vagy leáll. | Ez a hiba akkor fordul elő, ha a bővítmények egyike sikertelen kiépítési állapotba helyezi a virtuális gépet. Lépjen a bővítmények listára, ellenőrizze, hogy van-e sikertelen bővítmény, távolítsa el, majd próbálja meg újraindítani a virtuális gépet. Ha az összes bővítmény fut állapotban van, ellenőrizze, hogy fut-e a virtuálisgép-ügynök szolgáltatás. Ha nem, indítsa újra a VM Agent szolgáltatást. |
|**Hibakód**: UserErrorBCMPremiumStorageQuotaError<br/> **Hibaüzenet**: Nem lehet másolni a virtuális gép pillanatképét, mert nincs elég szabad hely a Storage-fiókban. | A virtuális gépek biztonsági mentési verem v1-es verziójának prémium szintű virtuális gépei esetén a pillanatképet a Storage-fiókba másolja. Ez a lépés gondoskodik arról, hogy a biztonsági mentési felügyeleti forgalom, amely a pillanatképen működik, nem korlátozza az alkalmazás számára elérhető IOPS számát a prémium szintű lemezek használatával. <br><br>Javasoljuk, hogy a teljes Storage-fiók területének 50%-os, 17,5 TB-os számát foglalja le. Ezután a Azure Backup szolgáltatás átmásolhatja a pillanatképet a Storage-fiókba, és átviheti az adatait a Storage-fiókból a tárolóba. |
| **Hibakód: 380008** <br/> **Hibaüzenet**: Nem sikerült telepíteni a Microsoft Recovery Services-bővítményt, mert a virtuális gép nem fut | A VM-ügynök az Azure Recovery Services bővítmény előfeltétele. Telepítse az Azure Virtual Machine Agent ügynököt, és indítsa újra a regisztrációs műveletet. <br> <ol> <li>Ellenőrizze, hogy a virtuális gép ügynöke megfelelően van-e telepítve. <li>Győződjön meg arról, hogy a virtuális gép konfigurációjának jelzője helyesen van beállítva.</ol> További információ a virtuálisgép-ügynök telepítéséről és a virtuálisgép-ügynök telepítésének ellenőrzéséről. |
| **Hibakód**: ExtensionSnapshotBitlockerError <br/> **Hibaüzenet**: A pillanatkép-művelet a Kötet árnyékmásolata szolgáltatás (VSS) művelet hibája **miatt sikertelen volt, mert az BitLocker meghajtótitkosítás zárolta a meghajtót. A meghajtót fel kell oldani a Vezérlőpultról.** |Kapcsolja ki a BitLockert a virtuális gépen lévő összes meghajtón, és ellenőrizze, hogy megoldódott-e a VSS-probléma. |
| **Hibakód**: VmNotInDesirableState <br/> **Hibaüzenet**:  A virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket. |<ul><li>Ha a virtuális gép átmeneti állapotban van a **Futtatás** és a **Leállítás**között, várjon, amíg az állapot megváltozhat. Ezután aktiválja a biztonsági mentési feladatot. <li> Ha a virtuális gép Linux rendszerű virtuális gép, és a fokozott biztonságú Linux kernel-modult használja, zárja ki az Azure Linux-ügynök elérési útját a biztonsági szabályzatból, és győződjön meg arról, hogy a biztonsági **/var/lib/waagent** telepítve van.  |
| A virtuálisgép-ügynök nincs jelen a virtuális gépen: <br>Telepítse az előfeltételt és a virtuálisgép-ügynököt. Ezután indítsa újra a műveletet. |További információ a [VM-ügynök telepítéséről és a virtuálisgép-ügynök telepítésének ellenőrzéséről](#vm-agent). |
| **Hibakód**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Hibaüzenet**: A pillanatkép-művelet sikertelen volt, mert nem sikerült létrehozni a biztonságos hálózati kommunikációs csatornát. | <ol><li> Nyissa meg a Beállításszerkesztőt a **Regedit. exe** futtatásával emelt szintű módban. <li> Azonosítsa a rendszeren lévő .NET-keretrendszer összes verzióját. A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**beállításkulcs hierarchiájában jelennek meg. <li> A beállításkulcsban található minden egyes .NET-keretrendszerhez adja hozzá a következő kulcsot: <br> **Alatt "= DWORD: 00000001**. </ol>|
| **Hibakód**: ExtensionVCRedistInstallationFailure <br/> **Hibaüzenet**: A pillanatkép-művelet sikertelen volt, mert nem sikerült C++ telepíteni a visual Studio 2012-hoz készült Visual terjeszthető változatot. | Navigáljon a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion, és telepítse a vcredist2013_x64.<br/>Győződjön meg arról, hogy a beállításjegyzék-kulcs értéke, amely lehetővé teszi, hogy a szolgáltatás telepítése a megfelelő értékre legyen állítva. Ez azt eredményezi, **hogy a** **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** értéke **3** , nem pedig **4**. <br><br>Ha továbbra is problémái vannak a telepítéssel, indítsa újra a telepítési szolgáltatást az msiexec **/UNREGISTER** , majd az **msiexec/Register** egy rendszergazda jogú parancssorból való futtatásával.  |


## <a name="jobs"></a>Feladatok

| Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- |
| Ez a feladattípus nem támogatja a megszakítást: <br>Várjon, amíg a feladatok befejeződik. |Nincsenek |
| A feladat nem törölhető állapotban van: <br>Várjon, amíg a feladatok befejeződik. <br>**or**<br> A kijelölt feladat nem törölhető állapotban van: <br>Várjon, amíg a feladatok befejeződik. |Valószínű, hogy a feladatot majdnem befejezték. Várjon, amíg a feladatok befejeződik.|
| A biztonsági mentés nem tudja megszakítani a feladatot, mert nincs folyamatban: <br>A megszakítás csak folyamatban lévő feladatok esetén támogatott. Próbálkozzon egy folyamatban lévő feladat megszakításával. |Ez a hiba átmeneti állapot miatt fordul elő. Várjon egy percet, és ismételje meg a megszakítási műveletet. |
| A biztonsági mentés nem tudta megszakítani a feladatot: <br>Várjon, amíg a feladatok befejeződik. |Nincsenek |

## <a name="restore"></a>Visszaállítás

| Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- |
| A Restore művelet belső felhőalapú hibával meghiúsult. |<ol><li>A felhőalapú szolgáltatás, amelyre a visszaállítást végzi, DNS-beállításokkal van konfigurálva. A következőket tekintheti meg: <br>**$Deployment = Get-AzureDeployment-szolgáltatásnév "szolgáltatásnév"-slot "Production" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Ha a **címe** konfigurálva van, a rendszer konfigurálja a DNS-beállításokat.<br> <li>A felhőalapú szolgáltatás, amelyre a visszaállítást végzi, a **foglalt IP**-sel van konfigurálva, a felhőalapú szolgáltatásban lévő meglévő virtuális gépek pedig leállított állapotban vannak. A következő PowerShell-parancsmagok használatával megtekintheti, hogy a felhőalapú szolgáltatás fenntartott-e egy IP-címet: **$Deployment = Get-AzureDeployment-szolgáltatásnév "szolgáltatásnév"-slot "Production" $DEP. ReservedIPName**. <br><li>Egy virtuális gépet a következő speciális hálózati konfigurációkkal próbál visszaállítani ugyanazon a felhőalapú szolgáltatásban: <ul><li>Virtuális gépek a terheléselosztó konfigurációjában, belső és külső.<li>Több fenntartott IP-címmel rendelkező virtuális gépek. <li>Több hálózati adapterrel rendelkező virtuális gépek. </ul><li>Válasszon egy új felhőalapú szolgáltatást a felhasználói felületen, vagy [](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) tekintse meg a speciális hálózati konfigurációval rendelkező virtuális gépek visszaállítási szempontjait.</ol> |
| A kiválasztott DNS-név már használatban van: <br>Adjon meg másik DNS-nevet, és próbálkozzon újra. |Ez a DNS-név a felhőalapú szolgáltatás nevére hivatkozik, általában a **. cloudapp.net**végződéssel. A névnek egyedinek kell lennie. Ha ezt a hibaüzenetet kapja, a visszaállítás során másik virtuálisgép-nevet kell választania. <br><br> Ez a hiba csak a Azure Portal felhasználói számára jelenik meg. A PowerShell-alapú visszaállítási művelet sikeres, mert csak a lemezeket állítja vissza, és nem hozza létre a virtuális gépet. A hiba akkor jelenik meg, ha a virtuális gépet explicit módon hozza létre a lemez-visszaállítási művelet után. |
| A virtuális hálózat megadott konfigurációja nem megfelelő: <br>Adjon meg másik virtuális hálózati konfigurációt, és próbálkozzon újra. |Nincsenek |
| A megadott felhőalapú szolgáltatás olyan fenntartott IP-címet használ, amely nem felel meg a visszaállítani kívánt virtuális gép konfigurációjának: <br>Olyan felhőalapú szolgáltatást válasszon, amely nem használ fenntartott IP-címet. Vagy válasszon egy másik helyreállítási pontot a visszaállításhoz. |Nincsenek |
| A felhőalapú szolgáltatás elérte a bemeneti végpontok számának korlátját: <br>Próbálja megismételni a műveletet egy másik felhőalapú szolgáltatás megadásával vagy egy meglévő végpont használatával. |Nincsenek |
| A Recovery Services-tár és a célként megadott Storage-fiók két különböző régióban található: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott Storage-fiók ugyanabban az Azure-régióban található, mint a Recovery Services-tároló. |Nincsenek |
| A visszaállítási művelethez megadott Storage-fiók nem támogatott: <br>Csak a helyileg redundáns vagy földrajzilag redundáns replikációs beállításokkal rendelkező alapszintű vagy standard szintű Storage-fiókok támogatottak. Válasszon egy támogatott Storage-fiókot. |Nincsenek |
| A visszaállítási művelethez megadott Storage-fiók típusa nincs online állapotban: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott Storage-fiók online állapotban van. |Ez a hiba az Azure Storage-ban vagy kimaradás miatti átmeneti hiba miatt fordulhat elő. Válasszon másik Storage-fiókot. |
| Elérte az erőforráscsoport-kvótát: <br>Töröljön néhány erőforráscsoportot a Azure Portal, vagy forduljon az Azure támogatási szolgálatához, és növelje a határértékeket. |Nincsenek |
| A kiválasztott alhálózat nem létezik: <br>Válasszon egy létező alhálózatot. |Nincsenek |
| A Backup szolgáltatásnak nincs engedélye az erőforrásokhoz való hozzáférésre az előfizetésében. |A hiba megoldásához először állítsa vissza a lemezeket a [biztonsági másolatba mentett lemezek visszaállítása](backup-azure-arm-restore-vms.md#restore-disks)című cikkben ismertetett lépések segítségével. Ezután használja a [virtuális gép létrehozása helyreállított lemezekről](backup-azure-vms-automation.md#restore-an-azure-vm)című témakör PowerShell-lépéseit. |

## <a name="backup-or-restore-takes-time"></a>A biztonsági mentés vagy a visszaállítás időt vesz igénybe
Ha a biztonsági mentés több mint 12 órát vesz igénybe, vagy a visszaállítás 6 óránál hosszabb ideig tart, tekintse át az [ajánlott eljárásokat](backup-azure-vms-introduction.md#best-practices) és a [teljesítménnyel kapcsolatos szempontokat](backup-azure-vms-introduction.md#backup-performance) .

## <a name="vm-agent"></a>VM-ügynök
### <a name="set-up-the-vm-agent"></a>A virtuálisgép-ügynök beállítása
A virtuálisgép-ügynök általában már jelen van az Azure Galleryből létrehozott virtuális gépeken. A helyszíni adatközpontokból áttelepített virtuális gépek azonban nem lesznek telepítve a virtuálisgép-ügynökkel. Ezekhez a virtuális gépekhez explicit módon telepíteni kell a virtuálisgép-ügynököt.

#### <a name="windows-vms"></a>Windows VMs

* Töltse le és telepítse az [ügynök MSI-t](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* A klasszikus üzemi modellel létrehozott virtuális gépek esetében [frissítse a VM tulajdonságot](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) , hogy jelezze, hogy telepítve van-e az ügynök. Ez a lépés nem szükséges Azure Resource Manager virtuális gépekhez.

#### <a name="linux-vms"></a>Linux VMs

* Telepítse az ügynök legújabb verzióját a terjesztési tárházból. A csomag nevével kapcsolatos részletekért tekintse meg a [Linux-ügynök tárházát](https://github.com/Azure/WALinuxAgent).
* A klasszikus üzemi modellel létrehozott virtuális gépek esetében [ebben a blogban](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) frissítheti a virtuálisgép-tulajdonságot, és ellenőrizheti, hogy az ügynök telepítve van-e. Ez a lépés nem szükséges a Resource Manager virtuális gépekhez.

### <a name="update-the-vm-agent"></a>A virtuálisgép-ügynök frissítése
#### <a name="windows-vms"></a>Windows VMs

* A VM-ügynök frissítéséhez telepítse újra a virtuálisgép- [ügynök bináris fájljait](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Az ügynök frissítése előtt győződjön meg arról, hogy a virtuálisgép-ügynök frissítése során nem történik biztonsági mentési művelet.

#### <a name="linux-vms"></a>Linux VMs

* A Linux rendszerű virtuális gép ügynökének frissítéséhez kövesse a Linux rendszerű [virtuális gép ügynökének frissítése](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című cikk utasításait.

    > [!NOTE]
    > Mindig a terjesztési tárházat használja az ügynök frissítéséhez.

    Ne töltse le az ügynök kódját a GitHubról. Ha a legújabb ügynök nem érhető el a terjesztéshez, forduljon a terjesztési támogatáshoz a legújabb ügynök beolvasására vonatkozó utasításokért. A legújabb [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) adatait is megtekintheti a GitHub-tárházban.

### <a name="validate-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése

Ellenőrizze a virtuálisgép-ügynök verzióját a Windows rendszerű virtuális gépeken:

1. Jelentkezzen be az Azure-beli virtuális gépre, és navigáljon a **C:\WindowsAzure\Packages**mappába. Keresse meg a **WaAppAgent. exe** fájlt.
2. Kattintson a jobb gombbal a fájlra, és válassza a **Tulajdonságok menüpontot**. Ezután válassza a **részletek** lapot. A **termék verzió** mezőjének 2.6.1198.718 vagy magasabbnak kell lennie.

## <a name="troubleshoot-vm-snapshot-issues"></a>VIRTUÁLIS gépek pillanatképével kapcsolatos problémák elhárítása
A virtuális gép biztonsági mentése a pillanatkép-parancsok alapjául szolgáló tárolóra támaszkodik. Ha nem fér hozzá a tárolóhoz, vagy késések vannak a pillanatképek futtatásához, a biztonsági mentési feladat sikertelen lehet. A következő feltételek okozhatnak pillanatkép-feladathoz tartozó hibát:

- A **tárterület hálózati hozzáférése a NSG használatával tiltható**le. További információ arról, hogyan [hozhatók létre hálózati hozzáférés](backup-azure-arm-vms-prepare.md#establish-network-connectivity) a tárolóhoz az IP-címek engedélyezett listája vagy egy proxykiszolgáló használatával.
- **A SQL Server biztonsági mentéssel konfigurált virtuális gépek a pillanatkép-feladatok késleltetését okozhatják**. Alapértelmezés szerint a virtuális gép biztonsági mentése létrehoz egy VSS teljes biztonsági mentést a Windows rendszerű virtuális gépeken. Azok a virtuális gépek, amelyek SQL Server futtatnak SQL Server biztonsági mentést, pillanatkép-késéseket tapasztalhatnak. Ha a pillanatkép-késések biztonsági mentési hibákat okoznak, állítsa be a következő beállításkulcsot:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **A virtuális gép állapota helytelenül van jelezve, mert a virtuális gép RDP-ben le van állítva**. Ha a távoli asztal használatával állítja le a virtuális gépet, ellenőrizze, hogy helyes-e a virtuális gép állapota a portálon. Ha az állapot nem megfelelő, a virtuális gép leállításához használja a portál virtuálisgép-irányítópultjának **Leállítás** lehetőségét.
- **Ha négynél több virtuális gép osztozik ugyanazzal a felhőalapú szolgáltatással, a virtuális gépeket több biztonsági mentési házirendben is elosztja**. A biztonsági mentés időpontjának felosztása, így a több mint négy virtuális gép biztonsági mentése egyidőben megkezdődik. Próbálja meg elkülöníteni a házirendek indítási időpontját legalább egy órával.
- **A virtuális gép magas processzoron vagy memórián fut**. Ha a virtuális gép nagy memórián vagy CPU-használaton fut, több mint 90 százalékkal, a pillanatkép-feladat várólistára kerül és késleltetve lesz. Végül túllépi az időkorlátot. Ha ez a probléma történik, próbálkozzon egy igény szerinti biztonsági mentéssel.

## <a name="networking"></a>Hálózat
Az összes bővítményhez hasonlóan a biztonsági mentési bővítményeknek is hozzá kell férniük a nyilvános internethez. Ha nem fér hozzá a nyilvános internethez, többféleképpen is megnyilvánulhat:

* A bővítmény telepítése sikertelen lehet.
* A biztonsági mentési műveletek, például a lemezes Pillanatképek sikertelenek lehetnek.
* A biztonsági mentési művelet állapotának megjelenítése sikertelen lehet.

A nyilvános internetes címek feloldásának szükségességét ebben az [Azure-támogatási blogban](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)tárgyaljuk. Ellenőrizze a VNET DNS-konfigurációit, és győződjön meg arról, hogy az Azure URI-k oldhatók fel.

A névfeloldás megfelelő elvégzése után az Azure IP-címeihez is hozzáférést kell biztosítani. Az Azure-infrastruktúra elérésének feloldásához kövesse az alábbi lépések egyikét:

- Azure Datacenter IP-címtartományok engedélyezési listájának engedélyezése:
   1. Az engedélyezési listán szereplő [Azure Datacenter IP](https://www.microsoft.com/download/details.aspx?id=41653) -címek listájának beolvasása.
   1. Az IP-címek feloldása a [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) parancsmag használatával. Futtassa ezt a parancsmagot az Azure-beli virtuális gépen egy emelt szintű PowerShell-ablakban. Futtatás rendszergazdaként.
   1. Ha van ilyen, az IP-címek elérésének engedélyezéséhez vegyen fel szabályokat a NSG.
- Hozzon létre egy elérési utat a HTTP-forgalomhoz a flow számára:
   1. Ha bizonyos hálózati korlátozásokkal rendelkezik, helyezzen üzembe egy HTTP-proxykiszolgálót a forgalom továbbításához. Ilyen például egy hálózati biztonsági csoport. Tekintse meg a [hálózati kapcsolat létesítése](backup-azure-arm-vms-prepare.md#establish-network-connectivity)a HTTP-proxykiszolgáló üzembe helyezésének lépéseit.
   1. Ha rendelkezik a NSG, adjon hozzá szabályokat az internethez a HTTP-proxytól való hozzáférés engedélyezéséhez.

> [!NOTE]
> A DHCP-t engedélyezni kell a vendégen a IaaS virtuális gép biztonsági mentésének működéséhez. Ha statikus magánhálózati IP-címmel kell rendelkeznie, konfigurálja a Azure Portal vagy a PowerShell használatával. Győződjön meg arról, hogy a virtuális gépen belül a DHCP-beállítás engedélyezve van.
> További információ arról, hogyan állítható be statikus IP-cím a PowerShell használatával:
> - [Statikus belső IP-cím hozzáadása meglévő virtuális géphez](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Hálózati adapterhez rendelt magánhálózati IP-cím kiosztási módszerének módosítása](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
