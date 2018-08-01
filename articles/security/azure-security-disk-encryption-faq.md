---
title: Az Azure Disk Encryption – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 98b8883a5ab0096102ab7daf90b5b2791a6f7e41
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389575"
---
# <a name="azure-disk-encryption-faq"></a>Az Azure Disk Encryption – gyakori kérdések

Ez a cikk az Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdések (GYIK) választ ad. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol található az Azure Disk Encryption az általánosan (elérhetővé tétel GA)?

Az általános rendelkezésre állás érdekében minden nyilvános Azure-régióban az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Melyik felhasználó érhetők el az Azure Disk Encryption?

Az Azure Disk Encryption általánosan elérhető az Azure Resource Manager sablonok, az Azure PowerShell és Azure parancssori felület támogatja. A több felhasználói élmények rugalmasságot biztosítanak. Lemez titkosítása az IaaS virtuális gépek három különböző lehetőségek állnak rendelkezésre. A felhasználói élmény és lépésenkénti útmutatóját az Azure Disk Encryption elérhető további információkért lásd: [engedélyezése Azure Disk Encryption a Windows](azure-security-disk-encryption-windows.md) és [engedélyezése az Azure Disk Encryption for Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Az Azure Disk Encryption mennyibe?

Semmilyen díjat nem az Azure Disk Encryption Virtuálisgép-lemezek titkosítása, de számítunk fel díjat a társított Azure Key Vault használatát. Az Azure Key Vault költségek további információkért lásd: a [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) lapot.


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Melyik virtuális gép csomagja támogatja az Azure Disk Encryption?

Beleértve a standard szintű virtuális gépeken érhető el az Azure Disk Encryption [A, D, DS, G, GS és F](https://azure.microsoft.com/pricing/details/virtual-machines/) sorozatú IaaS virtuális gépek. Egyben a premium storage virtuális gép esetében elérhető. Nem érhető el az alapszintű csomag virtuális gépeket.

## <a name="bkmk_LinuxOSSupport"></a> Milyen Linux-disztribúciók támogatja az Azure Disk Encryption?

Az Azure Disk Encryption támogatott a következő kiszolgáló Linux-disztribúciók és verziók:

| Linux-disztribúció | Verzió | A kötettípus titkosítás támogatott|
| --- | --- |--- |
| Ubuntu | 16.04-NAPI-LTS | Operációsrendszer- és lemez |
| Ubuntu | 14.04.5-DAILY-LTS | Operációsrendszer- és lemez |
| RHEL | 7.5 | Adatok lemezre * |
| RHEL | 7.4 | Adatok lemezre * |
| RHEL | 7.3 | Adatok lemezre * |
| RHEL | 7.2 | Adatok lemezre * |
| RHEL | 6.8 | Adatok lemezre * |
| RHEL | 6.7 | Adatok lemezre * |
| CentOS | 7.4 | Operációsrendszer- és lemez |
| CentOS | 7.3 | Operációsrendszer- és lemez |
| CentOS | 7.2n | Operációsrendszer- és lemez |
| CentOS | 6.8 | Operációsrendszer- és lemez |
| CentOS | 7.1 | Adatlemez |
| CentOS | 7.0 | Adatlemez |
| CentOS | 6.7 | Adatlemez |
| CentOS | 6.6 | Adatlemez |
| CentOS | 6.5 | Adatlemez |
| openSUSE | 13.2 | Adatlemez |
| SLES | 12 SP1 | Adatlemez |
| SLES | Prioritás: 12-SP1 | Adatlemez |
| SLES | HPC 12 | Adatlemez |
| SLES | Prioritás: 11-SP4 | Adatlemez |
| SLES | 11 SP4 | Adatlemez |


*__ADE RHEL adatlemez esetében támogatott. A jelenlegi ADE implementációja operációsrendszer-lemez esetében működik, de közösen jelenleg nem támogatott. A Microsoft és a Red Hat dolgozik egy közösen támogatott megoldás. Addig is lehet hivatkozni a [Linuxhoz készült Azure Disk Encryption](azure-security-disk-encryption-linux.md) cikk.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan lehet elindítani az Azure Disk Encryption használatával?

Első lépések, olvassa el a [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és az adatok kötetek az Azure Disk Encryption használatával titkosítsa?

Adatok és rendszerindító kötetek Igen, a Windows és Linux rendszerű IaaS virtuális gépek használatával titkosítsa. Windows-beli virtuális gépek az operációsrendszer-kötet titkosítása nélkül az adatok nem titkosíthatók. Linux rendszerű virtuális gépekhez legyen az operációs rendszer kötetének titkosításához először nélkül az adatmennyiség titkosíthatja. A titkosított operációsrendszer-kötet linuxhoz, miután letiltja a titkosítást az operációs rendszer kötet Linux rendszerű IaaS virtuális gépekhez nem támogatott.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Az Azure Disk Encryption lehetővé teszi, hogy a saját kulcs (használatának BYOK) funkció használata?

Igen, a saját kulcs titkosítási kulcsok is megadhatja. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. További információ a fő titkosítási kulcsok esetében, tekintse meg [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használható az Azure által létrehozott fő titkosítási kulcsot?

Igen, az Azure Key Vault segítségével hozzon létre egy kulcsalapú titkosítás kulcsa az Azure disk encryption használatra. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. A kulcsalapú titkosítás kulcsa további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használható egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Az Azure Key Vault szolgáltatás segítségével csak biztosítja a titkosítási kulcsok védelmét. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md) cikk létrehozása az Azure Active Directory-alkalmazás, hozzon létre egy új kulcstartót, vagy a titkosítási lemezelérést engedélyezheti a titkosítást egy meglévő key vault beállítása és titkos kulcsok védelme és kulcsok. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Hol kaphatok további információt a PowerShell használatát az Azure Disk Encryption konfigurálásához?

Nincsenek néhány nagyszerű cikk hogyan végezhetők el alapvető Azure Disk Encryption feladatot, valamint speciális forgatókönyvekhez. Az alapvető feladatok [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Speciális forgatókönyvek esetén lásd: [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Milyen az Azure PowerShell-verzió támogatja az Azure Disk Encryption?

Az Azure PowerShell SDK legújabb verziójának segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-s verzió által támogatott.

> [!NOTE]
> A Linux Azure lemez titkosítási előzetes bővítmény elavult. További információkért lásd: [kivezetése Azure lemez titkosítási előzetes bővítmény Linux rendszerű IaaS virtuális gépek](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Használhatom az Azure Disk Encryption saját egyéni Linux-rendszerképen?

Az egyéni Linux-rendszerképek az Azure Disk Encryption, nem lehet alkalmazni. Csak a katalógus típusú Linux-rendszerképeket emelte ki a korábban a támogatott disztribúciók esetében támogatottak. Egyéni Linux-rendszerképek jelenleg nem támogatottak.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Használhatom-e frissítéseket Linux Red Hat használó virtuális gép a yum használatával frissítést?

Igen, hajtsa végre a frissítést, vagy a Red Hat Linux rendszerű virtuális gép javítása. További információkért lásd: [frissítéseinek alkalmazása a yum használatával frissítés használatával titkosított Azure IaaS Red Hat VM](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Mi a javasolt az Azure disk encryption munkafolyamat Linux?

A következő munkafolyamatot javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a kívánt operációs rendszer disztribúció és verziónak megfelelő módosítás nélküli tőzsdei katalógus-rendszerképből
* Készítsen biztonsági másolatot minden olyan csatlakoztatott meghajtók, a rendszer titkosítja.  Ez lehetővé teszi a helyreállítási, ha a hiba, például ha a virtuális gép újraindul titkosítási befejezése előtt történik.
* Titkosítása (is igénybe vehet több órákig vagy akár napokig attól függően, virtuálisgép-jellemző és bármely más csatolt lemez mérete)
* Testre szabhatja, és igény szerint adjon hozzá szoftvereket a lemezképet.

Ha ezt a munkafolyamatot nem lehetséges, a függő entitások [a Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) a platform tárolási fiók réteg lehet a teljes lemeztitkosítás, dm-crypt használata helyett.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Mi az a lemez "Rendelkeznek BEk-kel kötet" vagy "/ mnt/azure_bek_disk"?

"Rendelkeznek BEk-kel kötet" Windows vagy a "/ mnt/azure_bek_disk" Linux olyan helyi kötet, amely biztonságosan tárolja a titkosítási kulcsok titkosított Azure IaaS virtuális gépekhez.
> [!NOTE]
> Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tudhatok meg kérdéseket tehet fel, vagy visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat a [az Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption kapcsolatos leggyakoribb kérdések. Ezt a szolgáltatást vagy képességeivel kapcsolatos további információkért lásd a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Az Azure virtuális gép titkosítása](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure-beli adat-titkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
