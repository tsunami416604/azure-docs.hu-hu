---
title: "Az Azure Storage használható funkciókat |} Microsoft Docs"
description: " Ez a cikk az alapvető áttekintést nyújt az Azure Storage használható az Azure biztonsági funkciók. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: a118bde2290e68c9a741e40cda210d47db918047
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-storage-security-overview"></a>Az Azure storage biztonsági áttekintése
Az Azure Storage az ügyfelek igényeihez alkalmazkodó, modern, a tartósságra, rendelkezésre állásra és méretezhetőségre tervezett alkalmazásokhoz legmegfelelőbb felhőalapú tárolómegoldás. Az Azure Storage biztonsági funkciók széles választékát nyújtja:

* A tárfiók a szerepköralapú hozzáférés-vezérlés és az Azure Active Directory használatával kell biztonságossá.
* A védett adatok az alkalmazás és az Azure közötti átvitel során ügyféloldali titkosítás, HTTPS és SMB 3.0-s.
* Adatok állítható be automatikusan titkosítja a tárhelyen, Azure Storage szolgáltatás titkosítási használatával.
* Virtuális gépek által használt operációsrendszer- és adatlemezek állítható be Azure Disk Encryption titkosítja.
* Meghatalmazott hozzáférést biztosít az Azure Storage adatobjektumok megosztott hozzáférési aláírásokkal használatával engedélyezhetők.
* Tároló elérésekor valaki által használt hitelesítési módszert használó tárolási analitika követhető nyomon.

Az Azure Storage biztonsági részletesebb vizsgálja meg, tekintse meg a [Azure Storage biztonsági útmutató](../storage/common/storage-security-guide.md). Ez az útmutató részletes bemutatója a tárfiókok kulcsait, például az Azure Storage a biztonsági funkciók adattitkosítás átvitel közben, és a rest és a tárolási analitika.

Ez a cikk áttekintést az Azure Storage használható az Azure biztonsági funkciók. A hivatkozások akkor megadott cikkekre mutató adja meg a egyes szolgáltatások, többet is megtudhat.

Az alábbiakban az alapvető szolgáltatásokat, a cikkben szerepelnek:

* Szerepköralapú hozzáférés-vezérlés
* Meghatalmazott hozzáférést biztosít a tárolási objektum
* Az átvitel során titkosítás
* Rest/Storage szolgáltatás titkosítási titkosítását
* Azure Disk Encryption
* Azure Key Vault

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A storage-fiókkal, és a szerepköralapú hozzáférés-vezérlést (RBAC) biztonságát. Alapján történő hozzáférés a [tudniuk kell, hogy](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági szint](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elveket elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. A következő hozzáférési jogokat kapnak a megfelelő RBAC szerepkört rendel a csoportok és alkalmazások egy adott hatókörben. Használhat [beépített RBAC-szerepkörök](../active-directory/role-based-access-built-in-roles.md), például a tárolási fiók közreműködői jogosultságokat hozzárendelése felhasználókhoz.

További információ:

* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Meghatalmazott hozzáférést biztosít a tárolási objektum
A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít azokhoz a tárfiókban lévő erőforrások. A biztonsági Társítások azt jelenti, hogy egy ügyfél egy adott időszakban, és meghatározott engedélyekkel vannak beállítva a tárfiókban lévő objektumokra vonatkozó engedélyeken csak korlátozott biztosíthat. Ezek korlátozott engedélyek megadásához anélkül, hogy a fiók hozzáférési kulcsait megosztásához. A SAS URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. Az ügyfél az SA-kat a tárolási erőforrások eléréséhez csak kell megadni. a SAS-t, a megfelelő konstruktort vagy metódust.

További információ:

* [A SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Hozzon létre, és használhatja az SAS-Blob-tároló](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Titkosítás az átvitel során, akkor egy eszköz adatok védelmének hálózatok közötti átvitelekor. Az Azure Storage segítségével védetté tehet adatokat:

* [Átviteli szintű titkosítást](../storage/common/storage-security-guide.md#encryption-in-transit), például a HTTPS vagy abból az Azure Storage-adatok átvitel során.
* [Hozzá kell fűznie titkosítási](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), például az Azure fájlmegosztások SMB 3.0 titkosítást.
* [Ügyféloldali titkosítás](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), az adatok titkosítása a tároló előtt, és az adatok visszafejtéséhez után tárolási kivitt.

További információ az ügyféloldali titkosítás:

* [A Microsoft Azure Storage ügyféloldali titkosítása](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Felhőalapú biztonsági intézkedések adatsorozat: titkosított adatokat átvitel közben](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A legtöbb szervezet számára [adatok titkosítását](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés adatvédelmi, megfelelőségi és az adatok közös joghatóság alá felé. Nincsenek adatok, amelyek "Inaktív" titkosítás három Azure funkciókat:

* [Storage szolgáltatás titkosítási](../storage/common/storage-security-guide.md#encryption-at-rest) lehetővé teszi, hogy a társzolgáltatás automatikusan adatok titkosítása az Azure Storage írásakor.
* [Ügyféloldali titkosítás](../storage/common/storage-security-guide.md#client-side-encryption) aktívan titkosítási funkcióval is rendelkezik.
* [Az Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) lehetővé teszi az operációs rendszer és az infrastruktúra-szolgáltatási virtuális gép által használt adatok lemezek titkosításához.

Ismerje meg a Storage szolgáltatás titkosítási:

* [Az Azure Storage szolgáltatás titkosítási](https://azure.microsoft.com/services/storage/) érhető el [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). További részletek a más az Azure storage-típusok: [fájl](https://azure.microsoft.com/services/storage/files/), [lemez (prémium szintű Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [tábla](https://azure.microsoft.com/services/storage/tables/), és [várólista](https://azure.microsoft.com/services/storage/queues/).
* [Az Azure Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
A virtuális gépek (VM) az Azure Disk Encryption segít cím szervezeti biztonsági és megfelelőségi követelményeknek a Virtuálisgép-lemezek (beleértve a rendszerindító és adatlemezek) titkosítja a kulcsokat és szabályozhatja a házirendek [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A Linux és Windows operációs rendszerek az adatok titkosítása a virtuális gépek működik. Key Vault segítségével megakadályozhatja a, kezelése és a lemez titkosítási kulcsok használatát naplózni is használja. A virtuális gép lemezeit az adatokat az Azure Storage-fiókok szabványos titkosítási technológiával titkosítása. A lemez titkosítása megoldást a Windows alapuló [Microsoft BitLocker meghajtótitkosítás](https://technet.microsoft.com/library/cc732774.aspx), és a Linux-megoldás alapul [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

További információ:

* [Windows és Linux rendszerű infrastruktúra-szolgáltatási virtuális gépeket az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Használja az Azure Disk Encryption [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével szabályozhatja és lemez titkosítási kulcsok és titkos kulcsainak kulcstároló-előfizetése kezeléséhez, miközben a győződjön meg arról, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek-e az Azure tárolás közben. Key Vault naplózása kulcsok és a házirend-használati kell használnia.

További információ:

* [Mi az Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md)
