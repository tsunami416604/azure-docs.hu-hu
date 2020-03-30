---
title: Biztonsági mentési hibák elhárítása az Azure virtuális gépeivel
description: Ebből a cikkből megtudhatja, hogyan háríthatja el az Azure virtuális gépek biztonsági mentésével és visszaállításával kapcsolatban tapasztalt hibákat.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297390"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Biztonsági mentési hibák elhárítása az Azure virtuális gépeken

Az Azure Backup használata során észlelt hibákat az alábbi információkkal háríthatja el:

## <a name="backup"></a>Backup

Ez a szakasz az Azure virtuális gép biztonsági mentési műveletének hibáját ismerteti.

### <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

* Győződjön meg arról, hogy a virtuálisgép-ügynök (WA Agent) a [legújabb verzió.](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)
* Győződjön meg arról, hogy a Windows vagy Linux VM operációs rendszer verziója támogatott, olvassa el az [IaaS virtuális gép biztonsági mentésének támogatási mátrixát.](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* Ellenőrizze, hogy nem fut-e egy másik biztonsági mentési szolgáltatás.
  * Annak érdekében, hogy ne legyenek pillanatkép-bővítményproblémák, [távolítsa el a bővítményeket az újratöltés kényszerítéséhez, majd próbálkozzon újra a biztonsági másolattal.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout)
* Ellenőrizze, hogy a virtuális gép rendelkezik-e internetkapcsolattal.
  * Győződjön meg arról, hogy egy másik biztonsági mentési szolgáltatás nem fut.
* A `Services.msc`programból győződjön meg arról, hogy a **Windows Azure vendégügynök** **szolgáltatása fut.** Ha a **Windows Azure vendégügynök** szolgáltatás hiányzik, telepítse az [Azure-beli virtuális gépek biztonsági mentéséről egy Recovery Services-tárolóba.](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)
* Az **eseménynapló** ban más biztonsági mentési termékekből, például a Windows Server biztonsági mentéséből származó biztonsági mentési hibák jelenhetnek meg, amelyek nem az Azure biztonsági mentésének köszönhetők. Az alábbi lépésekkel állapíthatja meg, hogy a probléma az Azure Backup szolgáltatással kapcsolatos-e:
  * Ha hiba van egy **bejegyzés biztonsági mentés** az esemény forrásában vagy üzenetben, ellenőrizze, hogy az Azure IaaS virtuális gép biztonsági mentésesikeres volt-e, és hogy a visszaállítási pont jött létre a kívánt pillanatkép-típussal.
  * Ha az Azure Backup működik, majd a probléma valószínűleg egy másik biztonsági mentési megoldás.
  * Íme egy példa egy 517-es eseménynapló-hibára, amelyben az Azure biztonsági mentése jól működött, de a "Windows Server Backup" nem sikerült:<br>
    ![A Windows Server biztonsági másolat hibás](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Ha az Azure Backup meghibásodik, keresse meg a megfelelő hibakódot a cikkben a közös virtuális gépek biztonsági mentési hibái című szakaszban.

## <a name="common-issues"></a>Gyakori problémák

Az alábbiakban az Azure virtuális gépek biztonsági mentési hibáival kapcsolatos gyakori problémák.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - A biztonsági másolat másolása a tárolóból idővel csökkent

Hibakód: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Hibaüzenet: A biztonsági másolat adatainak másolása a tárolóból idővel csökkent

Ez átmeneti tárolási hibák miatt fordulhat elő, vagy nem elegendő a tárfiók IOPS-a biztonsági mentési szolgáltatás adatok átvitele a tárolóba az időbeli eltöltött időn belül. Konfigurálja a virtuális gép biztonsági mentését az [alábbi gyakorlati tanácsok](backup-azure-vms-introduction.md#best-practices) használatával, és próbálja meg újra a biztonsági mentési műveletet.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInKívánatos - A virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket

Hibakód: UserErrorVmNotInDesirableState <br/>
Hibaüzenet: A virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket.<br/>

A biztonsági mentési művelet nem sikerült, mert a virtuális gép sikertelen állapotban van. A sikeres biztonsági mentéshez a virtuális gépnek Fut, Leállítva vagy Leállítva (felszabadítva) állapotúnak kell lennie.

* Ha a virtuális gép átmeneti állapotban van **a Futás** és **a Leállítás**között, várja meg, amíg az állapot megváltozik. Ezután indítsa el a biztonsági mentési feladatot.
* Ha a virtuális gép egy Linux virtuális gép, és a Security-Enhanced Linux kernel modult használja, zárja ki az Azure Linux Ügynök elérési útját **/var/lib/waagent** a biztonsági házirendből, és győződjön meg arról, hogy a biztonsági mentési bővítmény telepítve van.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – Nem sikerült lefagyasztani a virtuális gép egy vagy több csatlakoztatási pontját a fájlrendszer konzisztens pillanatképének készítéséhez

Hibakód: UserErrorFsFreezeFailed <br/>
Hibaüzenet: Nem sikerült lefagyasztani a virtuális gép egy vagy több csatlakoztatási pontját a fájlrendszer konzisztens pillanatképének készítéséhez.

* Az **umount** paranccsal lekell választani azokat az eszközöket, amelyek esetében a fájlrendszer állapota nem lett törölve.
* Futtasson fájlrendszer-konzisztenciaellenőrzést ezeken az eszközökön az **fsck** paranccsal.
* Csatlakoztassa újra az eszközöket, és próbálja meg újra a biztonsági mentési műveletet.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Com+ hiba miatt nem sikerült a kiterjesztés telepítése/művelete

Hibakód: ExtensionSnapshotFailedCOM <br/>
Hibaüzenet: Com+ hiba miatt nem sikerült a pillanatkép-művelet

Hibakód: ExtensionInstallationFailedCOM  <br/>
Hibaüzenet: Com+ hiba miatt nem sikerült a bővítmény telepítése/működése

Hibakód: ExtensionInstallationFailedMDTC <br/>
Hibaüzenet: A bővítmény telepítése nem sikerült a következő hiba miatt: "A COM+ nem tudott beszélni a Microsoft elosztott tranzakciók koordinátorával <br/>

A biztonsági mentési művelet a Windows **COM+ rendszeralkalmazással** kapcsolatos probléma miatt nem sikerült.  A probléma megoldásához kövesse az alábbi lépéseket:

* Próbálja meg elindítani/újraindítani a Windows **com+ rendszeralkalmazást** (emelt szintű parancssorból **- net start COMSysApp**).
* Győződjön meg arról, hogy **az Elosztott tranzakciók koordinátora** szolgáltatás **hálózati szolgáltatásfiókként** fut. Ha nem, módosítsa úgy, hogy **hálózati szolgáltatásfiókként** fusson, és indítsa újra a **COM+ rendszeralkalmazást.**
* Ha nem tudja újraindítani a szolgáltatást, telepítse újra az **Elosztott tranzakciók koordinátora** szolgáltatást az alábbi lépések végrehajtásával:
  * Állítsa le az MSDTC szolgáltatást
  * Nyisson meg egy parancssort (cmd)
  * Az "msdtc -uninstall" parancs futtatása
  * Az "msdtc -install" parancs futtatása
  * Indítsa el az MSDTC szolgáltatást
* Indítsa el a **Windows COM+ rendszeralkalmazását**. A **COM+ rendszeralkalmazás indítása** után indítsa el a biztonsági mentési feladatot az Azure Portalon.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - A pillanatkép-művelet nem sikerült, mert a VSS-írók rossz állapotban voltak

Hibakód: ExtensionFailedVssWriterInBadState <br/>
Hibaüzenet: A pillanatkép-művelet nem sikerült, mert a VSS-írók rossz állapotban voltak.

Indítsa újra a rossz állapotban lévő VSS-írókat. Egy rendszergazda jogú ```vssadmin list writers```parancssorból futtassa a parancsot. A kimenet tartalmazza az összes VSS-írót és azok állapotát. Minden olyan VSS-író esetén, amelynek állapota nem **[1] Stabil**, a VSS-író újraindításához futtassa a következő parancsokat egy rendszergazda jogú parancssorból:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – A biztonsági másolat bővítménykonfigurációjának elemzési sikertelensége

Hibakód: ExtensionConfigParsingFailure<br/>
Hibaüzenet: Nem sikerült elemezni a biztonsági mentési bővítmény konfigurációját.

A hiba a **MachineKeys** könyvtár ban megváltozott engedélyek miatt fordul elő: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Futtassa a következő parancsot, és ellenőrizze, hogy a **MachineKeys** könyvtár engedélyei alapértelmezettek-e:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Az alapértelmezett engedélyek a következők:

* Mindenki: (R, W)
* BUILTIN\Rendszergazdák: (F)

Ha a **MachineKeys** könyvtárban az alapértelmezéstől eltérő engedélyek jelennek meg, kövesse az alábbi lépéseket az engedélyek javításához, a tanúsítvány törléséhez és a biztonsági mentés elindításához:

1. A **MachineKeys** könyvtár engedélyeinek javítása. Az Intéző biztonsági tulajdonságainak és a könyvtár speciális biztonsági beállításainak használatával állítsa vissza az engedélyeket az alapértelmezett értékekre. Távolítsa el az összes felhasználói objektumot, kivéve az alapértelmezett beállításokat a könyvtárból, és győződjön meg arról, hogy a **Mindenki** engedély speciális hozzáféréssel rendelkezik az alábbiak szerint:

   * Mappa listázása/adatok olvasása
   * Attribútumok olvasása
   * Bővített attribútumok olvasása
   * Fájlok létrehozása/adatok írása
   * Mappák/hozzáfűzési adatok létrehozása
   * Attribútumok írása
   * Bővített attribútumok írása
   * Olvasási engedélyek
2. Törölje az összes olyan **tanúsítványt, amelynek célja** a klasszikus telepítési modell vagy a **Windows Azure CRP tanúsítványgenerátor:**

   * [Tanúsítványok megnyitása a helyi számítógép konzolján](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * A **Személyes** > **tanúsítványok csoportban**törölje az összes olyan tanúsítványt, **amelynek célja** a klasszikus telepítési modell vagy a Windows Azure **CRP tanúsítványgenerátora.**
3. Virtuálisgép biztonsági mentési feladat aktiválása.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - A kiterjesztés állapota nem támogatja a biztonsági mentési műveletet

Hibakód: ExtensionStuckInDeletionState <br/>
Hibaüzenet: A bővítmény állapota nem támogatja a biztonsági mentési műveletet

A biztonsági mentési művelet a biztonsági mentési bővítmény inkonzisztens állapota miatt nem sikerült. A probléma megoldásához kövesse az alábbi lépéseket:

* Ellenőrizze, hogy a vendégügynök telepítve van-e és működik-e
* Az Azure Portalon nyissa meg a **Virtuálisgép** > minden**beállításbővítményét.** **All Settings** > 
* Válassza ki a VmSnapshot vagy a VmSnapshotLinux nevű biztonsági mentési bővítményt, és kattintson az **Eltávolítás** elemre
* A biztonsági mentési bővítmény törlése után próbálkozzon újra a biztonsági mentési művelettel
* Ez a biztonsági mentési művelet a kívánt állapotban fogja telepíteni az új bővítményt

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - A pillanatkép-művelet nem sikerült, mert a csatlakoztatott lemezek némelyikéna túllépte a pillanatkép korlátját

Hibakód: ExtensionFailedSnapshotLimitReachedError <br/>
Hibaüzenet: A pillanatkép-művelet nem sikerült, mert a csatolt lemezek némelyikéna túllépte a pillanatképkorlátot

A pillanatkép-művelet nem sikerült, mert a pillanatkép-korlát túllépte a csatlakoztatott lemezek némelyikét. Hajtsa végre az alábbi hibaelhárítási lépéseket, majd próbálkozzon újra a művelettel.

* Törölje a lemezblob-pillanatképek, amelyek nem szükségesek. Legyen óvatos, hogy ne törölje a Lemez blob, csak pillanatkép blobok törölni kell.
* Ha a soft-delete engedélyezve van a virtuális gép lemezen Storage-Accounts, konfigurálja a helyreállítható törlés megőrzési úgy, hogy a meglévő pillanatképek kisebbek, mint a maximálisan engedélyezett bármely időpontban.
* Ha az Azure Site Recovery engedélyezve van a biztonsági mentést végző virtuális gépben, hajtsa végre az alábbi lépéseket:

  * Győződjön meg arról, hogy az **isanysnapshotfailed** értéke hamisként van beállítva az /etc/azure/vmbackup.conf fájlban.
  * Ütemezze az Azure Site Recovery egy másik időpontban, úgy, hogy nem ütközik a biztonsági mentési művelet.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive – A pillanatkép-művelet nem megfelelő virtuálisgép-erőforrások miatt nem sikerült

Hibakód: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Hibaüzenet: A pillanatkép-művelet nem sikerült a nem megfelelő virtuálisgép-erőforrások miatt.

A virtuális gép biztonsági mentési művelete sikertelen volt a hálózati hívások késleltetése miatt a pillanatkép-művelet végrehajtása közben. A probléma megoldásához hajtsa végre az 1. lépést. Ha a probléma továbbra is fennáll, próbálkozzon a 2. és a 3. lépéssel.

**1. lépés:** Pillanatfelvétel létrehozása a gazdagépen keresztül

Egy emelt szintű (rendszergazdai) parancssorból futtassa a következő parancsot:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Ez gondoskodik róla, hogy a pillanatképek a gazdagépen keresztül készüljenek a vendég helyett. Próbálkozzon újra a biztonsági mentési művelettel.

**2. lépés:** Próbálja meg módosítani a biztonsági mentés ütemezését egy olyan időpontra, amikor a virtuális gép kisebb terhelés alatt áll (kevesebb CPU/IOps stb.)

**3. lépés:** Próbálja [meg növelni a virtuális gép méretét,](https://azure.microsoft.com/blog/resize-virtual-machines/) majd próbálja meg újra a műveletet

## <a name="common-vm-backup-errors"></a>Virtuális gép biztonsági mentésének gyakori hibái

| A hiba részletei | Áthidaló megoldás |
| ------ | --- |
| **Hibakód**: 320001, ResourceNotFound <br/> **Hibaüzenet:** Nem lehetett végrehajtani a műveletet, mert a virtuális gép már nem létezik. <br/> <br/> **Hibakód**: 400094, BCMV2VMNemTalálható <br/> **Hibaüzenet**: A virtuális gép nem létezik <br/> <br/>  Egy Azure virtuális gép nem található.  |Ez a hiba akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is keresi a virtuális gép biztonsági mentését. A hiba kijavításához tegye a következő lépéseket: <ol><li> Hozza létre újra a virtuális gépet ugyanazzal a névvel és azonos erőforráscsoportnévvel, **felhőszolgáltatás nevével,**<br>**Vagy**</li><li> A biztonsági mentési adatok törlésével vagy anélkül állítsa le a virtuális gép védelmét. További információ: [Stop protecting virtual machines](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Hibakód**: UserErrorBCMPremiumStorageQuotaError<br/> **Hibaüzenet:** Nem lehetett másolni a virtuális gép pillanatképét, mert nincs elegendő szabad hely a tárfiókban | A virtuális gép biztonsági mentési verem V1 prémium szintű virtuális gépek, a pillanatkép a tárfiókba. Ez a lépés biztosítja, hogy a biztonsági mentési felügyeleti forgalom, amely a pillanatképen működik, nem korlátozza az alkalmazás számára elérhető prémium szintű lemezek száma. <br><br>Azt javasoljuk, hogy a teljes tárfiókterületnek csak 50 százalékát, 17,5 TB-ot rendeljen le. Ezután az Azure Backup szolgáltatás átmásolhatja a pillanatképet a tárfiókba, és adatokat továbbíthat erről a másolt helyről a tárfiókban a tárolóba. |
| **Hibakód**: 380008, AzureVmOffline <br/> **Hibaüzenet**: Nem sikerült telepíteni a Microsoft Recovery Services bővítményt, mivel a virtuális gép nem fut | A virtuálisgép-ügynök az Azure Recovery Services-bővítmény előfeltétele. Telepítse az Azure virtuálisgép-ügynököt, és indítsa újra a regisztrációs műveletet. <br> <ol> <li>Ellenőrizze, hogy a virtuálisgép-ügynök megfelelően van-e telepítve. <li>Győződjön meg arról, hogy a virtuális gép konfigurációjának jelzője megfelelően van beállítva.</ol> További információ a virtuálisgép-ügynök telepítéséről és a virtuálisgép-ügynök telepítésének érvényesítéséről. |
| **Hibakód**: ExtensionSnapshotBitlockerError <br/> **Hibaüzenet**: A pillanatkép-művelet nem sikerült a Kötet árnyékmásolata szolgáltatás (VSS) művelet hibája **hiba: A meghajtót a BitLocker meghajtótitkosítás zárolta. Ezt a meghajtót a Vezérlőpultról kell feloldania.** |Kapcsolja ki a BitLocker szolgáltatást a virtuális gép összes meghajtóján, és ellenőrizze, hogy a VSS-probléma megoldódott-e. |
| **Hibakód**: VmNotInDesirableState <br/> **Hibaüzenet:** A virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket. |<ul><li>Ha a virtuális gép átmeneti állapotban van **a Futás** és **a Leállítás**között, várja meg, amíg az állapot megváltozik. Ezután indítsa el a biztonsági mentési feladatot. <li> Ha a virtuális gép egy Linux virtuális gép, és a Security-Enhanced Linux kernel modult használja, zárja ki az Azure Linux Ügynök elérési útját **/var/lib/waagent** a biztonsági házirendből, és győződjön meg arról, hogy a biztonsági mentési bővítmény telepítve van.  |
| A virtuálisgép-ügynök nem jelenik meg a virtuális gépen: <br>Telepítse az előfeltételet és a virtuális gép ügynökét. Ezután indítsa újra a műveletet. |További információ a [Virtuálisgép-ügynök telepítéséről és a Virtuálisgép-ügynök telepítésének érvényesítéséről.](#vm-agent) |
| **Hibakód**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Hibaüzenet**: A pillanatkép-művelet nem sikerült, mert nem sikerült biztonságos hálózati kommunikációs csatornát létrehozni. | <ol><li> Nyissa meg a Rendszerleíróadatbázis-szerkesztőt úgy, hogy **a regedit.exe fájlt** emelt szintű módban futtatja. <li> Azonosítsa a rendszerben található . A rendszerleíró kulcs hierarchiája alatt vannak jelen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> A beállításkulcsban található minden . <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Hibakód**: ExtensionVCRedistInstallationFailure <br/> **Hibaüzenet**: A pillanatkép-művelet nem sikerült, mert nem sikerült telepíteni a Visual C++ újraterjeszthető visual studio 2012-hez. | Keresse meg a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion webhelyet, és telepítse vcredist2013_x64.<br/>Győződjön meg arról, hogy a szolgáltatás telepítését lehetővé tévő beállításkulcs-érték a megfelelő értékre van állítva. Ez azt jelenti, hogy a **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** **mappában** állítsa a Kezdő értéket **3-ra,** és ne **4-re.** <br><br>Ha továbbra is problémák merülnek fel a telepítéssel kapcsolatban, indítsa újra a telepítési szolgáltatást az **MSIEXEC /UNREGISTER** paranccsal, majd az **MSIEXEC /REGISTER** paranccsal egy rendszergazda jogú parancssorból.  |
| **Hibakód**: UserErrorRequestDisallowedByPolicy <BR> **Hibaüzenet:** Érvénytelen házirend van konfigurálva a virtuális gépen, amely megakadályozza a pillanatkép-műveletet. | Ha olyan Azure-szabályzattal [rendelkezik, amely szabályozza a címkéket a környezetében,](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)fontolja meg a szabályzat [megtagadása effektusról](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) [módosításra](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)való módosítását, vagy hozza létre manuálisan az erőforráscsoportot az [Azure Backup által igényelt elnevezési sémának](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines)megfelelően.
## <a name="jobs"></a>Feladatok

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| A lemondás nem támogatott ennél a feladattípusban: <br>Várjon, amíg a munka befejeződik. |None |
| A feladat nincs törölhető állapotban: <br>Várjon, amíg a munka befejeződik. <br>**Vagy**<br> A kijelölt feladat nincs törölhető állapotban: <br>Várd meg, amíg a munka befejeződik. |Valószínű, hogy a munka majdnem kész. Várjon, amíg a feladat befejeződik.|
| A biztonsági mentés nem szakíthatja meg a feladatot, mert nincs folyamatban: <br>A törlés csak a folyamatban lévő feladatok esetében támogatott. Próbáljon meg megszakítani egy folyamatban lévő feladatot. |Ez a hiba átmeneti állapot miatt történik. Várjon egy percet, majd próbálkozzon újra a megszakítási művelettel. |
| A biztonsági mentés nem tudta megszakítani a feladatot: <br>Várjon, amíg a munka befejeződik. |None |

## <a name="restore"></a>Visszaállítás

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| A visszaállítás nem sikerült egy belső felhőbeli hibával. |<ol><li>Az a felhőszolgáltatás, amelyre vissza szeretne állítani, DNS-beállításokkal van konfigurálva. Ellenőrizheti: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Ha **a Cím** beállítás van beállítva, akkor a DNS-beállítások is be vannak állítva.<br> <li>A felhőszolgáltatás, amelyre vissza szeretne állítani, **reservedIP-vel**van konfigurálva, és a felhőszolgáltatásban meglévő virtuális gépek leállított állapotban vannak. Ellenőrizheti, hogy egy felhőszolgáltatás lefoglalt-e egy IP-címet a következő PowerShell-parancsmagok használatával: **$deployment = Get-AzureDeployment -ServiceName "serviceName" -Slot "Production" $dep. ReservedIPName**. <br><li>A következő speciális hálózati konfigurációkkal rendelkező virtuális gépet próbál visszaállítani ugyanabba a felhőszolgáltatásba: <ul><li>Virtuális gépek terheléselosztó konfiguráció alatt, belső és külső.<li>Virtuális gépek több fenntartott IP-k. <li>Több hálózati adapterrel rendelkező virtuális gépek. </ul><li>Válasszon ki egy új felhőszolgáltatást a felhasználói felületen, vagy tekintse meg a speciális hálózati konfigurációval rendelkező virtuális gépek [visszaállítási szempontjait.](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)</ol> |
| A kiválasztott DNS-név már foglalt: <br>Adjon meg egy másik DNS-nevet, majd próbálkozzon újra. |Ez a DNS-név a felhőszolgáltatás nevére utal, amely általában **.cloudapp.net**végződéssel végződik. Ennek a névnek egyedinek kell lennie. Ha ez a hiba jelenik meg, a visszaállítás során másik virtuális gépnevet kell választania. <br><br> Ez a hiba csak az Azure Portal felhasználói számára jelenik meg. A PowerShell-en keresztüli visszaállítási művelet sikeres, mert csak a lemezeket állítja vissza, és nem hozza létre a virtuális gép. A hiba akkor kerül szembe, amikor a virtuális gép explicit módon hozza létre a lemez-visszaállítási művelet után. |
| A megadott virtuális hálózati konfiguráció nem megfelelő: <br>Adjon meg egy másik virtuális hálózati konfigurációt, majd próbálkozzon újra. |None |
| A megadott felhőszolgáltatás olyan fenntartott IP-címet használ, amely nem felel meg a visszaállítandó virtuális gép konfigurációjának: <br>Adjon meg egy másik felhőszolgáltatást, amely nem használ fenntartott IP-címet. Vagy válasszon egy másik helyreállítási pontot, ahonnan vissza szeretné állítani. |None |
| A felhőszolgáltatás elérte a bemeneti végpontok számára vonatkozó korlátot: <br>Próbálkozzon újra a művelettel egy másik felhőszolgáltatás megadásával vagy egy meglévő végpont használatával. |None |
| A Recovery Services-tároló és a céltárfiók két különböző régióban található: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott tárfiók ugyanabban az Azure-régióban található, mint a Recovery Services-tároló. |None |
| A visszaállítási művelethez megadott tárfiók nem támogatott: <br>Csak a helyiredundáns vagy georedundáns replikációs beállításokkal rendelkező alapszintű vagy standard szintű tárfiókok támogatottak. Válasszon ki egy támogatott tárfiókot. |None |
| A visszaállítási művelethez megadott tárfiók típusa nincs online állapotban: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott tárfiók online állapotban van. |Ez a hiba az Azure Storage-ban átmeneti hiba vagy egy kimaradás miatt fordulhat elő. Válasszon másik tárfiókot. |
| Elérte az erőforráscsoport kvótáját: <br>Töröljön néhány erőforráscsoportot az Azure Portalról, vagy lépjen kapcsolatba az Azure-támogatással a korlátok növeléséhez. |None |
| A kijelölt alhálózat nem létezik: <br>Jelöljön ki egy létező alhálózatot. |None |
| A biztonsági mentési szolgáltatás nem rendelkezik engedéllyel az előfizetés erőforrásainak eléréséhez. |A hiba elhárításához először állítsa vissza a lemezeket a [Biztonsági másolatok visszaállítása című](backup-azure-arm-restore-vms.md#restore-disks)segédprogram lépéseivel. Ezután használja a PowerShell lépéseit [a Virtuális gép létrehozása visszaállított lemezekről](backup-azure-vms-automation.md#restore-an-azure-vm)című részében. |

## <a name="backup-or-restore-takes-time"></a>A biztonsági mentés vagy visszaállítás időt vesz igénybe

Ha a biztonsági mentés több mint 12 órát vesz igénybe, vagy a visszaállítás több mint 6 órát vesz igénybe, tekintse át [az ajánlott eljárásokat](backup-azure-vms-introduction.md#best-practices)és [a teljesítménnyel kapcsolatos szempontokat](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Virtuális gép ügynöke

### <a name="set-up-the-vm-agent"></a>A virtuális gép ügynökének beállítása

Általában a virtuális gép ügynöke már jelen van az Azure-katalógusból létrehozott virtuális gépeken. De a virtuális gépek, amelyek a helyszíni adatközpontoknem lesz telepítve a virtuális gép ügynöke. Ezek a virtuális gépek esetén a virtuálisgép-ügynököt explicit módon kell telepíteni.

#### <a name="windows-vms"></a>Windows rendszerű virtuális gépek

* Töltse le és telepítse az [ügynök MSI-t](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokra van szükség.
* A klasszikus központi telepítési modell használatával létrehozott virtuális gépek esetén [frissítse a virtuális gép tulajdonságot,](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) jelezve, hogy az ügynök telepítve van. Ez a lépés nem szükséges az Azure Resource Manager virtuális gépek.

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

* Telepítse az ügynök legújabb verzióját a terjesztési tárházból. A csomag nevével kapcsolatos részletekért tekintse meg a [Linux Agent tárházát.](https://github.com/Azure/WALinuxAgent)
* A klasszikus központi telepítési modell használatával létrehozott virtuális gépek esetén használja ezt a [blogot](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) a virtuális gép tulajdonság ának frissítéséhez, és ellenőrizze, hogy az ügynök telepítve van-e. Ez a lépés nem szükséges az Erőforrás-kezelő virtuális gépek.

### <a name="update-the-vm-agent"></a>A virtuális gép ügynökének frissítése

#### <a name="windows-vms"></a>Windows rendszerű virtuális gépek

* A virtuális gép ügynökének frissítéséhez telepítse újra a [virtuálisgép-ügynök bináris fájljait.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) Az ügynök frissítése előtt győződjön meg arról, hogy a virtuális gép ügynök frissítése során nem történik biztonsági mentési művelet.

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

* A Linux virtuálisgép-ügynök frissítéséhez kövesse a [Linux virtuálisgép-ügynök frissítése](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című cikkben található utasításokat.

    > [!NOTE]
    > Mindig használja a terjesztési tárház az ügynök frissítéséhez.

    Ne töltse le az ügynökkódot a GitHubról. Ha a legújabb ügynök nem érhető el a terjesztéshez, forduljon a terjesztési támogatási szolgálathoz a legújabb ügynök beszerzésére vonatkozó utasításokért. A Legújabb Windows [Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) adatait is ellenőrizheti a GitHub-tárházban.

### <a name="validate-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése

Ellenőrizze a Virtuálisgép-ügynök verzióját Windows virtuális gépeken:

1. Jelentkezzen be az Azure virtuális gépére, és keresse meg a **C:\WindowsAzure\Packages mappát.** Meg kell találnia a **WaAppAgent.exe** fájlt.
2. Kattintson a jobb gombbal a fájlra, és válassza a **Tulajdonságok parancsot.** Ezután válassza a **Részletek** lapot. A **Termék verzió mezője** 2.6.1198.718 vagy újabb.

## <a name="troubleshoot-vm-snapshot-issues"></a>Virtuális gép pillanatképének hibáinak elhárítása

A virtuális gép biztonsági mentése az alapul szolgáló tároló pillanatkép-parancsok kiadására támaszkodik. Ha nem fér hozzá a tárhelyhez, vagy ha egy pillanatkép-feladat futtatása késlelteti, a biztonsági mentési feladat sikertelen lehet. A következő feltételek okozhatnak pillanatkép-feladat hibát:

* **Az SQL Server biztonsági másolattal konfigurált virtuális gépek pillanatkép-késleltetést okozhatnak.** Alapértelmezés szerint a virtuális gépek biztonsági mentése létrehoz egy VSS teljes biztonsági mentést a Windows virtuális gépeken. Az SQL Server t használó, SQL Server biztonsági mentést konfigurált virtuális gépek pillanatkép-késéseket tapasztalhatnak. Ha a pillanatképek késleltetése biztonsági mentési hibákat okoz, állítsa be a következő rendszerleíró kulcsot:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **A virtuális gép állapota helytelenül van jelentve, mert a virtuális gép le van állítva az RDP-ben.** Ha a távoli asztal segítségével állítsa le a virtuális gépet, ellenőrizze, hogy a virtuális gép állapota a portálon helyes-e. Ha az állapot nem megfelelő, használja a **Leállítás** beállítást a portálvirtuális gép irányítópultján a virtuális gép leállításához.
* **Ha négynél több virtuális gép ugyanazt a felhőszolgáltatást osztja meg, a virtuális gépeket több biztonsági mentési szabályzat között is elkell osztani.** A biztonsági mentési idők, így nem több, mint négy virtuális gép biztonsági mentések indul egy időben. Próbálja meg legalább egy órával elválasztani a házirendek kezdési időpontjait.
* **A virtuális gép nagy processzorral vagy memóriával fut.** Ha a virtuális gép fut a magas memória vagy a CPU-használat, több mint 90 százaléka, a pillanatkép feladat várólistára kerül, és késik. Végül is időát ki. Ha ez a probléma történik, próbálkozzon igény szerinti biztonsági másolattal.

## <a name="networking"></a>Hálózat

Az IaaS virtuális gépek biztonsági mentéséhez engedélyezni kell a DHCP-t a vendégen belül. Ha statikus privát IP-címre van szüksége, konfigurálja azt az Azure Portalon vagy a PowerShellen keresztül. Győződjön meg arról, hogy a virtuális gépen belüli DHCP-beállítás engedélyezve van.
További információ a statikus IP-cím PowerShellen keresztüli beállításáról:

* [Statikus belső IP hozzáadása meglévő virtuális géphez](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Hálózati adapterhez rendelt magánhálózati IP-cím foglalási módjának módosítása](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

