---
title: Hibaelhárítás – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk a hibaelhárítási tippek a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: e2464332727b0ef1e616c04a975df5ac475a7b19
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610285"
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

## <a name="bkmk_Ubuntu14"></a>Az Ubuntu 14,04 LTS alapértelmezett kernelének frissítése

Az Ubuntu 14,04 LTS-rendszerkép a 4,4-es alapértelmezett kernel-verzióval rendelkezik. Ennek a kernel-verziónak van egy ismert hibája, amely miatt a memóriából való gyilkos helytelenül leállítja a dd parancsot az operációs rendszer titkosítási folyamata során. Ezt a hibát a legújabb Azure tuned Linux kernelben javítottuk. Ha el szeretné kerülni ezt a hibát, mielőtt engedélyezi a titkosítást a rendszerképben, frissítsen az Azure-ban [hangolt kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) -es vagy újabb verziójára az alábbi parancsok használatával:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Miután a virtuális gép újra lett indítva az új kernelbe, az új kernel verziója a következő használatával erősíthető meg:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Az Azure-beli virtuálisgép-ügynök és-bővítmények verziójának frissítése

A Azure Disk Encryption műveletek a virtuálisgép-rendszerképeken az Azure Virtual Machine Agent nem támogatott verzióit használva sikertelenek lehetnek. A titkosítás engedélyezése előtt frissíteni kell a 2.2.38-nál korábbi ügynök-verziókkal rendelkező Linux-lemezképeket. További információkért lásd: [Az Azure Linux-ügynök frissítése](../virtual-machines/extensions/update-linux-agent.md) virtuális gépen, valamint a [virtuálisgép-ügynökök minimális verziójának támogatása az Azure-ban](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A Microsoft. Azure. Security. AzureDiskEncryption vagy a Microsoft. Azure. Security. AzureDiskEncryptionForLinux megfelelő verziója is szükséges. A bővítmények verzióit a platform automatikusan karbantartja és frissíti, amikor az Azure-beli virtuális gépek ügynökének előfeltételei teljesülnek, és a rendszer a virtuálisgép-ügynök támogatott verzióját használja.

A Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux kiterjesztés elavult, és már nem támogatott.  

## <a name="unable-to-encrypt-linux-disks"></a>A Linux-lemezek nem titkosíthatók

Bizonyos esetekben a lemeztitkosítás úgy tűnik, hogy "Az operációs rendszer lemezén titkosítás lépései" megakad Linux- és SSH le van tiltva. A titkosítási folyamat között a tőzsdei katalóguslemezt és 16 közötti 3 órát vehet igénybe. Ha több terabájt méretű adatlemezek hozzá vannak adva, a folyamat eltarthat nap.

A Linux operációsrendszer-lemez titkosítási feladatütemezési ideiglenesen leválasztja az operációs rendszer meghajtójának. Majd hajtja végre blokkonként-titkosítás a teljes operációsrendszer-lemezről, mielőtt titkosított állapotában Újracsatlakoztat azt. Ellentétben az Azure Disk Encryption a Windows Linux lemeztitkosítás nem engedélyezi a virtuális gép egyidejű használatra amíg folyamatban van a titkosítás. A virtuális gép a teljesítményjellemzők teheti a titkosítás befejezéséhez szükséges idő jelentős eltérés. Ezek a jellemzők a lemez, és hogy a tárfiók standard vagy prémium (SSD) tárolási méretét is.

A titkosítás állapotának megtekintéséhez lekérdezheti a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancs által visszaadott **feladatnézetben** mezőt. Az operációs rendszer meghajtójának titkosított, miközben a virtuális gép karbantartási állapotba kerül, és letiltja az SSH a folyamatban lévő folyamat bármely szolgáltatáskimaradás elkerülése érdekében. A **EncryptionInProgress** üzenet jelentésekben a legtöbb az idő, amíg folyamatban van a titkosítás. Néhány óra múlva, egy **VMRestartPending** üzenetben kéri, hogy indítsa újra a virtuális Gépet. Példa:


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

Ha engedélyezve van a titkosítás az [Azure ad hitelesítő adataival](azure-security-disk-encryption-prerequisites-aad.md), a CÉLKÉNT megadott virtuális gépnek Azure Active Directory végpontokhoz és Key Vault végpontokhoz is engedélyeznie kell a kapcsolatot. Az aktuális Azure Active Directory hitelesítési végpontok az [Office 365 URL-címeinek és IP-címtartományok](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentációjának 56-es és 59-es szakaszában maradnak. Key Vault útmutatást a [tűzfal mögötti Azure Key Vault elérésének](../key-vault/key-vault-access-behind-firewall.md)dokumentációjában talál.

### <a name="azure-instance-metadata-service"></a>Azure-Instance Metadata Service 
A virtuális gépnek képesnek kell lennie elérni az [Azure-példány metaadatainak szolgáltatási](../virtual-machines/windows/instance-metadata-service.md) végpontját, amely egy jól ismert, nem irányítható IP`169.254.169.254`-címet () használ, amely csak a virtuális gépről érhető el.  A helyi HTTP-forgalmat az erre a címmé megváltoztató proxy-konfigurációk nem támogatottak.

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

1. Írja be a következő parancsot:

   ```
   bdehdcfg.exe -target default
   ```

1. Ez a parancs létrehoz egy 550 MB-os rendszerpartíciót. Indítsa újra a rendszert.

1. Ellenőrizze a köteteket, és folytathatja a DiskPart használatával.  

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

A portál titkosított lemezként jelenhet meg, miután a virtuális gépen titkosítva lett.  Ez akkor fordulhat elő, ha az alacsony szintű parancsok használatával a rendszer a virtuális gépről közvetlenül titkosítja a lemezt a magasabb szintű Azure Disk Encryption felügyeleti parancsok használata helyett.  A magasabb szintű parancsok nem csak a virtuális gépről titkosítják a lemezt, hanem a virtuális GÉPEN kívül is frissítik a virtuális géphez tartozó fontos platform szintű titkosítási beállításokat és bővítmény-beállításokat.  Ha ezeket nem tartja összhangban, a platform nem fogja tudni bejelenteni a titkosítási állapotot, vagy megfelelően kiépíteni a virtuális gépet.   

A Azure Disk Encryption PowerShell-lel való letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsot, majd a Remove [-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A Remove-AzVMDiskEncryptionExtension futtatása a titkosítás letiltása előtt sikertelen lesz.

A CLI-vel való Azure Disk Encryption letiltásához használja [az az VM encryption disable](/cli/azure/vm/encryption)parancsot. 

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption és a problémák elhárítása néhány gyakori problémát. Ezt a szolgáltatást vagy képességeivel kapcsolatos további információkért lásd a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](../security-center/security-center-apply-disk-encryption.md)
- [Azure-beli adat-titkosítás inaktív állapotban](fundamentals/encryption-atrest.md)
