---
title: Storage-fiók tárolójának helyreállítása
description: Storage-fiók tárolójának helyreállítása
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562275"
---
# <a name="storage-account-container-recovery"></a>Storage-fiók tárolójának helyreállítása

Az Azure Storage az automatizált replikák használatával biztosítja az adatrugalmasságot. Ez azonban nem akadályozza meg, hogy az alkalmazás kódja vagy a felhasználók véletlenül vagy rosszindulatúan sértsék az adatsérülést. Az adathűségnek az alkalmazás vagy a felhasználói hiba miatti fenntartása fejlettebb technikákat igényel, például az adatok másolását egy másodlagos tárolóhelyre egy napló használatával.

## <a name="checking-azure-storage-account-type"></a>Az Azure Storage-fiók típusának ellenőrzése

1. Lépjen az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a Storage-fiókját.

3. Az **Áttekintés** szakaszban keresse meg a **replikálást**.

   ![Image (Kép)](media/storage-account-container-recovery/1.png)

4. Ha a replikálás típusa **GRS/ra-GRS**, a fiók tárolójának helyreállítása garancia nélkül lehetséges. Minden más replikációs típus esetében nem lehetséges.

5. Gyűjtse össze a következő információkat, és küldjön egy támogatási kérést Microsoft ügyfélszolgálata.

   * Storage-fiók neve:
   * Tároló neve:
   * Törlés időpontja:

   A következő táblázat áttekintést nyújt a Storage-fiók tárolójának helyreállítási hatóköréről a replikációs stratégiától függően.

   |Tartalom típusa|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Storage-tároló|Nem|Nem|Igen|Igen| 

   * Megpróbáljuk visszaállítani a Storage-fiók tárolóját, de garancia nélkül is. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>A helyreállítás sikerességéhez szükséges dolgok

* Ne hozza létre újra a tárolót (ugyanazzal a névvel).  
* Ha már újra létrehozta a tárolót, törölnie kell a tárolót a helyreállítási kérelem beküldése előtt.

## <a name="steps-to-prevent-this-in-the-future"></a>A jövőbeli megelőzési lépések

1. Ennek elkerüléséhez a javasolt funkció a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Javasoljuk továbbá a [Pillanatkép](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) -szolgáltatást.
 
## <a name="next-steps"></a>További lépések

A következő két mintakód szerepel a szolgáltatásban:

  * [BLOB-pillanatkép létrehozása és kezelése a .NET-ben](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [BLOB-Pillanatképek használata a PowerShell-lel](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

