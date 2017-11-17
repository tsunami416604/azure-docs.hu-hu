---
title: "Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption |} Microsoft Docs"
description: "Ez a cikk áttekintést nyújt a Microsoft Azure lemez titkosítása a Windows és Linux IaaS virtuális gépeket."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: kakhan
ms.openlocfilehash: 15ed35ab3a082db3376890992be3a29b6e042a2f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption
A Microsoft Azure elkötelezett a kifejezetten a adatvédelem, az adatok közös joghatóság alá és a lehetővé teszi, hogy az Azure tárolt adatok végig a vezérlő speciális technológiák titkosításához, szabályozása és titkosítási kulcsok kezeléséhez az adatok vezérlő & naplózási hozzáférést. Ez rugalmasságot biztosít, Azure-ügyfél és válassza ki a saját üzleti igényeinek leginkább megfelelő megoldást. A dokumentum azt kódelemeit egy új technológia megoldás "Azure Disk Encryption for Windows és Linux infrastruktúra-szolgáltatási virtuális gép által" és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni segítségével. A dokumentum biztosít részletes útmutatás a lemezt Azure titkosítási szolgáltatásai, beleértve a támogatott forgatókönyveket, és a felhasználói élmény.

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami azt eredményezi, hogy további licencek vagy előfizetések költségeit.

## <a name="overview"></a>Áttekintés
Az Azure Disk Encryption egy új képesség, amely segít a Windows és Linux IaaS virtuális gépek lemezeit titkosításához. Az Azure Disk Encryption használja. Ez az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows-szolgáltatás és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít. A megoldás integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével szabályozhatja, és a lemez-titkosítási kulcsok és titkos kulcsainak kulcstároló-előfizetése kezeléséhez. A megoldás biztosítja azt is, hogy a virtuális gép lemezeinek lévő összes adat titkosítva legyenek-e az Azure tárolás közben.

A Windows és Linux IaaS virtuális gépeket Azure lemeztitkosítás jelenleg **általánosan rendelkezésre álló** minden nyilvános Azure-régiók és AzureGov régióban a prémium szintű Storage szabványos virtuális gépek és virtuális gépek.

### <a name="encryption-scenarios"></a>Titkosítási forgatókönyvek
Az Azure Disk Encryption megoldás támogatja a következő forgatókönyvet:

* Engedélyezze a titkosítást a előzetes titkosítással VHD- és titkosítási kulcsok alapján létrehozott új IaaS virtuális gépeken
* A támogatott Azure-gyűjtemény lemezképei alapján létrehozott új IaaS virtuális gépeket a titkosítás engedélyezése
* Az Azure-ban futó meglévő infrastruktúra-szolgáltatási virtuális gépeket a titkosítás engedélyezése
* Tiltsa le a titkosítást a Windows IaaS virtuális gépeken
* Tiltsa le a titkosítást a adatmeghajtókon Linux IaaS virtuális gépek
* Felügyelt lemezes virtuális gépek titkosításának engedélyezése
* Egy meglévő titkosított premium és a nem prémium szintű storage VM titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás titkosított virtuális gépek

A megoldás a következő szituációkat IaaS virtuális gépek Microsoft Azure-ban engedélyezésekor:

