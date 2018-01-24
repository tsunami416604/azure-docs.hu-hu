---
title: "Az Azure Disk Encryption hibaelhárítása |} Microsoft Docs"
description: "Ez a cikk hibaelhárítási tippek a Microsoft Azure lemez Encryption for Windows és Linux IaaS virtuális gépeket."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: devtiw
ms.openlocfilehash: c252bc6aee79ad009684f9d3e62c42529c024109
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Az Azure Disk Encryption hibaelhárítási útmutató

Ez az útmutató informatikai szakemberek számára, adatbiztonsági elemzők és felhő rendszergazdák, amelynek a szervezet Azure Disk Encryption használják, és lemez-titkosítással kapcsolatos problémák megoldásához útmutatást kell is.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Hibaelhárítási Linux operációsrendszer-lemez titkosítása

Linux operációs rendszer lemeztitkosítás az operációs rendszer meghajtó kell választania, mielőtt futtatná azt a teljes lemez titkosítása folyamaton keresztül. Ha azt nem lehet leválasztani a meghajtó, egy hibaüzenet, a "nem tudta leválasztani után..." valószínű.

Ez a hiba akkor fordulhat elő, amikor az operációs rendszer lemeztitkosítás kísérletet egy virtuális gép célkörnyezet módosított vagy a támogatott készlet gyűjtemény kép változása a. Eltérések a támogatott lemezképből is zavarják a bővítmény leválasztása az operációs rendszer meghajtó közé a következők:
- Testreszabott lemezképeket már nem felel meg egy támogatott fájlrendszeren vagy particionálási sémát.
- Nagy alkalmazások, például SAP, a MongoDB, az Apache Cassandra és a Docker nem támogatottak, ha azok telepítve és futnak a titkosítási előtt az operációs rendszer.  Az Azure Disk Encryption nem tudja biztonságosan állítsa le ezeket a folyamatokat a operációsrendszer-meghajtó előkészítése a lemez titkosítása által megkövetelt módon.  Ha vannak még mindig aktív megnyitott fájlleíró rendelkezik az operációs rendszer meghajtójához való csatlakoztatásra folyamatok, az operációs rendszer meghajtó nem csatlakoztatott, ami azt eredményezi, az operációs rendszer meghajtójának titkosításához hibát nem lehet. 
- Egyéni parancsfájlok futtatásához Bezárás idő közelében, a titkosítás engedélyezése, vagy ha más módosul a virtuális Gépen a titkosítási folyamat során. Ez akkor fordulhat elő, ha az Azure Resource Manager-sablonok meghatározása végrehajtása egyidejűleg több kiterjesztést, vagy ha egy egyéni parancsprogramok futtatására szolgáló bővítmény vagy másik művelet fut egyidejűleg a lemez titkosítása. Szerializálása és lépéseket azoknak a előfordulhat, hogy a probléma megoldásához.
- Ahhoz, hogy a titkosítás, így a leválasztási lépés sikertelen biztonsági fokozott Linux (SELinux) nem lett letiltva. SELinux titkosítási befejezése után is újra kell engedélyezve.
- Az operációsrendszer-lemezképet egy logikai kötet Manager (LVM) sémát használja. Bár a korlátozott LVM adatok támogatása nem érhető el, egy LVM operációsrendszer-lemez nincs.
- Minimális memória feltételeknek nem felel meg (az operációs rendszer lemeztitkosítás 7 GB ajánlott).
- Adatmeghajtók rekurzív módon a /mnt/ könyvtár, vagy egymással (például /mnt/data1, /mnt/data2, /data3 + /data3/data4) csatlakoztatva.
- Egyéb Azure Disk Encryption [Előfeltételek](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Linux nem teljesülnek.

## <a name="unable-to-encrypt"></a>Nem sikerült titkosítani a

Néhány esetben az adatok titkosítása úgy tűnik, hogy "Az operációs rendszer lemezén lépések titkosítási" Beragadt Linux és SSH le van tiltva. A titkosítási folyamat között készlet gyűjtemény lemezképen 3-16 órát vehet igénybe. Ha multi-terabájt adatlemezt ad hozzá, a folyamat eltarthat nap.

A Linux operációs rendszert futtató lemez titkosítási feladatütemezési ideiglenesen leválasztja az operációsrendszer-meghajtón. Majd blokkonként-segítségével a titkosítást végzi, a teljes operációsrendszer-lemez, mielőtt titkosított állapotában Újracsatlakoztat azt. Eltérően Azure Disk Encryption használatát a Windows Linux lemez titkosítása nem teszi lehetővé a virtuális gép egyidejű használatra amíg folyamatban van a titkosítás. A virtuális gép teljesítményétől teheti a teljes titkosítás szükséges idő jelentős eltérés. E jellemzők mérete a lemez és hogy a tárfiók standard vagy prémium (SSD) tárolási közé.

A titkosítási állapot megtekintéséhez kérdezze le a **Feladatnézetben** által visszaadott mező a [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) parancsot. Az operációs rendszer meghajtó titkosított, amíg a virtuális gép karbantartási állapotba kerül, és letiltja az SSH fennakadást a folyamatban lévő folyamatban érdekében. A **EncryptionInProgress** üzenet az idő jelentős jelentésekre, amíg folyamatban van a titkosítás. Több órával később, a **VMRestartPending** üzenet kéri, hogy indítsa újra a virtuális Gépet. Példa:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Miután a virtuális gép újraindítását kéri, és a virtuális gép újraindul, az újraindítás és a befejező lépései végezhető el a célszámítógépen 2-3 percet várnia kell. Az üzenet állapotmódosítások esetén a titkosítást végül végezze el. Ez az üzenet nem érhető el, miután a titkosított meghajtón az operációs rendszer várhatóan használatra kész, és a virtuális gép újra használatra kész.

A következő esetekben javasoljuk, hogy a pillanatkép vagy közvetlenül a titkosítási előtt készült biztonsági másolatok állítsa helyre a virtuális Gépet:
   - Ha a rendszer újraindítása feladatütemezési leírt korábban nem fordulhat elő.
   - Ha a rendszerindítási információk, állapotüzenetet vagy más mutatók hibajelentést, hogy az operációs rendszer a titkosítás sikertelen volt a folyamat közepén. Üzenet például a "nem sikerült leválasztani", a jelen útmutatóban ismertetett hiba.

A következő kísérlet előtt újra kiértékelje a virtuális gép jellemzőit, és győződjön meg arról, hogy az összes előfeltétel teljesül.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Hibaelhárítás az Azure Disk Encryption tűzfal mögött
Kapcsolat egy tűzfal, a proxy követelmény vagy a hálózati biztonsági csoport (NSG) beállításainak korlátozza, amikor a bővítmény hajthatnak végre a szükséges feladatok előfordulhat, hogy működni. A megszakítás eredményezhet állapotüzeneteket, például a "Bővítmény állapota nem érhető el a virtuális Gépre." A várt esetben a titkosítás befejezése meghiúsul. A következő szakaszok rendelkezik néhány gyakori tűzfal problémát, előfordulhat, hogy vizsgálni.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Hálózati biztonsági csoport beállításai alkalmazott továbbra is engedélyeznie kell a végpontot a dokumentált hálózati konfigurációnak megfelelő [Előfeltételek](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) a lemez titkosításához.

### <a name="azure-key-vault-behind-a-firewall"></a>Az Azure Key Vault tűzfal mögött
A virtuális gép kulcstároló eléréséhez képesnek kell lennie. A key vault mögül tűzfalon a hozzáférést nyújtani hivatkozik, amely a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) team tart fenn.

