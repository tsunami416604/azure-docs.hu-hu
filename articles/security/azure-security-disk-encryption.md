---
title: Az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek áttekintést nyújt.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: devtiw
ms.openlocfilehash: f350716d0ca906376f3eadce9e117694ff14515c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "35756396"
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek
A Microsoft Azure számára kifejezetten fontos, hogy az adatvédelem, az adatok elkülönítése, és lehetővé teszi, hogy az Azure-ban üzemeltetett számos adatokat vezérlő speciális technológiák titkosításához, szabályozhatja, és kezelheti a titkosítási kulcs, biztosítva az adatok hozzáférését a ellenőrzés és naplózás. Ez rugalmasságának köszönhetően az Azure ügyfelei üzleti igényeiknek leginkább megfelelő megoldást választhatja. Ebből a cikkből hogy kódelemeit új technológiai megoldásokat "Az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gép a" a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok biztonsága érdekében. A tanulmány nyújt részletes útmutatást a az Azure disk encryption szolgáltatásai többek között a támogatott forgatókönyveket és a felhasználói élmény.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Áttekintés
Az Azure Disk Encryption egy olyan új képesség, amely segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. Az Azure Disk Encryption kihasználja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban.

Az Azure disk encryption Windows és Linux rendszerű IaaS virtuális gépekhez már az **általános rendelkezésre állás** minden nyilvános Azure-régióban és a premium storage Standard virtuális gépek és virtuális gépek AzureGov-régiókban.

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit.


### <a name="encryption-scenarios"></a>Titkosítási megoldások
Az Azure Disk Encryption megoldás a következő ügyfél-forgatókönyveket teszi lehetővé:

* Előzetes titkosítással VHD- és titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek titkosításának engedélyezése
* A támogatott Azure-gyűjtemény lemezképei alapján létrehozott új IaaS virtuális gépek titkosításának engedélyezése
* Azure-ban futó meglévő IaaS virtuális gépek titkosításának engedélyezése
* Windows IaaS virtuális gépek titkosításának letiltása
* Linux rendszerű IaaS virtuális gépek adatmeghajtók titkosításának letiltása
* Felügyelt lemezes virtuális gépek titkosításának engedélyezése
* Egy meglévő titkosított prémium és a virtuális Gépet nem prémium szintű storage titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás titkosított virtuális gépek

A megoldás a következő esetekben ha engedélyezve vannak a Microsoft Azure IaaS virtuális gépeket támogatja:

