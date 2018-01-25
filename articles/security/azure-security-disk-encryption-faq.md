---
title: "Az Azure Disk Encryption – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft Azure lemez titkosítása a Windows és Linux IaaS virtuális gépeket gyakran feltett kérdésekre adott válaszok."
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
ms.date: 08/11/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: 1144533cd64e80ed2b15c70732c94e332ea06c3d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-disk-encryption-faq"></a>Az Azure Disk Encryption – gyakori kérdések

Ez a cikk kapcsolatos gyakori kérdések (GYIK) lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket választ ad. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Általános kérdések
**K:** hol van Azure Disk Encryption általános elérhetőségével (GA)?

**V:** lemez titkosítása a Windows Azure Linux IaaS virtuális gépeket pedig általános rendelkezésre állás biztosításához az Azure nyilvános-régiók.

**K:** milyen felhasználó érhetők el az Azure Disk Encryption?

**V:** Azure lemez titkosítási GA támogatja az Azure Resource Manager sablonok, Azure PowerShell és az Azure parancssori felület. Ez lehetővé teszi a magas fokú rugalmasságot biztosít. Az infrastruktúra-szolgáltatási virtuális gépek lemeztitkosítás engedélyezésének három különböző lehetőségek állnak rendelkezésre. A felhasználói élmény és részletes útmutatás érhető el az Azure Disk Encryption további információkért lásd: Azure Disk Encryption üzembe helyezési forgatókönyvek és feladatait.

**K:** mennyi does Azure Disk Encryption költség?

**V:** használata virtuális gépek lemezei az Azure Disk Encryption titkosítási díjmentes.

**K:** mely virtuális gépek rétegek Azure Disk Encryption támogatja?

