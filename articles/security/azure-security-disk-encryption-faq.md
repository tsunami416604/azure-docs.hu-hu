---
title: Gyakori kérdések – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 4f2a34e63a870814c8d2a3ffe24c60083c9d7bb2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781106"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption IaaS virtuális gépekhez – gyakori kérdések

Ez a cikk az Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdések (GYIK) választ ad. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol található az Azure Disk Encryption az általánosan (elérhetővé tétel GA)?

Az általános rendelkezésre állás érdekében minden nyilvános Azure-régióban az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Melyik felhasználó érhetők el az Azure Disk Encryption?

Az Azure Disk Encryption általánosan elérhető az Azure Resource Manager sablonok, az Azure PowerShell és Azure parancssori felület támogatja. A különböző felhasználói élmények rugalmasságot biztosítanak. Lemez titkosítása az IaaS virtuális gépek három különböző lehetőségek állnak rendelkezésre. A felhasználói élmény és lépésenkénti útmutatóját az Azure Disk Encryption elérhető további információkért lásd: [engedélyezése Azure Disk Encryption a Windows](azure-security-disk-encryption-windows.md) és [engedélyezése az Azure Disk Encryption for Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Az Azure Disk Encryption mennyibe?

Semmilyen díjat nem az Azure Disk Encryption Virtuálisgép-lemezek titkosítása, de számítunk fel díjat a társított Azure Key Vault használatát. Az Azure Key Vault költségek további információkért lásd: a [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) lapot.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan lehet elindítani az Azure Disk Encryption használatával?

Első lépések, olvassa el a [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Milyen virtuálisgép-méreteket és operációs rendszereket támogat a Azure Disk Encryption?

A [Azure Disk Encryption előfeltételek](azure-security-disk-encryption-prerequisites.md) című cikk felsorolja a [virtuális gépek méretét](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes) és a Azure Disk Encryption támogató virtuálisgép- [operációs rendszereket](azure-security-disk-encryption-prerequisites.md#supported-operating-systems) .

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és az adatok kötetek az Azure Disk Encryption használatával titkosítsa?

Adatok és rendszerindító kötetek Igen, a Windows és Linux rendszerű IaaS virtuális gépek használatával titkosítsa. Windows-beli virtuális gépek az operációsrendszer-kötet titkosítása nélkül az adatok nem titkosíthatók. Linux rendszerű virtuális gépekhez legyen az operációs rendszer kötetének titkosításához először nélkül az adatmennyiség titkosíthatja. A titkosított operációsrendszer-kötet linuxhoz, miután letiltja a titkosítást az operációs rendszer kötet Linux rendszerű IaaS virtuális gépekhez nem támogatott. A méretezési csoportokban lévő Linux rendszerű virtuális gépek esetében csak az adatmennyiség titkosítható.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Titkosítható a leválasztott kötetek Azure Disk Encryption?

Nem, Azure Disk Encryption csak a csatlakoztatott köteteket titkosítja.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan elforgatni a titkokat vagy a titkosítási kulcsokat?

A titkok elforgatásához egyszerűen hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, egy másik Key Vault megadásával. A kulcs titkosítási kulcsának elforgatásához hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, és megadhatja az új kulcs titkosítását. 

>[!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad](azure-security-disk-encryption-prerequisites-aad.md) -alkalmazással az Azure ad-beli hitelesítő adatok megadásával a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hogyan a kulcs titkosítási kulcsának hozzáadását vagy eltávolítását, ha az eredetileg nem használ ilyet?

A kulcs titkosítási kulcsának hozzáadásához hívja meg újra az Enable parancsot a kulcs titkosítási kulcsának paraméterének átadásával. A kulcs titkosítási kulcsának eltávolításához hívja meg újra az Enable parancsot a kulcs titkosítási kulcs paramétere nélkül.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Az Azure Disk Encryption lehetővé teszi, hogy a saját kulcs (használatának BYOK)?

Igen, a saját kulcs titkosítási kulcsok is megadhatja. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. További információ a fő titkosítási kulcsok esetében, tekintse meg [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használható az Azure által létrehozott fő titkosítási kulcsot?

Igen, az Azure Key Vault segítségével hozzon létre egy kulcsalapú titkosítás kulcsa az Azure disk encryption használatra. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. A kulcsalapú titkosítás kulcsa további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használható egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Az Azure Key Vault szolgáltatás segítségével csak biztosítja a titkosítási kulcsok védelmét. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md) hozzon létre egy új kulcstartót cikket, vagy egy meglévő lemez titkosítási hozzáférés engedélyezése a titkosításhoz, és a kulcsok és titkok védelme key vault beállítása. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption az Azure AD-alkalmazás (előző kiadás)?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites-aad.md) cikk létrehozása az Azure Active Directory-alkalmazás, hozzon létre egy új kulcstartót, vagy a titkosítási lemezelérést engedélyezheti a titkosítást egy meglévő key vault beállítása és titkos kulcsok védelme és kulcsok. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

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

Igen, egy Red Hat Linux rendszerű virtuális gépen is elvégezheti a yum-frissítést.  További információ: Linux- [csomagok kezelése tűzfal mögött](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Mi a javasolt az Azure disk encryption munkafolyamat Linux?

A következő munkafolyamatot javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a szükséges operációs rendszer disztribúció és verzió a megfelelő módosítás nélküli tőzsdei katalógus-rendszerképből
* Készítsen biztonsági másolatot minden olyan csatlakoztatott meghajtók, a rendszer titkosítja.  Ez vissza akár lehetővé teszi a helyreállítási Ha egy sikertelen, például ha a virtuális gép újraindul, titkosítási befejezése előtt.
* Titkosítása (is igénybe vehet néhány Virtuálisgép-jellemző és bármely más csatolt lemez mérete attól függően órákig vagy akár napokig)
* Testre szabhatja, és igény szerint adjon hozzá szoftvereket a lemezképet.

Ha ezt a munkafolyamatot nem lehetséges, a függő entitások [a Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) a platform tárolási fiók réteg lehet a teljes lemeztitkosítás, dm-crypt használata helyett.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Mi az a lemez "Rendelkeznek BEk-kel kötet" vagy "/ mnt/azure_bek_disk"?

"Rendelkeznek BEk-kel kötet" Windows vagy a "/ mnt/azure_bek_disk" Linux olyan helyi kötet, amely biztonságosan tárolja a titkosítási kulcsok titkosított Azure IaaS virtuális gépekhez.
> [!NOTE]
> Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ az Azure Disk Encryption?

Windows rendszeren az ADE a BitLocker AES256 titkosítási módszert használja (a Windows Server 2012 előtti verziókban a AES256WithDiffuser). Linuxon az ADE az AES-XTS-plain64 alapértelmezett titkosítását használja egy 256 bites kötet főkulcsával.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Ha EncryptFormatAll használja, és adja meg a minden kötet esetében, azt az adatot töröl már titkosított adatok meghajtókon?
Nem, adatokat a rendszer nem törli a már Azure Disk Encryption használatával titkosított meghajtók. Hogyan EncryptFormatAll nem újratitkosítása az operációs rendszer meghajtójának hasonlóan, nem fog újra már a titkosított adatok meghajtójának titkosításához. További információkért lásd: a [EncryptFormatAll feltételek](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Támogatott-e a XFS fájlrendszer?
A XFS kötetek csak a EncryptFormatAll használhatók adatlemez-titkosításhoz. Ezzel újraformázza a kötetet, és törli az ott található összes adatmennyiséget. További információkért lásd: a [EncryptFormatAll feltételek](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Készíthetek biztonsági másolatot és állíthatok vissza egy titkosított virtuális gépet? 

Azure Backup lehetővé teszi a titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon előfizetésen és régión belül.  Útmutatásért tekintse meg a [titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)című témakört.  A titkosított virtuális gépek másik régióba való visszaállítása jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tudhatok meg kérdéseket tehet fel, vagy visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat a [az Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption kapcsolatos leggyakoribb kérdések. Ezzel a szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md)
- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-beli adat-titkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
