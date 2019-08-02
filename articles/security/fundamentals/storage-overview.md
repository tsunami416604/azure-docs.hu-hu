---
title: Az Azure Storage szolgáltatással használható biztonsági funkciók | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Storage szolgáltatással használható alapvető Azure-beli biztonsági funkciókról.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 249380b5bd9d95e969a9c7a812102b694b9d1e3b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726547"
---
# <a name="azure-storage-security-overview"></a>Az Azure Storage biztonsági áttekintése

Ez a cikk áttekintést nyújt az Azure Storage szolgáltatással használható Azure-beli biztonsági funkciókról. Az Azure Storage az ügyfelek igényeihez alkalmazkodó, modern, a tartósságra, rendelkezésre állásra és méretezhetőségre tervezett alkalmazásokhoz legmegfelelőbb felhőalapú tárolómegoldás. Az Azure Storage átfogó biztonsági funkciókat kínál. A következőket teheti:

* A Storage-fiókot szerepköralapú Access Control (RBAC) és Azure Active Directory használatával biztonságossá teheti.
* Az alkalmazások és az Azure közötti adatátvitelt az ügyféloldali titkosítás, a HTTPS vagy az SMB 3,0 használatával biztosíthatja.
* A Storage Service Encryption használatával automatikusan titkosíthatja az Azure Storage-ba való íráskor megjelenő adatkészleteket.
* A virtuális gépek (VM-EK) által használt operációs rendszer és adatlemezek beállítása Azure Disk Encryption használatával.
* A megosztott hozzáférési aláírások (SASs) használatával delegált hozzáférést biztosíthat az Azure Storage-beli adatobjektumokhoz.
* Az elemzés segítségével nyomon követheti, hogy valaki milyen hitelesítési módszert használ a tárolóhoz való hozzáféréshez.

