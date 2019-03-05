---
title: Hibaelhárítás – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk a hibaelhárítási tippek a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 03/04/2019
ms.custom: seodec18
ms.openlocfilehash: d4698ad54e08587b223bb65388d399c0cbf3ff63
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342513"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Az Azure Disk Encryption – hibaelhárítási útmutató

Ez az útmutató olyan informatikai szakemberek számára, adatbiztonsági elemzők és felhőszolgáltatás-rendszergazdák, akiknek szervezetek használata az Azure Disk Encryption. Ez a cikk azt a lemez-titkosítással kapcsolatos problémák elhárítása.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Hibaelhárítás a Linux operációs rendszer lemeztitkosítás

Linux operációs rendszer (OS) lemez titkosítása az operációs rendszer meghajtójának kell választania, mielőtt futtatná azt a teljes lemez titkosítása folyamaton keresztül. Ha azt nem lehet leválasztani a meghajtó, egy hibaüzenet, a "nem sikerült leválasztania után..." valószínű.

Ez a hiba akkor fordulhat elő, során, és az operációs rendszer lemeztitkosítás van egy cél virtuális gép környezetre, amely a támogatott tőzsdei katalóguslemezt változott. Eltérések a támogatott lemezképből zavarhatja a bővítmény lehetővé teszi az operációs rendszer meghajtójának leválasztása. Szorzataként lehet például a következő elemek:
- Egyéni rendszerképek már nem felel meg egy támogatott fájlrendszert vagy a particionálási sémát.
- Nagy méretű alkalmazások, például SAP, MongoDB, Apache Cassandra és a Docker telepítve, és a titkosítás előtt az operációs rendszer fut. Ha nem támogatottak. Az Azure Disk Encryption nem tudja állítsa le ezeket a folyamatokat, biztonságosan az operációs rendszer meghajtójának előkészítése a lemeztitkosítás által megkövetelt módon. Ha még mindig aktív folyamatok, az operációs rendszer meghajtójának Fájlleírók megnyitása rendelkezés van, az operációs rendszer meghajtójának nem lehet választani, az operációs rendszer meghajtójának titkosítására hibát eredményez. 
- Egyéni parancsfájlok futtatása a titkosítás engedélyezése a közeli idő közelében található, vagy ha más változik a virtuális gépen a titkosítási folyamat során. Az ütközés akkor fordulhat elő, ha az Azure Resource Manager-sablonok meghatározása egyidejű végrehajtása több bővítményt, vagy ha egy egyéni szkriptbővítményt vagy más művelet fut egyidejűleg a lemeztitkosítás. Szerializálása és lépéseket elkülönítése a előfordulhat, hogy a probléma megoldásához.
- Biztonsági fokozott Linux (SELinux) még nem le lett tiltva, mielőtt engedélyezné a titkosítás, így a leválasztás lépés sikertelen lesz. SELinux is újra kell engedélyezve titkosítás befejeződése után.
- Az operációsrendszer-lemez egy logikai kötet-kezelő (LVM) sémát használ. Bár a korlátozott LVM adatok lemeztámogatás érhető el, nem LVM operációsrendszer-lemezt.
- Nem teljesülnek a minimális memóriára vonatkozó követelményeknek (7 GB ajánlott az operációs rendszer lemeztitkosítás).
- Adatmeghajtók rekurzív módon a /mnt/ könyvtár vagy egymással (például /mnt/data1, /mnt/data2, /data3 + /data3/data4) csatlakoztatva.
- Más az Azure Disk Encryption [Előfeltételek](azure-security-disk-encryption-prerequisites.md) Linux rendszeren nem teljesülnek.

## <a name="bkmk_Ubuntu14"></a> Frissítés az alapértelmezett kernel Ubuntu 14.04 LTS

