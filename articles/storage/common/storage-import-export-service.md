---
title: Azure Import/Export használatával történő adatátvitelhez számára és Azure Storage-ból |} Microsoft Docs
description: Megtudhatja, hogyan importálási létrehozni és exportálni a feladatokat az adatoknak az Azure Storage érkező vagy oda irányuló az Azure portálon.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 83ba437e699eb150e86e6c89e478377394966419
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809542"
---
# <a name="what-is-azure-importexport-service"></a>Mi az Azure Import/Export szolgáltatás?

Az Azure Import/Export szolgáltatás biztonságosan importálása nagy mennyiségű adatok Azure Blob storage és Azure fájlok számára egy Azure-adatközpontban meghajtók szállítási szolgál. Ez a szolgáltatás adatátvitelt az Azure Blob storage merevlemez-meghajtók, és küldje el a helyszíni helyek használatával is lehet. Egy vagy több lemezt az adatok Azure Blob-tároló vagy Azure fájlok importálhatók. 

## <a name="azure-importexport-usecases"></a>Az Azure Import/Export usecases

Azure Import/Export szolgáltatás esetekben érdemes feltöltése vagy adatok letöltése a hálózaton keresztül túl lassú, vagy további hálózati sávszélesség első megfizethetetlenné. Ez a szolgáltatás a következő helyzetekben használhatja:

* **Adatok áttelepítése a felhőbe**: nagy mennyiségű adatok gyors áthelyezése az Azure-ba, és hatékonyan költség.
* **Tartalomterjesztést**: a felhasználói helyek gyorsan adatküldéshez.
* **Biztonsági mentés**: a helyszíni adatok Azure Storage tárolja a biztonsági másolatok készítése.
* **Adat-helyreállítás**: nagy mennyiségű storage-ban tárolt adatok helyreállításához, és annak a helyszíni helyre kézbesíteni.

## <a name="importexport-components"></a>Importálási/exportálási összetevők

Import/Export szolgáltatás a következő összetevőket használja:

- **Importálási/exportálási**szolgáltatás: Ez a szolgáltatás elérhető az Azure portál segítségével a felhasználó létrehozása és importálása nyomon követése és exportálni a feladatokat.  