### <a name="linux-package-management-behind-a-firewall"></a>Linux-csomag felügyeleti tűzfal mögött

Futásidőben Azure Disk Encryption Linux támaszkodik a cél terjesztési Csomagkezelő rendszeren titkosítás engedélyezése előtt szükséges előfeltételként szükséges összetevők telepítéséhez. Ha a tűzfal beállításai megakadályozza, hogy a virtuális gép igényt töltse le és telepítse ezeket az összetevőket, majd további hibák esetén várható. A Csomagkezelő rendszeren konfigurálásáról által terjesztési eltérőek lehetnek. A Red Hat Ha egy proxykiszolgáló szükség, gondoskodnia kell arról, hogy az előfizetés-kezelő és a yum megfelelően vannak beállítva. További információkért lásd: [előfizetés-kezelő és yum elhárítása](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core hibaelhárítása

A Windows Server 2016 Server Core az a bdehdcfg összetevő nem áll rendelkezésre, alapértelmezés szerint. Ez az összetevő Azure Disk Encryption használata szükséges. A rendszerkötet, amely az élettartam a virtuális gép csak egyszer legyen konfigurálva a rendszerkötet leválasztására szolgál. A bináris fájlok esetén nincs szükség újabb titkosítási műveletek során.

A megoldás a probléma, egy Windows Server 2016 Data Center virtuális Gépet ugyanarra a helyre, a Server Core fájlok másolása a következő 4:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Írja be a következő parancsot:

   ```
   bdehdcfg.exe -target default
   ```

   3. Ez a parancs létrehoz egy 550 MB-os rendszerpartíciót. Indítsa újra a rendszert.

   4. A DiskPart eszközzel ellenőrizze a köteteket, és azután folytassa a műveletet.  

Példa:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>Hibaelhárítási titkosítási állapotát

Ha a várt titkosítási állapot nem egyezik meg a portálon értéket, tekintse át a következő támogatási cikk: [titkosítási állapotát nem megfelelően jelenik meg az Azure felügyeleti portálon](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por)

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megismerte további információk az Azure Disk Encryption és a problémák hibaelhárítása olyan gyakori problémákat. Ez a szolgáltatás és platformképességei kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure virtuális gép titkosítása](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Az Azure data titkosítását](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
