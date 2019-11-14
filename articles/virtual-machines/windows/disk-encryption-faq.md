---
title: Gyakori kérdések – Azure Disk Encryption Windows rendszerű virtuális gépekhez
description: Ez a cikk a Windows IaaS virtuális gépek Microsoft Azure lemezes titkosításával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 98ab9ea67ceb1d965e248c93d27cb801ab8c7483
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033323"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure Disk Encryption Windows rendszerű virtuális gépekhez – gyakori kérdések

Ez a cikk a Windows rendszerű virtuális gépek Azure Disk Encryptionával kapcsolatos gyakori kérdésekre (GYIK) nyújt válaszokat. További információ erről a szolgáltatásról: [Azure Disk Encryption Overview (áttekintés](disk-encryption-overview.md)).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol található az Azure Disk Encryption az általánosan (elérhetővé tétel GA)?

A Azure Disk Encryption általánosan elérhető az összes Azure-beli nyilvános régióban.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Melyik felhasználó érhetők el az Azure Disk Encryption?

Az Azure Disk Encryption általánosan elérhető az Azure Resource Manager sablonok, az Azure PowerShell és Azure parancssori felület támogatja. A különböző felhasználói élmények rugalmasságot biztosítanak. Három különböző lehetőség áll rendelkezésre a lemez titkosításának engedélyezéséhez a virtuális gépeken. A Azure Disk Encryptionban elérhető felhasználói élményről és lépésenkénti útmutatóról a [Windows Azure Disk Encryption forgatókönyvei](disk-encryption-windows.md)című témakörben olvashat bővebben.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Az Azure Disk Encryption mennyibe?

A virtuálisgép-lemezek Azure Disk Encryption-mel való titkosítása díjmentes, de a Azure Key Vault használatával járó díjak is rendelkezésre állnak. Az Azure Key Vault költségek további információkért lásd: a [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) lapot.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan lehet elindítani az Azure Disk Encryption használatával?

