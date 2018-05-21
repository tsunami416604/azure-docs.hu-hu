---
title: Az Azure Storage használható funkciókat |} Microsoft Docs
description: Ez a cikk az alapvető áttekintést nyújt az Azure Storage használható az Azure biztonsági funkciók.
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
ms.openlocfilehash: 9558f1ec0d8ccd83da764a0967fa83d93e1e6a02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-storage-security-overview"></a>Az Azure Storage biztonsági áttekintése
Az Azure Storage az ügyfelek igényeihez alkalmazkodó, modern, a tartósságra, rendelkezésre állásra és méretezhetőségre tervezett alkalmazásokhoz legmegfelelőbb felhőalapú tárolómegoldás. Az Azure Storage átfogó biztonsági képességeket biztosít. A következőket teheti:

* A tárfiók biztonságos szerepköralapú hozzáférés-vezérlést (RBAC) és az Azure Active Directory használatával.
* Biztonságos adatokat átvitel közben, az alkalmazás és az Azure közötti ügyféloldali titkosítás, HTTPS és SMB 3.0-s.
* Adatok titkosításához automatikusan során az Azure Storage Storage szolgáltatás titkosítási használatával írt be.
* Set operációsrendszer- és adatlemezek virtuális gépek (VM) által használt titkosítandó Azure Disk Encryption használatával.
* Adja meg a meghatalmazott hozzáférést biztosít az adatok objektumok az Azure Storage közös hozzáférésű jogosultságkódokat (SASs) használatával.
* Analytics segítségével nyomon követheti a valaki tároló elérésekor által használt hitelesítési módszert.

Az Azure Storage biztonsági részletesebb vizsgálja meg, tekintse meg a [Azure Storage biztonsági útmutató](../storage/common/storage-security-guide.md). Ez az útmutató azokat a funkciókat az Azure Storage egy részletes bemutatója. A funkciók közé tartoznak a tárfiókok kulcsait, az átvitel során, és a többi, és a tárolási analitika adatok titkosítását.

Ez a cikk áttekintést is használhatja az Azure Storage Azure biztonsági funkciókat. Így további cikkek hivatkozásainak adja meg az egyes szolgáltatások adatait.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés használatával is a tárfiók biztonságossá. Alapján történő hozzáférés a [tudniuk kell, hogy](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági szint](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elveket elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. A következő hozzáférési jogokat kapnak a megfelelő RBAC szerepkört rendel a csoportok és alkalmazások egy adott hatókörben. Használhat [beépített RBAC-szerepkörök](../role-based-access-control/built-in-roles.md), például a tárolási fiók közreműködői jogosultságokat hozzárendelése felhasználókhoz.

További információ:

* [Az Azure Active Directory szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Meghatalmazott hozzáférést biztosít a tárolási objektum
A közös hozzáférésű jogosultságkód a tárfiókban lévő erőforrások delegált hozzáférést biztosít. A biztonsági Társítások azt jelenti, hogy is meg lehet adni egy ügyfél korlátozott engedélyekkel a tárfiókban lévő objektumokhoz, adott időtartamra és meghatározott engedélyekkel vannak beállítva. Ezek korlátozott engedélyek megadásához anélkül, hogy a fiók hozzáférési kulcsait megosztásához. 

A SAS URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. Az ügyfél az SA-kat a tárolási erőforrások eléréséhez csak kell megadni. a SAS-t, a megfelelő konstruktort vagy metódust.

További információ:

* [A SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Létrehozhat és használhat egy SAS az Blob storage szolgáltatással](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Titkosítás az átvitel során, akkor egy eszköz adatok védelmének hálózatok közötti átvitelekor. Az Azure Storage biztonságának adatok használatával:

* [Átviteli szintű titkosítást](../storage/common/storage-security-guide.md#encryption-in-transit), például a HTTPS-t, vagy abból az Azure Storage-adatok átvitel során.
* [Hozzá kell fűznie titkosítási](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), például az SMB 3.0-titkosítás, az Azure fájlmegosztások.
* [Ügyféloldali titkosítás](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), az adatok titkosítása a tároló előtt, és az adatok visszafejtéséhez után tárolási kivitt.

További információ az ügyféloldali titkosítás:

* [A Microsoft Azure Storage ügyféloldali titkosítása](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Felhőalapú biztonsági intézkedések adatsorozat: titkosított adatokat átvitel közben](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A legtöbb szervezet számára [adatok titkosítását](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés adatvédelmi, megfelelőségi és az adatok közös joghatóság alá felé. Három Azure funkcióival inaktív adatok titkosítása:

* [Storage szolgáltatás titkosítási](../storage/common/storage-security-guide.md#encryption-at-rest) lehetővé teszi, hogy a társzolgáltatás automatikusan adatok titkosítása az Azure Storage írásakor.
* [Ügyféloldali titkosítás](../storage/common/storage-security-guide.md#client-side-encryption) aktívan titkosítási funkcióval is rendelkezik.
* [Az Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) lehetővé teszi az operációs rendszer és a IaaS virtuális gépként használó adatok lemezek titkosításához.

Ismerje meg a Storage szolgáltatás titkosítási:

* [Az Azure Storage szolgáltatás titkosítási](https://azure.microsoft.com/services/storage/) érhető el [Azure Blob Storage tárolóban](https://azure.microsoft.com/services/storage/blobs/). További részletek a más az Azure storage-típusok: [Azure fájlok](https://azure.microsoft.com/services/storage/files/), [lemez (prémium szintű Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [Table storage](https://azure.microsoft.com/services/storage/tables/), és [várólista tárolási](https://azure.microsoft.com/services/storage/queues/).
* [Az Azure Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
A virtuális gépek az Azure Disk Encryption segít cím szervezeti biztonsági és megfelelőségi követelményeknek. A Virtuálisgép-lemezek (beleértve a rendszerindító és adatlemezek), az titkosítja a kulcsok és a házirendeket, amelyek a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A Linux és Windows operációs rendszerek az adatok titkosítása a virtuális gépek működik. Key Vault segítségével megakadályozhatja a, kezelése és a lemez titkosítási kulcsok használatát naplózni is használja. A virtuális gép lemezeit az adatokat az Azure storage-fiókok szabványos titkosítási technológiával titkosítása. A lemez titkosítása megoldást a Windows alapuló [Microsoft BitLocker meghajtótitkosítás](https://technet.microsoft.com/library/cc732774.aspx), és a Linux-megoldás alapul [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

További információ:

* [Windows és Linux rendszerű infrastruktúra-szolgáltatási virtuális gépeket az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Használja az Azure Disk Encryption [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével szabályozhatja és lemez titkosítási kulcsok és titkos kulcsainak kulcstároló-előfizetése kezeléséhez. Emellett biztosítja, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek az Azure tárolás közben. Key Vault naplózása kulcsok és a házirend-használati kell használnia.

További információ:

* [Mi az Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md)