Az Azure Storage biztonságával kapcsolatos részletesebb információkért tekintse meg az [Azure Storage biztonsági útmutatóját](../../storage/common/storage-security-guide.md). Ez az útmutató részletesen ismerteti az Azure Storage biztonsági funkcióit. Ezek a szolgáltatások közé tartoznak a Storage-fiók kulcsainak, az adattitkosítás az átvitelben és a nyugalmi állapotban, valamint a Storage Analytics.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A Storage-fiók a szerepköralapú Access Control segítségével biztonságossá tehető. Az adathozzáférésre vonatkozó biztonsági szabályzatokat kényszerítő szervezetek számára elengedhetetlen a hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legalacsonyabb jogosultsági szintű](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elvek alapján. Ezek a hozzáférési jogosultságok úgy érhetők el, hogy a megfelelő RBAC-szerepkört rendeli hozzá egy bizonyos hatókörhöz tartozó csoportokhoz és alkalmazásokhoz. A felhasználókhoz a jogosultságok hozzárendeléséhez használhatja a [beépített RBAC](/azure/role-based-access-control/built-in-roles)-szerepköröket, például a Storage-fiók közreműködőjét.

További információ:

* [Szerepkör-alapú Access Control Azure Active Directory](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>A Storage-objektumokhoz delegált hozzáférés

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Az SAS azt jelenti, hogy a megadott időszakra és meghatározott engedélyekkel engedélyezheti az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó korlátozott engedélyeket. Ezeket a korlátozott engedélyeket a fiók hozzáférési kulcsainak megosztása nélkül is megadhatja.

Az SAS egy URI, amely magában foglalja a lekérdezési paramétereit, a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információt. A tároló erőforrásainak SAS-vel való eléréséhez az ügyfélnek csak az SAS-t kell megadnia a megfelelő konstruktorhoz vagy metódushoz.

További információ:

* [Az SAS-modell megismerése](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [SAS létrehozása és használata blob Storage-tárolóval](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Titkosítás átvitel közben

Az átvitel közbeni titkosítás egy olyan mechanizmus, amely az adatok védelmét a hálózatokon keresztül továbbítja. Az Azure Storage használatával az alábbiakkal védheti meg az adatvédelmet:

* [Átviteli szintű titkosítás](/azure/storage/common/storage-security-guide#encryption-in-transit), például https, amikor adatátvitelt végez az Azure Storage szolgáltatásba vagy onnan kívülre.
* Az Azure-fájlmegosztás titkosítása [, például](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares)az SMB 3,0-titkosítás.
* [Ügyféloldali titkosítás](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), amely a tárterületre való átvitel előtt titkosítja az adatátvitelt, és visszafejti az adatmennyiséget a tárterületről való átadást követően.

További információ az ügyféloldali titkosításról:

* [Ügyféloldali titkosítás Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud Security Controls sorozat: Adatátvitel közbeni titkosítás](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Számos szervezet esetében az [adattitkosítás](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) az adatok védelme, a megfelelőség és az adatok szuverenitása szempontjából kötelező lépés. Három Azure-szolgáltatás biztosítja a nyugalmi állapotban lévő adatok titkosítását:

* A [Storage Service encryption](/azure/storage/common/storage-security-guide#encryption-at-rest) mindig engedélyezve van, és automatikusan titkosítja a tárolási szolgáltatáshoz tartozó, az Azure Storage-ba való íráskor.
* Az [ügyféloldali titkosítás](/azure/storage/common/storage-security-guide#client-side-encryption) emellett biztosítja a titkosítás nyugalmi funkcióját is.
* [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását.

További információ a Storage Service Encryptionról:

* Az Azure [Storage Service encryption](https://azure.microsoft.com/services/storage/) elérhető az [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-hoz. További információ az Azure-beli tárolási típusokról: [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/)és [üzenetsor-tárolás](https://azure.microsoft.com/services/storage/queues/).
* [Azure-Storage Service Encryption a nyugalmi állapotban lévő adatokhoz](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

A virtuális gépek Azure Disk Encryption a szervezeti biztonsági és megfelelőségi követelmények megoldásában nyújt segítséget. Titkosítja a virtuálisgép-lemezeket (beleértve a rendszerindítási és adatlemezeket is) a Azure Key Vaultban [](https://azure.microsoft.com/services/key-vault/)felügyelt kulcsok és szabályzatok használatával.

A virtuális gépek lemezes titkosítása Linux és Windows operációs rendszerek esetén működik. A Key Vault a lemezes titkosítási kulcsok védelméhez, kezeléséhez és naplózásához is segítséget nyújt. A virtuálisgép-lemezeken lévő összes információ az iparági szabványnak megfelelő titkosítási technológia használatával van titkosítva az Azure Storage-fiókokban. A Windows rendszerhez készült lemez-titkosítási megoldás a [Microsoft BitLocker meghajtótitkosításon](https://technet.microsoft.com/library/cc732774.aspx)alapul, és a Linux-megoldás a [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt)alapú.

Tudnivalók a modellalapú alkalmazások létrehozásáról

* [Azure Disk Encryption Windows és Linux rendszerű IaaS Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Tűzfalak és virtuális hálózatok

Az Azure Storage lehetővé teszi a tűzfalszabályok engedélyezését a Storage-fiókok számára. Ha engedélyezve van, letiltja a bejövő adatkéréseket, beleértve az egyéb Azure-szolgáltatásokból érkező kéréseket is. A kivételek konfigurálásával engedélyezheti a forgalmat. A tűzfalszabályok engedélyezhetők a meglévő Storage-fiókokon, illetve a létrehozás ideje alatt is.

Ezzel a funkcióval biztonságossá teheti a Storage-fiókjait az engedélyezett hálózatok egy adott készletén.

Az Azure Storage-tűzfalakkal és a virtuális hálózatokkal kapcsolatos további információkért tekintse át az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](/azure/storage/common/storage-network-security) című cikket.

## <a name="azure-data-box"></a>Azure Data Box

A Data Box, a Data Box Disk és a Data Box Heavy lehetővé teszi, hogy nagy mennyiségű adatot helyezzen át az Azure-ba, ha a hálózati megoldás nem használható. Ezeket az offline adatátviteli eszközöket a szervezet és az Azure-adatközpont között szállítjuk. Az eszközök AES-titkosítást használnak, amelyek a szállítás közben védik az adatait, a feltöltés után pedig alapos megtisztítási folyamaton esnek át, amelynek során minden adata törölve lesz az eszközről.

A Data Box Edge és a Data Box Gateway online adatáthelyezésre használható termékek, amelyek hálózati tárolóátjáróként funkcionálnak, és az Ön helyszíne és az Azure között kezelik az adatokat. A Data Box Edge egy helyszíni hálózati eszköz, amely az adatokat az Azure és a helyszín között helyezi át, és az adatok feldolgozásához mesterséges intelligenciát használó peremhálózati számítási megoldást használ. A Data Box Gateway egy tárolóátjáró képességgel rendelkező virtuális berendezés.

További információ:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Advanced Threat Protection

Az Azure Storage komplex veszélyforrások elleni védelmet biztosít a biztonsági intelligencia további rétege számára, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiók eléréséhez vagy kihasználásához. A komplex veszélyforrások elleni védelem figyeli az Azure Storage diagnosztikai naplóit a blob Storage-ba irányuló gyanús olvasási, írási és törlési kérelmek esetében.

A komplex veszélyforrások elleni védelem riasztásait [Azure Security Center](https://azure.microsoft.com/services/security-center/)lehet megtekinteni. Azure Security Center információt nyújt az észlelt gyanús tevékenységekről, és javaslatokat tesz a lehetséges fenyegetések kivizsgálására és szervizelésére.

További információ:

* [Az Azure Storage komplex veszélyforrások elleni védelem áttekintése](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

A Azure Disk Encryption a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével vezérli és felügyeli a lemezes titkosítási kulcsokat és a titkos kulcsokat a Key Vault-előfizetésben. Emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az Azure Storage-ban. A kulcsok és a házirendek használatának naplózásához Key Vaultt kell használnia.

Tudnivalók a modellalapú alkalmazások létrehozásáról

* [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)
