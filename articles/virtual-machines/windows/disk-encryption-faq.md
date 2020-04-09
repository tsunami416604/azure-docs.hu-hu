---
title: GYAKORI KÉRDÉSEK – Azure lemeztitkosítás Windows virtuális gépekhez
description: Ez a cikk választ ad a Microsoft Azure Lemeztitkosítás Windows IaaS virtuális gépekhez gyakran feltett kérdéseire.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: a4ab59ab5b988b54333c419ced7fa852da531eaa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984333"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Azure lemeztitkosítás Windows rendszerű virtuális gépekhez – gyakori kérdések

Ez a cikk választ ad az Azure Disk Encryption for Windows virtuális gépekhez gyakran feltett kérdésekre. A szolgáltatásról az Azure Disk Encryption – áttekintés című témakörben olvashat [bővebben.](disk-encryption-overview.md)

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Mi az Azure lemeztitkosítás windowsos virtuális gépekhez?

Az Azure Disk Encryption for Windows virtuális gépek a Windows Bitlocker szolgáltatását használja az operációs rendszer lemezének és adatlemezeinek teljes lemeztitkosításának biztosításához. Emellett biztosítja az ideiglenes erőforráslemez titkosítását, ha a [VolumeType paraméter Az Összes](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  A tartalom a virtuális gépről a storage háttérrendszerbe titkosított. Ezáltal végpontok közötti titkosítást biztosít egy ügyfél által felügyelt kulccsal.
 
Lásd: [Támogatott virtuális gépek és operációs rendszerek.](disk-encryption-overview.md#supported-vms-and-operating-systems)
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol van az Azure Disk Encryption általánosan elérhető (GA)?

Az Azure Disk Encryption általánosan elérhető az Azure nyilvános régióiban.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Milyen felhasználói élmény érhető el az Azure disk encryption?

Az Azure Disk Encryption GA támogatja az Azure Resource Manager-sablonokat, az Azure PowerShellt és az Azure CLI-t. A különböző felhasználói élmények rugalmasságot biztosítanak. Három különböző lehetőség közül választhat a virtuális gépek lemeztitkosításának engedélyezéséhez. Az Azure Disk Encryption szolgáltatásban elérhető felhasználói élményről és részletes útmutatásról az [Azure Lemeztitkosítás windowsos forgatókönyvei](disk-encryption-windows.md)című témakörben talál további információt.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Mennyibe kerül az Azure disk encryption?

A virtuális gép lemezeinek Azure Disk Encryption szolgáltatással való titkosítása díjmentes, de az Azure Key Vault használatával kapcsolatos díjak vannak társítva. Az Azure Key Vault költségeiről a [Key Vault díjszabási](https://azure.microsoft.com/pricing/details/key-vault/) lapján talál további információt.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan kezdhetem el használni az Azure Disk Encryption szolgáltatást?

Első lépésekhez olvassa el az [Azure disk encryption áttekintését.](disk-encryption-overview.md)

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Milyen virtuálisgép-méretek és operációs rendszerek támogatják az Azure disk encryption?

Az [Azure Disk Encryption áttekintése](disk-encryption-overview.md) cikk felsorolja a virtuális gép [méretei](disk-encryption-overview.md#supported-vms) és [a virtuális gép operációs rendszerek,](disk-encryption-overview.md#supported-operating-systems) amelyek támogatják az Azure lemeztitkosítás.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Titkosíthatók a rendszerindító és az adatkötetek az Azure Disk Encryption szolgáltatással?

Titkosíthatja a rendszerindító és az adatköteteket is, de az adatok nem titkosíthatók az operációs rendszer kötetének titkosítása nélkül.

Miután titkosította az operációs rendszer kötetét, az operációs rendszer kötetén a titkosítás letiltása nem támogatott.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Titkosítható egy lenemcsatlakoztatott kötet az Azure Disk Encryption segítségével?

Nem, az Azure Disk Encryption csak a csatlakoztatott köteteket titkosítja.

## <a name="what-is-storage-server-side-encryption"></a>Mi a Storage kiszolgálóoldali titkosítása?

A tárolási kiszolgálóoldali titkosítás titkosítja az Azure által kezelt lemezeket az Azure Storage-ban. A felügyelt lemezek alapértelmezés szerint kiszolgálóoldali titkosítással vannak titkosítva platformáltal felügyelt kulccsal (2017. június 10-től). A felügyelt lemezek titkosítását saját kulcsokkal kezelheti egy ügyfél által felügyelt kulcs megadásával. További információt az [Azure által felügyelt lemezek kiszolgálóoldali titkosítása című témakörben talál.](disk-encryption.md)
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Miben különbözik az Azure Disk Encryption a Storage kiszolgálóoldali titkosításától az ügyfél által felügyelt kulccsal, és mikor kell használni az egyes megoldásokat?

Az Azure Disk Encryption végpontok közötti titkosítást biztosít az operációs rendszer lemezeihez, az adatlemezekhez és az ideiglenes erőforráslemezhez egy ügyfél által felügyelt kulccsal.

- Ha a követelmények közé tartozik a fenti és végpontok közötti titkosítás titkosítása, használja az Azure Disk Encryption. 
- Ha a követelmények közé tartozik csak az adatok titkosítása az ügyfél által kezelt kulccsal, akkor használja [a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokkal.](disk-encryption.md) Az Azure Disk Encryption és storage kiszolgálóoldali titkosítással nem titkosíthat lemezt ügyfél által kezelt kulcsokkal.
_ Ha nem [támogatott Windows-esetekben](disk-encryption-windows.md#unsupported-scenarios)hívott forgatókönyvet használ, fontolja meg [a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokkal](disk-encryption.md). 
- Ha a szervezet szabályzata lehetővé teszi az inaktív tartalom titkosítását egy Azure által felügyelt kulccsal, akkor nincs szükség műveletre – a tartalom alapértelmezés szerint titkosítva van. Felügyelt lemezek esetén a tárolón belüli tartalom alapértelmezés szerint platformáltal kezelt kulccsal van titkosítva kiszolgálóoldali titkosítással. A kulcsot az Azure Storage szolgáltatás kezeli. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan forgathatom el a titkos kulcsokat vagy a titkosítási kulcsokat?

A titkos kulcsok elforgatásához csak hívja meg ugyanazt a parancsot, amelyet eredetileg a lemeztitkosítás engedélyezéséhez használt, és adjon meg egy másik Key Vaultot. A kulcstitkosítási kulcs elforgatásához hívja meg ugyanazt a parancsot, amelyet eredetileg a lemeztitkosítás engedélyezéséhez használt, és adja meg az új kulcstitkosítást. 

>[!WARNING]
> - Ha korábban már használta [az Azure Disk Encryption az Azure AD-alkalmazással](disk-encryption-windows-aad.md) az Azure AD hitelesítő adatok megadásával a virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. Az Azure Disk Encryption nem használható ezen a titkosított virtuális gépen, mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép AAD-alkalmazásról való váltás még nem támogatott.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hogyan vehetek fel vagy távolíthatok el kulcstitkosítási kulcsot, ha eredetileg nem használtam?

Kulcstitkosítási kulcs hozzáadásához hívja meg újra az enable parancsot, amely átmegy a kulcstitkosítási kulcs paraméterén. Kulcstitkosítási kulcs eltávolításához hívja meg újra az enable parancsot a kulcstitkosítási kulcs paramétere nélkül.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Az Azure Disk Encryption lehetővé teszi, hogy saját kulcsot (BYOK)?

Igen, saját kulcstitkosítási kulcsokat is megadhat. Ezek a kulcsok az Azure Key Vault, amely az Azure Disk Encryption kulcstárolója. A kulcstitkosítási kulcsok támogatási forgatókönyveiről az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál további információt.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használhatok Azure által létrehozott kulcstitkosítási kulcsot?

Igen, az Azure Key Vault segítségével létrehozhat egy kulcstitkosítási kulcsot az Azure lemeztitkosítási használatához. Ezek a kulcsok az Azure Key Vault, amely az Azure Disk Encryption kulcstárolója. A kulcstitkosítási kulcsról további információt az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása című témakörben talál.](disk-encryption-key-vault.md)

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használhatok helyszíni kulcskezelő szolgáltatást vagy HSM-et a titkosítási kulcsok védelmére?

A helyszíni kulcsfelügyeleti szolgáltatás vagy a HSM nem használhatja a titkosítási kulcsok at Az Azure Disk Encryption. Csak az Azure Key Vault szolgáltatás a titkosítási kulcsok védelme. A kulcstitkosítási kulcs támogatási forgatókönyveiről az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál további információt.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Milyen előfeltételei vannak az Azure disk encryption konfigurálásának?

Az Azure Disk Encryption előfeltételei vannak. Tekintse meg a [létrehozása és konfigurálása a key vault azure disk encryption](disk-encryption-key-vault.md) cikket hozzon létre egy új kulcstartót, vagy hozzon létre egy meglévő kulcstartó a lemeztitkosítási hozzáférés titkosítás engedélyezéséhez, és a titkos kulcsok és kulcsok védelme. A kulcstitkosítási kulcs támogatási forgatókönyveiről az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál további információt.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Milyen előfeltételei vannak az Azure Disk Encryption konfigurálásának egy Azure AD-alkalmazással (előző kiadás)?

Az Azure Disk Encryption előfeltételei vannak. Tekintse meg az [Azure Disk Encryption azure AD-tartalommal](disk-encryption-windows-aad.md) hozzon létre egy Azure Active Directory-alkalmazást, hozzon létre egy új kulcstartót, vagy hozzon létre egy meglévő kulcstartót a lemeztitkosítási hozzáférés hez a titkosítás engedélyezéséhez, valamint a titkos kulcsok és kulcsok védelméhez. A kulcstitkosítási kulcs támogatási forgatókönyveiről további információt az [Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel](disk-encryption-key-vault-aad.md)című témakörben talál.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Az Azure Disk Encryption egy Azure AD-alkalmazás használatával (előző kiadás) továbbra is támogatott?
Igen. Az Azure AD-alkalmazás használatával továbbra is támogatott a lemeztitkosítás. Azonban az új virtuális gépek titkosításakor ajánlott, hogy az új módszert használja, nem pedig egy Azure AD-alkalmazással való titkosítást. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Áttelepíthetem az Azure AD-alkalmazással titkosított virtuális gépeket titkosításra Egy Azure AD-alkalmazás nélkül?
  Jelenleg nincs közvetlen áttelepítési elérési út az Azure AD-alkalmazással titkosított gépek hez egy Azure AD-alkalmazás nélkül. Emellett nincs közvetlen elérési út a titkosításhoz az Azure AD-alkalmazás nélkül az AD-alkalmazással való titkosításhoz. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Az Azure PowerShell melyik verzióját támogatja az Azure Disk Encryption?

Az Azure PowerShell SDK legújabb verziójával konfigurálhatja az Azure Disk Encryption szolgáltatást. Töltse le az [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)legújabb verzióját. Az Azure Disk Encryption nem *támogatja* az Azure SDK 1.1.0-s verziója.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Mi a "Bek Volume" vagy "/mnt/azure_bek_disk" lemez?

A "Bek-kötet" egy helyi adatkötet, amely biztonságosan tárolja a titkosított Azure-beli virtuális gépek titkosítási kulcsait.

> [!NOTE]
> Ne törölje és ne szerkesztse a lemez tartalmát. Ne csatlakoztassa le a lemezt, mert a titkosítási kulcs jelenléte szükséges az IaaS virtuális gép titkosítási műveleteihez.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ az Azure Disk Encryption?

Az Azure Disk Encryption a Következőképpen választja ki a BitLocker titkosítási módszerét a Windows verziója alapján:

| Windows-verziók                 | Verzió | Titkosítási módszer        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 vagy nagyobb  | >=1511 |XTS-AES 256 bites           |
| Windows Server 2012, Windows 8, 8.1, 10 | 1511 < |AES 256 bites *              |
| Windows Server 2008R2            |        |AES 256 bites szidó |

\*A Diffúzorral rendelkező AES 256 bites verziót a Windows 2012 és újabb rendszer nem támogatja.

A Windows operációs rendszer verziójának meghatározásához futtassa a "winver" eszközt a virtuális gépben.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Ha én EncryptFormatAll és adja meg az összes kötettípust, akkor törli az adatokat az adatmeghajtókon, hogy már titkosított?
Nem, az adatok nem törlődnek az Azure Disk Encryption használatával már titkosított adatmeghajtókról. Hasonlóan ahhoz, ahogyan az EncryptFormatAll nem titkosította újra az operációs rendszer meghajtóját, nem fogja újra titkosítani a már titkosított adatmeghajtót. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Készíthetek biztonsági másolatot és visszaállíthatok egy titkosított virtuális gépről? 

Az Azure Backup egy mechanizmust biztosít a titkosított virtuális gépek biztonsági mentéséhez és visszaállításához ugyanazon az előfizetésen és régión belül.  További információt a [Titkosított virtuális gépek biztonsági mentése és visszaállítása az Azure Backup segítségével.](../../backup/backup-azure-vms-encryption.md)  A titkosított virtuális gép visszaállítása egy másik régióba jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tehetek fel kérdéseket, illetve hol küldhetek visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat az [Azure lemeztitkosítási fórumon.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban az Azure Disk Encryption szolgáltatással kapcsolatos leggyakoribb kérdésekről többet is megtud. A szolgáltatásról további információt az alábbi cikkekben talál:

- [Az Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Lemeztitkosítás alkalmazása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-adattitkosítás inaktív helyen](../../security/fundamentals/encryption-atrest.md)