* Az Azure Key Vault-integráció
* Standard szintű virtuális gépek: [A, D, DS, G, GS, F és egyebekben sorozatú IaaS virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Windows és Linux rendszerű IaaS virtuális gépek és a támogatott Azure-katalógus rendszerképeket a felügyelt lemezes virtuális gépek titkosításának engedélyezése
* Tiltsa le a Windows IaaS virtuális gépek és a felügyelt lemezes virtuális gépek operációsrendszer- és meghajtók titkosítását
* A Linux rendszerű IaaS virtuális gépek és a felügyelt lemezes virtuális gépek adatmeghajtók titkosításának letiltása
* Windows ügyfél operációs rendszert futtató IaaS virtuális gépek titkosításának engedélyezése
* Kötet csatlakoztatási elérési úttal titkosításának engedélyezése
* Linux rendszerű virtuális gépek lemezzel konfigurált titkosításának csíkozást (RAID) mdadm használatával
* Linux rendszerű virtuális gépekkel LVM adatlemezek titkosításának engedélyezése
* Az operációs rendszer és az adatlemezek Linux LVM 7.3 titkosításának engedélyezése 
* A tárolóhelyekkel konfigurált Windows virtuális gépek titkosításának engedélyezése
* Egy meglévő titkosított prémium és a virtuális Gépet nem prémium szintű storage titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás, a titkosított virtuális gépek nem-KEK és KEK-forgatókönyvekhez (KEK - kulcstitkosítási kulcs)
* Minden Azure-beli nyilvános és AzureGov régiókban érhető el

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológiák:

* Alapszintű csomag IaaS virtuális gépek
* Linux rendszerű IaaS virtuális gépek egy operációs rendszer meghajtójának titkosításának letiltása
* Adatmeghajtók titkosításának letiltása, ha az operációs rendszer meghajtójának titkosított Linux rendszerű Iaas virtuális gépekhez
* A klasszikus virtuális gép létrehozási módszer használatával létrehozott IaaS virtuális gépeken
* Windows és Linux rendszerű IaaS virtuális gépek ügyfél egyéni rendszerképek nem támogatja a titkosítást.
* Integráció a helyszíni kulcskezelő szolgáltatás
* Az Azure Files (megosztott fájlrendszert), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows virtuális gépek, amelyeken a szoftveres RAID-rendszerek

### <a name="encryption-features"></a>Titkosítási funkciók
Ha engedélyezi, és üzembe helyezése az Azure Disk Encryption az Azure IaaS virtuális gépek, a következő képességeket engedélyezve vannak, a megadott konfigurációtól függően:

* Az operációsrendszer-kötet védelmét a rendszerindító kötet a tárolás titkosítása
* Adatkötetek védelme érdekében az adatkötetek a tárolás titkosítása
* A Windows IaaS virtuális gépek az operációsrendszer- és meghajtók titkosításának letiltása
* Az adatok titkosításának letiltása meghajtók Linux rendszerű IaaS virtuális gépekhez (csak akkor, ha az operációs rendszer a nem titkosított meghajtó)
* A titkosítási kulcsok és titkos kulcsokat a key vault-előfizetés a védelme
* Jelentéskészítés a titkosított IaaS virtuális gépek lemeztitkosítási állapotát
* Lemeztitkosítás – az IaaS virtuális gép konfigurációs beállítások eltávolítása
* Biztonsági mentés és visszaállítás az Azure Backup szolgáltatás használatával titkosított virtuális gépek

Az Azure Disk Encryption Windows és Linux-megoldás IaaS virtuális gépekhez tartalmazza:

* A Windows-lemeztitkosítás bővítményt.
* A lemeztitkosítás bővítmény linuxhoz.
* Lemeztitkosítás – PowerShell-parancsmagok.
* A lemeztitkosítási parancsmagok az Azure parancssori felület (CLI).
* A lemeztitkosítás – Azure Resource Manager-sablonok.

Az Azure Disk Encryption megoldás Windows vagy Linux operációs rendszert futtató IaaS virtuális gépek esetén támogatott. A támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg az "Előfeltételek" szakaszban.

> [!NOTE]
> Nem jár további költségekkel Virtuálisgép-lemezek az Azure Disk Encryption titkosításához.

### <a name="value-proposition"></a>Értékajánlat
Amikor alkalmazza az Azure Disk Encryption-felügyeleti megoldás, a következő üzleti igényekre is képes eleget tenni:

* IaaS virtuális gépek az inaktív biztonságosak, mert az iparági szabványnak megfelelő titkosítási technológia használatával oldja meg a szervezeti biztonsági és megfelelőségi követelményeknek.
* IaaS virtuális gépek rendszerindítási alapján az ügyfél által felügyelt kulcsok és szabályzatok, és használatuk a key vaultban lévő naplózhatók.

### <a name="encryption-workflow"></a>Titkosítási munkafolyamat
Ahhoz, hogy a lemeztitkosítás Windows és Linux rendszerű virtuális gépekhez, tegye a következőket:

1. Válassza ki az előző titkosítási megoldások közül egy titkosítási forgatókönyvet.
2. Részvétel a engedélyezése lemeztitkosítás keresztül az Azure Disk Encryption Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a CLI-parancsot, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfiguráció engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást az IaaS virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyeken már fut az Azure-ban.

3. Hozzáférést biztosít az Azure platform a titkosítási kulcs anyag (BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-hozzáférési kódot olvasni az IaaS virtuális gép titkosításának a key vaultban.

4. Adja meg az Azure Active Directory (Azure AD) identitása a titkosítási kulcsot a key vault anyag írni. Ez lehetővé teszi az IaaS virtuális Gépen, a 2. lépésben említett forgatókönyvek esetén a titkosítást.

5. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.

 ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>A visszafejtés munkafolyamat
Lemeztitkosítás IaaS virtuális gépekhez letiltásához kövesse az alábbi magas szintű lépéseket:

1. Letiltja a titkosítást (visszafejtési) válassza az Azure-ban futó IaaS virtuális gépen az Azure Disk Encryption Resource Manager-sablon vagy a PowerShell-parancsmagok használatával, és adja meg a visszafejtési konfigurációt.

 Ebben a lépésben letiltja a titkosítást az operációs rendszer vagy adatmennyiség, illetve mindkettőt a futó Windows IaaS virtuális gépen. Azonban az előző szakaszban említett operációs rendszer lemeztitkosítás linuxos letiltása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtókon a Linux rendszerű virtuális gépek mindaddig, amíg az operációsrendszer-lemez nincs titkosítva.
2. Az Azure frissíti a virtuális gép modell, és az IaaS virtuális gép visszafejtett van megjelölve. A virtuális gép tartalmát már nem titkosított inaktív.

> [!NOTE]
> A disable-titkosítási művelet nem törli a kulcstartót és a titkosítási kulcs adatai (BitLocker titkosítási kulcsok Windows rendszerekhez) vagy a Linux-jelszó.
 > Linux operációs rendszer lemez titkosításának letiltása nem támogatott. A visszafejtési lépés csak a Linux rendszerű virtuális gépek adatmeghajtók engedélyezett.
Adatok lemeztitkosítás linuxos letiltása nem támogatott, ha az operációs rendszer meghajtójának titkosítva van.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt engedélyezné az Azure Disk Encryption az Azure IaaS virtuális gépekhez a támogatott forgatókönyvek esetén az "Áttekintés" szakaszban tárgyalt, tekintse meg a következő előfeltételek vonatkoznak:

* Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.
* Az Azure Disk Encryption esetében támogatott a Windows Server következő verziói: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016-ban.
* Az Azure Disk Encryption Windows ügyfél a következő verziók esetében támogatott: Windows 8 és Windows 10-es ügyfélprogram.

> [!NOTE]
> A Windows Server 2008 R2, a .NET-keretrendszer 4.5 telepítve van az Azure-ban titkosítás engedélyezése előtt kell rendelkeznie. Telepítheti a Windows Update telepít a választható frissítést a Microsoft .NET-keretrendszer 4.5.2-es verziója a Windows Server 2008 R2 x64 alapú rendszerekhez ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Az Azure Disk Encryption, csak a meghatározott Azure-katalógus-alapú Linux-kiszolgáló disztribúciók és verziók.  A jelenleg támogatott verziók listájáért tekintse meg a [Azure Disk Encryption – gyakori kérdések](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq).

* Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés.

> [!NOTE]
> Az erőforrások konfigurálását külön régiókban okoz a az Azure Disk Encryption engedélyezésével.

* Telepítése és konfigurálása a key vault az Azure Disk Encryption, további tájékoztatást **beállítása beállítása és konfigurálása a key vault az Azure Disk Encryption** a a *Előfeltételek* című szakaszát.
* Állítsa be, és az Azure AD-alkalmazás konfigurálása az Azure Active Directoryban az Azure Disk Encryption, további tájékoztatást **állítsa be az Azure AD-alkalmazást az Azure Active Directoryban** a a *Előfeltételek* szakasza Ez a cikk.
* Állítsa be, és az Azure AD-alkalmazás számára a kulcstartó hozzáférési szabályzat konfigurálása, lásd a szakasz **a kulcstartó hozzáférési szabályzatot az Azure AD-alkalmazás beállítása** a a *Előfeltételek* című szakaszát.
* Előzetes titkosítással Windows virtuális merevlemez előkészítése, további tájékoztatást **előzetes titkosítással Windows virtuális merevlemez előkészítése** a a *függelék*.
* Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése, további tájékoztatást **előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése** a a *függelék*.
* Az Azure platform hozzá kell férnie a titkosítási kulcsok vagy titkos kulcsokat tárol a kulcstárolóban, így a virtuális gép számára elérhető, amikor elindul és visszafejti a virtuális gép operációsrendszer-kötet. Az Azure platform engedélyeket, állítsa be a **EnabledForDiskEncryption** tulajdonság a key vaultban. További információkért lásd: **beállítása beállítása és konfigurálása a key vault az Azure Disk Encryption** a függelékben.
* A key vault titkos kulcsából, és KEK URL-címek verziószámmal kell lennie. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

  * Példa: egy érvényes titkos URL-címe:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Példa: egy érvényes KEK URL-cím:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption nem támogatja a key vault titkos kódok és KEK URL-címek részeként megadásához használt portszámokat. Nem támogatott, és támogatott a key vault URL-címek példákért tekintse meg a következőket:

  * Érvénytelen a key vault URL-címe  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható a key vault URL-címe:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Ahhoz, hogy az Azure Disk Encryption szolgáltatást, az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:
  * Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  * A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  * Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.

  > [!NOTE]
  > Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről.
  >
  >Konfigurálása és az Azure Key Vault eléréséhez () egy tűzfal mögötthttps://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* A legújabb Azure PowerShell SDK-verziójának segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Nem támogatott az Azure Disk Encryption [Azure PowerShell SDK 1.1.0-s](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Ha azért küldtük Önnek, 1.1.0-s Azure PowerShell-lel kapcsolatos hibát, lásd: [Azure lemez titkosítási hiba kapcsolódó az Azure PowerShell 1.1.0-s](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Azure CLI bármely parancsához futtatásához, és társíthatja azt az Azure-előfizetéshez, először telepítenie kell az Azure CLI:
  * Azure CLI telepítése, és társíthatja azt az Azure-előfizetéséhez, tekintse meg a [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md).
  * Az Azure parancssori felület Mac, Linux és Windows az Azure Resource Manager használatához lásd: [Resource Manager módban az Azure CLI-parancsok](../virtual-machines/azure-cli-arm-commands.md).

* Ha egy felügyelt lemez titkosítása, kötelező előfeltétel, hogy a felügyelt lemez pillanatképét vagy biztonsági másolatot a lemezen kívül az Azure Disk Encryption titkosítás engedélyezése előtt.  A hely biztonsági másolat nélkül bármilyen titkosítás során váratlan hiba lehetséges, hogy jelennek meg a lemez és a virtuális gép egy helyreállítási kapcsoló nélkül nem érhető el.  Jelenleg nem felügyelt lemezek biztonsági mentése a Set-azurermvmdiskencryptionextension parancs, és hiba lesz, ha egy felügyelt lemezt használja, ha a - skipVmBackup paraméter lett megadva.  Ez a paraméter nem biztonságos, használja a biztonsági mentés kívül az Azure Disk Encryption már megtörtént.   Ha a - skipVmBackup paraméter meg van adva, a parancsmag nem hajt végre a felügyelt lemez titkosítási előtt biztonsági másolatot.  Ebből kifolyólag tekintendő kötelező előfeltétel, hogy biztonsági másolatot a felügyelt lemezes virtuális gép abban az esetben a helyreállítási később szükség van, amely lehetővé teszi, hogy az Azure Disk Encryption előtt érvényben van.  
> [!NOTE]
 > A - skipVmBackup paraméter soha nem kell használni, kivéve, ha egy pillanatkép vagy a biztonsági mentés már megtörtént kívül az Azure Disk Encryption. 

* Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakoztatott virtuális gépeket, a nem ügyfélleküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://technet.microsoft.com/library/ee706521).
* Egyéni csoportházirend-tartományhoz csatlakoztatott virtuális gépeken a BitLocker-házirendnek tartalmaznia kell a következő beállítást: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` az Azure Disk Encryption sikertelen lesz, ha egyéni csoportházirend-beállítások a BitLocker nem kompatibilis. A megfelelő házirend nem rendelkező gépek a beállítást, az új szabályzatot, az új szabályzat frissítése (a gpupdate.exe/Force) kényszerítése és indítsa újra lehet szükség.  
* Szeretne létrehozni egy Azure AD-alkalmazást, hozzon létre egy kulcstartót, vagy egy meglévő key vault létrehozása, és engedélyezze a titkosítást, olvassa el a [az Azure Disk Encryption előfeltétel-ellenőrzési PowerShell-parancsprogram](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Az Azure CLI-vel lemeztitkosítás – Előfeltételek konfigurálása, lásd: [a Bash-szkript](https://github.com/ejarvi/ade-cli-getting-started).
* Az Azure Backup szolgáltatás segítségével biztonsági mentése és visszaállítása titkosított virtuális gépek esetén, amikor az Azure Disk Encryption titkosítás engedélyezett, a virtuális gépek titkosítása az Azure Disk Encryption-kulcs konfigurációjától használatával. A Backup szolgáltatás támogatja a no-KEK- vagy KEK-konfigurációk használatával titkosított virtuális gépek. Lásd: [biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure Backup-titkosítás](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* A Linux operációs rendszer kötetének titkosításához, vegye figyelembe, hogy indítsa újra a virtuális gép jelenleg szükség a folyamat végén. Ezt megteheti a portal, powershell vagy parancssori felület használatával.   A titkosítási folyamat nyomon követéséhez rendszeres időközönként lekérdezi a Get-AzureRmVMDiskEncryptionStatus által visszaadott állapotüzenet https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Titkosítás után ez a parancs által visszaadott állapotüzenet ez fogja jelezni. Például "Feladatnézetben: sikerült az operációsrendszer-lemeze titkosítva, indítsa újra a virtuális gép" ezen a ponton a virtuális gép újraindítása és használják.  

* Az Azure Disk Encryption for Linux Linux rendszerű csatlakoztatott fájlrendszer rendelkezik előtt titkosítási adatok lemezt igényel

* Rekurzív módon csatlakoztatott lemezek nem támogatja az Azure Disk Encryption for Linux adatokat. Ha például a célrendszer rendelkezik csatlakoztatott lemez/foo/sáv és majd egy másik /foo/bar/baz, /foo/bar/baz titkosítása a sikeres lesz, de titkosítási/foo/sáv sikertelen lesz. 

* Az Azure Disk Encryption csak a támogatott Azure-katalógus-rendszerképek, amely megfelel a fenti előfeltételeknek támogatott. Egyéni rendszerképek ügyfél egyéni partíciósémák, és előfordulhat, hogy ezek a lemezképek a létező folyamat viselkedések miatt nem támogatott. További még akkor is image z galerie-alapú virtuális gép, amely kezdetben előfeltételek teljesülnek, de lehet, hogy nem kompatibilis létrehozása után módosították.  Amely OK, a javasolt eljárás a Linux rendszerű virtuális gép titkosítására, hogy kezdhet tiszta katalóguslemezt, a virtuális gép titkosítása és egyéni szoftver vagy adatok adja hozzá a virtuális géphez, igény szerint.  

* Az Azure Disk Encryption és helyi adatmennyiség - rendelkeznek BEk-kel köteten, a Windows és a/mnt/azure_bek_disk Linux rendszerű IaaS virtuális gépek a titkosítási kulcs biztonságos tárolására. Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket. INFORMÁCIÓS fájl tartalmazza a kötet tartalmaz további részleteket.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Az Azure Active Directoryban az Azure AD-alkalmazás beállítása
Engedélyezni kell az Azure-ban futó virtuális gépek titkosítási van szüksége, amikor az Azure Disk Encryption állít elő, és ír a titkosítási kulcsok a key vaultban. Az Azure AD-hitelesítés kezelése a titkosítási kulcsok a key vaultban lévő igényel.

Erre a célra hozzon létre egy Azure AD-alkalmazást. Részletes lépéseket megtalálja az "Első az identitás a az alkalmazás" szakaszban, a blogbejegyzés az alkalmazások regisztrálásának [Azure Key Vault - lépésről lépésre](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Ebben a bejegyzésben számos beállítása és konfigurálása a key vault hasznos példákat is tartalmaz. Hitelesítési célra használhatja az ügyfél titkos kulcs alapú hitelesítés vagy az Azure AD ügyféltanúsítvány-alapú ügyfél-hitelesítéshez.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Ügyfél titkos kulcs alapú hitelesítés az Azure ad-hez
A következő szakaszok segítségével az Azure ad egy ügyfél titkos kulcs alapú hitelesítés konfigurálása.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Hozzon létre egy Azure AD-alkalmazást az Azure PowerShell-lel
A következő PowerShell-parancsmag segítségével hozzon létre egy Azure AD-alkalmazást:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $aadClientSecret $azureAdApplication.ApplicationId az Azure AD ClientID és a titkos ügyfélkulcsot, amely az Azure Disk Encryption engedélyezése később használjon. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Az Azure Portalról az Azure AD ügyfél-azonosító és titkos kulcs beállítása
Akkor is állíthat be az Azure AD ügyfél-Azonosítót és a titkos kulcsot az Azure Portal használatával. Ez a feladat végrehajtásához tegye a következőket:

1. Válassza ki **minden szolgáltatás > az Azure Active Directory**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Válassza ki **alkalmazásregisztrációk > új alkalmazás regisztrálása**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Adja meg a kért információkat, és az alkalmazás létrehozása:

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Válassza ki az újonnan létrehozott alkalmazást, hogy megtekinthesse a tulajdonságait, beleértve az alkalmazás azonosítóját.  Az alkalmazás egy kulcs létrehozásához válassza **beállítások > kulcsok**, adjon hozzá egy leírást és a kulcs lejárati, és kattintson a **mentése**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Másolja ki a létrehozott titkos értéket, és megfelelő védelme érdekében.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>A meglévő alkalmazások használata
Hajtsa végre a következő parancsokat, beszerzése és használata a [Azure AD PowerShell-modul](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> A következő parancsokat egy új PowerShell-ablakot a kell végrehajtani. Ne használja az Azure PowerShell vagy az Azure Resource Manager-ablakban, hajtsa végre a parancsokat. Ez a módszer azt javasoljuk, mivel ezeket a parancsmagokat az MSOnline modul vagy az Azure AD PowerShell-lel.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Az Azure ad-alapú hitelesítés
> [!NOTE]
> Az Azure AD-alapú hitelesítés jelenleg nem támogatott Linuxos virtuális gépeken.

A következő szakaszok bemutatják, hogyan konfigurálja a Tanúsítványalapú hitelesítés az Azure ad.

##### <a name="create-an-azure-ad-application"></a>Az Azure AD-alkalmazás létrehozása
Hozzon létre egy Azure AD-alkalmazáshoz, hajtsa végre a következő PowerShell-parancsmagok:

> [!NOTE]
> Cserélje le a következő `yourpassword` a biztonságos jelszó karakterlánc, és megakadályozhatja a jelszót.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Miután elvégezte ezt a lépést, a PFX-fájlok feltöltése a key vaultban, és üzembe helyezéséhez a tanúsítvány virtuális géphez szükséges hozzáférési szabályzatának engedélyezéséhez.

##### <a name="use-an-existing-azure-ad-application"></a>Használjon egy meglévő Azure AD-alkalmazás
Ha Tanúsítványalapú hitelesítés egy meglévő alkalmazáshoz konfigurál, az itt látható PowerShell-parancsmag használatával. Mindenképpen hajtsa végre őket egy új PowerShell-ablakot.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Miután elvégezte ezt a lépést, PFX fájl feltöltése a key vaultban, és a tanúsítvány telepítése egy virtuális géphez szükséges hozzáférési szabályzatának engedélyezéséhez.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>A PFX-fájlok feltöltése a key vaulttal
Ez a folyamat részletes leírását lásd: [a hivatalos Azure Key Vaultért felelős csapat blogjára](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Azonban a következő PowerShell-parancsmagok lesz szüksége a feladat. Mindenképpen végre őket az Azure PowerShell-konzolon.

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

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>A key vaultban lévő tanúsítványt egy meglévő virtuális gép üzembe helyezése
Miután elvégezte a PFX feltöltése, telepítsen központilag egy tanúsítványt a key vaultban egy meglévő virtuális Gépet a következő:
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

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>A kulcstartó hozzáférési szabályzatot az Azure AD-alkalmazás beállítása
Az Azure AD-alkalmazást a és a tárolóban lévő titkos hozzáférési jogokkal kell. Használja a [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) engedélyeket az alkalmazást, az ügyfél-azonosító (amely lett létrehozva, amikor az alkalmazás regisztrálva lett) használatával a parancsmag a _– ServicePrincipalName_ paraméter értéke. További tudnivalókért tekintse meg a következő blogbejegyzésben: [Azure Key Vault - lépésről lépésre](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). A következő példa bemutatja, hogyan Powershellen keresztül a feladat végrehajtásához:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Az Azure Disk Encryption megköveteli, hogy az Azure AD-ügyfélalkalmazásnak a következő hozzáférési szabályzatok konfigurálhatók: _WrapKey_ és _beállítása_ engedélyeket.

## <a name="terminology"></a>Terminológia
Ez a technológia által használt gyakori kifejezések megérteni, használja a következő terminológia táblázat:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Az Azure AD egy [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Az Azure AD-fiókja hitelesítéséhez, tárolására és titkos kódok lekérése a key vault előfeltétele. |
| Azure Key Vault | A Key Vault titkosítási, key management szolgáltatása a Federal Information Processing szabványok FIPS szabványú hardveres biztonsági modulok, mellyel a kriptográfiai kulcsok és titkok bizalmas alapul. További információkért lásd: [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| ARM | Azure Resource Manager |
| BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparági ismerhető fel Windows kötet titkosítási technológia, amely lehetővé teszi a lemeztitkosítást a Windows IaaS virtuális gépeket, hogy. |
| BEK | A BitLocker titkosítási kulcsokkal titkosítása az operációs rendszer rendszerindító kötet és adatkötetek. A BitLocker-kulcsok számára biztosít védelmet a kulcstartóban található titkos kódként. |
| parancssori felület | Lásd: [Azure parancssori felület](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) a Linux-alapú, átlátható lemeztitkosítás alrendszer, amely engedélyezi a lemeztitkosítást a Linuxos IaaS virtuális gépek szolgál. |
| KEK | Kulcsalapú titkosítás kulcsa az az aszimmetrikus kulcs (RSA 2048), amellyel védelméhez vagy burkolásához a titkos kulcsot. Megadhat egy hardveres biztonsági modulok (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További részletekért lásd: [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| Powershell parancsmagjaival | Lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>És az Azure Disk Encryption a key vault beállítása
Az Azure Disk Encryption segítségével a biztonságosabb a lemeztitkosítási kulcsokat és titkos kulcsokat tárol a kulcstárolóban. A key vault beállítása az Azure Disk Encryption, hajtsa végre a következő szakaszok minden.

#### <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, használja az alábbi lehetőségek egyikét:

* ["101-Key-Vault-" Resource Manager-sablon létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Az Azure key vault PowerShell-parancsmagok](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Hogyan [kulcstartó védelme](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Ha már beállította a key vault létrehozása az előfizetéshez tartozó, ugorjon a következő szakaszban.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Állítsa be a kulcsalapú titkosítás kulcsa (nem kötelező)
Ha azt szeretné, egy további BitLocker titkosítási kulcsok biztonsági réteget egy KEK használandó, egy KEK hozzáadása a key vaultban. Használja a [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) parancsmaggal hozzon létre egy fő titkosítási kulcsot a key vaultban. A helyszíni kulcskezelő HSM is importálhat egy KEK. További részletekért lásd: [Key Vault-dokumentáció](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

A KEK adhat hozzá, az Azure Resource Manager-címen vagy a key vault-felület használatával.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>A key vault-engedélyek beállítása
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. Az Azure platform engedélyeket, állítsa be a **EnabledForDiskEncryption** tulajdonság a kulcs táron a key vault PowerShell-parancsmag segítségével:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Is beállíthat a **EnabledForDiskEncryption** funkcionáló tulajdonság a [Azure erőforrás-kezelő](https://resources.azure.com).

Ahogy korábban említettük, be kell állítani a **EnabledForDiskEncryption** a kulcstartóra vonatkozó tulajdonság. Ellenkező esetben a telepítés sikertelen lesz.

Állíthat be hozzáférési szabályzatokat az Azure AD-alkalmazást a key vaulttal felhasználói felületről, az itt látható módon:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Az a **speciális hozzáférési szabályzatok** lapra, győződjön meg arról, hogy a kulcstartó engedélyezve van-e az Azure Disk Encryption:

![Az Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Lemeztitkosítás – központi telepítési forgatókönyvei és a felhasználói élmény
Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok a forgatókönyvek részletesebben ismerteti.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Új IaaS virtuális gépen a Marketplace-ről létrehozott titkosítás engedélyezése
Lemeztitkosítás IaaS a piactérről, az Azure-ban új Windows virtuális gépen használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Kattintson az Azure gyors üzembe helyezési sablon **üzembe helyezés az Azure**, írja be a titkosítási konfiguráció a **paraméterek** panelről, és kattintson **OK**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, jogi feltételeket és szerződés, és kattintson a **létrehozás** engedélyezheti a titkosítást egy új IaaS virtuális gépen.

> [!NOTE]
> Ez a sablon létrehoz egy új titkosított Windows virtuális Gépet, amely a Windows Server 2012 image z galerie használja.

Ennek használatával engedélyezheti a lemeztitkosítást a egy új IaaS Red Hat Linux 7.2 rendszerű virtuális Gépet egy 200 GB-os RAID-0 tömbbel rendelkező [Resource Manager-sablon](https://aka.ms/fde-rhel). Után a sablon üzembe helyezéséhez, ellenőrizze a virtuális gép titkosítási állapotát a `Get-AzureRmVmDiskEncryptionStatus` leírtak szerint a parancsmag [futó Linux rendszerű virtuális gépek a titkosított operációsrendszer-meghajtók](#encrypting-os-drive-on-a-running-linux-vm). Ha a gép egy állapotának visszaadása _VMRestartPending_, indítsa újra a virtuális Gépet.

Az alábbi táblázat a Resource Manager-Sablonparaméterek az új virtuális gépek a Marketplace-en forgatókönyvet használja az Azure AD ügyfél-azonosító:

| Paraméter | Leírás |
| --- | --- |
| adminUserName | A virtuális gép rendszergazdai felhasználóneve. |
| adminPassword | A virtuális gép rendszergazdai felhasználójának jelszava. |
| newStorageAccountName | Neve a tárfiók operációsrendszer- és virtuális merevlemezek tárolásához. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, D és G sorozat támogatottak. |
| virtualNetworkName | Neve a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia. |
| subnetName | A virtuális hálózat, amely a virtuális gép hálózati Adaptert kell tartoznia az alhálózat neve. |
| AADClientID | Ügyfél-Azonosítóját az Azure AD-alkalmazást, amely jogosult a kulcstartó titkos kódok írni. |
| AADClientSecret | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultURL | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs URL-címe. A parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a generált (nem kötelező) a BitLocker-kulcs titkosításához használt URL-címe. |
| keyVaultResourceGroup | A kulcstároló erőforráscsoport. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Is tenné a saját KEK számára további védelmi a szolgáltatásadat-titkosítási kulcs (a jelszó titkos kódot) tárol a kulcstárolóban.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Új IaaS virtuális gépek ügyfél-titkosítású VHD- és titkosítási kulcsok alapján létrehozott titkosításának engedélyezése
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik.

Kövesse az utasításokat az ezekben a szakaszokban használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről az egyik. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](#preparing-a-pre-encrypted-windows-vhd)
* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használatával
Ön lemeztitkosítás a titkosított virtuális merevlemezen használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Kattintson az Azure gyors üzembe helyezési sablon **üzembe helyezés az Azure**, írja be a titkosítási konfiguráció a **paraméterek** panelről, és kattintson **OK**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, jogi feltételeket és szerződés, és kattintson a **létrehozás** engedélyezheti a titkosítást az új IaaS virtuális gépen.

A következő táblázat felsorolja a Resource Manager-sablon paramétereit a titkosított virtuális merevlemez:

| Paraméter | Leírás |
| --- | --- |
| newStorageAccountName | A tárfiók tárolja a titkosított virtuális Rendszerlemez neve. Ezt a tárfiókot kell már létrejöttek az ugyanabban az erőforráscsoportban és ugyanazon a helyen a virtuális géppel. |
| osVhdUri | URI-ját a rendszert tartalmazó virtuális Merevlemezt a tárfiókból. |
| osType | Az operációs rendszer terméktípusa (Windows/Linux). |
| virtualNetworkName | Neve a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia. A név kell már létrejöttek az ugyanabban az erőforráscsoportban és ugyanazon a helyen a virtuális gép. |
| subnetName | A-alhálózatot a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia a neve. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, D és G sorozat támogatottak. |
| keyVaultResourceID | Az erőforrás-azonosító, amely azonosítja a kulcstároló erőforrása az Azure Resource Manager. A PowerShell-parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL-címe a lemez-titkosítási kulcs, amely a key vaultban van beállítva. |
| keyVaultKekUrl | A kulcsalapú titkosítás kulcsa a generált lemez-titkosítási kulcs titkosításához URL-címe. |
| vmName | Az IaaS virtuális gép nevét. |

#### <a name="using-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Parancssori felület parancsait használva
Ebben a forgatókönyvben a lemeztitkosítás engedélyezve a CLI-parancsokkal, tegye a következőket:

1. A kulcstartó hozzáférési házirendjeinek beállítása:

   * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Az Azure AD-alkalmazás titkos kódok írni a kulcstartó engedélyeinek beállítása:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Egy meglévő vagy futó virtuális gép titkosításának engedélyezéséhez írja be:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Titkosítás állapotának lekérése:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről titkosításának engedélyezéséhez használja az alábbi paramétereket a `azure vm create` parancsot:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Engedélyezze a titkosítást a meglévő vagy IaaS Windows virtuális gép futtatása az Azure-ban
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A következő szakaszok részletesen bemutatják, hogyan engedélyezheti a Resource Manager-sablon és a CLI-parancsok használatával.

#### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használatával
Disk encryption szolgáltatást meglévő vagy IaaS Windows virtuális gépek futtatása az Azure-beli használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Kattintson az Azure gyors üzembe helyezési sablon **üzembe helyezés az Azure**, írja be a titkosítási konfiguráció a **paraméterek** panelről, és kattintson **OK**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, jogi feltételeket és szerződés, és kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek a Resource Manager sablon paramétereit:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. |
| sequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Is tenné a saját KEK számára további védelmi a szolgáltatásadat-titkosítási kulcs (BitLocker titkosítási titkos kódot) a key vaultban.

#### <a name="using-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Parancssori felület parancsait használva
Ahhoz, hogy a meglévő vagy IaaS Windows virtuális gépek futtatásához az Azure parancssori felület parancsait használva a titkosítást, tegye a következőket:

1. Hozzáférési szabályzatok beállítása a key vaultban:
   * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Az Azure AD-alkalmazás titkos kódok írni a kulcstartó engedélyeinek beállítása:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Egy meglévő vagy futó virtuális gép titkosításának engedélyezése:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. A titkosítási állapot beolvasása:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről titkosításának engedélyezéséhez használja az alábbi paramétereket a `azure vm create` parancsot:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Engedélyezze a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen az Azure-ban
Lemeztitkosítás IaaS virtuális gépen meglévő vagy futó Linux rendszerű Azure-beli használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kattintson a **üzembe helyezés az Azure** a az Azure gyors üzembe helyezési sablon, adja meg a titkosítási konfiguráció a **paraméterek** panelről, és kattintson **OK**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, jogi feltételeket és szerződés, és kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek futtatása a Resource Manager-Sablonparaméterek:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes támogatott értékei a következők _operációs rendszer_ vagy _összes_ (lásd a támogatott Linux-disztribúciók és azok verzióinak prerequisiteis szakaszban korábban operációsrendszer- és adatlemezek számára). |
| sequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |
| hozzáférési kód | Írjon be egy erős jelszót a szolgáltatásadat-titkosítási kulcs. |

> [!NOTE]
> _KeyEncryptionKeyURL_ egy nem kötelező paraméter. Is tenné a saját KEK számára további védelmi a szolgáltatásadat-titkosítási kulcs (a jelszó titkos kódot) tárol a kulcstárolóban.

#### <a name="cli-commands"></a>CLI-parancsok
Engedélyezheti lemeztitkosítás a titkosított virtuális merevlemezen történő telepítéssel és a [CLI-paranccsal](../cli-install-nodejs.md). Ahhoz, hogy a meglévő vagy IaaS Linuxos virtuális gépek futtatása az Azure CLI-parancsok segítségével a titkosítást, tegye a következőket:

1. A kulcstartó hozzáférési házirendjeinek beállítása:

 * Állítsa be a **EnabledForDiskEncryption** jelző:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Az Azure AD-alkalmazás titkos kódok írni a kulcstartó engedélyeinek beállítása:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Egy meglévő vagy futó virtuális gép titkosításának engedélyezése:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Titkosítás állapotának lekérése:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Egy új virtuális Gépet, a titkosított virtuális merevlemezről titkosításának engedélyezéséhez használja az alábbi paramétereket a `azure vm create` parancsot:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Egy titkosított IaaS virtuális gép titkosítási állapotának lekérése
Az Azure Resource Managerrel, a titkosítási állapot kaphat [PowerShell-parancsmagok](/powershell/azure/overview), vagy a CLI-parancsokat. Az alábbi szakaszok ismertetik, hogyan használhatja az Azure Portal és CLI-parancsok a titkosítás állapotának beolvasása.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Az Azure Resource Managerrel egy titkosított Windows virtuális gép titkosítási állapotának lekérése
Megtekintheti az IaaS virtuális gépek lemeztitkosítási állapotát az Azure Resource Manager az alábbiak szerint:

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com/), és kattintson a **virtuális gépek** a bal oldali panelen, egy virtuális gépet az előfizetésében összefoglaló nézet megjelenítéséhez. Az előfizetés nevét, a kiválasztásával szűrheti a virtuális gépek megtekintése a **előfizetés** legördülő listából.

2. Felső részén a **virtuális gépek** kattintson **oszlopok**.

3. Az a **válasszon oszlop** panelen válassza ki **lemeztitkosítás**, és kattintson a **frissítés**. Megjelenik a lemeztitkosítási oszlop titkosítási állapotát megjelenítő _engedélyezve_ vagy _nincs engedélyezve a_ minden virtuális géphez, az alábbi ábrán látható módon:

 ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Lemeztitkosítás – PowerShell-parancsmag használatával egy titkosított (Windows/Linux) IaaS virtuális gép titkosítási állapotának lekérése
Az IaaS virtuális gépek lemeztitkosítási állapotát kérhet le a lemeztitkosítás – PowerShell-parancsmag `Get-AzureRmVMDiskEncryptionStatus`. A titkosítási beállítások a virtuális gép lekéréséhez adja meg a következőket:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Vizsgálhatja meg a kimenetét _Get-AzureRmVMDiskEncryptionStatus_ titkosítási kulcs URL-címeket.

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

A OSVolumeEncrypted és DataVolumesEncrypted beállítások értékek vannak beállítva _titkosított_, amely azt ismerteti, hogy mindkét kötetek vannak az Azure Disk Encryption használatával titkosított. Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Linuxos virtuális gépeken, percet vesz igénybe a három-négy a `Get-AzureRmVMDiskEncryptionStatus` parancsmag a titkosítási állapotát.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>A lemeztitkosítási CLI parancsból az IaaS virtuális gép titkosítási állapotának lekérése
Az IaaS virtuális gépek lemeztitkosítási állapotát megtekintheti a lemeztitkosítási CLI-paranccsal `azure vm show-disk-encryption-status`. A titkosítási beállítások a virtuális gép lekéréséhez adja meg az Azure CLI-munkamenetet:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Windows IaaS virtuális Gépen futó titkosításának letiltása
Az Azure Disk Encryption Resource Manager-sablon vagy a PowerShell-parancsmagok egy futó Windows vagy Linux rendszerű IaaS virtuális gép titkosításának letiltása, és adja meg a visszafejtési konfigurációt.

##### <a name="windows-vm"></a>Windows rendszerű virtuális gép
A disable-titkosítás lépés letiltja a titkosítást az operációs rendszer, az adatmennyiség vagy mindkettő a futó Windows IaaS virtuális gépen. Tiltsa le az operációsrendszer-kötet lehet, és hagyja meg a titkosított adatok kötet. A disable-titkosítás a lépést, ha a klasszikus Azure üzemi modell frissíti a virtuális gép modell, és a Windows IaaS virtuális gép visszafejtett van megjelölve. A virtuális gép tartalmát már nem titkosított inaktív. A titkosítást nem törli a kulcstartót és a titkosítási kulcs adatai (a BitLocker titkosítási kulcsokat a Windows és Linux-jelszó).

##### <a name="linux-vm"></a>Linux rendszerű virtuális gép
A disable-titkosítás lépés letiltja a titkosítást, a virtuális gépen futó Linux rendszerű IaaS adatmennyiség. Ez a lépés csak akkor működik, ha az operációsrendszer-lemez nincs titkosítva.

> [!NOTE]
> Az operációsrendszer-lemez titkosításának letiltása a Linux rendszerű virtuális gépek nem engedélyezett.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Egy meglévő vagy futó IaaS virtuális gép titkosításának letiltása
Letilthatja a lemeztitkosítást a Windows IaaS virtuális gépek futtatása a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Kattintson az Azure gyors üzembe helyezési sablon **üzembe helyezés az Azure**, adja meg a visszafejtési konfigurációs a **paraméterek** panelről, és kattintson **OK**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, jogi feltételeket és szerződés, és kattintson a **létrehozás** engedélyezheti a titkosítást egy új IaaS virtuális gépen.

Linux rendszerű virtuális gépekhez, letilthatja a titkosítás használatával az [futó Linux rendszerű virtuális gépek titkosításának letiltása](https://aka.ms/decrypt-linuxvm) sablont.

Az alábbi táblázat a Resource Manager sablon paramétereinek futó IaaS virtuális gépek titkosításának letiltása:

| Paraméter | Leírás |
| --- | --- |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani.
| volumeType | Egy visszafejtési műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. A Windows IaaS virtuális gép operációs rendszer és rendszerindító kötet futó anélkül, hogy letiltja a titkosítást a titkosítás nem tiltható le a _adatok_ kötet. Azt is vegye figyelembe, hogy az operációsrendszer-lemez titkosításának letiltása nem engedélyezett a Linux rendszerű virtuális gépekhez. |
| sequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor egy lemezt a visszafejtési művelet ugyanarról a virtuális gépről történik. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Egy meglévő vagy futó IaaS virtuális gép titkosításának letiltása
Egy meglévő vagy futó IaaS virtuális gép titkosításának letiltása a PowerShell-parancsmag használatával, lásd: [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Ez a parancsmag a Windows és Linux rendszerű virtuális gépek is támogatja. Titkosítás letiltásához a virtuális gép bővítmény telepíti azt. Ha a _neve_ paraméter nincs megadva, az alapértelmezett névvel rendelkező bővítmény _AzureDiskEncryption a Windows virtuális gépek_ jön létre.

Linuxos virtuális gépeken az AzureDiskEncryptionForLinux bővítményből szolgál.

> [!NOTE]
> Ez a parancsmag a virtuális gép újraindul.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Engedélyezheti a titkosítást előre titkosított IaaS virtuális gép Azure által felügyelt lemezzel
Előre titkosított virtuális merevlemezből található ARM-sablon használatával titkosított virtuális gép létrehozása az Azure által felügyelt lemez ARM-sablon használatával   
[Titkosított új felügyelt lemez létrehozása egy előzetes titkosítással VHD/storage-blobból] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Egy új Linux rendszerű IaaS virtuális gépen az Azure Managed Disk encryption engedélyezése
Az Azure által felügyelt lemez ARM-sablon használatával hozzon létre egy új titkosított Linuxos IaaS virtuális gép található ARM-sablon használatával   
[A teljes lemeztitkosítás RHEL 7.2 telepítése] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Engedélyezheti a titkosítást egy új Windows IaaS virtuális gép Azure által felügyelt lemezzel
 Az Azure által felügyelt lemez ARM-sablon használatával hozzon létre egy új titkosított Linuxos IaaS virtuális gép található ARM-sablon használatával   
 [Új titkosított Windows IaaS felügyelt lemez virtuális gép létrehozása katalógus-lemezkép] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt.  A felügyelt lemez pillanatképét elvégezhet a portálról, vagy az Azure Backup segítségével.  Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben.  Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával.  Ez a parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Egy meglévő titkosított nem prémium szintű virtuális gép titkosítási beállításainak módosítása
  Használhatja a meglévő Azure-lemez a titkosítás támogatott felületek a futó virtuális gép [PS parancsmagok, CLI és ARM sablonok] a titkosítási beállítások frissítéséhez, mint az AAD-Ügyfélazonosító azonosítója vagy titkos kódja Kulcsalapú titkosítás kulcsa [KEK], a BitLocker titkosítási kulcs a Windows virtuális gép vagy a Linux rendszerű virtuális gép jelszava stb. A frissítés titkosítási beállítás prémium és a nem prémium szintű tárolós virtuális gépek esetén támogatott.

## <a name="appendix"></a>Függelék
### <a name="connect-to-your-subscription"></a>Csatlakozás az előfizetéshez
Mielőtt folytatná, tekintse át a *Előfeltételek* szakasz ebben a cikkben. Miután meggyőződött arról, hogy minden előfeltétel teljesül-e, csatlakozzon az előfizetéséhez az alábbiak szerint:

1. Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő parancsot:

    `Connect-AzureRmAccount`

2. Ha több előfizetéssel rendelkezik, és adjon meg egy használni kívánt, írja be a következő, a fiókhoz tartozó előfizetések megtekintéséhez:

    `Get-AzureRmSubscription`

3. Válassza ki a használni kívánt előfizetést, írja be:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Győződjön meg arról, hogy helyesen szerepel-e beállítva az előfizetés, írja be:

    `Get-AzureRmSubscription`

5. Ellenőrizze az Azure Disk Encryption parancsmagjai telepítve vannak-e, írja be:

    `Get-command *diskencryption*`

6. Az alábbi Példakimenet megerősíti, hogy az Azure Disk Encryption PowerShell telepítése:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Előzetes titkosítással Windows virtuális merevlemez előkészítése
A következő szakaszok szükség annak előre titkosított Windows virtuális merevlemez előkészítése az Azure IaaS-titkosított merevlemezként üzembe helyezéshez. Az információk segítségével készítheti elő, és indítsa el az Azure Site Recovery vagy az Azure friss Windows virtuális gép (VHD).

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Nem TPM-OS védelem engedélyezése csoportházirend frissítése
A BitLocker csoportházirend-beállítás konfigurálása **a BitLocker meghajtótitkosítás**, amelyek alapján megtalálhatja **helyi számítógép-házirend** > **számítógép konfigurációja**  >  **Felügyeleti sablonok** > **Windows-összetevők**. Ez a beállítás módosítása **operációsrendszer-meghajtók** > **további hitelesítés indításkor szükséges** > **kompatibilisTPMnélküliBitLockerengedélyezése**, az alábbi ábrán látható módon:

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>A BitLocker a szolgáltatás-összetevők telepítése
Windows Server 2012 és újabb verziók használja a következő parancsot:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

A Windows Server 2008 R2 a következő paranccsal:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Készítse elő az operációsrendszer-kötet BitLocker segítségével `bdehdcfg`
Az operációs rendszer partíció tömörítése, és a gép előkészítése a BitLocker, hajtsa végre a következő parancsot:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Az operációsrendszer-kötet védelmét a BitLocker használatával
Használja a [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) paranccsal engedélyezheti a titkosítást a rendszerindító kötet egy külső kulcsvédő segítségével. A külső meghajtók vagy kötetek is elhelyezhető a külső kulcs (.bek fájl). Titkosítás után a következő újraindításkor engedélyezve van a rendszerlemez vagy rendszerindító köteten.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Készítse elő a virtuális Gépet egy külön data/erőforrás VHD-t a bitlockerrel a külső kulcs beolvasása.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Egy Linux virtuális gépen futó operációs rendszer meghajtójának titkosításához

##### <a name="prerequisites-for-os-disk-encryption"></a>Az operációs rendszer lemeztitkosítás előfeltételei

* A virtuális Gépet kell használnia egy kompatibilis az operációs rendszer lemeztitkosítás terjesztési felsorolt a [Azure Disk Encryption – gyakori kérdések](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* A virtuális Gépen a Marketplace-lemezképből az Azure Resource Manager kell létrehozni.
* Az Azure virtuális gép legalább 4 GB RAM (javasolt a mérete, 7 GB).
* (Az RHEL és CentOS) Tiltsa le a SELinux. Tiltsa le a SELinux, lásd: "4.4.2. Letiltás SELinux"az a [SELinux felhasználói és rendszergazdai útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális gépen.
* Miután letiltja az SELinux, legalább egyszer indítsa újra a virtuális gép.

##### <a name="steps"></a>Lépések
1. Virtuális gép létrehozása a korábban megadott disztribúció használatával.

 7.2 CentOS az operációs rendszer lemeztitkosítás támogatott keresztül egy rendszerképet. Ez a rendszerkép használatához adja meg a "7.2n" Termékváltozat, a virtuális gép létrehozásakor:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurálja a virtuális gép igény szerint. Ha a (operációs rendszer és összes adatok) titkosítása meghajtók, az adatmeghajtók kell lennie a megadott és a csatlakoztatható /etc/fstab fog.

 > [!NOTE]
 > Használja az UUID azonosítója =... megadásához adatmeghajtók az/etc/fstab fájlban a blokk-eszköz neve (például/dev/sdb1) megadása helyett. Titkosítás során meghajtók sorrendje módosítja, a virtuális gépen. Ha a virtuális gép egy adott rendelés blokkeszközöket támaszkodik, azt fogja tudni titkosítás után csatlakoztassa őket.

3. Jelentkezzen ki az SSH-munkamenetet.

4. Titkosítása az operációs rendszer, adja meg, mint volumeType **összes** vagy **operációs rendszer** amikor, [engedélyezze a titkosítást](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Az összes felhasználói térben futó folyamatok, nem, `systemd` szolgáltatások le fognak állni a egy `SIGKILL`. Indítsa újra a virtuális Gépet. Ha engedélyezi az operációs rendszer lemeztitkosítás egy futó virtuális gépen, tervezze meg a virtuális gépek üzemszünete.

5. Rendszeres időközönként szakaszban foglaltak szerint a titkosítás állapotának figyelése a [következő szakasz](#monitoring-os-encryption-progress).

6. Miután a Get-AzureRmVmDiskEncryptionStatus "VMRestartPending" jeleníti meg, indítsa újra a virtuális gép jelentkezik be, vagy a portal, PowerShell vagy parancssori felület használatával.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Indítsa újra, mielőtt azt javasoljuk, hogy mentse [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) a virtuális gép.

#### <a name="monitoring-os-encryption-progress"></a>Az operációs rendszer titkosítási folyamat figyelése
Az operációs rendszer titkosítási folyamat három módon figyelheti:

* Használja a `Get-AzureRmVmDiskEncryptionStatus` parancsmagot, és vizsgálja meg a Feladatnézetben mező:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Miután a virtuális gép eléri a "Az operációs rendszer lemezén titkosítás lépései", körülbelül 40 – 50 percet vesz igénybe egy Premium Storage virtuális gépek biztonsági.

 Mert [#388 ki](https://github.com/Azure/WALinuxAgent/issues/388) WALinuxAgent, a `OsVolumeEncrypted` és `DataVolumesEncrypted` szabályzatként jelenik meg `Unknown` az egyes disztribúciók. A WALinuxAgent verzió 2.1.5, és később, a probléma automatikusan megoldódik. Ha látja `Unknown` a kimenetben ellenőrizheti lemeztitkosítási állapotát az Azure Resource Explorer használatával.

 Lépjen a [Azure erőforrás-kezelő](https://resources.azure.com/), majd bontsa ki a hierarchiában, a bal oldali panelen kiválasztása:

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

 A az InstanceView görgessen le a titkosítási állapot meghajtó megtekintéséhez.

 ![Virtuális gép példányait tartalmazó nézet](./media/azure-security-disk-encryption/vm-instanceview.png)

* Tekintse meg [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). A ADE bővítményből származó üzenetek előtaggal kell `[AzureDiskEncryption]`.

* Jelentkezzen be a virtuális gép SSH-n keresztül, és a bővítmény a napló beolvasása:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Azt javasoljuk, hogy nem jelentkezik be a virtuális gép amíg folyamatban van az operációs rendszer titkosítási. Másolja a naplókat, csak akkor, ha a két módszer nem sikerült.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése
##### <a name="ubuntu-16"></a>Ubuntu 16
A terjesztési telepítése közben titkosítás beállítása az alábbiak szerint:

1. Válassza ki **titkosított kötetek konfigurálni** mikor particionálni a lemezeket.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy külön rendszerindítási meghajtót, amely nem lesznek titkosítva. A legfelső szintű meghajtójának titkosításához.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszót, amely feltölti a key vaulthoz.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Fejezze be a particionálást.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. A virtuális gép előkészítése Azure-ban való fel [ezek az utasítások](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

Adja meg a titkosítás működéséhez az Azure-ral az alábbiak szerint:

1. Hozzon létre egy fájlt /usr/local/sbin/azure_crypt_key.sh, a következő parancsfájl tartalmát. A KeyFileName figyelmet fordítania, mert az Azure által használt a hozzáférési kódot fájlnév.

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

2. Módosítsa a titkosítási konfiguráció a */etc/crypttab*. A listának így kell kinéznie:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Ha szerkeszti *azure_crypt_key.sh* Windows, és másolja a, Linux, futtassa `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adja hozzá a parancsfájl végrehajtható engedélyek:
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
6. Futtatás `update-initramfs -u -k all` frissíteni a initramfs győződjön meg arról, hogy a `keyscript` érvénybe léptetéséhez.

7. Most már Ön is a virtuális gép megszüntetéséhez.

 ![Ubuntu 16.04-telepítő](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Folytassa a következő lépéssel és [töltse fel a VHD-t](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure-bA.

##### <a name="opensuse-132"></a>openSUSE, 13.2
Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. Amikor particionálni a lemezeket, válassza ki a **Kötetcsoport titkosítása**, majd írja be a jelszót. Ez az a jelszó, amely feltölti a kulcstartóba.

 ![openSUSE, 13.2 beállítása](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gép az Ön jelszavát.

 ![openSUSE, 13.2 beállítása](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. A virtuális gép előkészítése Azure-ban utasításait követve feltöltése [SLES- vagy opensuse-alapú virtuális gép előkészítése Azure-](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

A titkosítás működéséhez az Azure-ral konfigurálásához tegye a következőket:
1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
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

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
 ```
    DRACUT_SYSTEMD=0
 ```
És az összes előfordulását módosítsa:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
erre:
```
    if [ 1 ]; then
```
4. Szerkesztés /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, és fűzze hozzá a "# nyitott LUKS eszköz":

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
5. Futtatás `/usr/sbin/dracut -f -v` az initrd frissíteni.

6. Most már Ön is a virtuális gép megszüntetése és [töltse fel a VHD-t](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure-bA.

##### <a name="centos-7"></a>CentOS 7
Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. Válassza ki **az adatok titkosítása** lemezek particionálása esetén.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg arról, hogy **titkosítása** gyökérpartíciót van kiválasztva.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszót, amely feltölti a kulcstartóba.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

 ![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. A virtuális gép előkészítése Azure-ba való feltöltése, a "CentOS 7.0 +" szakaszban foglaltak szerint [CentOS-alapú virtuális gép előkészítése Azure-](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

6. Most már Ön is a virtuális gép megszüntetése és [töltse fel a VHD-t](#upload-encrypted-vhd-to-an-azure-storage-account) az Azure-bA.

A titkosítás működéséhez az Azure-ral konfigurálásához tegye a következőket:

1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
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

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
```
    DRACUT_SYSTEMD=0
```
És az összes előfordulását módosítsa:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
erre:
```
    if [ 1 ]; then
```
4. Szerkesztés /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, és a hozzáfűző ez után a "# nyitott LUKS eszköz":
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
5. Futtassa a "/ usr/sbin/dracut - f - v" az initrd frissíteni.

![CentOS 7 beállítása](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális lemezek feltöltése az Azure storage-fiókba
BitLocker-titkosítást vagy DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemez kell feltölteni a tárfiókba.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel a lemeztitkosítási titkos kulcsot a key vault előzetes titkosított virtuális gép
A lemeztitkosítási titok beszerzett korábban fel kell tölteni, egy titkos kulcsot tárol a kulcstárolóban. A key vaultban kell rendelkeznie a lemeztitkosítás és az Azure AD-ügyfél engedélyezve.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Lemeztitkosítás titkos kódja egy KEK nem titkosított
Állítsa be a titkos kulcsot tárol a kulcstárolóban, használja a [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Ha egy Windows virtuális gépet, a blokktitkosítási kulcsot fájl Base64 kódolású karakterláncként kódolt és majd a key vaulttal történő feltöltésekor a `Set-AzureKeyVaultSecret` parancsmagot. Linux esetén a hozzáférési kódot Base64 kódolású karakterláncként kódolt és feltölthetők a key vaulthoz. Emellett ellenőrizze, hogy a titkos kulcsot a key vaultban történő létrehozásakor a következő címkék vannak-e beállítva.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Használja a `$secretUrl` esetében a következő lépésben [KEK használata nélkül az operációsrendszer-lemez csatolása](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Lemeztitkosítás titkos kódja egy KEK titkosítva
Mielőtt feltölti a titkos kulcsot a key vaulthoz, igény szerint titkosíthatók, kulcstitkosítási kulcs használatával. Használja a sortörés [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) először mesterkulcs a kulcstitkosítási kulcs használatával. A sortörés művelet kimenete egy base64 URL-kódolású karakterláncot, amely ezt követően feltöltheti, titkos kulcs használatával a [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) parancsmagot.

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

Használat `$KeyEncryptionKey` és `$secretUrl` esetében a következő lépésben [KEK használatával az operációsrendszer-lemez csatolása](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Adja meg titkos URL-cím, egy operációsrendszer-lemez csatolása
#### <a name="without-using-a-kek"></a>Egy KEK használata nélkül
Amíg az operációsrendszer-lemez való csatlakoztatás, teljesítenie kell a `$secretUrl`. Az URL-címet az "a-lemeztitkosítás titkos kulcs egy KEK nem titkosított" szakaszban jött létre.

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
Ha az operációsrendszer-lemez csatolásához `$KeyEncryptionKey` és `$secretUrl`. Az URL-címet az "a-lemeztitkosítás titkos kulcs egy KEK nem titkosított" szakaszban jött létre.

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

## <a name="for-more-information"></a>További tudnivalók
[Az Azure Disk Encryption megismerése az Azure PowerShell – 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Az Azure Disk Encryption megismerése az Azure PowerShell – 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
