---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391666"
---
Az Azure-fájlmegosztás az Azure Portalon vagy az SMB használatával végrehajtott módosításokat a rendszer nem észleli azonnal, és replikálja a kiszolgálóvégpont módosításaiként. Az Azure Files még nem rendelkezik változási értesítésekkel vagy naplózással, így nem lehet automatikusan kezdeményezni a szinkronizálási munkamenetet a fájlok módosításakor. Windows Server rendszerben az Azure File Sync [a Windows USN naplózása](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) segítségével automatikusan szinkronizálási munkamenetet kezdeményez a fájlok változásakor.

Az Azure fájlmegosztás módosítási lehetőségének észleléséhez az Azure File Sync egy ütemezett feladatkal rendelkezik, amelyet *változásészlelési feladatnak*neveznek. A változásészlelési feladat számba veszi a fájlmegosztás minden fájlját, majd összehasonlítja azt a fájl szinkronizálási verziójával. Amikor a változásészlelési feladat megállapítja, hogy a fájlok megváltoztak, az Azure File Sync szinkronizálási munkamenetet kezdeményez. A változásészlelési feladat 24 óránként indul. Mivel a változásészlelési feladat úgy működik, hogy az Azure-fájlmegosztás minden fájlját számba veszi, a módosításészlelés hosszabb időt vesz igénybe a nagyobb névterekben, mint a kisebb névterekben. Nagy névterek esetén 24 óránként egynél hosszabb időt vehet igénybe annak megállapítása, hogy mely fájlok változtak meg.

Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az **Invoke-AzStorageSyncChangeDetection** PowerShell-parancsmag segítségével manuálisan kezdeményezheti az Azure-fájlmegosztás változásainak észlelését. Ez a parancsmag olyan esetekben készült, ahol valamilyen automatizált folyamat módosítja az Azure-fájlmegosztást, vagy a módosításokat egy rendszergazda végzi (például fájlok és könyvtárak áthelyezése a megosztásba). A végfelhasználói módosítások, a javaslat az, hogy telepítse az Azure File Sync ügynök egy IaaS virtuális gép, és a végfelhasználók számára a fájlmegosztás az IaaS virtuális gép. Így minden módosítás gyorsan szinkronizálható más ügynökökkel anélkül, hogy az Invoke-AzStorageSyncChangeDetection parancsmag használata szükséges lenne. További információ: [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentáció.

>[!NOTE]
>Az Azure-fájlmegosztás rest használatával végzett módosításai nem frissítik az SMB utolsó módosítási idejét, és nem tekintik a szinkronizálás általvégrehajtott módosításnak.

A Windows Server kötetek USN-hez hasonló Azure-fájlmegosztások módosításészlelésének hozzáadását vizsgáljuk. Segítsen nekünk fontossági sorrendbe állítva ezt a funkciót a jövőbeli fejlesztés hez, ha az [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)webhelyen megszavazza.
