---
title: Az Azure Storage Service Encryption for Data at Rest |} A Microsoft Docs
description: Azure Blob storage Szolgáltatásoldali titkosítását, ha az adatok tárolása Azure Storage Service Encryption szolgáltatással, és visszafejteni az adatok lekérésekor.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: lakasa
ms.openlocfilehash: d469dfb5092f1269a6600ee8ee2f81778fd83b96
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449926"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Az Azure Storage szolgáltatás inaktívadat-titkosítása

Inaktív adatok az Azure Storage Service Encryption segítségével a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok védelme. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat előtt átlátni, hogy az Azure Storage, és mindig visszafejti az adatokat lekérés előtt. A titkosítás és a rest, visszafejtési és kulcskezelési a Storage Service Encryption titkosítás kezelése a felhasználók számára átlátható. Azure Storage tárterületre írt összes adat titkosítva van segítségével 256 bites [AES-titkosítás](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), egyik legerősebb Rejtjelek érhető el.

A Storage Service Encryption engedélyezve van az összes meglévő és új storage-fiók, és nem tiltható le. Alapértelmezés szerint az adatok biztonságos, mert nem kell módosítani a kód vagy a Storage Service Encryption szolgáltatással is.

A funkció automatikusan titkosítja az adatokat:

- Mindkét teljesítményszint (Standard és prémium).
- Mindkét üzemi modellben (Azure Resource Manager és Klasszikus modell).
- Az összes az Azure Storage-szolgáltatások (a Blob storage, Queue storage, Table storage és az Azure Files). 

A Storage Service Encryption nem befolyásolja az Azure Storage teljesítményét.

A Storage Service Encryptionnel is használhatja a Microsoft által felügyelt titkosítási kulcsokat, vagy használhatja a saját titkosítási kulcsokat. A saját kulcsok használatával kapcsolatos további információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nézet titkosítási beállítások az Azure Portalon

A Storage Service Encryption beállításainak megtekintéséhez jelentkezzen be a [az Azure portal](https://portal.azure.com) és a egy tárfiók kiválasztását. Az a **beállítások** panelen válassza a **titkosítási** beállítás.

![Portál képernyőképe a titkosítási beállítás](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>A Storage Service Encryption szolgáltatással kapcsolatos gyakori kérdések

**K: rendelkezem egy klasszikus tárfiókot. Rá lehet engedélyezni a Storage Service Encryption?**

V: a Storage Service Encryption engedélyezve van az összes storage-fiókok (klasszikus és Resource Manager).

**K: hogyan használatával titkosítsa a klasszikus tárfiókot az adatokat?**

V: a titkosítás alapértelmezés szerint engedélyezve van az Azure Storage automatikusan titkosítja az új adatokat. 

**K: rendelkezem egy Resource Manager-tárfiókot. Rá lehet engedélyezni a Storage Service Encryption?**

V: storage Service Encryption szolgáltatást minden meglévő Resource Manager-tárfiókok alapértelmezés szerint engedélyezve van. A Blob storage, Table storage, Queue storage és az Azure Files támogatják. 

**K: hogyan titkosítják az adatokat egy Resource Manager-storage-fiókban?**

V: a Storage Service Encryption engedélyezve van az összes storage-fiókok – klasszikus és Resource Manager, a storage-fiók létrehozása előtt engedélyezték a titkosítást a meglévő fájljai fog visszamenőlegesen beolvasása egy titkosítási háttérfolyamat titkosítja.

**K: tárfiókokat is létre a Storage Service Encryptionnel engedélyezve van az Azure PowerShell és az Azure CLI használatával?**

V: a Storage Service Encryption alapértelmezés szerint engedélyezve van minden olyan storage-fiók létrehozása idején (klasszikus vagy Resource Manager). Azure PowerShell-lel és az Azure CLI használatával ellenőrizheti a fiók tulajdonságait.

**K: hogyan sok egyéb mellett az Azure Storage szolgáltatás Ha engedélyezve van a Storage Service Encryption szolgáltatással?**

V: nincs további költség nélkül.

**K: használhatok saját titkosítási kulcsokat?**

V: Igen, használhatja a saját titkosítási kulcsokat. További információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

V: Igen, ha Ön [saját titkosítási kulcsok használatához](storage-service-encryption-customer-managed-keys.md) az Azure Key Vaultban.

**K: Storage Service Encryption alapértelmezés szerint engedélyezve van, egy storage-fiók létrehozásakor?**

V: Igen, a Storage Service Encryption engedélyezett összes storage-fiókok és az Azure Storage-szolgáltatásokhoz.

**K: Miben különbözik ez az Azure Disk Encryption?**

V: az azure Disk Encryption IaaS virtuális gépek az operációsrendszer- és adatlemezek titkosítására szolgál. További információkért lásd: a [Storage biztonsági útmutatóját](../storage-security-guide.md).

**K: Mi történik, ha engedélyezhető az Azure Disk Encryption a moje datové disky?**

V: Ez zökkenőmentesen működik. Mindkét módszer titkosítja az adatokat.

**K: a tárfiók földrajzi redundantly replikálandó van beállítva. A Storage Service Encryptionnel saját redundáns példány is titkosítva lesznek?**

V: Igen, a storage-fiók összes másolatát vannak titkosítva. Beállítások támogatottak – redundancia helyileg redundáns tárolás, a zónaredundáns tárolás, a georedundáns tárolás és az írásvédett georedundáns tárolás.

**K: titkosítási is letiltja a storage-fiókom?**

V: titkosítás alapértelmezés szerint engedélyezve van, és nem rendelkeznek a tárfiók titkosításának letiltása. 

**K: a Storage Service Encryption megengedett csak bizonyos régiókban?**

V: a storage szolgáltatás titkosításának szolgáltatásokhoz minden régióban érhető el.

**K: a Storage Service Encryption FIPS 140-2 szabványnak megfelelő?**

V: Igen, a Storage Service Encryption a FIPS 140-2 szabványnak megfelelő.

**K: hogyan vehetem fel a kapcsolatot valaki Ha I problémába ütközik, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) bármilyen problémák és a Storage Service Encryption szolgáltatással kapcsolatos visszajelzéseket.

## <a name="next-steps"></a>További lépések
Az Azure Storage kínál átfogó készlete biztonsági képességeket, hogy együtt a fejlesztők biztonságos alkalmazásokat hozhat létre. További információkért lásd: a [Storage biztonsági útmutatóját](../storage-security-guide.md).
