---
title: Gyakori kérdések – Azure Disk Encryption Linux rendszerű virtuális gépekhez | Microsoft Docs
description: Ez a cikk a Linux IaaS virtuális gépek Microsoft Azure lemezes titkosításával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: f8d31c8df4d073ccd744e792d1316ce02e4bf387
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828643"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption IaaS virtuális gépekhez – gyakori kérdések

Ez a cikk a Linux rendszerű virtuális gépek Azure Disk Encryptionével kapcsolatos gyakori kérdésekre (GYIK) ad választ. További információ erről a szolgáltatásról: [Azure Disk Encryption Overview (áttekintés](disk-encryption-overview.md)).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol található az Azure Disk Encryption az általánosan (elérhetővé tétel GA)?

A Linux rendszerű virtuális gépek Azure Disk Encryption általánosan elérhetők az összes Azure-beli nyilvános régióban.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Melyik felhasználó érhetők el az Azure Disk Encryption?

Az Azure Disk Encryption általánosan elérhető az Azure Resource Manager sablonok, az Azure PowerShell és Azure parancssori felület támogatja. A különböző felhasználói élmények rugalmasságot biztosítanak. Három különböző lehetőség áll rendelkezésre a lemez titkosításának engedélyezéséhez a virtuális gépeken. További információ a Azure Disk Encryptionban elérhető felhasználói élményről és lépésenkénti útmutatóról: [Azure Disk Encryption forgatókönyvek Linux rendszerhez](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Az Azure Disk Encryption mennyibe?

A virtuálisgép-lemezek Azure Disk Encryption-mel való titkosítása díjmentes, de a Azure Key Vault használatával járó díjak is rendelkezésre állnak. Az Azure Key Vault költségek további információkért lásd: a [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) lapot.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan lehet elindítani az Azure Disk Encryption használatával?

Első lépések, olvassa el a [áttekintése az Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Milyen virtuálisgép-méreteket és operációs rendszereket támogat a Azure Disk Encryption?

A [Azure Disk Encryption áttekintő](disk-encryption-overview.md) cikk a [virtuális gépek méretét](disk-encryption-overview.md#supported-vm-sizes) és a Azure Disk Encryption támogató virtuálisgép- [operációs rendszereket](disk-encryption-overview.md#supported-operating-systems) sorolja fel.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és az adatok kötetek az Azure Disk Encryption használatával titkosítsa?

Igen, titkosíthatja a rendszerindító és az adatköteteket is, vagy titkosíthatja az adatmennyiséget anélkül, hogy először titkosítania kellene az operációs rendszer kötetét. 

Miután titkosította az operációsrendszer-kötetet, a titkosítás letiltása az operációsrendszer-köteten nem támogatott. A méretezési csoportokban lévő Linux rendszerű virtuális gépek esetében csak az adatmennyiség titkosítható.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Titkosítható a leválasztott kötetek Azure Disk Encryption?

Nem, Azure Disk Encryption csak a csatlakoztatott köteteket titkosítja.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan elforgatni a titkokat vagy a titkosítási kulcsokat?

A titkok elforgatásához egyszerűen hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, egy másik Key Vault megadásával. A kulcs titkosítási kulcsának elforgatásához hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, és megadhatja az új kulcs titkosítását. 

>[!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](disk-encryption-linux-aad.md) az Azure ad-beli hitelesítő adatok megadásával a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható Azure Disk Encryption, mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.

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

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg az Azure AD-tartalommal [rendelkező Azure Disk Encryption](disk-encryption-linux-aad.md) Azure Active Directory alkalmazás létrehozásához, egy új kulcstartó létrehozásához vagy egy meglévő Key Vault beállításához a titkosítás engedélyezéséhez, valamint a titkok és kulcsok védelméhez. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryption Azure ad-vel](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Használja az Azure AD-alkalmazás (előző kiadás) továbbra is támogatott az Azure Disk Encryption szolgáltatást?
Igen. Az Azure AD-alkalmazás használatával lemeztitkosítás továbbra is támogatott. Azonban új virtuális gépek titkosításához javasoljuk az új módszer helyett az Azure AD-alkalmazás titkosítására használja. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>A titkosítás nélkül az Azure AD-alkalmazás Azure AD-alkalmazás a titkosított virtuális gépeket telepíthet át?
  Jelenleg nem áll rendelkezésre egy közvetlen áttelepítési útvonal az Azure AD-alkalmazás, a titkosítás nélkül az Azure AD-alkalmazás titkosított gépek. Ezenkívül nem áll rendelkezésre egy közvetlen útvonal, a titkosítás nélkül az Azure AD-alkalmazás a titkosítást, AD-alkalmazás. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Milyen az Azure PowerShell-verzió támogatja az Azure Disk Encryption?

Az Azure PowerShell SDK legújabb verziójának segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-s verzió által támogatott.

> [!NOTE]
> A Linux Azure Disk Encryption Preview "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" kiterjesztése elavult. Ezt a bővítményt közzétettük az Azure Disk Encryption előzetes kiadásában. Ne használja a bővítmény előzetes verzióját a tesztelési vagy éles üzemi környezetben.

> Olyan telepítési forgatókönyvek esetén, mint például a Azure Resource Manager (ARM), ahol a Linux rendszerű virtuális gép számára szükséges az Azure Disk Encryption bővítmény üzembe helyezése a Linux IaaS virtuális gépen, az Azure Disk Encryption Production által támogatott bővítményt kell használnia. Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Használhatom az Azure Disk Encryption saját egyéni Linux-rendszerképen?

Az egyéni Linux-rendszerképek az Azure Disk Encryption, nem lehet alkalmazni. Csak a katalógus típusú Linux-rendszerképeket emelte ki a korábban a támogatott disztribúciók esetében támogatottak. Egyéni Linux-rendszerképek jelenleg nem támogatottak.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Használhatom-e frissítéseket Linux Red Hat használó virtuális gép a yum használatával frissítést?

Igen, egy Red Hat Linux rendszerű virtuális gépen is elvégezheti a yum-frissítést.  További információ: Linux- [csomagok kezelése tűzfal mögött](disk-encryption-troubleshooting.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Mi a javasolt az Azure disk encryption munkafolyamat Linux?

A következő munkafolyamatot javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a szükséges operációs rendszer disztribúció és verzió a megfelelő módosítás nélküli tőzsdei katalógus-rendszerképből
* Készítsen biztonsági másolatot minden olyan csatlakoztatott meghajtók, a rendszer titkosítja.  Ez vissza akár lehetővé teszi a helyreállítási Ha egy sikertelen, például ha a virtuális gép újraindul, titkosítási befejezése előtt.
* Titkosítása (is igénybe vehet néhány Virtuálisgép-jellemző és bármely más csatolt lemez mérete attól függően órákig vagy akár napokig)
* Testre szabhatja, és igény szerint adjon hozzá szoftvereket a lemezképet.

Ha ezt a munkafolyamatot nem lehetséges, a függő entitások [a Storage Service Encryption](../../storage/common/storage-service-encryption.md) (SSE) a platform tárolási fiók réteg lehet a teljes lemeztitkosítás, dm-crypt használata helyett.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Mi az a lemez "Rendelkeznek BEk-kel kötet" vagy "/ mnt/azure_bek_disk"?

A "Bek Volume" egy helyi adatkötet, amely biztonságosan tárolja a titkosított Azure-beli virtuális gépek titkosítási kulcsait.
> [!NOTE]
> Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ az Azure Disk Encryption?

Azure Disk Encryption az AES-XTS-plain64 alapértelmezett titkosítását használja egy 256 bites kötet főkulcsával.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Ha EncryptFormatAll használja, és adja meg a minden kötet esetében, azt az adatot töröl már titkosított adatok meghajtókon?
Nem, adatokat a rendszer nem törli a már Azure Disk Encryption használatával titkosított meghajtók. Hogyan EncryptFormatAll nem újratitkosítása az operációs rendszer meghajtójának hasonlóan, nem fog újra már a titkosított adatok meghajtójának titkosításához. További információkért lásd: a [EncryptFormatAll feltételek](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Támogatott-e a XFS fájlrendszer?
A XFS kötetek csak a EncryptFormatAll használhatók adatlemez-titkosításhoz. Ezzel újraformázza a kötetet, és törli az ott található összes adatmennyiséget. További információkért lásd: a [EncryptFormatAll feltételek](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Készíthetek biztonsági másolatot és állíthatok vissza egy titkosított virtuális gépet? 

Azure Backup lehetővé teszi a titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon előfizetésen és régión belül.  Útmutatásért tekintse meg a [titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)című témakört.  A titkosított virtuális gépek másik régióba való visszaállítása jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tudhatok meg kérdéseket tehet fel, vagy visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat a [az Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption kapcsolatos leggyakoribb kérdések. Ezzel a szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-beli adat-titkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
