---
title: Az Azure Disk Encryption IaaS virtuális gépek – áttekintés |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption áttekintést nyújt az IaaS virtuális gépekhez.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/10/2018
ms.openlocfilehash: ea72e4ac778e52d4f6e4f5597d38a1de59ba43fc
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348939"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Az Azure Disk Encryption IaaS virtuális gépekhez 
A Microsoft Azure számára fontos, hogy annak biztosítása, az adatvédelem, az adatok elkülönítése, és a vezérlő lehetővé teszi, hogy az Azure-ban üzemeltetett adatok titkosításához, szabályozhatja és titkosítási kulcsok kezelése fejlett technológiák számos keresztül, és az adatok hozzáférés-vezérlő & naplózási. Ez a vezérlő rugalmasságának köszönhetően az Azure ügyfelei üzleti igényeiknek leginkább megfelelő megoldást választhatja. Ez a cikk bemutatja, egy technológiai megoldást, az "Azure lemez titkosítási a Windows és Linux rendszerű IaaS virtuális gépek", a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok biztonsága érdekében. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Áttekintés
Az Azure Disk Encryption (ADE) egy olyan funkció, amely segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. ADE kihasználja az iparági szabvány [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsok. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban.

Az Azure disk encryption Windows és Linux rendszerű IaaS virtuális gépek van **általános rendelkezésre állás** minden nyilvános Azure-régióban és a premium storage Standard virtuális gépek és virtuális gépek AzureGov-régiókban. Amikor alkalmazza az Azure Disk Encryption-felügyeleti megoldás, a következő üzleti igényekre is képes eleget tenni:

* IaaS virtuális gépek védett szervezeti biztonsági és megfelelőségi követelmények iparági szabványnak megfelelő titkosítási technológia használatával.
* IaaS virtuális gépek rendszerindítási alapján az ügyfél által felügyelt kulcsok és szabályzatok, és használatuk a key vaultban lévő naplózhatók.


Ha az Azure Security Center használatához, riasztást küld, ha virtuális gépeket, amelyek nem titkosítottak. Ezek a riasztások magas súlyossági szinttel jelennek meg. A rendszer ilyenkor javasolja, hogy titkosítsa az érintett virtuális gépeket.
![Az Azure Security Center titkosítási riasztás](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit.


## <a name="encryption-scenarios"></a>Titkosítási megoldások
Az Azure Disk Encryption megoldás a következő ügyfél-forgatókönyveket teszi lehetővé:

* Új Windows IaaS virtuális gépek előzetes titkosítással VHD-t és a titkosítási kulcsok alapján létrehozott titkosításának engedélyezése 
* A támogatott Azure-gyűjtemény lemezképei alapján létrehozott új IaaS virtuális gépek titkosításának engedélyezése
* Azure-ban futó meglévő IaaS virtuális gépek titkosításának engedélyezése
* Engedélyezze a titkosítást a Windows virtual machine scale sets
* Linux virtuális gép méretezési csoportokhoz tartozó adatmeghajtók titkosításának engedélyezése
* Windows IaaS virtuális gépek titkosításának letiltása
* Linux rendszerű IaaS virtuális gépek adatmeghajtók titkosításának letiltása
* Tiltsa le a titkosítást a Windows virtual machine scale sets
* Linux virtuális gép méretezési csoportokhoz tartozó adatmeghajtók titkosításának letiltása
* Felügyelt lemezes virtuális gépek titkosításának engedélyezése
* Egy meglévő titkosított prémium és a virtuális Gépet nem prémium szintű storage titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás titkosított virtuális gépek

A megoldás a következő eseteket támogatja IaaS virtuális gépekhez, ha engedélyezve vannak a Microsoft Azure-ban:

* Az Azure Key Vault-integráció
* Standard szintű virtuális gépek: [A, D, DS, G, GS, F és egyebekben sorozatú IaaS virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * [Linux rendszerű virtuális gépek](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) ezen a szinten belül meg kell felelnie a minimális memóriára vonatkozó követelmény, 7 GB
* Titkosítás engedélyezése a Windows és Linux rendszerű IaaS virtuális gépek, felügyelt lemezes és a méretezési csoport virtuális gépeket adja meg a támogatott Azure-katalógus-rendszerképek
* Tiltsa le a Windows IaaS virtuális gépek méretezési csoport operációsrendszer- és meghajtók titkosításának virtuális gépei, és a felügyelt lemezes virtuális gépek
* Tiltsa le a Linuxos IaaS virtuális gépek méretezési adatmeghajtók titkosításának virtuális gépei, és a felügyelt lemezes virtuális gépek
* Windows ügyfél operációs rendszert futtató IaaS virtuális gépek titkosításának engedélyezése
* Kötet csatlakoztatási elérési úttal titkosításának engedélyezése
* Linux rendszerű virtuális gépek lemezzel konfigurált titkosításának csíkozást (RAID) mdadm használatával
* Linux rendszerű virtuális gépekkel LVM adatlemezek titkosításának engedélyezése
* Linux rendszerű virtuális gép operációsrendszer- és adatlemezek titkosításának engedélyezése 
* A tárolóhelyekkel konfigurált Windows virtuális gépek titkosításának engedélyezése
* Egy meglévő titkosított prémium és a virtuális Gépet nem prémium szintű storage titkosítási beállításainak módosítása
* Biztonsági mentés és visszaállítás, a titkosított virtuális gépek nem-KEK és KEK-forgatókönyvekhez (KEK - kulcstitkosítási kulcs)
* Minden Azure-beli nyilvános és AzureGov régiókban érhető el

A megoldás a következő forgatókönyvek, szolgáltatások és technológiák nem támogatja:

* Alapszintű csomag IaaS virtuális gépek
* Linux rendszerű IaaS virtuális gépek egy operációs rendszer meghajtójának titkosításának letiltása
* Adatmeghajtók titkosításának letiltása, ha az operációs rendszer meghajtójának titkosított Linux rendszerű Iaas virtuális gépekhez
* A klasszikus virtuális gép létrehozási módszer használatával létrehozott IaaS virtuális gépeken
* Linux rendszerű IaaS virtuális gépek ügyfél egyéni rendszerképek titkosításának engedélyezése
* Integráció a helyszíni kulcskezelő szolgáltatás
* Az Azure Files (megosztott fájlrendszert), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows virtuális gépek, amelyeken a szoftveres RAID-rendszerek

## <a name="encryption-features"></a>Titkosítási funkciók
Ha engedélyezi, és üzembe helyezése az Azure Disk Encryption az Azure IaaS virtuális gépek, a következő képességeket engedélyezve vannak, a megadott konfigurációtól függően:

* Az operációsrendszer-kötet védelmét a rendszerindító kötet a tárolás titkosítása
* Adatkötetek védelme érdekében az adatkötetek a tárolás titkosítása
* A Windows IaaS virtuális gépek az operációsrendszer- és meghajtók titkosításának letiltása
* Linux rendszerű IaaS virtuális gépekhez az adatmeghajtók titkosításának letiltása (csak akkor, ha operációs rendszer meghajtójának nem titkosított)
* A titkosítási kulcsok és titkos kulcsokat a key vault-előfizetés a védelme
* Jelentéskészítés a titkosított IaaS virtuális gépek lemeztitkosítási állapotát
* Lemeztitkosítás – az IaaS virtuális gép konfigurációs beállítások eltávolítása
* Biztonsági mentés és visszaállítás az Azure Backup szolgáltatás használatával titkosított virtuális gépek

Az Azure Disk Encryption Windows és Linux-megoldás IaaS virtuális gépekhez tartalmazza:

* A Windows disk encryption bővítményt.
* Linux-disk encryption bővítményt.
* A lemeztitkosítás PowerShell-parancsmagokat.
* A lemez titkosítása az Azure parancssori felület (CLI) parancsmagok.
* A lemez titkosítása az Azure Resource Manager-sablonok.

Az Azure Disk Encryption megoldás Windows vagy Linux operációs rendszert futtató IaaS virtuális gépek esetén támogatott. A támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg a [Előfeltételek](azure-security-disk-encryption-prerequisites.md) cikk.

> [!NOTE]
> Nem áll az Azure Disk Encryption Virtuálisgép-lemezek titkosítása külön díj. Standard szintű [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) fogja tárolni a titkosítási kulcsok a key vault vonatkozik. 


## <a name="encryption-workflow"></a>Titkosítási munkafolyamat

 Ahhoz, hogy a lemeztitkosítás Windows és Linux rendszerű virtuális gépekhez, tegye a következőket:

1. Válassza ki az előző titkosítási megoldások közül egy titkosítási forgatókönyvet.
2. Részvétel a engedélyezése lemeztitkosítás keresztül az Azure Disk Encryption Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a CLI-parancsot, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfiguráció engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást az IaaS virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyeken már fut az Azure-ban.

3. Hozzáférést biztosít az Azure platform a titkosítási kulcs anyag (BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-hozzáférési kódot olvasni az IaaS virtuális gép titkosításának a key vaultban.

4. Az Azure frissíti a titkosítás, a key vault beállítása a virtuális gép modell, és beállítja a titkosított virtuális gép.

 ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>A visszafejtés munkafolyamat
Lemeztitkosítás IaaS virtuális gépekhez letiltásához kövesse az alábbi magas szintű lépéseket:

1. Dönt, hogy tiltsa le a titkosítást (visszafejtési) az Azure-ban futó IaaS virtuális gépen, és adja meg a visszafejtési konfigurációt. Az Azure Disk Encryption Resource Manager-sablon, a PowerShell-parancsmagok vagy az Azure CLI-n keresztül is letilthatja.

 Ebben a lépésben letiltja a titkosítást az operációs rendszer vagy adatmennyiség, illetve mindkettőt a futó Windows IaaS virtuális gépen. Azonban az előző szakaszban említett operációs rendszer lemeztitkosítás linuxos letiltása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtókon a Linux rendszerű virtuális gépek mindaddig, amíg az operációsrendszer-lemez nincs titkosítva.
2. Az Azure frissíti a virtuális gép modell, és az IaaS virtuális gép visszafejtett van megjelölve. A virtuális gép tartalmát már nem titkosított inaktív.

> [!NOTE]
> A disable-titkosítási művelet nem törli a kulcstartót és a titkosítási kulcs adatai (BitLocker titkosítási kulcsok Windows rendszerekhez) vagy a Linux-jelszó.
 > Linux operációs rendszer lemez titkosításának letiltása nem támogatott. A visszafejtési lépés csak a Linux rendszerű virtuális gépek adatmeghajtók engedélyezett.
Adatok lemeztitkosítás linuxos letiltása nem támogatott, ha az operációs rendszer meghajtójának titkosítva van.


## <a name="encryption-workflow-previous-release"></a>Titkosítási munkafolyamat (előző kiadás)

Az Azure disk encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell egy Azure AD hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal. Engedélyezi a lemeztitkosítást a Windows és Linux rendszerű virtuális gépek (előző kiadás), hajtsa végre a következő lépéseket:

1. Válassza ki az előző titkosítási megoldások közül egy titkosítási forgatókönyvet.
2. Részvétel a engedélyezése lemeztitkosítás keresztül az Azure Disk Encryption Resource Manager-sablon, a PowerShell-parancsmagokkal vagy a CLI-parancsot, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfiguráció engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást az IaaS virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyeken már fut az Azure-ban.

3. Hozzáférést biztosít az Azure platform a titkosítási kulcs anyag (BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-hozzáférési kódot olvasni az IaaS virtuális gép titkosításának a key vaultban.

4. Adja meg az Azure Active Directory (Azure AD) identitása a titkosítási kulcsot a key vault anyag írni. Ez lehetővé teszi az IaaS virtuális Gépen, a 2. lépésben említett forgatókönyvek esetén a titkosítást.

5. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.


## <a name="terminology"></a>Terminológia
Ez a technológia által használt gyakori kifejezések megérteni, használja a következő terminológia táblázat:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Az Azure AD egy [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Az Azure AD-fiókja hitelesítéséhez, tárolásához és titkos kódok lekérése a key vault szolgál. |
| Azure Key Vault | A Key Vault egy titkosítási, key management-szolgáltatás, amely a Federal Information Processing szabványok (FIPS) alapján ellenőrzött hardveres biztonsági modulok, amelyek védelme kriptográfiai kulcsok és a bizalmas, titkos kulcsok. További információkért lásd: [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparági ismerhető fel Windows kötet titkosítási technológia, amely lehetővé teszi a lemeztitkosítást a Windows IaaS virtuális gépeket, hogy. |
| BEK | A BitLocker titkosítási kulcsokkal titkosítása az operációs rendszer rendszerindító kötet és adatkötetek. A BitLocker-kulcsok számára biztosít védelmet a kulcstartóban található titkos kódként. |
| parancssori felület | Lásd: [Azure parancssori felület](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) a Linux-alapú, átlátható lemeztitkosítás alrendszer, amely engedélyezi a lemeztitkosítást a Linuxos IaaS virtuális gépek szolgál. |
| KEK | Kulcsalapú titkosítás kulcsa az az aszimmetrikus kulcs (RSA 2048), amellyel védelméhez vagy burkolásához a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért lásd: [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| Powershell parancsmagjaival | Lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure Disk Encryption előfeltételei](azure-security-disk-encryption-prerequisites.md)
