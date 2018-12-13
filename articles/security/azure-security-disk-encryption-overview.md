---
title: Áttekintés – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption áttekintést nyújt az IaaS virtuális gépekhez.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 73a00756928fd476b723e0b43accf46378ae14cc
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093284"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Az Azure Disk Encryption IaaS virtuális gépekhez

A Microsoft Azure számára fontos, hogy az adatvédelem és az adatok elkülönítése. Az Azure segítségével szabályozható a titkosítása, szabályozhatja és titkosítási kulcsokat, és az adatok ellenőrzésére és hozzáférés kezelése a fejlett technológiák számos Azure-ban tárolt adatait. Ez a vezérlő biztosít az Azure-ügyfelek rugalmasan választhatja ki a saját üzleti igényeinek leginkább megfelelő megoldást. Ez a cikk bemutatja a technológiai megoldásokat: "Az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek (VM)." Ez a technológia segítségével a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok biztonságos megőrzésében. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption egy olyan funkció, amely segítséget nyújt a Windows és Linux rendszerű IaaS VM-lemezek titkosítása. Lemeztitkosítás kihasználja az iparági szabvány [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsok. A megoldás emellett biztosítja, hogy a Virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban.

Általában minden nyilvános Azure-régióban és a standard szintű virtuális gépek és az Azure Premium Storage virtuális gépek Azure Government-régiók rendelkezésre állása Disk Encryption Windows és Linux rendszerű IaaS virtuális gépekhez. Amikor alkalmazza a lemeztitkosítás felügyeleti megoldás, akkor a következő üzleti igényekre is képes kielégíteni:

* IaaS virtuális gépek védett inaktív szervezeti biztonsági és megfelelőségi követelmények teljesítésére az iparági szabványnak megfelelő titkosítási technológia használatával.
* IaaS virtuális gépek rendszerindítási ügyfél által felügyelt kulcsok és szabályzatok alapján. A key vaultban lévő naplózhatja a használatuk.

Az Azure Security Center, ha rögtön riasztást kap, ha azok nem titkosított virtuális gépek. A riasztások magas súlyossági szintű szabályzatként jelenik meg, és az javasoljuk, hogy ezek a virtuális gépek titkosításához.

![Az Azure Security Center titkosítási riasztás](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, a hálózati, vagy a számítási erőforrás-használat és a további licencek vagy előfizetések költségeit eredményez.


## <a name="encryption-scenarios"></a>Titkosítási megoldások

A Disk Encryption megoldás a következő ügyfél-forgatókönyveket teszi lehetővé:

* Új Windows IaaS virtuális gépek előzetes titkosítással VHD-t és a titkosítási kulcsok alapján létrehozott titkosításának engedélyezése.
* A támogatott Azure-gyűjtemény lemezképei alapján létrehozott új IaaS virtuális gépek titkosításának engedélyezése.
* A titkosítást a meglévő IaaS virtuális gépeken, amelyek az Azure-ban futnak.
* A Windows virtual machine scale sets titkosítást.
* Linux virtuális gép méretezési csoportokhoz tartozó adatmeghajtók titkosításának engedélyezése.
* Tiltsa le a Windows IaaS virtuális gépek a titkosítást.
* Linux rendszerű IaaS virtuális gépek adatmeghajtók titkosításának letiltása.
* Tiltsa le a Windows virtual machine scale sets titkosítást.
* Linux virtuális gép méretezési csoportokhoz tartozó adatmeghajtók titkosításának letiltása.
* Felügyelt lemezes virtuális gépek titkosításának engedélyezése.
* Egy meglévő titkosított prémium és a nem Premium Storage virtuális gépek titkosítási beállításainak frissítése.
* Biztonsági mentése és visszaállítása titkosított virtuális gépek.

A megoldás a következő eseteket támogatja IaaS virtuális gépekhez, ha engedélyezve vannak a Microsoft Azure-ban:

* Az Azure Key Vault-integráció.
* Standard szintű virtuális gépek: [A, D, DS, G, GS, F és így tovább, sorozat IaaS virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/). [Linux rendszerű virtuális gépek](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) ezen a szinten belül meg kell felelnie a minimális memóriára vonatkozó követelmény, 7 GB.
* Titkosítás engedélyezése a Windows és Linux rendszerű IaaS virtuális gépek, felügyelt lemezes és a méretezési csoport virtuális gépek a támogatott Azure-katalógus-rendszerképek állítható be.
* Tiltsa le a Windows IaaS virtuális gépek méretezési csoport operációsrendszer- és meghajtók titkosításának virtuális gépei, és a felügyelt lemezes virtuális gépek.
* Tiltsa le a Linuxos IaaS virtuális gépek méretezési adatmeghajtók titkosításának virtuális gépei, és a felügyelt lemezes virtuális gépek.
* A Windows ügyfél operációs rendszerű IaaS virtuális gépek titkosításának engedélyezése.
* Csatlakoztatási elérési úttal köteteken titkosítást.
* Linux rendszerű virtuális gépek, amelyeken lemez összevonása (RAID) mdadm használatával a titkosítást.
* Linux rendszerű virtuális gépeken, amelyek LVM adatlemezeket a titkosítást.
* A Linux rendszerű virtuális gép operációsrendszer- és adatlemezek titkosításának engedélyezése.

   > [!NOTE]
   > Egyes Linux-disztribúciókon az operációs rendszer meghajtótitkosítás nem támogatott. További információkért lásd: a [Azure Disk Encryption – gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) cikk.
   
* Windows virtuális gépek, amelyeken a Windows tárolóhelyek titkosításának engedélyezése.
* Egy meglévő titkosított prémium és a nem Premium Storage virtuális gépek titkosítási beállításainak frissítése.
* Biztonsági mentése és visszaállítása titkosított virtuális gépek kulcstitkosítási kulcs-(KEK) és a nem KEK-forgatókönyvekhez.
* Az összes Azure-beli nyilvános és az Azure Government-régiók támogatottak.

A megoldás a következő forgatókönyvek, szolgáltatások és technológiák nem támogatja:

* Alapszintű csomag IaaS virtuális gépeket.
* Linux rendszerű IaaS virtuális gépek egy operációs rendszer meghajtójának titkosításának letiltása.
* Adatmeghajtók titkosításának letiltása, ha az operációs rendszer meghajtójának Linux rendszerű Iaas virtuális gépek titkosítva van.
* IaaS virtuális gépek jönnek létre a klasszikus virtuális gép létrehozási módszer használatával.
* Az ügyfél Linux rendszerű IaaS virtuális gépek egyéni rendszerképek titkosítást.
* Integráció a helyszíni kulcskezelő rendszerrel.
* Az Azure Files (megosztott fájlrendszert).
* Hálózati fájlrendszer (NFS).
* A dinamikus köteteket.
* Windows virtuális gépek, amelyeken a szoftveres RAID-rendszerekkel.

## <a name="encryption-features"></a>Titkosítási funkciók

Engedélyezi, és az Azure IaaS virtuális gépek üzembe helyezése a Lemeztitkosítást, ha az alábbi képességeket engedélyezve vannak a megadott konfigurációtól függően:

* Az operációsrendszer-kötet védelmét a rendszerindító kötet a tárolás titkosítása.
* Adatkötetek védelme érdekében az adatkötetek a tárolás titkosítása.
* Tiltsa le a Windows IaaS virtuális gépek az operációsrendszer- és meghajtók titkosítását.
* Linux rendszerű IaaS virtuális gépekhez az adatmeghajtók titkosításának letiltása (csak akkor, ha az operációs rendszer meghajtójának nem titkosított)
* Titkosítási kulcsok és titkos kulcsok Azure Key Vault-előfizetésből az biztosítása érdekében.
* A jelentés a titkosított IaaS virtuális gépek lemeztitkosítási állapotát.
* Távolítsa el az IaaS virtuális gépek lemezkonfigurációs titkosítási beállításait.
* Készítsen biztonsági másolatot, és a titkosított virtuális gépek visszaállítása az Azure Backup szolgáltatás használatával.

Az Azure Disk Encryption Windows és Linux-megoldás IaaS virtuális gépekhez tartalmazza:

* A Windows disk encryption bővítményt.
* Linux-disk encryption bővítményt.
* A PowerShell lemez titkosítási parancsmagokat.
* Az Azure CLI-vel lemez titkosítási parancsmagok.
* Az Azure Resource Manager lemez titkosítási sablonok.

Az Azure Disk Encryption megoldás IaaS virtuális gépeket futtató, Windows vagy Linux operációs rendszer esetében támogatott. A támogatott operációs rendszerekkel kapcsolatos további információkért tekintse meg a [Előfeltételek](azure-security-disk-encryption-prerequisites.md) cikk.

> [!NOTE]
> Nem jár további költségekkel együtt az Azure Disk Encryption Virtuálisgép-lemezek titkosítása. Standard szintű [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) vonatkozik a kulcstartóhoz, amely a titkosítási kulcsok tárolására szolgál. 


## <a name="encryption-workflow"></a>Titkosítási munkafolyamat

Ahhoz, hogy a lemeztitkosítás Windows és Linux rendszerű virtuális gépekhez, tegye a következőket:

1. Egy titkosítási forgatókönyvet a felsorolt forgatókönyvek közül választhat a [titkosítási megoldások](#encryption-scenarios) szakaszban.

1. Részvétel a az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül a lemeztitkosítás engedélyezve, és adja meg a titkosítási konfiguráció.

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfiguráció engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást az IaaS virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyek már futtatnak az Azure-ban.

1. Hozzáférést biztosít az Azure platform a titkosítási kulcs adatai (a BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-hozzáférési kódot olvasni az IaaS virtuális gép titkosításának a key vaultban.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.

   ![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>A visszafejtés munkafolyamat
Lemeztitkosítás IaaS virtuális gépekhez letiltásához kövesse az alábbi magas szintű lépéseket:

1. Dönt, hogy tiltsa le a titkosítást (visszafejtési) az Azure-ban futó IaaS virtuális gépen, és adja meg a visszafejtési konfigurációt. Az Azure Disk Encryption Resource Manager-sablon, PowerShell-parancsmagok vagy az Azure CLI-n keresztül is letilthatja.

   Ebben a lépésben letiltja a titkosítást az operációs rendszer vagy adatmennyiség, illetve mindkettőt a futó Windows IaaS virtuális gépen. Az előző szakaszban említett operációs rendszer lemeztitkosítás linuxos letiltása nem támogatott. A visszafejtési lépés csak engedélyezett adatmeghajtókon a Linux rendszerű virtuális gépek mindaddig, amíg az operációsrendszer-lemez nincs titkosítva.

1. A Virtuálisgép-modell és az IaaS virtuális gép van megjelölve, Azure-frissítések visszafejteni. A virtuális gép tartalmát már nem titkosított inaktív.

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

   * Az ügyfél által titkosított VHD forgatókönyvhöz töltse fel a titkosított virtuális Merevlemezt a storage-fiók és a titkosítási kulcs adatai a kulcstartóba. Ezután adja meg a titkosítási konfiguráció engedélyezze a titkosítást egy új IaaS virtuális gépen.
   * Adja meg a titkosítási konfiguráció engedélyezze a titkosítást az IaaS virtuális gépen a Marketplace-ről létrehozott új virtuális gépeket és a meglévő virtuális gépek, amelyek már futtatnak az Azure-ban.

1. Hozzáférést biztosít az Azure platform a titkosítási kulcs adatai (a BitLocker titkosítási kulcsok Windows rendszerekhez) és a Linux-hozzáférési kódot olvasni az IaaS virtuális gép titkosításának a key vaultban.

1. Adja meg az Azure ad-ben a titkosítási kulcsot a key vault anyag írása identitása. Ez a lépés lehetővé teszi a titkosítást az IaaS virtuális gépen a 2. lépésben említett forgatókönyvekhez.

1. Azure titkosítási és a key vault beállítása a virtuális gép modell frissíti, és beállítja a titkosított virtuális gép.


## <a name="terminology"></a>Terminológia
Az alábbi táblázat a technológia egyes használt gyakori kifejezések meghatározása:

| Terminológia | Meghatározás |
| --- | --- |
| Azure AD | Egy [Azure ad-ben](https://azure.microsoft.com/documentation/services/active-directory/) fiók hitelesítéséhez, tárolása és titkos kódok lekérése a key vault segítségével. |
| Azure Key Vault | A Key Vault szolgáltatás titkosítási, key management, amelyek az rendelkezik a Federal Information Processing szabványok (FIPS) hitelesített hardveres biztonsági modulokban. Ezen irányelvek segítenek a kriptográfiai kulcsok és a bizalmas, titkos kulcsok védelme érdekében. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparági ismerhető fel Windows kötet titkosítási technológia, amely lehetővé teszi a lemeztitkosítást a Windows IaaS virtuális gépeket, hogy. |
| BEK | A BitLocker titkosítási kulcsok (rendelkeznek BEk-KEL) segítségével titkosítja az operációs rendszer rendszerindító kötet és adatkötetek. BEKs számára biztosít védelmet a kulcstartóban található titkos kódként. |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) kezelésére és felügyeletére az Azure-erőforrások parancssorból van optimalizálva.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) a Linux-alapú, átlátható lemeztitkosítás alrendszer, amely engedélyezi a lemeztitkosítást a Linuxos IaaS virtuális gépek szolgál. |
| KEK | Kulcstitkosítási kulcs-(KEK) az az aszimmetrikus kulcs (RSA 2048), amellyel védelméhez vagy burkolásához a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért lásd: a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját. |
| PowerShell-parancsmagok | További információkért lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Disk Encryption előfeltételei](azure-security-disk-encryption-prerequisites.md)
