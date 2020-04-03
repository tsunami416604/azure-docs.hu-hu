---
title: GYAKORI KÉRDÉSEK – Azure lemeztitkosítás Windows virtuális gépekhez
description: Ez a cikk választ ad a Microsoft Azure Lemeztitkosítás Windows IaaS virtuális gépekhez gyakran feltett kérdéseire.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 5cc6a3d8d736209f762959bca6f8ad379c14203f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582718"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure lemeztitkosítás windowsos virtuális gépekhez – gyakori kérdések

Ez a cikk választ ad az Azure Disk Encryption for Windows virtuális gépekhez gyakran feltett kérdésekre. A szolgáltatásról az Azure Disk Encryption – áttekintés című témakörben olvashat [bővebben.](disk-encryption-overview.md)

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
