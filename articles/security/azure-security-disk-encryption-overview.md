---
title: Mi az Azure Disk Encryption?
description: Ez a cikk az Azure Disk Encryption áttekintést nyújt.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b00a5262b1e144aa47cd7fd640906225ff4fecd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068791"
---
# <a name="azure-disk-encryption-overview"></a>Az Azure Disk Encryption áttekintése

Az Azure Disk Encryption segítségével a biztonsági és megfelelőségi követelmények kielégítésével az adatok biztonságos megőrzésében. Használja a [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux Azure-beli virtuális gépek (VM) az operációs rendszer és az adatlemezek kötettitkosítást biztosít. Is integrálva van az [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsok és titkos kódokat, és minden adatot a Virtuálisgép-lemezek inaktív állapotban az Azure storage titkosítja. Általában minden nyilvános Azure-régióban és a standard szintű virtuális gépek és az Azure Premium Storage virtuális gépek Azure Government-régiók rendelkezésre állása az Azure Disk Encryption Windows és Linux rendszerű virtuális gépekhez. 

Az Azure Security Center, ha rögtön riasztást kap, ha azok nem titkosított virtuális gépek. A riasztások magas súlyossági szintű szabályzatként jelenik meg, és az javasoljuk, hogy ezek a virtuális gépek titkosításához.

![Az Azure Security Center titkosítási riasztás](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, a hálózati, vagy a számítási erőforrás-használat és a további licencek vagy előfizetések költségeit eredményez.


## <a name="encryption-scenarios"></a>Titkosítási megoldások

Az Azure Disk Encryption, kezelheti szervezete biztonsági és megfelelőségi követelmények szerint biztonságossá tétele az Azure virtuális gépek, iparági szabványnak megfelelő titkosítási technológia használatával. Az ügyfél által felügyelt kulcsok és szabályzatok használatának (BYOK) rendszerindító virtuális gép konfigurálása, és ezek a kulcsok használatát naplózni a key vaultban lévő is.

Az Azure Disk Encryption támogatja a következő forgatókönyvet:

* Titkosítás engedélyezése és letiltása a támogatott Azure-gyűjtemény lemezképei alapján létrehozott új gépeken.
* Titkosítás engedélyezése és letiltása a meglévő virtuális gépeken, amelyek az Azure-ban futnak.
* Titkosítás engedélyezése és letiltása előzetes titkosítással VHD- és titkosítási kulcsok alapján létrehozott új Windows virtuális gépeken.
* A Windows virtuálisgép-méretezési csoport titkosítás engedélyezése és letiltása állítja be.
* Az adatok titkosítás engedélyezése és letiltása meghajtók Linux virtuálisgép-méretezési csoportok esetében.
* Titkosítás engedélyezése és letiltása a felügyelt lemezes virtuális gépek.
* Egy meglévő titkosított prémium és a nem Premium Storage virtuális gépek titkosítási beállításainak frissítése.
* Biztonsági mentése és visszaállítása titkosított virtuális gépek.
* A saját titkosítási (BYOE) használata, és a saját kulcs (használatának BYOK) forgatókönyvek, amelyben az ügyfelek a saját titkosítási kulcsok használatához, és tárolja őket egy Azure key vault használata.

Azt is a következő eseteket támogatja a virtuális gépek Ha engedélyezve vannak a Microsoft Azure-ban:

* Az Azure Key Vault-integráció.
* [Standard szintű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/). [Linux rendszerű virtuális gépek](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) ezen a szinten belül meg kell felelnie a minimális memóriára vonatkozó követelmény, 7 GB. 
* A támogatott Azure-katalógus-rendszerképek virtuális gépek Windows és Linux rendszerű virtuális gépek, felügyelt lemezes és a méretezési csoport titkosításának engedélyezésével állítható be.
* Az operációs rendszer és az adatok titkosításának letiltása meghajtók Windows virtuális gépek esetén a méretezési csoport virtuális gépei és felügyelt lemezes virtuális gépek.
* Adatmeghajtók letiltását titkosításának Linux rendszerű virtuális gépekhez, a méretezési csoport virtuális gépei, és a felügyelt lemezes virtuális gépek.
* A Windows ügyfél operációs rendszer futtató virtuális gépek titkosításának engedélyezését.
* Csatlakoztatási elérési úttal köteteken titkosításának engedélyezését.
* Linux rendszerű virtuális gépek, amelyeken lemez összevonása (RAID) mdadm használatával titkosításának engedélyezése.
* Adatlemezek LVM használó Linux rendszerű virtuális gépek titkosításának engedélyezését.
* A Linux rendszerű virtuális gép operációsrendszer- és adatlemezek titkosításának engedélyezése.

   > [!NOTE]
   > Egyes Linux-disztribúciókon az operációs rendszer meghajtótitkosítás nem támogatott. További információkért lásd: a [Azure Disk Encryption – gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) cikk.
   
* A Windows tárolóhelyek elején a Windows Server 2016 rendszerben konfigurált virtuális gépek titkosításának engedélyezését.
* Biztonsági mentése és visszaállítása titkosított virtuális gépek kulcstitkosítási kulcs-(KEK) és a nem KEK-forgatókönyvekhez.

Az Azure Disk Encryption nem működik a következő forgatókönyvek, szolgáltatások és technológiák:

* Alapszintű virtuális gép vagy a klasszikus virtuális gép létrehozási módjának használatával létrehozott virtuális gépek titkosítása.
* Egy operációs rendszer vagy adatok meghajtó, Linux rendszerű virtuális gép titkosításának letiltása, ha az operációs rendszer meghajtójának titkosítva van.
* A Linux rendszerű virtuálisgép-méretezési csoport operációs rendszer meghajtójának titkosítására állítja be.
* Szoftveres RAID-rendszerekkel való konfigurált Windows virtuális gépek titkosítása.
* Egyéni rendszerképek Linux rendszerű virtuális gépek titkosítása.
* Integráció a helyszíni kulcskezelő rendszerrel.
* Az Azure Files (megosztott fájlrendszert).
* Hálózati fájlrendszer (NFS).
* A dinamikus köteteket.

## <a name="encryption-features"></a>Titkosítási funkciók

Ha engedélyezi, és üzembe helyezése az Azure Disk Encryption az Azure virtuális gépek, engedélyezni kell a következő lehetőségeket konfigurálhatja:

* Az operációsrendszer-kötet védelmét a rendszerindító kötet a tárolás titkosítása.
* Adatkötetek védelme érdekében az adatkötetek a tárolás titkosítása.
* Windows virtuális gépek az operációsrendszer- és meghajtók titkosításának letiltása.
* Az adatok titkosításának letiltása meghajtók Linux rendszerű virtuális gépekhez (csak akkor, ha az operációs rendszer meghajtójának nem titkosított).
* A titkosítási kulcsok és titkos kulcsok Azure Key Vault-előfizetésből az védelmére.
* Jelentéskészítés a titkosított virtuális gép titkosítási állapotát.
* A lemez titkosítási konfigurációs beállításainak eltávolítása a virtuális gépről.
* Biztonsági mentése, és a titkosított virtuális gépek visszaállítása az Azure Backup szolgáltatás használatával.

Virtuális gépek a Windows és Linux rendszerhez készült Azure Disk Encryption tartalmazza:

* [A disk encryption bővítményt a Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [A disk encryption bővítményt linuxos](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [A PowerShell-lemez titkosítási parancsmagok](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Az Azure CLI-vel lemez titkosítási parancsmagok](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Az Azure Resource Manager-lemez titkosítási sablonok](azure-security-disk-encryption-appendix.md#resource-manager-templates).

Az Azure Disk Encryption Windows vagy Linux operációs rendszer futtató virtuális gépek esetén támogatott. A támogatott operációs rendszerekkel kapcsolatos további információkért lásd: [– gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).

> [!NOTE]
> Nem jár további költségekkel együtt az Azure Disk Encryption Virtuálisgép-lemezek titkosítása. Standard szintű [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) vonatkozik a kulcstartóhoz, amely a titkosítási kulcsok tárolására szolgál. 

## <a name="encryption-workflow"></a>Titkosítási munkafolyamat

Ahhoz, hogy a lemeztitkosítás Windows és Linux rendszerű virtuális gépekhez, tegye a következőket:

1. Részvétel a az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül a lemeztitkosítás engedélyezve, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg az új virtuális gép titkosításának titkosítási konfiguráció.
   * Támogatott a gyűjtemény lemezképei alapján létrehozott új virtuális gépeket, és a meglévő virtuális gépek, amelyek már futtatnak az Azure-ban adja meg a titkosítási konfiguráció engedélyezze a titkosítást a virtuális gépen.

1. Hozzáférést biztosít az Azure platformon, olvassa el a titkosítási kulcs adatai (BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-jelszót a virtuális gép titkosításának a key vaultból.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.

   ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>A visszafejtés munkafolyamat
Tiltsa le a lemeztitkosítás virtuális gépekhez, hajtsa végre a következő magas szintű lépéseket:

1. Letiltja a titkosítást (visszafejtési) egy futó virtuális gépen az Azure-ban válassza ki, és adja meg a visszafejtési konfigurációt. Az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül is letilthatja.

   Ebben a lépésben letiltja a titkosítást az operációs rendszer vagy adatmennyiség, illetve mindkettőt a futó Windows virtuális gépen. Az előző szakaszban említett operációs rendszer lemeztitkosítás linuxos letiltása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtókon a Linux rendszerű virtuális gépek mindaddig, amíg az operációsrendszer-lemez nincs titkosítva.

1. A Virtuálisgép-modell és a virtuális gép van megjelölve, Azure-frissítések visszafejteni. A virtuális gép tartalmát már nem titkosított inaktív.

   > [!NOTE]
   > A titkosítási művelet nem törli a kulcstartót és a titkosítási kulcs adatai (BitLocker titkosítási kulcsok Windows rendszerekhez) vagy a Linux-jelszó.
   >
   > Nem támogatott Linux operációs rendszer lemez titkosításának letiltása. A visszafejtési lépés csak a Linux rendszerű virtuális gépek adatmeghajtók engedélyezett.
   >
   > Adatok lemeztitkosítás linuxos letiltása nem támogatott, ha az operációs rendszer meghajtójának titkosítva van.


## <a name="encryption-workflow-previous-release"></a>Titkosítási munkafolyamat (előző kiadás)

Az új kiadásban az Azure Disk Encryption, így nem kell adja meg egy Azure Active Directory (Azure AD) application paramétert a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással akkor már nem kell engedélyezése titkosítási lépés során egy Azure AD hitelesítő adatok megadása. Valamennyi új virtuális gépeket titkosítani kell az Azure AD-alkalmazás paraméterek nélkül az új kiadás használatakor. Már az Azure AD-alkalmazás paraméterekkel titkosított virtuális gépek továbbra is támogatottak, és továbbra is az Azure AD-szintaxissal kell tartani. Engedélyezi a lemeztitkosítást a Windows és Linux rendszerű virtuális gépek (előző kiadás), hajtsa végre a következő lépéseket:

1. Egy titkosítási forgatókönyvet a felsorolt forgatókönyvek közül választhat a [titkosítási megoldások](#encryption-scenarios) szakaszban.

1. Részvétel a az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül a lemeztitkosítás engedélyezve, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg az új virtuális gép titkosításának titkosítási konfiguráció.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást a virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyek már futtatnak az Azure-ban.

1. Hozzáférést biztosít az Azure platformon, olvassa el a titkosítási kulcs adatai (BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-jelszót a virtuális gép titkosításának a key vaultból.

1. Adja meg az Azure ad-ben a titkosítási kulcsot a key vault anyag írása identitása. Ez a lépés lehetővé teszi a titkosítást a 2. lépésben említett forgatókönyvek esetén a virtuális gépen.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.


## <a name="terminology"></a>Terminológia
Az alábbi táblázat az egyes az Azure disk encryption dokumentációban használt gyakori kifejezések határozza meg:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Egy [Azure ad-ben](https://azure.microsoft.com/documentation/services/active-directory/) fiók hitelesítéséhez, tárolása és titkos kódok lekérése a key vault segítségével. |
| Azure Key Vault | A Key Vault szolgáltatás titkosítási, key management, amelyek az rendelkezik a Federal Information Processing szabványok (FIPS) hitelesített hardveres biztonsági modulokban. Ezen irányelvek segítenek a kriptográfiai kulcsok és a bizalmas, titkos kulcsok védelme érdekében. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparági ismerhető fel Windows kötet titkosítási technológia, amely lehetővé teszi a lemeztitkosítást a Windows virtuális gépek, hogy. |
| BEK | A BitLocker titkosítási kulcsok (rendelkeznek BEk-KEL) segítségével titkosítja az operációs rendszer rendszerindító kötet és adatkötetek. BEKs számára biztosít védelmet a kulcstartóban található titkos kódként. |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) kezelésére és felügyeletére az Azure-erőforrások parancssorból van optimalizálva.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) a Linux-alapú, átlátható lemeztitkosítás alrendszer, amely engedélyezi a lemeztitkosítást a Linux rendszerű virtuális gépek szolgál. |
| Kulcsalapú titkosítás kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védelméhez vagy burkolásához a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| PowerShell-parancsmagok | További információkért lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

## <a name="next-steps"></a>További lépések

Első lépésként tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