* Az Azure Key Vault integrációja
* Standard szint virtuális gépek: [A, D, DS, G, GS, F és stb adatsorozat IaaS virtuális gépeket](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Engedélyezze a titkosítást a Windows és Linux IaaS virtuális gépeket és a felügyelt lemezes virtuális gépeket, a támogatott Azure-gyűjtemény lemezképei alapján
* Tiltsa le a titkosítást az operációs rendszer és az adatok meghajtókon Windows IaaS virtuális gépeket és a felügyelt lemezes virtuális gépek
* A Linux IaaS virtuális gépeket és a felügyelt lemezes virtuális gépek adatmeghajtók titkosításának letiltása
* Engedélyezheti a titkosítást IaaS virtuális gépeket futtató Windows ügyfél operációs rendszer
* Engedélyezze a titkosítást a csatlakoztatási elérési utak köteteken
* Engedélyezze a titkosítást a Linux virtuális gépeken lemezzel konfigurált csíkozást (RAID) mdadm használatával
* Linux virtuális gépek LVM adatlemezek titkosításának engedélyezése
* Engedélyezheti a titkosítást az operációs rendszer és az adatlemezek Linux LVM 7.3. 
* Engedélyezze a titkosítást a tárolóhelyekkel konfigurált Windows virtuális gépeken
* Egy meglévő titkosított premium és a nem prémium szintű storage VM titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás titkosított virtuális, mind a nem-KEK KEK forgatókönyvek (KEK - fő titkosítási kulcs)
* Minden Azure nyilvános és AzureGov régiók támogatottak.

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológia:

* Az alapszintű csomag IaaS virtuális gépeket
* Linux IaaS virtuális gépeket egy operációs rendszer meghajtóján titkosításának letiltása
* Egy adatmeghajtó titkosításának letiltása, ha az operációs rendszer meghajtó titkosított Linux Iaas virtuális gépek
* Infrastruktúra-szolgáltatási virtuális gépeket, a klasszikus virtuális gép létrehozási módszer használatával létrehozott
* Engedélyezze a titkosítást a Windows és Linux IaaS virtuális gépeket a felhasználói egyéni lemezképek nem támogatott.
* Integráció a helyszíni kulcskezelő szolgáltatás
* Az Azure Files (megosztott fájlrendszer), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows alapú szoftveres RAID-rendszerek használatára konfigurált virtuális gépek

### <a name="encryption-features"></a>Titkosítási funkciók
Engedélyezze és központi telepítésekor Azure Disk Encryption Azure IaaS virtuális gépek, a következő lehetőségeket engedélyezve vannak, attól függően, hogy a megadott konfiguráció:

* A tárolás közben a rendszerindító kötet védelme érdekében az operációs rendszer kötet titkosítása
* A tárolás közben az adatok kötetek védelméhez az adatkötetek titkosítása
* Windows IaaS virtuális gépeket az operációs rendszer és az adatok meghajtókon titkosításának letiltása
* Az adatok titkosításának letiltása meghajtók Linux IaaS virtuális gépek (csak akkor, ha az operációs rendszer a nem titkosított meghajtó)
* A titkosítási kulcsok és titkos kulcsokat a kulcstartót előfizetésben védelme
* Jelentéskészítési infrastruktúra-szolgáltatási virtuális gép titkosított titkosítási állapotát
* Az infrastruktúra-szolgáltatási virtuális gép lemeztitkosítás konfigurációs beállítások eltávolítása
* Biztonsági mentés és visszaállítás titkosított virtuális gépek az Azure Backup szolgáltatás használatával

Infrastruktúra-szolgáltatási virtuális gépek Windows és Linux-megoldás az Azure Disk Encryption tartalmazza:

* A Windows lemeztitkosítás bővítményt.
* A Linux-lemeztitkosítás kiterjesztését.
* A lemez-titkosítás PowerShell-parancsmagokkal.
* A lemez-titkosítás Azure parancssori felület (CLI) parancsmagok.
* A lemez-titkosítás Azure Resource Manager-sablonok.

Az Azure Disk Encryption megoldás Windows vagy Linux operációs rendszert futtató IaaS virtuális gépek esetén támogatott. A támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg az "Előfeltételek" szakaszban.

> [!NOTE]
> Nem kell külön fizetni virtuális gépek lemezei az Azure Disk Encryption titkosítási van.

### <a name="value-proposition"></a>Értékajánlat
Ha a Azure lemez titkosítási-kezelési megoldást alkalmaz, felel meg a következő üzleti igényeinek:

* Infrastruktúra-szolgáltatási virtuális gépek védett aktívan, mert szabványos titkosítási technológia segítségével kezelje a szervezeti biztonsági és megfelelőségi követelményeknek.
* Az ügyfél által felügyelt kulcsok és a házirendeket, és IaaS virtuális gépeket rendszerindító naplózhatja a key vaultban lévő használatát.

### <a name="encryption-workflow"></a>Titkosítási munkafolyamat
Ahhoz, hogy a Windows és Linux virtuális gépek lemeztitkosítás, tegye a következőket:

1. Az előző titkosítási forgatókönyvek közül egy titkosítási forgatókönyv kiválasztása.
2. Engedélyezve a engedélyezése az Azure lemez titkosítási Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a CLI parancs lemeztitkosítás, és adja meg a titkosítási konfigurációt.

   * Az ügyfél titkosított VHD forgatókönyvhöz a titkosított VHD-fájlt feltölti a tárfiók és a titkosítási kulcsokat tárol a key vaultban. Ezt követően adja meg a titkosítási konfigurációját, és engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * A piactér a létrehozott új virtuális gépek és a meglévő virtuális gépek, amelyeken már az Azure-ban adja meg a titkosítási konfigurációját, és engedélyezze a titkosítást az IaaS virtuális gépen.

3. Hozzáférést biztosít az Azure platformon, a titkosítási kulcs anyagok (a BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux jelszava olvasni a kulcstartót engedélyezheti a titkosítást az IaaS virtuális gépen.

4. Adja meg a titkosítási kulcsot a kulcstartót anyag írni az Azure Active Directory (Azure AD) identitása. Ezzel engedélyezi a titkosítás az IaaS virtuális gépen, 2. lépésben említett forgatókönyvek.

5. Azure frissíti a virtuális gép szolgáltatásmodell titkosítás és a kulcstároló konfigurációval, és beállítja a titkosított virtuális Gépet.

 ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Visszafejtési munkafolyamat
Tiltsa le a lemez titkosítása IaaS virtuális gépekhez, végezze el az alábbi általános lépésekből áll:

1. Dönt, hogy tiltsa le a titkosítást (visszafejtési) az Azure-ban futó IaaS virtuális gépen a Azure lemez titkosítási Resource Manager-sablon vagy a PowerShell-parancsmagok használatával, és adja meg a visszafejtési konfigurációt.

 Ezzel a lépéssel letiltja a titkosítást az operációs rendszer vagy adatmennyiség vagy mindkét szolgáltatás a Windows infrastruktúra-szolgáltatási virtuális gép. Azonban, mivel az előző szakaszban említett, letiltása a Linux operációs rendszer lemez titkosítása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtók Linux virtuális gépeken, amíg az operációsrendszer-lemez nem titkosított.
2. Azure frissíti a virtuális gép modell, és az infrastruktúra-szolgáltatási virtuális gép visszafejtett van megjelölve. A virtuális gép tartalmát lévő, aktívan nem titkosított.

> [!NOTE]
> A disable-titkosítási művelet nem törli a kulcstartót és a titkosítási kulcsokat tárol (a Windows rendszereken a BitLocker titkosítási kulcsok) vagy a Linux jelszava.
 > Nem támogatott Linux operációs rendszer lemez titkosításának letiltása. A visszafejtési lépés csak a Linux virtuális gépeken adatmeghajtók engedélyezett.
Lemez adattitkosítás a Linux letiltása nem támogatott, ha az operációs rendszer meghajtó van titkosítva.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt engedélyezte a támogatott forgatókönyveket, a "Overview" szakaszban tárgyalt Azure Disk Encryption Azure IaaS virtuális gépeken, tekintse meg a következő előfeltételek teljesülését:

* Rendelkeznie kell egy érvényes aktív Azure-előfizetéshez erőforrásokat létrehozni a támogatott régiók az Azure-ban.
* Az Azure Disk Encryption támogatott a következő Windows Server-verziók: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016.
* Az Azure Disk Encryption támogatott a következő Windows-ügyfélverziókat: Windows 8 ügyfél és a Windows 10-ügyfeleknek.

> [!NOTE]
> Windows Server 2008 R2 rendelkeznie kell az Azure-titkosítás engedélyezése előtt telepített .NET-keretrendszer 4.5. Telepítheti a Windows Update szolgáltatással a Microsoft .NET-keretrendszer 4.5.2-es verzióját a Windows Server 2008 R2 x64-alapú rendszerek nem kötelező frissítés telepítésével ([KB2901983](https://support.microsoft.com/kb/2901983)).

* A következő Azure Disk Encryption támogatott Azure-katalógus alapú Linux server disztribúciók és verziók:

| A Linux-Disztribúció | Verzió | Támogatott titkosítási a kötet típusa|
| --- | --- |--- |
| Ubuntu | 16.04-NAPI-ES LTS VERZIÓ | Operációs rendszer és az adatok lemezre |
| Ubuntu | 14.04.5-DAILY-LTS | Operációs rendszer és az adatok lemezre |
| Ubuntu | 12.10 | Adatlemez |
| Ubuntu | 12.04 | Adatlemez |
| RHEL | 7.4 | Operációs rendszer és az adatok lemezre |
| RHEL | 7.3 | Operációs rendszer és az adatok lemezre |
| RHEL | 7.3 LVM | Operációs rendszer és az adatok lemezre |
| RHEL | 7.2 | Operációs rendszer és az adatok lemezre |
| RHEL | 6.8 | Operációs rendszer és az adatok lemezre |
| RHEL | 6.7 | Adatlemez |
| CentOS | 7.3 | Operációs rendszer és az adatok lemezre |
| CentOS | 7.2n | Operációs rendszer és az adatok lemezre |
| CentOS | 6.8 | Operációs rendszer és az adatok lemezre |
| CentOS | 7.1 | Adatlemez |
| CentOS | 7.0 | Adatlemez |
| CentOS | 6.7 | Adatlemez |
| CentOS | 6.6 | Adatlemez |
| CentOS | 6.5 | Adatlemez |
| openSUSE | 13.2 | Adatlemez |
| SLES | 12 SP1 | Adatlemez |
| SLES | 12-SP1 (prémium) | Adatlemez |
| SLES | HPC 12 | Adatlemez |
| SLES | 11-SP4 (prémium) | Adatlemez |
| SLES | 11 SP4 | Adatlemez |

* Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek találhatók az azonos Azure-régió, és az előfizetés.

> [!NOTE]
> Az Azure Disk Encryption funkció engedélyezése az erőforrások konfigurálása külön régióban hibát okoz.

* Beállítását, és a kulcstartót konfigurálása az Azure Disk Encryption, című **állítsa össze, és a kulcstartót konfigurálása az Azure Disk Encryption** a a *Előfeltételek* című szakaszát.
* Állítsa be, és az Azure AD-alkalmazást az Azure Active directory konfigurálása Azure Disk Encryption, című **beállítása az Azure AD-alkalmazás az Azure Active Directoryban** a a *Előfeltételek* című szakaszát.
* Állítsa be, és az Azure AD-alkalmazás a kulcstároló hozzáférési házirend konfigurálása, lásd a szakasz **a kulcstároló hozzáférési házirendben az Azure AD alkalmazás** a a *Előfeltételek* című szakaszát.
* Előzetes titkosítással Windows virtuális merevlemez elkészítéséhez című **előzetes titkosítással Windows virtuális merevlemez előkészítése** a a *függelék*.
* Előzetes titkosítással Linux virtuális merevlemez elkészítéséhez című **előzetes titkosítással Linux virtuális merevlemez előkészítése** a a *függelék*.
* Az Azure platformon hozzá kell férnie a titkosítási kulcsok vagy titkos kulcsok, így ezek a virtuális gép számára elérhető indul el, és a virtuális gép operációs rendszer kötetén visszafejti key vaultban lévő. Adja meg az Azure platform engedélyeket, állítsa be a **EnabledForDiskEncryption** a key vaultban tulajdonság. További információkért lásd: **állítsa össze, és a kulcstartót konfigurálása az Azure Disk Encryption** a függelékben.
* A kulcstároló titkos kulcs és a KEK URL-címek rendszerverzióval ellátott kell lennie. Azure érvénybe lépteti az e versioning korlátozása. Érvényes titkos kulcs és KEK URL-címek lásd az alábbi példákat:

  * Érvényes titkos URL-címet: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Érvényes KEK URL-címet: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption nem támogatja a kulcstároló titkok és KEK URL-címek részeként megadását portszámokat. Nem támogatott és a támogatott kulcstároló URL-címek, tekintse meg a következő:

  * Nem fogadható el kulcstároló URL-cím *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx https://contosovault.vault.azure.net:443/titkos kulcsok/contososecret*
  * Elfogadható kulcstároló URL-cím: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Ahhoz, hogy az Azure Disk Encryption a funkciót, az infrastruktúra-szolgáltatási virtuális gépek a következő hálózati végpont konfigurációs követelményeknek kell megfelelniük:
  * A kulcstartót csatlakozni egy token beszerzéséhez az infrastruktúra-szolgáltatási virtuális gép kell kapcsolódnia kell egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  * A titkosítási kulcsok írni a kulcstároló, az infrastruktúra-szolgáltatási virtuális gép csatlakozni tudjanak a kulcstartót végpont kell lennie.
  * Az infrastruktúra-szolgáltatási virtuális gép csatlakozni az Azure storage a végpont Azure bővítménytárban és az Azure storage-fiók, amelyen a VHD-fájlokat üzemeltető képesnek kell lennie.

  > [!NOTE]
  > Ha a biztonsági házirend korlátozza az Azure virtuális gépek Internet-hozzáféréssel, oldja meg az előző URI, és egy adott szabályt, amely engedélyezi a kimenő kapcsolódás az IP-címek konfigurálásához.
  >
  >Konfigurálja, és az Azure Key Vault eléréséhez (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall) tűzfal mögött

* A legújabb Azure PowerShell SDK-verzió segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Nem támogatott az Azure Disk Encryption [Azure PowerShell SDK verzió 1.1.0-ás](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Ha kap az Azure PowerShell 1.1.0-ás kapcsolatos hibát, lásd: [Azure lemez titkosítási hiba vonatkozó Azure PowerShell 1.1.0-ás](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Bármely Azure CLI parancsot, és társítsa azt az Azure-előfizetése, először telepítenie kell az Azure parancssori felület:
  * Az Azure parancssori felület telepítése, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md).
  * Az Azure parancssori felület Mac, Linux és a Windows Azure Resource Manager használatával, lásd: [Azure parancssori felület parancsait erőforrás-kezelő módban](../virtual-machines/azure-cli-arm-commands.md).

* Egy kezelt lemez titkosítása esetén, a felügyelt lemezes pillanatképet, vagy a biztonsági mentés a lemezen kívül Azure Disk Encryption titkosítás engedélyezése előtt kötelező előfeltétel.  A hely biztonsági másolat nélkül minden titkosítás során váratlan hiba okozhatja a lemez és a virtuális gép nem érhető el egy helyreállítási beállítás megadása nélkül.  Jelenleg nem kezelt lemezek biztonsági mentése a Set-AzureRmVMDiskEncryptionExtension, és hiba lesz, ha a használt felügyelt lemezes ellen, kivéve, ha a - skipVmBackup paraméter van megadva.  Ez a paraméter nem biztonságos használatára, kivéve, ha a biztonsági mentés kívül Azure Disk Encryption már megtörtént.   A - skipVmBackup paraméter megadása esetén a parancsmag nem lesz előtt titkosítási felügyelt lemezes biztonsági másolatot készíteni.  Emiatt egy kötelező előfeltétel a virtuális gép van beállítva, mielőtt engedélyezné az Azure Disk Encryption, helyreállítási később szükséges győződjön meg arról, hogy a felügyelt lemezes biztonsági másolatának számít.  
> [!NOTE]
 > A - skipVmBackup paraméter soha ne használja, ha egy pillanatkép vagy a biztonsági mentés már megtörtént Azure Disk Encryption kívül. 

* Az Azure Disk Encryption megoldást használ a BitLocker külső kulcsvédő Windows IaaS virtuális gépeket. Tartományhoz csatlakoztatott virtuális gépeket, a nem leküldéses, amelyeket TPM védelmi csoport házirendekben. "A BitLocker engedélyezése kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-referenciája](https://technet.microsoft.com/library/ee706521).
* Egyéni csoportházirenddel tartományhoz csatlakoztatott virtuális gépeken a BitLocker házirendnek tartalmaznia kell a következő beállítás: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption sikertelenek lesznek, amikor egyéni csoportházirend-beállítások a BitLocker nem kompatibilisek. Olyan gépeken, amelyek nem rendelkezett a megfelelő házirend-beállításokra, az új házirend alkalmazása, újraindítás az új házirend frissítése (gpupdate.exe Force), és indítsa újra lehet szükség.  
* Egy Azure AD-alkalmazást létrehozni, hozzon létre egy kulcstartót, vagy állítson be egy meglévő kulcstároló és engedélyezheti a titkosítást, lásd: a [Azure Disk Encryption előfeltétel PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Az Azure parancssori felület használatával lemeztitkosítás Előfeltételek konfigurálásához lásd: [a Bash parancsfájlok](https://github.com/ejarvi/ade-cli-getting-started).
* Az Azure Backup szolgáltatás segítségével biztonsági mentése és visszaállítása titkosított virtuális gépeken, ha az Azure Disk Encryption titkosítás engedélyezve van, a virtuális gépek titkosításához Azure Disk Encryption kulcs konfigurációval. A biztonsági mentési szolgáltatás támogatja a virtuális gépek nem-KEK vagy KEK-konfigurációk használatával titkosított. Lásd: [biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure biztonsági másolatok titkosításának](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

* A Linux operációs rendszer kötetének titkosításához, vegye figyelembe, hogy a virtuális gép újraindítása jelenleg szükséges, a folyamat végén. Ezt megteheti a portálon, a powershell vagy a parancssori felület.   A titkosítási előrehaladását úgy követheti nyomon, rendszeres időközönként lekérdezi a Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus által visszaadott állapotüzenet.  Ha a titkosítás befejeződött, a Ez a parancs által visszaadott állapotüzenet jelzi ezt.  Például "Feladatnézetben: operációsrendszer-lemez titkosítása sikeres volt, indítsa újra a virtuális gép" ezen a ponton a virtuális gép újraindítása és használt.  

* Az Azure Disk Encryption Linux rendszeren való csatlakoztatott fájlrendszer Linux előtt titkosítási adatok lemezt igényel

* Rekurzív módon csatlakoztatott lemezek nem támogatja az Azure Disk Encryption Linux adatokat. Ha például a célrendszer rendelkezik csatlakoztatott lemez/PEL/sáv és majd egy másik /foo/bar/baz, /foo/bar/baz titkosítását a sikeres lesz, de a/PEL/sáv titkosítása sikertelen lesz. 

* Az Azure Disk Encryption csak támogatott támogatott Azure katalógusában képek, amely megfelel a fenti előfeltételeknek. Egyéni partíciós séma és a folyamat viselkedések ezeket a lemezképeket az esetleg meglévő miatt nem támogatottak a felhasználói egyéni lemezképek. További még akkor is gyűjteménye a Képalapú a virtuális gép kezdetben előfeltételek teljesülnek, de lehet, hogy nem kompatibilis létrehozása után módosított.  Az adott, a Linux virtuális gépek titkosításához a javasolt eljárás azért indítása tiszta gyűjtemény lemezképről, a virtuális gép titkosításához és majd egyéni szoftverfrissítési vagy adatok hozzáadása a virtuális gép szükség szerint.  

* Az Azure Disk Encryption és helyi adatmennyiség - Bek kötet a Windows és a/mnt/azure_bek_disk Linux IaaS virtuális gépek a titkosítási kulcs biztonságos tárolásához. Nem törölhető és nem bármely ezt a lemezt a tartalom szerkesztése. Nem leválasztani a lemezt, mivel a titkosítási kulcs jelenléte szükséges a titkosítási műveleteket a IaaS virtuális Gépen. A kötet információs fájl további részleteit tartalmazza.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Állítsa be az Azure AD-alkalmazás az Azure Active Directoryban
Titkosítási engedélyezni kell az Azure-ban futó virtuális gép van szüksége, amikor Azure Disk Encryption hoz létre, és a titkosítási kulcsok ír a kulcstartót. A kulcstartót a titkosítási kulcsok kezelése az Azure AD-hitelesítés szükséges.

Erre a célra hozzon létre egy Azure AD-alkalmazást. Az alkalmazás regisztrálásához blogbejegyzést "Beolvasása az identitás a az alkalmazás" szakaszában található részletes lépéseket [Azure Key Vault - részletes](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). A feladás egy vagy több beállítása és konfigurálása a kulcstartót hasznos példák számos is tartalmaz. Hitelesítési célokra is használhatja, vagy titkos kulcs-alapú ügyfélhitelesítés, vagy az Azure AD-alapú ügyfél-hitelesítéshez.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Az Azure AD ügyfél titkos kulcs alapú hitelesítés
A következő szakaszok segíthetnek az Azure AD egy ügyfél titkos kulcs alapú hitelesítés beállítása.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Az Azure AD alkalmazás létrehozása az Azure PowerShell használatával
A következő PowerShell-parancsmag segítségével hozzon létre egy Azure AD-alkalmazást:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId az Azure AD ClientID és $aadClientSecret engedélyezése az Azure Disk Encryption később használjon titkos ügyfélkódot. Az Azure AD ügyfélkulcs megfelelően védelme.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Az ügyfél-azonosító az Azure AD és a titkos kulcs beállítása az Azure-portálon
Is állíthatja be az ügyfél-azonosító az Azure AD és a titkos kulcsot az Azure portál használatával. Ez a feladat végrehajtásához tegye a következőket:

1. Kattintson a **Active Directory** fülre.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Kattintson a **alkalmazás hozzáadása**, majd írja be az alkalmazás nevét.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Kattintson a nyílra, és adja meg az alkalmazástulajdonságokhoz.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Kattintson a pipa jelre a bal alsó befejezéséhez. Az alkalmazás konfigurációs lap jelenik meg, és a lap alján megjelenik az Azure AD ügyfél-azonosító.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Az Azure AD ügyfélkulcs gombra kattintva mentse a **mentése** gombra. Megjegyzés: az Azure AD titkos kulcsok szövegmezőben. Annak megfelelően védelme.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>Használjon egy meglévő alkalmazást
A következő parancsok végrehajtása megszerezze és használja a [Azure AD PowerShell modult](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> A következő parancsokat egy új PowerShell-ablakban kell végrehajtani. Ne használja az Azure PowerShell vagy az Azure erőforrás-kezelő ablak a parancsok. Ez a megközelítés azt javasoljuk, mert ezeket a parancsmagokat a MSOnline modul vagy az Azure AD PowerShell segítségével is.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Az Azure AD-alapú hitelesítés
> [!NOTE]
> Az Azure AD-alapú hitelesítés jelenleg nem támogatott Linux virtuális gépeken.

A következő szakaszok bemutatják, hogyan konfigurálhatja a Tanúsítványalapú hitelesítés az Azure AD.

##### <a name="create-an-azure-ad-application"></a>Az Azure AD-alkalmazás létrehozása
Hozzon létre egy Azure AD-alkalmazást, hajtsa végre a következő PowerShell-parancsmagokat:

> [!NOTE]
> Cserélje le a következő `yourpassword` a biztonságos jelszó karakterlánc, és megakadályozhatja a jelszót.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Ez a lépés befejezése után a kulcstartót PFX fájl feltöltése, és a tanúsítvány telepítése egy virtuális géphez szükséges hozzáférési szabályzatának engedélyezéséhez.

##### <a name="use-an-existing-azure-ad-application"></a>Egy Azure AD-alkalmazást használja
Ha konfigurál egy meglévő alkalmazás tanúsítvány alapú hitelesítést, használja az itt látható a PowerShell-parancsmagokkal. Győződjön meg arról, egy új PowerShell-ablakot a végrehajtásához.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Ez a lépés befejezése után a kulcstartót PFX fájl feltöltése, és a tanúsítvány telepítése egy virtuális géphez szükséges hozzáférési szabályzatának engedélyezéséhez.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>A kulcstartót PFX fájl feltöltése
Ez az eljárás részletes leírását, lásd: [a hivatalos Azure Key Vault fejlesztői Blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Azonban a következő PowerShell-parancsmagok szükség a feladathoz. Győződjön meg arról, Azure PowerShell-konzolon végrehajtásához.

> [!NOTE]
> Cserélje le a következő `yourpassword` a biztonságos jelszó karakterlánc, és megakadályozhatja a jelszót.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Meglévő virtuális key vaultban lévő tanúsítvány központi telepítése
A PFX feltöltése után a key vaultban tanúsítvány központi telepítése egy meglévő virtuális gépre, a következő:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>A kulcstároló hozzáférési házirend az Azure AD-alkalmazás beállítása
Az Azure AD-alkalmazást kell hozzáférjenek a kulcsok vagy titkos kulcsok a tárolóban lévő állapottal. Használja a [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) parancsmag használatával engedélyezze, hogy az alkalmazás, az ügyfél-azonosító (amely lett létrehozva, az alkalmazás regisztrálásakor) használja, mint a _– ServicePrincipalName_ paraméter értékét. További tudnivalókért lásd a következő blogbejegyzésben [Azure Key Vault - részletes](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Íme egy példa bemutatja, hogyan végrehajtani a feladatot a PowerShell használatával:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Az Azure Disk Encryption viszonyt kell konfigurálnia a következő hozzáférési házirendek az Azure AD ügyfélalkalmazáshoz: _WrapKey_ és _beállítása_ engedélyek.

## <a name="terminology"></a>Terminológia
Ha szeretné megtudni, egyes közös kifejezések ezt a technológiát használja, a következő táblázattal terminológia:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Azure ad [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Az Azure AD-fiókot hitelesítéséhez, tárolásának és titkos kulcsok beolvasása a kulcstároló előfeltétele. |
| Azure Key Vault | Key Vault egy kriptográfiai, kulcs szolgáltatást a Federal Information Processing szabványok FIPS érvényesített hardveres biztonsági modulok, amely segítségével megakadályozhatja a kriptográfiai kulcsokat és a bizalmas titkos alapul. További információkért lásd: [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| ARM | Azure Resource Manager |
| A BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparági felismerhető Windows kötet titkosítási technológia, amely lehetővé teszi az adatok titkosítása a Windows IaaS virtuális gépeken, hogy. |
| BEK | A BitLocker titkosítási kulcsokat használ az operációs rendszer rendszerindító kötet és az adatkötetek titkosítására. A BitLocker-kulcsok elő a kulcstároló, a titkos kulcsok. |
| parancssori felület | Lásd: [Azure parancssori felület](../cli-install-nodejs.md). |
| DM-crypt program segítségével |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) a Linux-alapú, átlátszó lemeztitkosítás alrendszer, amely lehetővé teszi az adatok titkosítása a Linux IaaS virtuális gépeken, hogy. |
| KEK | Kulcstitkosítás kulcsa az aszimmetrikus kulcs (RSA 2048), melyekkel történő védelmére vagy a titkos kulcs burkolja. Megadhat egy hardveres biztonsági modulok (HSM)-kulcs vagy kulcs szoftveres védelemmel ellátott védett. További részletekért lásd: [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| PS parancsmagok | Lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Állítson be és az Azure Disk Encryption key vaultban konfigurálása
Az Azure Disk Encryption segíti biztonságos működés érdekében a lemez-titkosítási kulcsok és titkos key vaultban lévő. Azure Disk Encryption key vaultban beállítása, hajtsa végre az alábbi szakaszok mindegyikében.

#### <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, használja az alábbi lehetőségek közül:

* ["101-kulcs-tároló-" Resource Manager-sablon létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Az Azure PowerShell kulcstároló-parancsmagok](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Hogyan [a kulcstartót biztonságos](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Ha már állított be kulcstároló az előfizetéséhez, ugorjon a következő szakaszban.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Állítsa be a titkosítás kulcsot (nem kötelező)
Ha a BitLocker titkosítási kulcsok biztonsági szint a KEK használni kívánt, vegye fel a KEK a kulcstartót. Használja a [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) kulcstitkosítási kulcs létrehozására a key vault-parancsmagot. A helyszíni kulcskezelő hardveres biztonsági MODULT is importálhat egy KEK. További részletekért lásd: [Key Vault dokumentáció](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

A KEK adhat hozzá, az Azure Resource Manager címen, vagy a kulcstartót felület használatával.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Kulcstároló engedélyek beállítása
Az Azure platformon hozzá kell férnie a titkosítási kulcsok vagy titkos kulcsok, hogy elérhetők legyenek a virtuális gép rendszerindítást, és a kötetek visszafejtésére key vaultban lévő. Adja meg az Azure platformra engedélyeket, állítsa be a **EnabledForDiskEncryption** tulajdonság a kulcsot a tároló a kulcstartót PowerShell parancsmag segítségével:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Azt is beállíthatja a **EnabledForDiskEncryption** tulajdonság látogasson el a [Azure erőforrás-kezelő](https://resources.azure.com).

Amint azt korábban említettük, meg kell adni a **EnabledForDiskEncryption** a kulcstartót tulajdonságát. Ellenkező esetben a telepítés meghiúsul.

Beállíthat hozzáférési házirendek az Azure AD alkalmazás a kulcstartót felhasználói felületről, itt látható módon:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Az a **speciális hozzáférési házirendek** lapra, győződjön meg arról, hogy a kulcstartót engedélyezve van-e az Azure Disk Encryption:

![Az Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Lemeztitkosítás üzembe helyezési forgatókönyvek és a felhasználói élmény
Sok lemeztitkosítás lehetőségeket engedélyezhet, és a lépéseket a forgatókönyv szerint változhatnak. Az alábbi szakaszok a forgatókönyvek részletesebben foglalkozik.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Új IaaS virtuális gépeket a piactérről létrehozott titkosításának engedélyezése
Új IaaS Windows virtuális Gépet az Azure piactérről lemez titkosítás használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Az Azure gyors üzembe helyezési-sablont, kattintson a **az Azure telepítéséhez**, a titkosítási konfigurációs adja meg a **paraméterek** panelt, és kattintson **OK**.

2. Válassza ki a előfizetés, az erőforráscsoport, a erőforráscsoport helye, a jogi feltételeket és a megállapodás, és kattintson **létrehozása** engedélyezheti a titkosítást egy új IaaS virtuális gépen.

> [!NOTE]
> Ez a sablon létrehoz egy új titkosított Windows virtuális Gépet, amely a Windows Server 2012 gyűjtemény lemezképet használja.

Segítségével engedélyezheti az adatok titkosítása a új IaaS RedHat Linux 7.2 rendszerű virtuális gép egy 200 GB-os RAID-0 tömbbel rendelkező [Resource Manager-sablon](https://aka.ms/fde-rhel). A sablon telepítése után ellenőrizze a virtuális gép titkosítási állapotát használatával a `Get-AzureRmVmDiskEncryptionStatus` parancsmag, a [az operációs rendszer titkosított meghajtó a futó Linux virtuális gép](#encrypting-os-drive-on-a-running-linux-vm). Ha a gép állapotot ad vissza _VMRestartPending_, indítsa újra a virtuális Gépet.

Az alábbi táblázat a Resource Manager sablon paramétereit a piactér forgatókönyvet az Azure AD ügyfél-azonosító segítségével új virtuális gépek esetén:

| Paraméter | Leírás |
| --- | --- |
| adminUserName | A virtuális gép rendszergazdai felhasználónév. |
| adminPassword | A virtuális gép rendszergazdai jelszóval. |
| newStorageAccountName | A tárfiók operációsrendszer- és virtuális merevlemezek tárolására neve. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, a D és G adatsorozat támogatottak. |
| virtualNetworkName | A VNet neve, amely a virtuális gép hálózati Adaptert kell tartoznia. |
| subnetName | A vnetben, amely a virtuális gép hálózati Adaptert kell tartoznia az alhálózat neve. |
| AADClientID | Ügyfél-Azonosítóját az Azure AD-alkalmazás, amely jogosult beírja a titkokat a key vaultban. |
| AADClientSecret | Az Azure AD-alkalmazás, amely jogosult beírja a titkokat a kulcstartót a titkos ügyfélkódot. |
| keyVaultURL | A BitLocker-kulcsot fel kell tölteni a kulcstároló URL-CÍMÉT. A parancsmag használatával kaphat `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL-címe (nem kötelező) létrehozott BitLocker kulcs titkosításához használt kulcs titkosítási kulcsot. |
| keyVaultResourceGroup | A key vault erőforráscsoportot. |
| vmName | A titkosítási művelet nem hajtható végre a virtuális gép neve. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Helyezheti a saját KEK a további védelmi az adattitkosítási kulcs (a hozzáférési kód titkos kulcs) key vaultban lévő.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Új IaaS virtuális gépeket ügyfél titkosított virtuális merevlemez és a titkosítási kulcsokat a létrehozott titkosításának engedélyezése
Ebben az esetben is engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a parancssori felület parancsait használatával titkosított. Az alábbi szakaszok ismertetik részletesebben a Resource Manager-sablon és a parancssori felület parancsait.

Kövesse az utasításokat egy ezekben a szakaszokban való felkészülés előzetes titkosítással képek használható az Azure-ban. A lemezkép létrehozása után egy titkosított Azure virtuális gép létrehozásához a lépéseket használhatja a következő szakaszban.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](#preparing-a-pre-encrypted-windows-vhd)
* [Előzetes titkosítással Linux virtuális merevlemez előkészítése](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használatával
Használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Az Azure gyors üzembe helyezési-sablont, kattintson a **az Azure telepítéséhez**, a titkosítási konfigurációs adja meg a **paraméterek** panelt, és kattintson **OK**.

2. Válassza ki a előfizetés, az erőforráscsoport, a erőforráscsoport helye, a jogi feltételeket és a megállapodás, és kattintson **létrehozása** engedélyezheti a titkosítást az új IaaS virtuális gépen.

A következő táblázat a Resource Manager sablon paramétereit a titkosított virtuális merevlemez:

| Paraméter | Leírás |
| --- | --- |
| newStorageAccountName | A tárfiók tárolja a titkosított az operációs rendszer virtuális merevlemez nevét. Ezt a tárfiókot kell már létrejött a ugyanazt az erőforráscsoportot és és a virtuális gép ugyanazon a helyen. |
| osVhdUri | Az operációs rendszer virtuális merevlemez a tárfiók URI Azonosítóját. |
| osType | Az operációs rendszer terméktípusának (Windows/Linux). |
| virtualNetworkName | A VNet neve, amely a virtuális gép hálózati Adaptert kell tartoznia. A név kell már létrejött a ugyanazt az erőforráscsoportot és és a virtuális gép ugyanazon a helyen. |
| subnetName | A virtuális hálózaton, amelyek a virtuális gép hálózati Adaptert kell tartoznia az alhálózat neve. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, a D és G adatsorozat támogatottak. |
| keyVaultResourceID | Az erőforrás-azonosítója, amely azonosítja a kulcstároló erőforrásához az Azure Resource Manager. A PowerShell-parancsmaggal kaphat `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | A lemez-titkosítási kulcs, amely be van állítva a key vaultban URL-CÍMÉT. |
| keyVaultKekUrl | A titkosítás kulcsot a létrehozott lemez-titkosítási kulcs titkosításához URL-CÍMÉT. |
| vmName | Az infrastruktúra-szolgáltatási virtuális gép neve. |

#### <a name="using-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Parancssori felület parancsai használatával
Ebben a forgatókönyvben az adatok titkosítása engedélyezze a parancssori felület parancsait, tegye a következőket:

1. A kulcstároló hozzáférési házirendek meg:

   * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Állítsa be az Azure AD-alkalmazást, hogy beírja a titkokat a key vaultban.

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Egy meglévő vagy a futó virtuális Gépre a titkosítás engedélyezéséhez írja be:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Titkosítási állapot beolvasása:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről a titkosítás engedélyezéséhez használja a következő paramétereket a `azure vm create` parancs:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Meglévő vagy az Azure-beli infrastruktúra-szolgáltatási Windows virtuális gép titkosításának engedélyezése
Ebben az esetben is engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a parancssori felület parancsait használatával titkosított. Az alábbi szakaszok részletesen bemutatják, hogyan engedélyezheti a Resource Manager-sablon és a parancssori felület parancsait.

#### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használatával
Adatok titkosítása a meglévő vagy az Azure-beli infrastruktúra-szolgáltatási Windows virtuális gépek használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Az Azure gyors üzembe helyezési-sablont, kattintson a **az Azure telepítéséhez**, a titkosítási konfigurációs adja meg a **paraméterek** panelt, és kattintson **OK**.

2. Válassza ki a előfizetés, az erőforráscsoport, a erőforráscsoport helye, a jogi feltételeket és a megállapodás, és kattintson **létrehozása** engedélyezheti a titkosítást a meglévő vagy nem futnak IaaS virtuális Gépen.

Az alábbi táblázat a Resource Manager sablon paramétereinek meglévő vagy az Azure AD ügyfél-Azonosítót használó virtuális gépeken futó:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Ügyfél-Azonosítóját az Azure AD-alkalmazás, amely jogosult beírja a titkokat a key vault a. |
| AADClientSecret | Az Azure AD-alkalmazás, amely jogosult beírja a titkokat a key vault a titkos ügyfélkódot. |
| keyVaultName | A BitLocker-kulcsot fel kell tölteni a kulcstároló neve. A parancsmag használatával kaphat `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | A kulcs titkosítási kulcsot, a BitLocker létrehozott kulcs titkosításához használt URL-CÍMÉT. Ez a paraméter nem kötelező, ha **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ érték. |
| volumeType | A titkosítási művelet végrehajtott kötet típusa. Érvényes értékek a következők _OS_, _adatok_, és _összes_. |
| sequenceVersion | A BitLocker művelet feladatütemezési verzióját. Ez a verziószám növelése, minden alkalommal, amikor egy lemeztitkosítás műveletet végzi el az azonos virtuális gépen. |
| vmName | A titkosítási művelet nem hajtható végre a virtuális gép neve. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Helyezheti a saját KEK a további védelmi az adattitkosítási kulcs (a BitLocker titkosítási titkos kulcs) a key vaultban.

#### <a name="using-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [Azure Disk Encryption megismerkedhet az Azure PowerShell - 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [Azure Disk Encryption megismerkedhet az Azure PowerShell - 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Parancssori felület parancsai használatával
Ahhoz, hogy a titkosítást a meglévő vagy az Azure-ban a parancssori felület parancsait IaaS Windows virtuális gép fut, tegye a következőket:

1. A kulcstároló hozzáférési házirendek megadása
   * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Állítsa be az Azure AD-alkalmazást, hogy beírja a titkokat a key vaultban.

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Egy meglévő vagy a futó virtuális gép titkosításának engedélyezése:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. A titkosítási állapot beolvasása:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről a titkosítás engedélyezéséhez használja a következő paramétereket a `azure vm create` parancs:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Engedélyezheti a titkosítást egy már létező vagy nem futnak IaaS Linux virtuális Gépre az Azure-ban
Egy már létező vagy nem futnak IaaS Linux virtuális gépre az Azure-ban lemeztitkosítás használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kattintson a **az Azure telepítéséhez** Azure gyors üzembe helyezési a sablonban, adja meg a titkosítási konfiguráció a **paraméterek** panelt, és kattintson **OK**.

2. Válassza ki a előfizetés, az erőforráscsoport, a erőforráscsoport helye, a jogi feltételeket és a megállapodás, és kattintson **létrehozása** engedélyezheti a titkosítást a meglévő vagy nem futnak IaaS virtuális Gépen.

Az alábbi táblázat a Resource Manager sablon paramétereinek meglévő vagy az Azure AD ügyfél-Azonosítót használó virtuális gépeken futó:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Ügyfél-Azonosítóját az Azure AD-alkalmazás, amely jogosult beírja a titkokat a key vault a. |
| AADClientSecret | Az Azure AD-alkalmazás, amely jogosult beírja a titkokat a kulcstartót a titkos ügyfélkódot. |
| keyVaultName | A BitLocker-kulcsot fel kell tölteni a kulcstároló neve. A parancsmag használatával kaphat `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | A kulcs titkosítási kulcsot, a BitLocker létrehozott kulcs titkosításához használt URL-CÍMÉT. Ez a paraméter nem kötelező, ha **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ érték. |
| volumeType | A titkosítási művelet végrehajtott kötet típusa. Érvényes támogatott értékek a következők _OS_ vagy _összes_ (lásd a támogatott Linux disztribúciókkal és az operációsrendszer- és adatlemezek prerequisiteis szakaszban korábbi verziók). |
| sequenceVersion | A BitLocker művelet feladatütemezési verzióját. Ez a verziószám növelése, minden alkalommal, amikor egy lemeztitkosítás műveletet végzi el az azonos virtuális gépen. |
| vmName | A titkosítási művelet nem hajtható végre a virtuális gép neve. |
| hozzáférési kód | Írjon be egy erős jelszót az adattitkosítási kulcsot. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Helyezheti a saját KEK a további védelmi az adattitkosítási kulcs (a hozzáférési kód titkos kulcs) key vaultban lévő.

#### <a name="cli-commands"></a>Parancssori felület parancsai
Engedélyezheti lemeztitkosítás a titkosított virtuális merevlemezen történő telepítéssel és használattal a [CLI parancs](../cli-install-nodejs.md). Ahhoz, hogy a meglévő vagy az Azure-beli infrastruktúra-szolgáltatási Linux virtuális gépek parancssori felület parancsait használva a titkosítás, tegye a következőket:

1. A kulcstároló hozzáférési házirendek meg:

 * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Állítsa be az Azure AD-alkalmazást, hogy beírja a titkokat a key vaultban.

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Egy meglévő vagy a futó virtuális gép titkosításának engedélyezése:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Titkosítási állapot beolvasása:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről a titkosítás engedélyezéséhez használja a következő paramétereket a `azure vm create` parancs:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Egy titkosított infrastruktúra-szolgáltatási virtuális gép titkosítási állapotának beolvasása
Azure Resource Manager használatával kaphat a titkosítási állapot [PowerShell-parancsmagok](/powershell/azure/overview), vagy a parancssori felület parancsait. Az alábbi szakaszok ismertetik, hogyan használható az Azure portál és a parancssori felület parancsait a titkosítási állapot lekérdezése céljából.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Egy titkosított Windows virtuális Gépre titkosítási állapotának lekérése az Azure Resource Manager használatával
Kaphat az infrastruktúra-szolgáltatási virtuális gép titkosítási állapotát az Azure Resource Manager a következő módon:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/), és kattintson a **virtuális gépek** a bal oldali ablaktáblán, hogy a virtuális gépek az előfizetéshez összefoglaló áttekintést. A virtuális gépek nézet szűrheti az előfizetés nevét kiválasztva a **előfizetés** legördülő listából.

2. Felső részén a **virtuális gépek** kattintson **oszlopok**.

3. Az a **válasszon oszlop** panelen válassza **lemeztitkosítás**, és kattintson a **frissítés**. Láthatja, hogy a lemez-titkosítás oszlop titkosítási állapotát megjelenítő _engedélyezve_ vagy _nincs engedélyezve a_ az egyes virtuális gépek, az alábbi ábrán látható módon:

 ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>A lemez-titkosítás PowerShell parancsmag használatával egy titkosított (Windows/Linux) infrastruktúra-szolgáltatási virtuális gép titkosítási állapotának beolvasása
A titkosítás állapotát az infrastruktúra-szolgáltatási virtuális gép lekérheti a lemez-titkosítás PowerShell-parancsmag `Get-AzureRmVMDiskEncryptionStatus`. Ahhoz, hogy a titkosítási beállítások a virtuális gép számára, írja be a következőt:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Vizsgálhatja meg a kimeneti _Get-AzureRmVMDiskEncryptionStatus_ titkosítási kulcs az URL-címeket.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

A OSVolumeEncrypted és DataVolumesEncrypted beállítások értéke _titkosított_, amely jelzi, hogy mindkét kötet titkosítása Azure Disk Encryption használatával. Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [Azure Disk Encryption megismerkedhet az Azure PowerShell - 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [Azure Disk Encryption megismerkedhet az Azure PowerShell - 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> A Linux virtuális gépeken, percet vesz igénybe három-négy a `Get-AzureRmVMDiskEncryptionStatus` parancsmag jelentheti a titkosítás állapotát.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>A lemez-titkosítás CLI parancs az infrastruktúra-szolgáltatási virtuális gép titkosítási állapotának beolvasása
A lemez-titkosítás CLI paranccsal kaphat az infrastruktúra-szolgáltatási virtuális gép titkosítási állapotát `azure vm show-disk-encryption-status`. Ahhoz, hogy a titkosítási beállítások a virtuális gép számára, adja meg az Azure CLI-munkamenetben:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Tiltsa le a titkosítást a windowsos infrastruktúra-szolgáltatási virtuális gép futó
Tiltsa le a titkosítást a futó Windows vagy Linux IaaS virtuális Gépet az Azure lemez titkosítási Resource Manager-sablon vagy a PowerShell-parancsmagok használatával, és adja meg a visszafejtési konfigurációt.

##### <a name="windows-vm"></a>Windowsos VM
A disable-titkosítás lépés letiltja a titkosítást az operációs rendszer, az adatmennyiség vagy mindkét szolgáltatás a Windows infrastruktúra-szolgáltatási virtuális gép. Az operációs rendszer kötetén tiltása nem, és a titkosított adatok kötet hagyja. A disable-titkosítás a lépést, ha az Azure klasszikus üzembe helyezési modellel frissíti a virtuális gép modell, és a Windows-infrastruktúra-szolgáltatási virtuális gép visszafejtett van megjelölve. A virtuális gép tartalmát lévő, aktívan nem titkosított. A visszafejtés nem törli a kulcstartót és a titkosítási kulcs anyagok (a BitLocker titkosítási kulcsokat a Windows és Linux jelszava).

##### <a name="linux-vm"></a>Linux virtuális gép
A disable-titkosítás lépés letiltja a titkosítást az adatmennyiség a futó Linux IaaS virtuális Gépen. Ez a lépés csak akkor működik, ha az operációs rendszer lemezének nem titkosított.

> [!NOTE]
> Az operációs rendszer lemezének titkosításának letiltása a Linux virtuális gépeken nem engedélyezett.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Tiltsa le a titkosítást egy már létező vagy nem futnak IaaS virtuális gépen
Adatok titkosítása a Windows IaaS virtuális gépeken futó segítségével letilthatja a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Az Azure gyors üzembe helyezési-sablont, kattintson a **az Azure telepítéséhez**, a visszafejtés konfigurációs adja meg a **paraméterek** panelt, és kattintson **OK**.

2. Válassza ki a előfizetés, az erőforráscsoport, a erőforráscsoport helye, a jogi feltételeket és a megállapodás, és kattintson **létrehozása** engedélyezheti a titkosítást egy új IaaS virtuális gépen.

Linux virtuális gépekhez, letilthatja titkosítás használata a [tiltsa le a titkosítást a futó Linux virtuális gép](https://aka.ms/decrypt-linuxvm) sablont.

Az alábbi táblázat a Resource Manager sablon paramétereinek egy infrastruktúra-szolgáltatási virtuális gép titkosításának letiltása:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet nem hajtható végre a virtuális gép neve.
| volumeType | A visszafejtési művelet végrehajtott kötet típusa. Érvényes értékek a következők _OS_, _adatok_, és _összes_. A futó Windows infrastruktúra-szolgáltatási virtuális gép operációs rendszer vagy rendszerindító kötet nélkül titkosításának letiltása a titkosítás nem tiltható le a _adatok_ kötet. Ne feledje, hogy az operációs rendszer lemezének titkosításának letiltása nem engedélyezett a Linux virtuális gépeken. |
| sequenceVersion | A BitLocker művelet feladatütemezési verzióját. Ez a verziószám növelése, minden alkalommal, amikor egy lemezt visszafejtési műveletet végzi el az azonos virtuális gépen. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Tiltsa le a titkosítást egy már létező vagy nem futnak IaaS virtuális gépen
Egy már létező vagy nem futnak infrastruktúra-szolgáltatási virtuális gép titkosításának letiltása a PowerShell-parancsmag használatával, lásd: [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Ez a parancsmag a Windows és Linux virtuális gépeket is támogatja. Tiltsa le a titkosítást, hogy telepít egy bővítmény a virtuális gép. Ha a _neve_ paraméter nincs megadva, az alapértelmezett név egy névtelen kiterjesztést _AzureDiskEncryption a Windows virtuális gépek_ jön létre.

A Linux virtuális gépeken a AzureDiskEncryptionForLinux kiterjesztését.

> [!NOTE]
> Ez a parancsmag a virtuális gép újraindul.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Engedélyezze a titkosítást a Azure felügyelt lemezes előzetes titkosítással IaaS virtuális gépen
Egy előre titkosított virtuális merevlemezről helyen ARM-sablon használatával titkosított virtuális gép létrehozása az Azure Managed lemez ARM-sablon segítségével   
[Előzetes titkosítással virtuális merevlemez tárolási blob hozhat létre új titkosított felügyelt lemezt] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Engedélyezheti a titkosítást egy új Linux IaaS virtuális Gépet az Azure Managed lemez
Az Azure Managed lemez ARM-sablon használatával új titkosított Linux infrastruktúra-szolgáltatási virtuális gép létrehozása az ARM-sablon helye:   
[A teljes lemez titkosítása RHEL 7.2 telepítését] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Engedélyezheti a titkosítást egy új Windows IaaS virtuális Gépet az Azure Managed lemez
 Az Azure Managed lemez ARM-sablon használatával új titkosított Linux infrastruktúra-szolgáltatási virtuális gép létrehozása az ARM-sablon helye:   
 [Új titkosított Windows IaaS kezelt lemez virtuális gép létrehozása gyűjtemény lemezképből] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Pillanatkép kötelező és/vagy a biztonsági mentési felügyelt lemezes alapú Virtuálisgép-példány kívülre, és az Azure Disk Encryption engedélyezése előtt.  A felügyelt lemezes pillanatképet lehessen állítani a portálról, vagy az Azure Backup használható.  Biztonsági mentések gondoskodjon arról, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás során.  Miután biztonsági másolatból történik, a Set-AzureRmVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával.  Ez a parancs elleni felügyelt lemezes virtuális gép sikertelen lesz, amíg a biztonsági mentést készített, és ez a paraméter van megadva.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Meglévő titkosított nem prémium szintű virtuális titkosítási beállításainak módosítása
  Használható a meglévő lemezt Azure támogatott titkosítási felületek futtatásakor [PS parancsmagok, CLI és ARM-sablonok] frissítése a titkosítási beállítások VM AAD ügyfél azonosítója/titkos, fő titkosítási kulcs [KEK], a BitLocker titkosítási kulcsa: Windows virtuális gép vagy jelszavát a Linux virtuális gép stb. A frissítés titkosítási beállítása a prémium és a nem prémium szintű tároló virtuális gépek esetén támogatott.

## <a name="appendix"></a>Függelék:
### <a name="connect-to-your-subscription"></a>Csatlakozás az előfizetéshez
Mielőtt folytatná, tekintse át a *Előfeltételek* című részben. Miután meggyőződött arról, hogy a szükséges előfeltételek maradéktalanul teljesülnek, csatlakozás az előfizetéshez a következő módon:

1. Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjával a következő parancsot:

    `Login-AzureRmAccount`

2. Ha több előfizetéssel rendelkezik, és adjon meg másik szeretne, írja be a fiókhoz tartozó előfizetések megjelenítéséhez a következőket:

    `Get-AzureRmSubscription`

3. Adja meg az előfizetést szeretné használni, írja be:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Győződjön meg arról, hogy helyesek-e beállítva az előfizetés, írja be:

    `Get-AzureRmSubscription`

5. Az Azure Disk Encryption parancsmagjai telepítve vannak, írja be:

    `Get-command *diskencryption*`

6. Az alábbi Példakimenet megerősíti, hogy az Azure lemez titkosítási PowerShell telepítési:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Előzetes titkosítással Windows virtuális merevlemez előkészítése
A következő szakaszok előre titkosított Windows virtuális merevlemez, egy titkosított Azure IaaS virtuális merevlemez telepítésének előkészítéséhez szükséges. Az információk segítségével készít, és indítsa el az Azure Site Recovery vagy Azure friss Windows virtuális gép (VHD).

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Nem TPM-OS védelem engedélyezése csoportházirend frissítése
A BitLocker csoportházirend-beállítás konfigurálása **a BitLocker meghajtótitkosítás**, amely alatt található **helyi számítógép-házirend** > **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**. Módosítsa ezt a beállítást, a **operációsrendszer-meghajtók** > **indításkor további hitelesítést** > **BitLocker engedélyezése kompatibilis TPM nélküli**, az alábbi ábrán látható módon:

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>A BitLocker-szolgáltatás összetevőinek telepítése
A Windows Server 2012 és újabb verziók használja a következő parancsot:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

A Windows Server 2008 R2 a következő paranccsal:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>A BitLocker az operációs rendszer kötetén előkészítéséhez segítségével`bdehdcfg`
Az operációs rendszer partíció tömörítéséhez, és a gép előkészítése a BitLocker, hajtsa végre a következő parancsot:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>A BitLocker használatával az operációs rendszer kötetének védelme
Használja a [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) parancs futtatásával engedélyezze a titkosítást használ egy külső kulcsvédő rendszerindító köteten. A külső meghajtó vagy kötet is elhelyezhető a külső kulcs (.bek fájl). Engedélyezve van a rendszerlemez vagy rendszerindító köteten a következő újraindítás után.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Készítse elő a virtuális Géphez egy külön adatok és az erőforrások virtuális Merevlemezt a BitLocker használatával a külső kulcs beolvasása.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Az operációs rendszer meghajtóján futó Linux virtuális gép titkosítása
Egy futó Linux virtuális gép operációs rendszer meghajtójának titkosítás funkciót támogatja, a következő terjesztéseket:

* 7.2 RHEL
* 7.2 centOS
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Az operációs rendszer lemeztitkosítás előfeltételei

* A virtuális gép kell létrehozni a Piactéri lemezképből az Azure Resource Manager.
* Az Azure virtuális gép, és legalább 4 GB RAM (mérete 7 GB ajánlott).
* (Az RHEL és CentOS) Tiltsa le a SELinux. Tiltsa le a SELinux, lásd a "4.4.2. SELinux "található letiltásával a [SELinux felhasználói és rendszergazdai útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális Gépen.
* Letiltja a SELinux, újraindítása után a virtuális gép legalább egy alkalommal.

##### <a name="steps"></a>Lépések
1. Hozzon létre egy virtuális Gépet a korábban megadott terjesztéseket egyikének használatával.

 7.2 – CentOS operációsrendszer-lemez titkosítása támogatott keresztül egy különös lemezképet. Ez a rendszerkép használatához adja meg a "7.2n" a Termékváltozat, a virtuális gép létrehozásakor:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurálja a virtuális gép szükség szerint. Ha minden az (operációs rendszer + adatok) titkosításához meghajtók, a meghajtók kell lennie a megadott és a csatlakoztatható /etc/fstab fog.

 > [!NOTE]
 > Használja az UUID =... adhatja meg az/etc/fstab a blokk-eszköz nevét (például/dev/sdb1) megadása helyett adatmeghajtókon. Titkosítás során a meghajtó változik a virtuális Gépen. Ha a virtuális Gépet a megadott sorrendben blokk eszközök támaszkodik, nem tudja titkosítás után csatlakoztassa őket.

3. Jelentkezzen ki az SSH-munkamenet.

4. Titkosításához az operációs rendszer, adja meg, mint volumeType **minden** vagy **OS** amikor Ön [engedélyezheti a titkosítást](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Minden felhasználói térben futó folyamatok, nem mint `systemd` rendelkező szolgáltatások el kell egy `SIGKILL`. Indítsa újra a virtuális Gépet. Ha engedélyezi az operációs rendszer lemeztitkosítás egy futó virtuális gépen, tervezze meg a virtuális gép állásidő.

5. Rendszeresen figyelje a folyamatban lévő titkosítási utasításait a [következő szakasz](#monitoring-os-encryption-progress).

6. Get-AzureRmVmDiskEncryptionStatus "VMRestartPending" jeleníti meg, miután próbál bejelentkezni, vagy a portál, a PowerShell vagy a parancssori felület használatával indítsa újra a virtuális Gépet.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Ahhoz, hogy újraindította, azt javasoljuk, hogy menti [rendszerindítási diagnosztika](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) a virtuális gép.

#### <a name="monitoring-os-encryption-progress"></a>Az operációs rendszer titkosítási folyamat figyelése
Figyelheti, hogy az operációs rendszer titkosítási folyamat három módon:

* Használja a `Get-AzureRmVmDiskEncryptionStatus` parancsmag és a Feladatnézetben mező vizsgálata:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Miután a virtuális gép eléri "Az operációs rendszer lemezén lépések titkosítási", körülbelül 40 – 50 percet vesz igénybe a prémium szintű storage a biztonsági másolatot a virtuális gép.

 Mert [#388 ki](https://github.com/Azure/WALinuxAgent/issues/388) WALinuxAgent, a `OsVolumeEncrypted` és `DataVolumesEncrypted` megjelennek, `Unknown` az egyes terjesztési. A WALinuxAgent verzió 2.1.5 és újabb verziók, ez a probléma fennáll automatikusan. Ha látja `Unknown` a kimenetben ellenőrizheti lemez-titkosítás állapotát az Azure erőforrás-kezelő használatával.

 Ugrás a [Azure erőforrás-kezelő](https://resources.azure.com/), majd bontsa ki az ebben a hierarchiában, a bal oldali panelen kiválasztása:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 A InstanceView görgessen le a meghajtók titkosítási állapotát tekintheti meg.

 ![Virtuális gép példányait tartalmazó nézet](./media/azure-security-disk-encryption/vm-instanceview.png)

* Nézze meg [rendszerindítási diagnosztika](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). A ADE bővítményből származó üzenetek kell előtagként `[AzureDiskEncryption]`.

* Jelentkezzen be a virtuális gép SSH-n keresztül, és a bővítmény naplóban az beszerzése:

    /var/log/Azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Azt javasoljuk, hogy nem bejelentkezik a virtuális gép operációs rendszer titkosítás közben. A naplók másolása, csak akkor, ha a két módszer sikertelen.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Előzetes titkosítással Linux virtuális merevlemez előkészítése
##### <a name="ubuntu-16"></a>Ubuntu 16
A terjesztési telepítése közben titkosítás konfigurálása a következő módon:

1. Válassza ki **titkosított kötetek konfigurálása** amikor particionálni a lemezeket.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy külön rendszerindítási meghajtót, amely nem lesznek titkosítva. A legfelső szintű meghajtó titkosítására.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez egy, a jelszót, amelyet a key vault feltöltött.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Fejezze be a particionálást.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Ha a virtuális gép rendszerindítási és egy jelszót a rendszer felkéri, használja a 3. lépésében megadott jelszót.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. A virtuális gép előkészítése az Azure használatával történő feltöltése [ezeket az utasításokat](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Ne futtassa az utolsó lépést (a virtuális gép megszüntetés) még.

Adja meg a titkosítás működéséhez az Azure-ral, az alábbi lépésekkel:

1. Hozzon létre egy fájlt az /usr/local/sbin/azure_crypt_key.sh, a következő parancsfájl tartalmát. Nagy figyelmet fordítani az a KeyFileName, mert az Azure-ban használt jelszót fájlnevet.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Módosítsa a titkosítási konfigurációs */etc/crypttab*. A listának így kell kinéznie:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Ha az éppen szerkesztett *azure_crypt_key.sh* Windows, és másolja azt Linux futtatása `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. A parancsfájl végrehajtható engedélyek hozzáadása:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Szerkesztés */etc/initramfs-tools/modules* sorok hozzáfűzésével:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Futtatás `update-initramfs -u -k all` frissíteni a initramfs, hogy a `keyscript` érvénybe léptetéséhez.

7. Most a virtuális gép is kiosztásának megszüntetése.

 ![Ubuntu 16.04 beállítása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. A következő lépésben és [a virtuális merevlemez feltöltéséhez](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure.

##### <a name="opensuse-132"></a>openSUSE, 13.2
A terjesztési telepítése közben a titkosítás konfigurálására, tegye a következőket:
1. A lemezek particionálása, válassza ki **titkosítása kötet csoport**, majd írja be a jelszót. Ez az a jelszavát, amelyet fel kell töltenie a kulcstartót.

 ![openSUSE, 13.2 beállítása](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gép az Ön jelszavát.

 ![openSUSE, 13.2 beállítása](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. A virtuális gép előkészítése a utasításait követve az Azure-bA feltölteni [SLES vagy openSUSE virtuális gép előkészítése Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Ne futtassa az utolsó lépést (a virtuális gép megszüntetés) még.

A titkosítás működéséhez az Azure-ral konfigurálásához tegye a következőket:
1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Ezek a sorok megjegyzéssé a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. A fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elején a következő sor hozzáfűzése:
 ```
    DRACUT_SYSTEMD=0
 ```
És összes előfordulását módosítsa:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
erre:
```
    if [ 1 ]; then
```
4. /Usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh szerkesztése és fűzi azokat hozzá "# nyitott LUKS eszköz":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Futtatás `/usr/sbin/dracut -f -v` a initrd frissítéséhez.

6. Most is kiosztásának megszüntetése a virtuális gép és [a virtuális merevlemez feltöltéséhez](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure.

##### <a name="centos-7"></a>CentOS 7
A terjesztési telepítése közben a titkosítás konfigurálására, tegye a következőket:
1. Válassza ki **az adatok titkosítása** amikor particionálni a lemezeket.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg arról, hogy **titkosítása** van kiválasztva a legfelső szintű partíció.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez egy, a jelszót, amelyet fel kell töltenie a kulcstartót.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Ha a virtuális gép rendszerindítási és egy jelszót a rendszer felkéri, használja a 3. lépésében megadott jelszót.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. A virtuális gép előkészítése feltöltése az Azure "CentOS 7.0 +" utasításait használatával [CentOS-alapú virtuális gép előkészítése Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Ne futtassa az utolsó lépést (a virtuális gép megszüntetés) még.

6. Most is kiosztásának megszüntetése a virtuális gép és [a virtuális merevlemez feltöltéséhez](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure.

A titkosítás működéséhez az Azure-ral konfigurálásához tegye a következőket:

1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Ezek a sorok megjegyzéssé a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. A fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elején a következő sor hozzáfűzése:
```
    DRACUT_SYSTEMD=0
```
És összes előfordulását módosítsa:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
erre:
```
    if [ 1 ]; then
```
4. /Usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh szerkesztése, és ezt követően a "# nyitott LUKS eszköz" hozzáfűzése:
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Futtassa a "/ usr/sbin/dracut - f - v" a initrd frissítéséhez.

![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított VHD-fájlt feltölti egy Azure storage-fiók
Miután a BitLocker-titkosítást vagy a DM-Crypt titkosítás engedélyezve van, a helyi titkosított virtuális Merevlemezt kell fel kell tölteni a tárfiók.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel a lemez-titkosítási titkos kulcsot a kulcstartót a előre titkosított virtuális gépek
A lemez-titkosítási titkos beszerzett korábban fel kell tölteni, mint a key vaultban lévő titkos kulcs. A key vault kell az adatok titkosítása és az Azure AD-ügyfél engedélyezve.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Lemez titkosítási titkos kulcs nem titkosított a KEK
Állítsa be a key vaultban lévő titkos kulcsot, használja a [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Ha egy Windows rendszerű virtuális gép, a bek fájl kódolt Base64 kódolású karakterlánc és a kulcstartót használó majd feltölteni a `Set-AzureKeyVaultSecret` parancsmag. Linux a jelszót kódolt Base64 kódolású karakterlánc, és a key vault majd feltölteni. Ezenkívül győződjön meg arról, hogy a titkos kulcsot a kulcstartó létrehozásakor, a következő címkék vannak-e beállítva.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Használja a `$secretUrl` a következő lépésben [az operációsrendszer-lemez csatolása KEK használata nélkül](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Lemez titkosítási titkos kulcsot egy KEK titkosítva
Előtt a titkos kulcsot a key vault feltöltött, opcionális titkosításhoz kulcs titkosítási kulcs használatával. Használja a sortörés [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) először titkosítani a titkos kulcs titkosítási kulcs használatával. Naplócsonkulási művelet eredménye egy base64 kódolású URL karakterláncot, amely majd feltöltheti egy titkos kulcsként használva a [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) parancsmag.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Használjon `$KeyEncryptionKey` és `$secretUrl` a következő lépésben [KEK használatával operációsrendszer-lemez csatolása](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Adjon meg egy titkos URL-cím, egy operációsrendszer-lemez csatolása
#### <a name="without-using-a-kek"></a>Egy KEK használata nélkül
Amíg az operációsrendszer-lemezképet szeretne csatolni, kell átadni `$secretUrl`. Az URL-címet a "lemez-titkosítás a KEK nem titkosított titkos" szakaszban hozott létre.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Egy KEK használatával
Az operációsrendszer-lemez csatolása átadni `$KeyEncryptionKey` és `$secretUrl`. Az URL-címet a "lemez-titkosítás a KEK nem titkosított titkos" szakaszban hozott létre.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Ez az útmutató letöltése
Ez az útmutató a programot letöltheti a [TechNet Gallery](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>További információ
[Fedezze fel az Azure Disk Encryption Azure PowerShell - 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Fedezze fel az Azure Disk Encryption Azure PowerShell - 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
