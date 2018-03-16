---
title: "Az inaktív adatok Azure Storage szolgáltatás titkosítási |} Microsoft Docs"
description: "Azure Blob storage szolgáltatás oldalán titkosítani, ha az adatok tárolása az Azure Storage szolgáltatás titkosítási szolgáltatás segítségével, és visszafejteni az adatok beolvasása közben."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: d9df2218acc218a796e502fa4e3b94573af86ca8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Az Azure Storage szolgáltatás inaktívadat-titkosítása

Inaktív adatok Azure Storage szolgáltatás titkosítási védheti az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi jár kötelezettségekkel. Ez a szolgáltatás Azure Storage automatikusan titkosítja az adatokat előtt az Azure Storage megőrzése, és az adatok beolvasása előtt visszafejti. A titkosítás és a többi, visszafejtéshez és a Storage szolgáltatás titkosítási kulcskezelés titkosítását kezelésének felhasználó számára nem látható. Minden Azure tárhelyen adattitkosítás keresztül 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

Storage szolgáltatás titkosítási engedélyezve van az összes új és meglévő tárfiók, és nem tiltható le. Az adatok védett alapértelmezés szerint, mert nem kell módosítani a kód vagy a Storage szolgáltatás titkosítási előnyeit alkalmazások.

A szolgáltatás automatikusan titkosítja az adatokat:

- Mindkét teljesítmény rétegek (Standard és prémium).
- Két üzembe helyezési modell (Azure Resource Manager és klasszikus).
- Az összes az Azure Storage-szolgáltatások (a Blob storage, Queue storage, a Table storage és Azure-fájlok). 

Storage szolgáltatás titkosítási Azure tárolási teljesítménye nincs hatással.

A Storage szolgáltatás titkosítási is használhatja a Microsoft által felügyelt titkosítási kulcsokat, vagy használhatja a saját titkosítási kulccsal. A saját kulcsok használatával kapcsolatos további információkért lásd: [Storage szolgáltatás titkosítási kulcsokkal ügyfél által felügyelt Azure Key Vault a](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Titkosítási beállítások megjelenítése az Azure-portálon

A Storage szolgáltatás titkosítási beállítások megtekintéséhez jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és válasszon egy tárfiókot. Az a **beállítások** ablaktáblán válassza előbb a **titkosítási** beállítást.

![A titkosítási beállításokkal portál képernyőfelvétel](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>A Storage szolgáltatás titkosítási – gyakori kérdések

**K: van egy hagyományos tárolási fiókot. Engedélyezhető a Storage szolgáltatás titkosítási?**

Az összes storage-fiók alapértelmezés szerint engedélyezve van a válasz: Storage szolgáltatás titkosítási (klasszikus és Resource Manager).

**K: hogyan is titkosítani a klasszikus tárfiókban lévő adatokat?**

A: titkosítással alapértelmezés szerint engedélyezve van az Azure Storage automatikusan titkosítja az új adatokat. 

**K: van egy erőforrás-kezelő tárfiókot. Engedélyezhető a Storage szolgáltatás titkosítási?**

Válasz: az összes meglévő Resource Manager storage-fiók alapértelmezés szerint engedélyezve van a storage szolgáltatás titkosítási. Ez a Blob-tároló, a Table storage, Queue storage és Azure-fájlok esetén támogatott. 

**K: hogyan titkosítják egy erőforrás-kezelő tárfiókban lévő adatokat?**

Az összes storage-fiókok – klasszikus alapértelmezés szerint engedélyezve van a válasz: Storage szolgáltatás titkosítási és erőforrás-kezelő. Azonban a meglévő adatok nem titkosítottak. Meglévő adatok titkosítása, másolja azt egy másik nevet vagy egy másik tárolóban, és távolítsa el a titkosítatlan verziója. 

**K: létrehozhatók storage-fiókok a Storage szolgáltatás titkosítási engedélyezve van az Azure PowerShell és az Azure parancssori felület használatával?**

V: Storage szolgáltatás titkosítási alapértelmezés szerint engedélyezve van a tárfiók létrehozása idején (klasszikus és Resource Manager). Azure PowerShell és az Azure parancssori felület használatával ellenőrizheti a fiók tulajdonságait.

**K: hogyan sokkal költségeket a Azure Storage a Storage szolgáltatás titkosítási engedélyezésekor?**

V: nincs további költség nélkül.

**K: használhatok saját titkosítási kulcsokat?**

V: Igen, a saját titkosítási kulcsokat is használhatja. További információkért lásd: [Storage szolgáltatás titkosítási kulcsokkal ügyfél által felügyelt Azure Key Vault a](storage-service-encryption-customer-managed-keys.md).

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

V: Igen, ha Ön [saját titkosítási kulcsok használata](storage-service-encryption-customer-managed-keys.md) az Azure Key Vault.

**K: Storage szolgáltatás titkosítási alapértelmezés szerint engedélyezve van, a storage-fiók létrehozásakor?**

A: Igen Storage szolgáltatás titkosítási (a Microsoft által felügyelt kulcsok használata) alapértelmezés szerint engedélyezve van az összes storage-fiókok – Azure Resource Manager és klasszikus. Azt engedélyezte az összes szolgáltatáshoz, valamint – Blob-tároló, a Table storage, Queue storage és Azure fájlokat.

**K: hogyan eltér a Azure Disk Encryption?**

V: azure Disk Encryption operációsrendszer- és adatlemezek az infrastruktúra-szolgáltatási virtuális gépek titkosítására szolgál. További információkért lásd: a [tárolási biztonsági útmutatója](../storage-security-guide.md).

**K: Azure Disk Encryption Mit tegyek, ha az adatok lemezeken engedélyezhető?**

A: Ez zökkenőmentesen működnek. Mindkét módszer titkosítja az adatokat.

**K: a tárfiók földrajzi redundantly replikálható be van állítva. A Storage szolgáltatás titkosítási saját redundáns példány is titkosítva lesznek?**

A: a storage-fiók összes másolatát Igen, vannak titkosítva. Beállítások támogatottak--redundancia helyileg redundáns tárolás, a zónaredundáns tárolás, a georedundáns tárolást és az írásvédett georedundáns tárolás.

**K: titkosítási is letiltja a storage-fiókom?**

Válasz: alapértelmezés szerint engedélyezve van a titkosítás, és a tiltsa le a titkosítást a tárfiók nem rendelkeznek. 

**K: Storage szolgáltatás titkosítási engedélyezett csak a meghatározott régióiba?**

V: storage szolgáltatás titkosítási szolgáltatások minden területen érhető el. 

**K: hogyan do I kapcsolatfelvételre Ha nem sikerül, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) az esetleges problémák vagy a Storage szolgáltatás titkosítási kapcsolódik.

## <a name="next-steps"></a>További lépések
Az Azure Storage előírja egy átfogó biztonsági képességeket, hogy együtt súgó fejlesztők build biztonságos alkalmazások. További információkért lásd: a [tárolási biztonsági útmutatója](../storage-security-guide.md).
