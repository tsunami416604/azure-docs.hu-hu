---
title: Az Azure Disk Encryption – gyakori kérdések |} Microsoft Docs
description: Ez a cikk ismerteti a Microsoft Azure lemez titkosítása a Windows és Linux IaaS virtuális gépeket gyakran feltett kérdésekre adott válaszok.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disk-encryption-faq"></a>Az Azure Disk Encryption – gyakori kérdések

Ez a cikk kapcsolatos gyakori kérdések (GYIK) lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket választ ad. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Ha az Azure Disk Encryption az általános elérhetőségével (GA)?

Általános rendelkezésre állás biztosításához az Azure nyilvános-régiók Azure lemez titkosítása a Windows és Linux IaaS virtuális gépeket.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Milyen a felhasználói élmény érhetők el az Azure Disk Encryption?

Az Azure lemez titkosítási GA támogatja az Azure Resource Manager-sablonok, Azure PowerShell és az Azure parancssori felület. Ez lehetővé teszi a magas fokú rugalmasságot biztosít. Az infrastruktúra-szolgáltatási virtuális gépek lemeztitkosítás engedélyezésének három különböző lehetőségek állnak rendelkezésre. A felhasználói élmény és részletes útmutatás érhető el az Azure Disk Encryption további információkért lásd: [Azure Disk Encryption üzembe helyezési forgatókönyvek és lép](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Milyen mértékű nem költségű Azure Disk Encryption?

Virtuális gépek lemezei az Azure Disk Encryption titkosítási ingyenesek, de nincsenek az Azure Key Vault használatával járó díjakat. További információk az Azure Key Vault költségek tekintse meg a [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/) lap.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Mely virtuálisgép-réteg nem támogatja az Azure Disk Encryption?

Az Azure Disk Encryption érhető el standard csomagra virtuális gépeken, beleértve a [A, D, DS, G, GS és F](https://azure.microsoft.com/pricing/details/virtual-machines/) adatsorozat IaaS virtuális gépeket. Akkor érhető el virtuális gépek prémium szintű Storage. Nem érhető el a virtuális gépek alapszintű rétegben.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Milyen Linux terjesztésekről támogatja az Azure Disk Encryption?

Az Azure Disk Encryption támogatott a következő Linux server disztribúciók és verziók:

| A Linux-disztribúció | Verzió | Támogatott titkosítási a kötet típusa|
| --- | --- |--- |
| Ubuntu | 16.04-NAPI-ES LTS VERZIÓ | Operációs rendszer és az adatok lemezre |
| Ubuntu | 14.04.5-DAILY-LTS | Operációs rendszer és az adatok lemezre |
| RHEL | 7.4 | Adatok lemez * |
| RHEL | 7.3 | Adatok lemez * |
| RHEL | 7.2 | Adatok lemez * |
| RHEL | 6.8 | Adatok lemez * |
| RHEL | 6.7 | Adatok lemez * |
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
| SLES | Prioritás: 12-SP1 | Adatlemez |
| SLES | HPC 12 | Adatlemez |
| SLES | Prioritás: 11-SP4 | Adatlemez |
| SLES | 11 SP4 | Adatlemez |

*__Adatlemez az RHEL ADE esetén támogatott. A jelenlegi ADE implementációja operációsrendszer-lemez esetében működik, de közösen jelenleg nem támogatott. A Microsoft és a Red Hat dolgozik közösen támogatott megoldás. Az idő alatt is hivatkozni lehessen a ADE tanulmány a Linux operációs rendszert futtató lemeztitkosítás [Itt](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan tudom kezdheti Azure Disk Encryption?

Első lépések, olvassa el a [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) találhatók meg.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és adatkötetek számára, az Azure Disk Encryption is titkosítani?

Igen, rendszerindító- és adatkötetek titkosíthatja a Windows és Linux IaaS virtuális gépeket. Windows virtuális gépek anélkül, hogy az operációs rendszer kötetének titkosításához az adatok nem titkosíthatók. Linux virtuális gépekhez anélkül, hogy az operációs rendszer kötetének titkosításához először adatmennyiség titkosíthatja. Miután az operációs rendszer kötetén Linux titkosított, Linux IaaS virtuális gépeket az operációs rendszer kötet titkosításának letiltása nem támogatott.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Azure Disk Encryption lehetővé teszi, hogy a saját kulcs (használatának BYOK) funkció?

Igen, megadhatja a saját kulcs titkosítási kulcsokat. Ezek a kulcsok elő az Azure Key Vault, ez az az Azure Disk Encryption kulcstároló. További információ a fő titkosítási kulcsok helyzetek feltételeinek megteremtésére című [Azure Disk Encryption üzembe helyezési forgatókönyvek és lép](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure által létrehozott kulcs titkosítási kulcs használata

Igen, az Azure Key Vault segítségével Azure disk encryption titkosítás kulcs létrehozása. Ezek a kulcsok elő az Azure Key Vault, ez az az Azure Disk Encryption kulcstároló. A kulcs titkosítási kulcs támogatási példák további információkért lásd: [Azure Disk Encryption üzembe helyezési forgatókönyvek és lép](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használhatok egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Használhatja az Azure Key Vault szolgáltatás csak a titkosítási kulcsok védelme. A kulcs titkosítási kulcs támogatási példák további információkért lásd: [Azure Disk Encryption üzembe helyezési forgatókönyvek és lép](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik az Előfeltételek konfigurálása az Azure Disk Encryption?

Nincs előfeltétel PowerShell-parancsfájlt. Ezzel a parancsprogrammal hozzon létre egy Azure Active Directory-alkalmazás, hozzon létre egy új kulcstartó vagy egy meglévő kulcstároló a lemezekhez titkosítási való hozzáférés engedélyezése a titkosítás és a biztonságos működés érdekében titkos kulcsok és a kulcsok beállításához. A kulcs titkosítási kulcs támogatási példák további információkért lásd: [Azure Disk Encryption előfeltétel és üzembe helyezési forgatókönyvek és lép](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Hol találhatok további információt a PowerShell használata Azure Disk Encryption konfigurálásához?

Van néhány nagy cikkeket hogyan hajthat végre a alapszintű Azure Disk Encryption feladatokat, valamint speciális forgatókönyvekhez. Az egyszerű feladatokat [Azure Disk Encryption megismerkedhet az Azure PowerShell – 1. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Speciális forgatókönyvek esetén lásd: [Azure Disk Encryption megismerkedhet az Azure PowerShell – 2. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure PowerShell melyik verzióját nem támogatja az Azure Disk Encryption?

Azure Disk Encryption konfigurálása az Azure PowerShell SDK legújabb verzióját használja. Töltse le a legújabb verzióját [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-ás verziója támogatott.

> [!NOTE]
> A Linux Azure lemez titkosítási preview bővítmény elavult. További információkért lásd: [elavulttá Azure lemez titkosítási preview bővítmény Linux IaaS virtuális gépek](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Alkalmazhatók az Azure Disk Encryption saját egyéni Linux rendszerképre?

Az egyéni Linux lemezkép az Azure Disk Encryption nem alkalmazható. A támogatott disztribúcióiról feltüntettük korábban csak a lévő Linux képek támogatjuk. Jelenleg nem támogatott Linux egyéni lemezképek.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Képes vagyok frissítések alkalmazása Linux Red Hat virtuális gép által használt yum frissítés?

Igen, hajtsa végre a frissítést, vagy a Red Hat Linux virtuális gép javítása. További információkért lásd: [egy titkosított Azure IaaS Red Hat VM frissítéseinek alkalmazása a yum update segítségével](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Mi az az ajánlott Azure lemez titkosítási munkafolyamat Linux?

Az alábbi munkafolyamat javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a kívánt operációs rendszer distro és a verziója megfelelő változatlan készlet gyűjtemény kép
* Készítsen biztonsági másolatot minden csatlakoztatott meghajtókon tárolt titkosított marad.  Ez lehetővé teszi a helyreállítási hiba, például ha a virtuális gép újraindítása után titkosítási befejezése előtt esetén.
* Titkosításához (eltarthat több órákig vagy akár napokig függően vm jellemzőit és a mellékelt adatok lemezek mérete)
* Testre szabhatja, és adjon hozzá szoftvereket a lemezképet, igény szerint.

Ha a munkafolyamat nem lehetséges, a függő [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE), a platform tárolási fiók réteg lehet a teljes lemez titkosítása dm-crypt használata helyett.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Mi az a lemez "Bek Volume" vagy "/ mnt/azure_bek_disk"?

"Bek volume" a Windows vagy a "/ mnt/azure_bek_disk" Linux nem egy helyi adatmennyiség, amely biztonságosan tárolja a titkosítási kulcsokat a titkosított Azure IaaS virtuális gépeket.
> [!NOTE]
> Nem törölhető és nem bármely ezt a lemezt a tartalom szerkesztése. Nem leválasztani a lemezt, mivel a titkosítási kulcs jelenléte szükséges a titkosítási műveleteket a IaaS virtuális Gépen.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Ahol folytathatja kérdései vannak, vagy visszajelzést?

Kérdései vannak, vagy visszajelzést adhat a [Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, további információk a leggyakoribb Azure Disk Encryption kapcsolatos kérdésekre. Ez a szolgáltatás és platformképességei kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure virtuális gép titkosítása](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Az Azure data titkosítását](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