Első lépések, olvassa el a [áttekintése az Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Milyen virtuálisgép-méreteket és operációs rendszereket támogat a Azure Disk Encryption?

A [Azure Disk Encryption áttekintő](disk-encryption-overview.md) cikk a [virtuális gépek méretét](disk-encryption-overview.md#supported-vm-sizes) és a Azure Disk Encryption támogató virtuálisgép- [operációs rendszereket](disk-encryption-overview.md#supported-operating-systems) sorolja fel.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és az adatok kötetek az Azure Disk Encryption használatával titkosítsa?

Titkosíthatja a rendszerindító és az adatköteteket is, de az operációs rendszer kötetének első titkosítása nélkül nem titkosíthatja az adattitkosítást.

Miután titkosította az operációsrendszer-kötetet, a titkosítás letiltása az operációsrendszer-köteten nem támogatott.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Titkosítható a leválasztott kötetek Azure Disk Encryption?

Nem, Azure Disk Encryption csak a csatlakoztatott köteteket titkosítja.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan elforgatni a titkokat vagy a titkosítási kulcsokat?

A titkok elforgatásához egyszerűen hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, egy másik Key Vault megadásával. A kulcs titkosítási kulcsának elforgatásához hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, és megadhatja az új kulcs titkosítását. 

>[!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](disk-encryption-windows-aad.md) az Azure ad-beli hitelesítő adatok megadásával a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható Azure Disk Encryption, mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hogyan a kulcs titkosítási kulcsának hozzáadását vagy eltávolítását, ha az eredetileg nem használ ilyet?

A kulcs titkosítási kulcsának hozzáadásához hívja meg újra az Enable parancsot a kulcs titkosítási kulcsának paraméterének átadásával. A kulcs titkosítási kulcsának eltávolításához hívja meg újra az Enable parancsot a kulcs titkosítási kulcs paramétere nélkül.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Az Azure Disk Encryption lehetővé teszi, hogy a saját kulcs (használatának BYOK)?

Igen, a saját kulcs titkosítási kulcsok is megadhatja. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. A kulcs titkosítási kulcsait támogató forgatókönyvekkel kapcsolatos további információkért lásd: [kulcstartó létrehozása és konfigurálása a Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használható az Azure által létrehozott fő titkosítási kulcsot?

Igen, az Azure Key Vault segítségével hozzon létre egy kulcsalapú titkosítás kulcsa az Azure disk encryption használatra. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. További információ a kulcs titkosítási kulcsáról: [kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használható egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Az Azure Key Vault szolgáltatás segítségével csak biztosítja a titkosítási kulcsok védelmét. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [Key Vault létrehozása és konfigurálása a Azure Disk Encryption](disk-encryption-key-vault.md) cikkhez új kulcstartó létrehozásához vagy egy meglévő kulcstartó beállítása a titkosítás engedélyezéséhez, valamint a titkok és kulcsok védelméhez. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption az Azure AD-alkalmazás (előző kiadás)?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg az Azure AD-tartalommal [rendelkező Azure Disk Encryption](disk-encryption-windows-aad.md) Azure Active Directory alkalmazás létrehozásához, egy új kulcstartó létrehozásához vagy egy meglévő Key Vault beállításához a titkosítás engedélyezéséhez, valamint a titkok és kulcsok védelméhez. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryption Azure ad-vel](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Használja az Azure AD-alkalmazás (előző kiadás) továbbra is támogatott az Azure Disk Encryption szolgáltatást?
Igen. Az Azure AD-alkalmazás használatával lemeztitkosítás továbbra is támogatott. Azonban új virtuális gépek titkosításához javasoljuk az új módszer helyett az Azure AD-alkalmazás titkosítására használja. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>A titkosítás nélkül az Azure AD-alkalmazás Azure AD-alkalmazás a titkosított virtuális gépeket telepíthet át?
  Jelenleg nem áll rendelkezésre egy közvetlen áttelepítési útvonal az Azure AD-alkalmazás, a titkosítás nélkül az Azure AD-alkalmazás titkosított gépek. Ezenkívül nem áll rendelkezésre egy közvetlen útvonal, a titkosítás nélkül az Azure AD-alkalmazás a titkosítást, AD-alkalmazás. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Milyen az Azure PowerShell-verzió támogatja az Azure Disk Encryption?

Az Azure PowerShell SDK legújabb verziójának segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-s verzió által támogatott.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Mi az a lemez "Rendelkeznek BEk-kel kötet" vagy "/ mnt/azure_bek_disk"?

A "Bek Volume" egy helyi adatkötet, amely biztonságosan tárolja a titkosított Azure-beli virtuális gépek titkosítási kulcsait.

> [!NOTE]
> Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ az Azure Disk Encryption?

Azure Disk Encryption a BitLocker AES256 titkosítási módszert használja (a Windows Server 2012 előtti verziókban a AES256WithDiffuser). 

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Ha EncryptFormatAll használja, és adja meg a minden kötet esetében, azt az adatot töröl már titkosított adatok meghajtókon?
Nem, adatokat a rendszer nem törli a már Azure Disk Encryption használatával titkosított meghajtók. Hogyan EncryptFormatAll nem újratitkosítása az operációs rendszer meghajtójának hasonlóan, nem fog újra már a titkosított adatok meghajtójának titkosításához. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Készíthetek biztonsági másolatot és állíthatok vissza egy titkosított virtuális gépet? 

Azure Backup lehetővé teszi a titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon előfizetésen és régión belül.  Útmutatásért tekintse meg a [titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](../../backup/backup-azure-vms-encryption.md)című témakört.  A titkosított virtuális gépek másik régióba való visszaállítása jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tudhatok meg kérdéseket tehet fel, vagy visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat a [az Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption kapcsolatos leggyakoribb kérdések. Ezzel a szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-beli adat-titkosítás inaktív állapotban](../../security/fundamentals/encryption-atrest.md)
