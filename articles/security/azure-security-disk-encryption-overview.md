---
title: Mi az Azure Disk Encryption?
description: Ez a cikk áttekintést nyújt Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/17/2019
ms.custom: seodec18
ms.openlocfilehash: a67f19f0823827dad74e7aba15a92d696fbf580b
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304268"
---
# <a name="azure-disk-encryption-overview"></a>Azure Disk Encryption áttekintése

Azure Disk Encryption segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. A Windows [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját használja, hogy mennyiségi titkosítást biztosítson az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára. Emellett integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) a lemezes titkosítási kulcsok és titkos kódok szabályozásához és kezeléséhez, és gondoskodik arról, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az Azure Storage-ban. A Windows-és Linux-alapú virtuális gépek Azure Disk Encryption általánosan elérhetők az összes Azure-beli nyilvános régióban és Azure Government régióban a standard virtuális gépekhez és a virtuális gépekhez az Azure Premium Storage. 

Az Azure Security Center, ha rögtön riasztást kap, ha azok nem titkosított virtuális gépek. A riasztások magas súlyossági szintű szabályzatként jelenik meg, és az javasoljuk, hogy ezek a virtuális gépek titkosításához.

![Az Azure Security Center titkosítási riasztás](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, a hálózati, vagy a számítási erőforrás-használat és a további licencek vagy előfizetések költségeit eredményez.


## <a name="encryption-scenarios"></a>Titkosítási megoldások

A Azure Disk Encryption segítségével a szervezeti biztonsági és megfelelőségi követelményeket úgy kezelheti, ha az Azure-beli virtuális gépeket az iparági szabványnak megfelelő titkosítási technológiával védi. A virtuális gépeket konfigurálhatja úgy is, hogy az ügyfél által vezérelt kulcsok és szabályzatok (BYOK-EK) alá induljon, és naplózza a kulcsok használatát a kulcstartóban.

Azure Disk Encryption a következő felhasználói forgatókönyveket támogatja:

* A támogatott Azure Gallery-rendszerképekből létrehozott új virtuális gépek titkosításának engedélyezése és letiltása.
* Az Azure-ban futó meglévő virtuális gépek titkosításának engedélyezése és letiltása.
* A titkosítás engedélyezése és letiltása az előre titkosított VHD-és titkosítási kulcsokból létrehozott új Windows-alapú virtuális gépeken.
* A titkosítás engedélyezése és letiltása a Windows rendszerű virtuálisgép-méretezési csoportokban.
* A Linux rendszerű virtuálisgép-méretezési csoportokhoz tartozó adatmeghajtók titkosításának engedélyezése és letiltása.
* Felügyelt lemezes virtuális gépek titkosításának engedélyezése és letiltása.
* Meglévő titkosított prémium és nem Premium Storage virtuális gép titkosítási beállításainak frissítése.
* Titkosított virtuális gépek biztonsági mentése és visszaállítása.
* Saját titkosítást (BYOE) alkalmazhat, és saját kulcsokra (BYOK) használhat, amelyekben az ügyfelek a saját titkosítási kulcsaikat használják, és egy Azure Key vaultban tárolják őket.

Emellett a következő forgatókönyveket is támogatja a virtuális gépekhez, amikor engedélyezve vannak Microsoft Azureban:

* Az Azure Key Vault-integráció.
* [Standard szintű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/) , amelyek megfelelnek a minimálisan szükséges [memória követelményeinek](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* A titkosítás engedélyezése Windows és Linux rendszerű virtuális gépeken, felügyelt lemezeken és a virtuális gépek méretezési csoportján a támogatott Azure Gallery-rendszerképekből.
* A Windows rendszerű virtuális gépek, a méretezési csoport virtuális gépei és a felügyelt lemezes virtuális gépek titkosításának letiltása az operációsrendszer-és adatmeghajtókon
* A Linux rendszerű virtuális gépek, a méretezési csoport virtuális gépei és a felügyelt lemezes virtuális gépek adatmeghajtóinak titkosításának letiltása.
* A titkosítás engedélyezése a Windows ügyfél operációs rendszerét futtató virtuális gépeken.
* A kötetek titkosításának engedélyezése csatlakoztatási útvonalakkal.
* A titkosítás engedélyezése olyan Linux rendszerű virtuális gépeken, amelyek a mdadm használatával vannak konfigurálva lemezes csíkozással (RAID).
* Az LVM-t használó Linux rendszerű virtuális gépek titkosításának engedélyezése adatlemezekhez.
* A titkosítás engedélyezése a Linux RENDSZERű virtuális gépek és az adatlemezek esetében.

   > [!NOTE]
   > Egyes Linux-disztribúciókon az operációs rendszer meghajtótitkosítás nem támogatott. További tudnivalókért tekintse meg a [Azure Disk Encryption támogatott operációs rendszereket: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Titkosítás engedélyezése olyan virtuális gépeken, amelyek Windows-tárolóhelyekkel vannak konfigurálva a Windows Server 2016-es verziójától kezdődően. A Közvetlen tárolóhelyek (S2D) még nem támogatott.
* Titkosított virtuális gépek biztonsági mentése és helyreállítása a kulcs titkosítási kulcs (KEK) és a nem KEK-forgatókönyvek esetében.

A Azure Disk Encryption a következő forgatókönyvek, funkciók és technológiák esetében nem működik:

* A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gép vagy virtuális gépek titkosítása.
* Egy Linux rendszerű virtuális gép operációsrendszer-meghajtóján vagy adatmeghajtóján lévő titkosítás letiltása az operációs rendszer meghajtójának titkosítása esetén.
* Operációs rendszer meghajtójának titkosítása linuxos virtuálisgép-méretezési csoportokhoz.
* A szoftveres RAID-rendszerekkel konfigurált Windows-alapú virtuális gépek titkosítása.
* Egyéni lemezképek titkosítása Linux rendszerű virtuális gépeken.
* Integráció egy helyszíni kulcskezelő rendszerrel.
* Az Azure Files (megosztott fájlrendszert).
* Hálózati fájlrendszer (NFS).
* A dinamikus köteteket.

## <a name="encryption-features"></a>Titkosítási funkciók

Azure Disk Encryption Azure-beli virtuális gépekhez való engedélyezésekor és telepítésekor a következő képességeket engedélyezheti:

* Az operációs rendszer kötetének titkosítása, hogy megvédje a rendszerindító kötetet a tárolóban található nyugalmi állapotban.
* Az adatkötetek titkosításával gondoskodhat a tárterületen tárolt adatmennyiségek védelme érdekében.
* A titkosítás letiltása a Windows rendszerű virtuális gépek operációsrendszer-és adatmeghajtóján.
* A Linux rendszerű virtuális gépekhez tartozó adatmeghajtók titkosításának letiltása (csak akkor, ha az operációsrendszer-meghajtó nincs titkosítva).
* A titkosítási kulcsok és a titkos kódok védelme a Azure Key Vault-előfizetésben.
* A titkosított virtuális gép titkosítási állapotának jelentése.
* A lemez titkosítási konfigurációs beállításainak eltávolítása a virtuális gépről.
* A titkosított virtuális gépek biztonsági mentése és visszaállítása a Azure Backup szolgáltatás használatával.

A Windows és a Linux rendszerű virtuális gépekhez Azure Disk Encryption a következők:

* [A Windows lemez titkosítási bővítménye](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [A Linux rendszerhez készült lemez-titkosítási bővítmény](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [A PowerShell-lemez titkosítási parancsmagjai](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Az Azure CLI lemez titkosítási parancsmagjai](/cli/azure/vm/encryption?view=azure-cli-latest).
* [A Azure Resource Manager lemez titkosítási sablonjai](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Nem jár további költségekkel együtt az Azure Disk Encryption Virtuálisgép-lemezek titkosítása. Standard szintű [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) vonatkozik a kulcstartóhoz, amely a titkosítási kulcsok tárolására szolgál. 

## <a name="encryption-workflow"></a>Titkosítási munkafolyamat

Ahhoz, hogy a lemeztitkosítás Windows és Linux rendszerű virtuális gépekhez, tegye a következőket:

1. Részvétel a az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül a lemeztitkosítás engedélyezve, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfigurációt, hogy engedélyezze a titkosítást egy új virtuális gépen.
   * A támogatott katalógus-rendszerképekből létrehozott új virtuális gépek és az Azure-ban már meglévő virtuális gépek esetében adja meg a titkosítási konfigurációt a virtuális gép titkosításának engedélyezéséhez.

1. Hozzáférés biztosítása az Azure platformhoz a Key vaultban lévő titkosítási kulcs (a Windows rendszerekhez és a Linux-jelszóhoz tartozó titkosítási kulcsok) elolvasásához, hogy a virtuális gép titkosítása engedélyezve legyen.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.

   ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>A visszafejtés munkafolyamat
A virtuális gépek lemezes titkosításának letiltásához hajtsa végre a következő magas szintű lépéseket:

1. Letilthatja a titkosítást (visszafejtést) egy futó virtuális gépen az Azure-ban, és megadhatja a visszafejtési konfigurációt. Az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül is letilthatja.

   Ez a lépés letiltja az operációs rendszer vagy az adatkötet titkosítását, vagy mindkettőt a futó Windows-alapú virtuális gépen. Az előző szakaszban említett operációs rendszer lemeztitkosítás linuxos letiltása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtókon a Linux rendszerű virtuális gépek mindaddig, amíg az operációsrendszer-lemez nincs titkosítva.

1. Az Azure frissíti a virtuálisgép-szolgáltatás modelljét, a virtuális gép pedig visszafejtett van megjelölve. A virtuális gép tartalmát már nem titkosított inaktív.

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

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfigurációt, hogy engedélyezze a titkosítást egy új virtuális gépen.
   * A piactéren és az Azure-ban már futó meglévő virtuális gépeken létrehozott új virtuális gépek esetében adja meg a titkosítási konfigurációt a virtuális gép titkosításának engedélyezéséhez.

1. Hozzáférés biztosítása az Azure platformhoz a Key vaultban lévő titkosítási kulcs (a Windows rendszerekhez és a Linux-jelszóhoz tartozó titkosítási kulcsok) elolvasásához, hogy a virtuális gép titkosítása engedélyezve legyen.

1. Adja meg az Azure ad-ben a titkosítási kulcsot a key vault anyag írása identitása. Ez a lépés lehetővé teszi a virtuális gép titkosítását a 2. lépésben említett forgatókönyvek esetében.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.


## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure Disk Encryption dokumentációjában használt általános kifejezéseket ismerteti:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Egy [Azure ad-ben](https://azure.microsoft.com/documentation/services/active-directory/) fiók hitelesítéséhez, tárolása és titkos kódok lekérése a key vault segítségével. |
| Azure Key Vault | A Key Vault szolgáltatás titkosítási, key management, amelyek az rendelkezik a Federal Information Processing szabványok (FIPS) hitelesített hardveres biztonsági modulokban. Ezen irányelvek segítenek a kriptográfiai kulcsok és a bizalmas, titkos kulcsok védelme érdekében. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| BitLocker |A [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparág által felismert Windows mennyiségi titkosítási technológia, amely lehetővé teszi a lemezes titkosítás használatát a Windows rendszerű virtuális gépeken. |
| BEK | A BitLocker titkosítási kulcsok (rendelkeznek BEk-KEL) segítségével titkosítja az operációs rendszer rendszerindító kötet és adatkötetek. BEKs számára biztosít védelmet a kulcstartóban található titkos kódként. |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) kezelésére és felügyeletére az Azure-erőforrások parancssorból van optimalizálva.|
| DM-Crypt |A [dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) a Linux-alapú, transzparens lemezes titkosítási alrendszer, amely lehetővé teszi a lemezes titkosítás használatát a Linux rendszerű virtuális gépeken. |
| Kulcs titkosítási kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védetté teheti vagy becsomagolhatja a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| PowerShell-parancsmagok | További információkért lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

## <a name="next-steps"></a>További lépések

Első lépésként tekintse meg a [Azure Disk Encryption előfeltételek](azure-security-disk-encryption-prerequisites.md)című témakört.

