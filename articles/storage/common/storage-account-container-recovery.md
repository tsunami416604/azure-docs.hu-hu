---
title: Tárfiók tárolójának helyreállítása
description: Tárfiók tárolójának helyreállítása
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562275"
---
# <a name="storage-account-container-recovery"></a>Tárfiók tárolójának helyreállítása

Az Azure Storage automatikus replikákon keresztül biztosítja az adatok rugalmasságát. Ez azonban nem akadályozza meg az alkalmazáskódot vagy a felhasználókat abban, hogy véletlenül vagy rosszindulatúan megrongálják az adatokat. Az adatok hűségének fenntartása az alkalmazás vagy a felhasználói hiba esetén fejlettebb technikákat igényel, például az adatok másolását egy másodlagos tárolóhelyre egy naplóval.

## <a name="checking-azure-storage-account-type"></a>Az Azure Storage-fiók típusának ellenőrzése

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/)

2. Keresse meg a Storage-fiókját.

3. Az **Áttekintés csoportban** ellenőrizze a **replikációt.**

   ![Kép](media/storage-account-container-recovery/1.png)

4. Ha a replikáció típusa **GRS/RA-GRS,** a fióktárolók helyreállítása garancia nélkül lehetséges. Az összes többi replikációs típus esetében ez nem lehetséges.

5. Gyűjtse össze az alábbi információkat, és nyújtson be támogatási kérelmet a Microsoft támogatási szolgálatához.

   * Tárfiók neve:
   * Tároló neve:
   * A törlés időpontja:

   Az alábbi táblázat áttekintést nyújt a tárfiók-tároló-helyreállítás hatóköréről a replikációs stratégiától függően.

   |A tartalom típusa|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Tároló tároló|Nem|Nem|Igen|Igen| 

   * Megpróbálhatjuk visszaállítani a tárfiók tárolóját, de garancia nélkül. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>A dolgok nem kell tennie, hogy visszaszerezze a sikeres

* Ne hozza létre újra a tárolót (ugyanazzal a névvel).  
* Ha már újra létrehozta a tárolót, törölnie kell a tárolót, mielőtt támogatási kérelmet nyújtana be a helyreállításhoz.

## <a name="steps-to-prevent-this-in-the-future"></a>Lépéseket annak megakadályozására, hogy ez a jövőben

1. Ennek elkerülése érdekében a jövőben a legajánlottabb funkció a [Soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Azt is javasoljuk, hogy a [Pillanatkép](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) funkció.
 
## <a name="next-steps"></a>További lépések

Itt van két minta kód a funkció:

  * [Blob-pillanatkép létrehozása és kezelése a .NET-ben](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Blob-pillanatképek használata a PowerShell használatával](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