- **WAImportExport eszköz**: Ez az parancssori eszköz, amely a következőket teszi: 
    - Előkészíti a meghajtók, amelyek szállított importálása.
    - Lehetővé teszi az adatok másolását a meghajtóra.
    - Titkosítja az adatokat a meghajtón a BitLocker szolgáltatással.
    - Hozza létre az Importálás létrehozása során használt meghajtó napló fájljait.
    - Segíti az azonosítását exportálási feladat szükséges meghajtók számát.

    Ez az eszköz érhető két esetén 1 és 2. Azt javasoljuk, hogy használja:

    - 1-es verziójú importálási/exportálási az Azure Blob Storage tárolóban. 
    - Version 2 adatok importálása az Azure files.

    A WAImportExport eszköze csak 64 bites Windows operációs rendszerrel kompatibilis. Adott operációsrendszer-verziók támogatottak, látogasson el [Azure Import/Export követelmények](storage-import-export-requirements.md#supported-operating-systems).

- **Lemezek**: elküldhet a SSD-meghajtót (SSD) vagy a merevlemezt (HDD) meghajtók az Azure-adatközpontban. Az importálási feladat létrehozásakor küldje el az adatokat tartalmazó meghajtók. Exportálási feladat létrehozásakor üres meghajtókat, az Azure-adatközpontban küldje el. Adott lemez legyen kijelölve, látogasson el [támogatott lemeztípusokkal](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hogyan működik az Import/Export?

Azure Import/Export szolgáltatás lehetővé teszi az adatok átvitele Azure Blobok és Azure fájlok feladatok létrehozása. Azure-portálon vagy az Azure Resource Manager REST API használatával hozzon létre feladatokat. Minden feladat tartozik egy tárfiókot. 

A feladatok importálása és exportálni a feladatokat. Az importálási feladat lehetővé teszi, hogy adatokat importáljon belőlük az Azure BLOB vagy az Azure files, mivel az exportálási feladat lehetővé teszi az adatok Azure BLOB exportálni kell. Hogy az importálás küldje el az adatokat tartalmazó meghajtókat. Exportálási feladat létrehozásakor egy Azure-adatközpontban üres meghajtók küldje el. Minden esetben elküldhet a feladatok száma legfeljebb 10 lemezmeghajtókat.

> [!IMPORTANT]
> Adatok exportálása az Azure-fájlok nem támogatott.

Ebben a szakaszban magas szintű lépéseket szerepet játszanak az importálási és exportálási feladatokat ismerteti. 


### <a name="inside-an-import-job"></a>Az importálási feladat belül

Magas szinten az importálási feladat a következő lépéseket foglalja magában:

1. Határozza meg az adatokat importált, hány meghajtót kell, az adatok az Azure storage blob célhelyét.
2. Másolja az adatokat a lemezmeghajtók a WAImportExport eszközzel. A lemez is van a BitLocker titkosítást.
3. A céloldali tárfiók egy importálási feladat létrehozása az Azure portálon. Töltse fel a meghajtó Adatbázisnapló-fájlok.
2. Adja meg a címet és szolgáltatónként számát a meghajtók szállítási vissza szeretné.
3. Küldje el a merevlemez-meghajtók, a feladat létrehozásakor megadott szállítási címre.
4. A nyomon követési száma az importálási feladat részleteit a kézbesítési frissítése, és küldje el az importálási feladat.
5. Fogadott és az Azure-adatközpont feldolgozni.
6. Meghajtók mellékeltük a vivőjel-fiókkal a található az importálási feladat Válaszcím.
  
    ![Ábra 1:Import feladat folyamata](./media/storage-import-export-service/importjob.png)

Lépésenkénti adatokat importálni, írja be:

- [Adatok importálása az Azure BLOB](storage-import-export-data-to-blobs.md)
- [Adatok importálása az Azure-fájlok](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Exportálási feladat belül

> [!IMPORTANT]
> A szolgáltatás csak az Azure BLOB exportálása támogatja. Az Azure files az exportálás nem támogatott.

Magas szinten exportálási feladat a következő lépéseket foglalja magában:

1. Határozza meg az exportálandó adatokat, a meghajtók száma meg kell, a forrás blobokkal vagy a tároló elérési az adatok a Blob Storage tárolóban.
3. A forrás tárfiókban lévő exportálási feladat létrehozása Azure-portálon.
4. Adja meg a forrás blobokkal vagy a tároló elérési exportálandó adatokat.
5. Adja meg a címet és szolgáltatónként számát a meghajtók szállítási vissza szeretné.
6. Küldje el a merevlemez-meghajtók, a feladat létrehozásakor megadott szállítási címre.
7. Frissítse a kézbesítési követési szám a exportálási feladat részletei, valamint az exportálási feladat elküldéséhez.
8. A fogadott és az Azure-adatközpont feldolgozni.
9. A Bitlockerrel titkosított meghajtókat, és a kulcsok elérhetők az Azure-portálon.  
10. A meghajtók mellékeltük a vivőjel-fiókkal a található az importálási feladat Válaszcím.
  
    ![Ábra 2:Export feladat folyamata](./media/storage-import-export-service/exportjob.png)

Adatok exportálása lépésenkénti, Ugrás [exportál adatokat az Azure BLOB](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Régiónkénti elérhetőség 

Az Azure Import/Export szolgáltatás támogatja az adatok másolását, és az összes Azure storage-fiók. A felsorolt helyek valamelyikén meghajtók elküldhet. Ha a tárfiók egy Azure-beli hely, amely nincs megadva itt, egy másik szállítási helyre valósul meg a feladat létrehozásakor.

### <a name="supported-shipping-locations"></a>A szállítási helyeken támogatott


|Ország  |Ország  |Ország  |Ország  |
|---------|---------|---------|---------|
|USA keleti régiója    | Észak-Európa        | Közép-India        |USA-beli államigazgatás – Iowa         |
|USA nyugati régiója     |Nyugat-Európa         | Dél-India        | US DoD – Kelet        |
|USA 2. keleti régiója    | Kelet-Ázsia        |  Nyugat-India        | US DoD – Középső régió        |
|USA nyugati régiója, 2.     | Délkelet-Ázsia        | Közép-Kanada        | Kelet-Kína         |
|USA középső régiója     | Kelet-Ausztrália        | Kelet-Kanada        | Észak-Kína        |
|USA északi középső régiója     |  Délkelet-Ausztrália       | Dél-Brazília        | Az Egyesült Királyság déli régiója        |
|USA déli középső régiója     | Nyugat-Japán        |Korea középső régiója         | Közép-Németország        |
|USA nyugati középső régiója     |  Kelet-Japán       | USA-beli államigazgatás – Virginia        | Északkelet-Németország        |


## <a name="security-considerations"></a>Biztonsági szempontok

A meghajtón található adatokat a BitLocker meghajtótitkosítás titkosítja. A titkosítási védi az adatokat, amíg az átvitel során.

Az importálási feladatok a meghajtók két módon vannak titkosítva.  


- Adja meg a beállítást, ha *dataset.csv* fájl meghajtó előkészítése során a WAImportExport eszköz futtatása során. 

- Engedélyezze manuálisan a meghajtón a BitLocker-titkosítást. Adja meg a titkosítási kulcsot a a *driveset.csv* futtatásakor WAImportExport eszköz parancssori meghajtó előkészítése során.


Az exportálási feladatok a meghajtók, az adatok másolását követően a szolgáltatás titkosítja a meghajtón a BitLocker használatával, mielőtt azt vissza. A titkosítási kulcsot az Azure-portálon valósul meg.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Díjszabás

**Meghajtó díj kezelése**

Minden meghajtó, az importálás részeként feldolgozott meghajtó kezelési díj ellenében történik, vagy exportálja a feladatot. A részletek a [Azure Import/Export szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Szállítási költségek**

Amikor Ön Azure-meghajtóval, a szállítási szolgáltatói fizetett szállítási költsége. Amikor Microsoft, a meghajtók visszatér, a vivőjel-fiók, amely a feladat létrehozásakor, a megadott díjfizetéssel szállítási költsége.

**Tranzakciós költségek**

Nincsenek nem tranzakciós költségek mellett standard tárolási tranzakciós költségeket adatok importálása az Azure Storage. A szabványos kilépő költségek is alkalmazható, ha adatait a Blob-tároló exportálja. Tranzakciós költségek további információkért lásd: [adatátviteli díjszabás.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>További lépések

Útmutató: az Import/Export szolgáltatás használata:
* [Adatok importálása az Azure BLOB](storage-import-export-data-to-blobs.md)
* [Exportál adatokat az Azure BLOB](storage-import-export-data-from-blobs.md)
* [Adatok importálása az Azure Fileshoz](storage-import-export-data-to-files.md)

