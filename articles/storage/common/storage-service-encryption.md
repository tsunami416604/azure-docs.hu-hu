---
title: "Az inaktív adatok Azure Storage szolgáltatás titkosítási |} Microsoft Docs"
description: "Az Azure Storage szolgáltatás titkosítási szolgáltatás segítségével az Azure Blob Storage szolgáltatás oldalán titkosítani, ha az adatok tárolása, és visszafejteni az adatok beolvasása közben."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 29f6a38f7a7f0f107dab8c99e750a8dec803f6f0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Az Azure Storage szolgáltatás inaktívadat-titkosítása

Az Azure Storage szolgáltatás titkosítási (SSE) inaktív adatok segítségével és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni. Ez a szolgáltatás Azure Storage automatikusan titkosítja az adatokat előtt, az Azure Storage megőrzése, és beolvasása előtt visszafejti. A kezelési titkosítás és a többi, visszafejtéshez és SSE által biztosított kulcskezelés titkosítását olyan felhasználók számára átlátható. Azure Storage írt összes adata titkosításra kerül, 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

SSE engedélyezve van az összes új és meglévő tárfiók, és nem tiltható le. Az adatok védett alapértelmezés szerint, mert nem kell módosítani a kód vagy alkalmazások SSE előnyeit.

 SSE automatikusan titkosítja az összes teljesítmény rétegek (Standard és prémium), minden üzembe helyezési modellel (Azure Resource Manager és klasszikus) és az összes Azure Storage szolgáltatás (Blob, várólista, a táblának és fájl). 

A Microsoft által felügyelt titkosítási kulcsok használatával SSE, vagy használhatja a saját titkosítási kulccsal. A saját kulcsok használatával kapcsolatos további információkért lásd: [Storage szolgáltatás titkosítási ügyfél használatával felügyelt kulcsok Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Titkosítási beállítások megjelenítése az Azure-portálon

A Storage szolgáltatás titkosítási beállítások megtekintéséhez jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és válasszon egy tárfiókot. Az a **beállítások** paneljén kattintson arra a titkosítási beállításokkal.

![A titkosítási beállítással portál ábrázoló képernyőfelvétel](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>A Storage szolgáltatás titkosítási – gyakori kérdések

**K: van egy létező klasszikus storage-fiókot. Engedélyezhető az SSE rajta?**

V: SSE összes tárfiókok (klasszikus és Resource Manager storage-fiókok) alapértelmezés szerint engedélyezve van.

**K: hogyan is titkosítani a klasszikus tárfiókban lévő adatokat?**

A: az új adatok titkosítással alapértelmezés szerint engedélyezve van, automatikusan titkosítja a tároló szolgáltatást. 

**K: van egy meglévő Resource Manager storage-fiókot. Engedélyezhető az SSE rajta?**

V: SSE alapértelmezés szerint az összes meglévő Resource Manager storage-fiók engedélyezve van. Ez támogatott BLOB, a táblák, a fájl és a várólista tárolási. 

**K: szeretnék a Resource Manager meglévő tárfiókot az aktuális adatok titkosítása?**

V: az SSE összes storage-fiókok – klasszikus és Resource Manager alapértelmezés szerint engedélyezett a storage-fiókok. Azonban már jelen adatok nem lesznek titkosítva. Meglévő adatok titkosítása, másolja őket egy másik nevet vagy egy másik tárolóban, és távolítsa el a titkosítatlan verziója. 

**K: hozható létre új tárfiókok az SSE engedélyezve van az Azure PowerShell és az Azure parancssori felület használatával?**

V: SSE alapértelmezés szerint engedélyezve van a bármely (klasszikus és Resource Manager-fiókok) storage-fiók létrehozásának időpontjában. Ellenőrizheti a fióktulajdonságok Azure PowerShell és az Azure parancssori felület használatával.

**K: hogyan sokkal nem költségeket, Azure Storage Ha SSE engedélyezve van?**

V: nincs további költség nélkül.

**K: kezelő a titkosítási kulcsokat?**

A: a Microsoft által felügyelt a kulcsokat.

**K: használhatok saját titkosítási kulcsokat?**

V: jelenleg is dolgozunk az ügyfelek számára a saját titkosítási kulcsok állapotba képességek biztosítása.

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

V: jelenleg nem; a kulcsok teljes mértékben a Microsoft által felügyelt.

**K: SSE alapértelmezés szerint engedélyezve van, egy új tárfiók létrehozásakor?**

A: Igen Microsoft Managed kulcsokkal SSE alapértelmezés szerint engedélyezve van az összes tárfiók - Azure Resource Manager és klasszikus tárfiókokkal. Az összes szolgáltatás, valamint - Blob, Table, várólistára és fájltárolás engedélyezve van.

**K: hogyan eltér a Azure Disk Encryption?**

V: azure Disk Encryption operációsrendszer- és adatlemezek az infrastruktúra-szolgáltatási virtuális gépek titkosítására szolgál. További részletekért tekintse meg a [tárolási biztonsági útmutatója](../storage-security-guide.md).

**K: Azure Disk Encryption Mit tegyek, ha az adatok lemezeken engedélyezhető?**

A: Ez zökkenőmentesen működnek. Mindkét módszer által az adatok titkosítva lesznek.

**K: a tárfiók földrajzi redundantly replikálható be van állítva. Az SSE saját redundáns példány is titkosítva lesznek?**

V: Igen, a tárfiók összes másolatát titkosított, és – helyileg redundáns tárolás (LRS), zóna-redundáns tárolás (ZRS), Georedundáns tárolás (GRS) és írásvédett Georedundáns tárolás (RA-GRS) – összes redundancia beállítások támogatottak.

**K: I titkosítás nem tiltható le a storage-fiókom.**

Válasz: alapértelmezés szerint engedélyezve van a titkosítás, és a tiltsa le a titkosítást a tárfiók nem rendelkeznek. 

**K: SSE csak engedélyezett meghatározott régióiba?**

V: az SSE szolgáltatások minden területen érhető el. 

**K: hogyan do I kapcsolatfelvételre Ha I problémák merülnek fel, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) a Storage szolgáltatás titkosítási okozó problémákat.

## <a name="next-steps"></a>További lépések
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja. További részletekért látogasson el a [tárolási biztonsági útmutatója](../storage-security-guide.md).