Az Ubuntu 14.04 LTS rendszerképet tartalmaz egy alapértelmezett kernel 4.4-es verzióját. A kernel verziója rendelkezik egy ismert probléma, amelyben / memória ablak lelövés megfelelően megszakítja a dd parancs az operációs rendszer titkosítási folyamat során. Ezt a hibát kijavították a a legújabb Azure Linux-kernel lehetőségeire. Ez a hiba, a képet, a titkosítás engedélyezése előtt elkerülése érdekében frissítse a [Azure lehetőségeire kernel 4.15](https://packages.ubuntu.com/trusty/linux-azure) vagy később a következő parancsokkal:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Miután a virtuális gép újraindult be az új kernelbe, az új kernelverzióját erősíthető használatával:

```
uname -a
```

## <a name="unable-to-encrypt-linux-disks"></a>Nem sikerült titkosítani a Linux-lemezek

Bizonyos esetekben a lemeztitkosítás úgy tűnik, hogy "Az operációs rendszer lemezén titkosítás lépései" megakad Linux- és SSH le van tiltva. A titkosítási folyamat között a tőzsdei katalóguslemezt és 16 közötti 3 órát vehet igénybe. Ha több terabájt méretű adatlemezek hozzá vannak adva, a folyamat eltarthat nap.

A Linux operációsrendszer-lemez titkosítási feladatütemezési ideiglenesen leválasztja az operációs rendszer meghajtójának. Majd hajtja végre blokkonként-titkosítás a teljes operációsrendszer-lemezről, mielőtt titkosított állapotában Újracsatlakoztat azt. Ellentétben az Azure Disk Encryption a Windows Linux lemeztitkosítás nem engedélyezi a virtuális gép egyidejű használatra amíg folyamatban van a titkosítás. A virtuális gép a teljesítményjellemzők teheti a titkosítás befejezéséhez szükséges idő jelentős eltérés. Ezek a jellemzők a lemez, és hogy a tárfiók standard vagy prémium (SSD) tárolási méretét is.

A titkosítás állapotának lekérdezéséhez a **Feladatnézetben** által visszaadott mező a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsot. Az operációs rendszer meghajtójának titkosított, miközben a virtuális gép karbantartási állapotba kerül, és letiltja az SSH a folyamatban lévő folyamat bármely szolgáltatáskimaradás elkerülése érdekében. A **EncryptionInProgress** üzenet jelentésekben a legtöbb az idő, amíg folyamatban van a titkosítás. Néhány óra múlva, egy **VMRestartPending** üzenetben kéri, hogy indítsa újra a virtuális Gépet. Példa:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Miután kéri, hogy a virtuális gépet, és a virtuális gép újraindul, meg kell várnia a 2-3 perc alatt az újraindítás és a végső lépéseket kell végrehajtania a célon. Az állapot módosul, ha a titkosítás a végül befejezéséhez. Érhető el ezt az üzenetet, miután a titkosított operációsrendszer-meghajtón várhatóan használatra kész lesz, és a virtuális gép újra használatra kész.

A következő esetekben javasoljuk, hogy a virtuális gép térjen vissza a pillanatkép vagy közvetlenül a titkosítási előtt készült biztonsági másolatok visszaállítása:
   - Ha az újraindítás feladatütemezési, az előzőekben leírt nem valósul meg.
   - Ha a rendszerindító információkat, folyamatállapot-üzenet vagy más hiba mutatókat, hogy az operációs rendszer a titkosítás lépései a folyamat során nem sikerült. Üzenet például a "nem sikerült leválasztani", ebben az útmutatóban leírt hiba.

A következő kísérlet előtt kiértékeli a virtuális gép jellemzőit, és győződjön meg arról, hogy az összes előfeltétel teljesül.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Hibaelhárítás az Azure Disk Encryption tűzfal mögött
Kapcsolat korlátozza egy tűzfal, proxy követelmény vagy hálózati biztonsági csoportok (NSG) beállításait, ha a szükséges feladatok végrehajtásához a bővítmény képessége előfordulhat, hogy szakadhat meg. Ez zavart okozhat állapotüzeneteket, például a "Bővítmény állapota nem érhető el a virtuális gépen." A várt forgatókönyveket a titkosítás befejezéséhez sikertelen lesz. A következő szakaszok rendelkezik néhány tűzfal problémára, előfordulhat, hogy megvizsgálja.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Hálózati biztonsági csoport beállításai alkalmazott továbbra is engedélyeznie kell a végpontot, hogy megfeleljen a dokumentált hálózati konfiguráció [Előfeltételek](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) lemeztitkosításra.

### <a name="azure-key-vault-behind-a-firewall"></a>Az Azure Key Vault tűzfal mögött

Ha titkosítás alatt álló engedélyezve van a [Azure AD hitelesítő adatait](azure-security-disk-encryption-prerequisites-aad.md), a cél virtuális Gépen kell engedélyezi a csatlakozást az Azure Active Directory-végpontokhoz és a Key Vault-végpont. Azure Active Directory-hitelesítés aktuális végpontokat karbantartása az 56. és 59 a [Office 365 URL-címei és IP-címtartományok](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentációját. A Key Vault utasítások itt találhatók a dokumentációban való [hozzáférés az Azure Key Vault tűzfal mögötti](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Az Azure Instance Metadata szolgáltatás 
A virtuális gép eléréséhez képesnek kell lennie a [Azure Instance Metadata szolgáltatás](../virtual-machines/windows/instance-metadata-service.md) végpontot, amely jól ismert nem átirányítható IP-címet használ (`169.254.169.254`), amelyek elérhetők csak a virtuális gépen.  Amely erre a címre (például egy X-továbbított – a fejléc hozzáadása) helyi HTTP-forgalom alter proxykiszolgáló-konfigurációk nem támogatottak.

### <a name="linux-package-management-behind-a-firewall"></a>Linux-csomagkezelés tűzfal mögött

Futásidőben a Linuxhoz készült Azure Disk Encryption támaszkodik a cél terjesztési Csomagkezelő rendszeren a titkosítás engedélyezéséhez szükséges, előfeltételként szükséges összetevők telepítése. Ha a tűzfal beállításai a virtuális gép nem tud letölteni és telepíteni ezeket az összetevőket, majd ezt követő hibák várható. Konfigurálásáról a Csomagkezelő rendszeren któl terjesztési változhat. Red hat ha proxykiszolgáló szükséges, győződjön meg arról, hogy az előfizetés-kezelő és a yum használatával állíthatók be megfelelően. További információkért lásd: [előfizetés-kezelő és a yum használatával kapcsolatos problémák elhárítása](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>A Windows Server 2016 Server Core hibaelhárítása

A Windows Server 2016 Server Core a bdehdcfg összetevő nem érhető el, alapértelmezés szerint. Az Azure Disk Encryption ehhez az összetevőhöz szükséges. A rendszerkötet az operációsrendszer-kötet, amely csak egyszer történik a virtuális gép élettartama a felosztás szolgál. Ezek a bináris fájlok nem szükséges újabb titkosítási műveletek során.

A probléma megkerüléséhez másolja a következő négy fájlokat egy Windows Server 2016 Data Center virtuális Gépet ugyanarra a helyre, Server Core-on:

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

   4. Ellenőrizze a köteteket, és folytathatja a DiskPart használatával.  

Példa:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>Titkosítási állapot hibaelhárítása 

A portál pedig megjelenítheti egy lemezt titkosított, volt a virtuális gépen nem titkosított után is.  Ez akkor fordulhat elő, amikor az alacsony szintű parancsok használják a lemezt a virtuális gépen, a magasabb szintű Azure Disk Encryption-kezelési parancsok használata helyett közvetlenül feloldására.  A magasabb szintű parancsai nem csak a lemezt a virtuális gépen feloldására, de a VM-en kívül is frissítse fontos platform a blokkszintű titkosítás és a virtuális Géphez társított bővítmény beállításait.  Ha ezek nem tartják összhangban, a platform nem lesz képes titkosítási állapotát, vagy a virtuális gép megfelelően üzembe.   

Az Azure Disk Encryption megfelelően letiltásához a titkosítás engedélyezve van egy korábbi hibátlan állapotra kezdhet, és használja a [Disable-AzureRmVmDiskEncryption](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) és [Remove-azurermvmdiskencryptionextension parancs](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/remove-azurermvmdiskencryptionextension) PowerShell-parancsok, vagy a [letiltása az vm encryption](https://docs.microsoft.com/en-us/cli/azure/vm/encryption) CLI-parancsot. 

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption és a problémák elhárítása néhány gyakori problémát. Ezt a szolgáltatást vagy képességeivel kapcsolatos további információkért lásd a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](../security-center/security-center-apply-disk-encryption.md)
- [Azure-beli adat-titkosítás inaktív állapotban](azure-security-encryption-atrest.md)