**V:** Azure Disk Encryption érhető el standard csomagra virtuális gépeken, beleértve a [A, D, DS, G, GS és F](https://azure.microsoft.com/pricing/details/virtual-machines/) adatsorozat IaaS virtuális gépeket. Akkor érhető el virtuális gépek prémium szintű Storage. Nem érhető el a virtuális gépek alapszintű rétegben.

**K:** mi Linux terjesztésekről does Azure Disk Encryption támogatás?

**V:** Azure Disk Encryption támogatott a következő Linux server disztribúciók és verziók:

| A Linux-disztribúció | Verzió | Támogatott titkosítási a kötet típusa|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Operációs rendszer és az adatok lemezre |
| Ubuntu | 14.04.5-DAILY-LTS | Operációs rendszer és az adatok lemezre |
| RHEL | 7.3 | Operációs rendszer és az adatok lemezre |
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
| SLES | Prioritás: 12-SP1 | Adatlemez |
| SLES | HPC 12 | Adatlemez |
| SLES | Prioritás: 11-SP4 | Adatlemez |
| SLES | 11 SP4 | Adatlemez |

**K:** hogyan tudom kezdheti Azure Disk Encryption?

**V:** első lépések, olvassa el a [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) találhatók meg.

**K:** titkosíthatók a rendszerindító és adatkötetek számára, az Azure Disk Encryption?

**V:** Igen, a Windows és Linux IaaS virtuális gépeket titkosíthatja rendszerindító- és adatkötetek számára. Windows virtuális gépek anélkül, hogy az operációs rendszer kötetének titkosításához az adatok nem titkosíthatók. Linux virtuális gépekhez anélkül, hogy az operációs rendszer kötetének titkosításához először adatmennyiség titkosíthatja. Miután az operációs rendszer kötetén Linux titkosított, Linux IaaS virtuális gépeket az operációs rendszer kötet titkosításának letiltása nem támogatott.

**K:** Does Azure Disk Encryption teszik lehetővé a saját kulcs (használatának BYOK) funkció érdekében?

**V:** Igen, megadhatja a saját kulcs titkosítási kulcsokat. Ezek a kulcsok elő az Azure Key Vault, ez az az Azure Disk Encryption kulcstároló. További információ a fő titkosítási kulcsok forgatókönyvek támogatása, tekintse meg az Azure Disk Encryption üzembe helyezési forgatókönyvek és feladatait.

**K:** használhatók az Azure által létrehozott kulcs titkosítási kulcsot?

**V:** Igen, használja a Azure Key Vault Azure disk encryption titkosítás kulcs létrehozásához. Ezek a kulcsok elő az Azure Key Vault, ez az az Azure Disk Encryption kulcstároló. A kulcs titkosítási kulcs támogatási példák további információkért lásd: Azure Disk Encryption üzembe helyezési forgatókönyvek és feladatait.

**K:** használhatók egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

**V:** a helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Használhatja az Azure Key Vault szolgáltatás csak a titkosítási kulcsok védelme. A kulcs titkosítási kulcs támogatási példák további információkért lásd: Azure Disk Encryption üzembe helyezési forgatókönyvek és feladatait.

**K:** Mik az Előfeltételek konfigurálása az Azure Disk Encryption?

**V:** van egy előfeltételként szükséges PowerShell-parancsfájlt. Ezzel a parancsprogrammal hozzon létre egy Azure Active Directory-alkalmazás, hozzon létre egy új kulcstartó vagy egy meglévő kulcstároló a lemezekhez titkosítási való hozzáférés engedélyezése a titkosítás és a biztonságos működés érdekében titkos kulcsok és a kulcsok beállításához. A kulcs titkosítási kulcs támogatási példák további információkért lásd: Azure Disk Encryption előfeltétel és üzembe helyezési forgatókönyvek és feladatait.

**K:** Honnan kaphatok további információt a PowerShell használata Azure Disk Encryption konfigurálásához?

**V:** van néhány nagy cikkeket hogyan hajthat végre a alapszintű Azure Disk Encryption feladatokat, valamint speciális forgatókönyvekhez. Az egyszerű feladatokat [Azure Disk Encryption megismerkedhet az Azure PowerShell – 1. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Speciális forgatókönyvek esetén lásd: [Azure Disk Encryption megismerkedhet az Azure PowerShell – 2. rész](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

**K:** Azure PowerShell verziójának Azure Disk Encryption támogatja?

**V:** Azure Disk Encryption konfigurálása az Azure PowerShell SDK legújabb verzióját használja. Töltse le a legújabb verzióját [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-ás verziója támogatott.

> [!NOTE]
> A Linux Azure lemez titkosítási preview bővítmény elavult. További információkért lásd: [elavulttá Azure lemez titkosítási preview bővítmény Linux IaaS virtuális gépek](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

**K:** alkalmazhatók az Azure Disk Encryption saját egyéni Linux rendszerképre?

**V:** Azure Disk Encryption az egyéni Linux-lemezkép nem lehet alkalmazni. A támogatott disztribúcióiról feltüntettük korábban csak a lévő Linux képek támogatjuk. Jelenleg nem támogatott Linux egyéni lemezképek.

**K:** I alkalmazható frissítések Linux Red Hat virtuális gép által használt yum frissítés?

**V:** Igen, hajtsa végre a frissítést, vagy a Red Hat Linux virtuális gép javítása. További információkért lásd: [egy titkosított Azure IaaS Red Hat VM frissítéseinek alkalmazása a yum update segítségével](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

**K:** Mi az az ajánlott Azure lemez titkosítási munkafolyamat Linux?

**V:** az alábbi munkafolyamat javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a kívánt operációs rendszer distro és a verziója megfelelő változatlan készlet gyűjtemény kép
* Készítsen biztonsági másolatot minden csatlakoztatott meghajtókon tárolt titkosított marad.  Ez lehetővé teszi a helyreállítási hiba, például ha a virtuális gép újraindítása után titkosítási befejezése előtt esetén.
* Titkosításához (eltarthat több órákig vagy akár napokig függően vm jellemzőit és a mellékelt adatok lemezek mérete)
* Testre szabhatja, és adjon hozzá szoftvereket a lemezképet, igény szerint.

Ha a munkafolyamat nem lehetséges, a függő [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE), a platform tárolási fiók réteg lehet a teljes lemez titkosítása dm-crypt használata helyett.

**K:** Mi az a lemez "Bek Volume" vagy "/ mnt/azure_bek_disk"?

**V:** "Bek kötet" a Windows vagy a "/ mnt/azure_bek_disk" Linux olyan helyi adatok kötet, amely biztonságosan tárolja a titkosítási kulcsokat a titkosított Azure IaaS virtuális gépeket.
> [!NOTE]
> Nem törölhető és nem bármely ezt a lemezt a tartalom szerkesztése. Nem leválasztani a lemezt, mivel a titkosítási kulcs jelenléte szükséges a titkosítási műveleteket a IaaS virtuális Gépen.

**K:** ahol lehet ugrani kérdései vannak, vagy visszajelzést?

**V:** kérdései vannak, vagy visszajelzést adhat a [Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, további információk a leggyakoribb Azure Disk Encryption kapcsolatos kérdésekre. Ez a szolgáltatás és platformképességei kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure virtuális gép titkosítása](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Az Azure data titkosítását](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
