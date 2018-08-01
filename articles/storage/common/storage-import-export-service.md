---
title: Azure Import/Export történő adatátvitelt, és az Azure Storage-ból |} A Microsoft Docs
description: Ismerje meg, hogyan importálás létrehozni és exportálni a feladatokat az adatoknak az Azure Storage szolgáltatásba vagy onnan az Azure Portalon.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: alkohli
ms.openlocfilehash: e9fc74e6cd145cbba5b620b9db6db9635a0c4c77
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364525"
---
# <a name="what-is-azure-importexport-service"></a>Mi az Azure Import/Export szolgáltatás?

Az Azure Import/Export szolgáltatás segítségével biztonságosan importálása az Azure-adatközpont-meghajtókon szállításával az Azure Blob storage és az Azure Files nagy mennyiségű adat. Ez a szolgáltatás adatainak átvitelét az Azure Blob storage-ból a merevlemez-meghajtók, és küldje el a helyszíni hely a használatával is lehet. Egy vagy több lemezmeghajtót származó adatok importálhatók, vagy az Azure Blob storage vagy az Azure Files. 

Adja meg, hogy saját lemezmeghajtókat, és adatátvitelt az Azure Import/Export szolgáltatással. A Microsoft által biztosított lemezmeghajtók is használhatja. 

Ha azt szeretné, a Microsoft által biztosított meghajtók használata az adatok átviteléhez használható [Azure Data Box-lemezek](../../databox/data-box-disk-overview.md) adatok importálása az Azure-bA. A Microsoft 40 TB-os kapacitásáig keresztül egy regionális szolgáltató adatközpontjában megrendelésenként legfeljebb 5 titkosított tartós állapotú meghajtók (SSD-kkel) tartalmaz. Gyorsan lemezmeghajtók konfigurálása, másolja az adatokat a lemezmeghajtók USB 3.0-kapcsolaton keresztül és szállíthat a lemezmeghajtók vissza az Azure-bA. További információért ugorjon [áttekintése az Azure Data Box-lemezek](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-usecases"></a>Az Azure Import/Export usecases

Fontolja meg az Azure Import/Export szolgáltatás használatát, amikor feltöltése vagy a hálózaton keresztül adatokat tölti le túl lassúak, vagy további hálózati sávszélesség a kezdeti költségekkel járnának. A szolgáltatás használatához a következő esetekben:

* **Adatok migrálása a felhőbe**: nagy mennyiségű adat áthelyezése az Azure-ban gyorsan és költséghatékonyan.
* **A tartalom terjesztési**: pedig gyorsan elküldheti adatait az ügyfelek telephelyeire.
* **Biztonsági mentés**: az Azure Storage szolgáltatásban tárolja a helyszíni adatok biztonsági másolatok készítése.
* **Adat-helyreállítás**: nagy mennyiségű storage-ban tárolt adatok helyreállításához és szállítását a helyszíni helyre.

## <a name="importexport-components"></a>Importálási/exportálási összetevők

Import/Export szolgáltatás a következő összetevőket használja:

- **Importálási/exportálási szolgáltatás**: az Azure Portalon elérhető szolgáltatás segítségével a felhasználók használhassanak, és nyomon követése adatok (feltöltés) importálása és exportálása a feladatok (Letöltés).  

- **WAImportExport eszköz**: Ez a parancssori eszköz, amely a következőket: 
    - Előkészíti a lemezmeghajtók szállított importálása.
    - Lehetővé teszi az adatok másolása a meghajtóra.
    - Az adatok a meghajtón a BitLocker titkosítja.
    - Állít elő, a meghajtó Adatbázisnapló-fájlok importálása létrehozása során használt.
    - Export-feladatok szükséges meghajtók számú segíti az azonosítását.
    
> [!NOTE]
> A WAImportExport eszköz két verziója, 1. és 2 verzió érhető el. Azt javasoljuk, hogy használja:
> - 1. verzió az importálási és exportálási szolgáltatáshoz az Azure Blob storage-bA. 
> - 2 az adatok importálása az Azure files-verzió.
>
> A WAImportExport eszköz csak nem kompatibilis a 64 bites Windows operációs rendszert. Lépjen az adott operációsrendszer-verziók támogatott, [Azure Import/Export követelmények](storage-import-export-requirements.md#supported-operating-systems).

- **Lemezmeghajtók**: el olyan tartós állapotú meghajtókhoz (SSD-kkel) vagy a merevlemez-(HDD) meghajtók az Azure-adatközpontba. Importálási feladat létrehozásakor, az adatokat tartalmazó meghajtók szállításra. Exportálási feladat létrehozásakor, az Azure-adatközpontba üres meghajtókon szállításra. Adott lemez esetében, ugorjon [támogatja a lemeztípusok](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hogyan működik az importálási/exportálási?

Az Azure Import/Export szolgáltatás lehetővé teszi, hogy az adatforgalom az Azure-Blobok és az Azure Files feladatok létrehozásával. Az Azure portal vagy Azure Resource Manager REST API használatával hozzon létre feladatokat. Minden egyes feladat társítva az egy tárfiókban. 

A feladatok importálása vagy exportálása feladatok. Importálási feladat lehetővé teszi, hogy az adatok importálása az Azure-Blobokban vagy az Azure files, míg az exportálási feladatot lehetővé teszi, hogy az Azure-Blobokból az exportálandó adatokat. Importálási feladatokhoz szállítja az adatokat tartalmazó meghajtókat. Exportálási feladat létrehozásakor, az Azure-adatközpont üres meghajtókon szállításra. Minden esetben el olyan legfeljebb 10 meghajtók száma alapján történik.

### <a name="inside-an-import-job"></a>Importálási feladat belül

Magas szintű az importálási feladat az alábbi lépésekből áll:

1. Határozza meg az adatokat importált, hány meghajtót van szüksége, az adatok Azure Storage-blob célhelyét.
2. A WAImportExport eszközzel másolhat adatokat a lemezmeghajtók. A merevlemez-meghajtók BitLocker-titkosítása.
3. Importálási feladat létrehozása a céloldali tárfiók Azure Portalon. A meghajtó naplófájlok feltöltése.
4. Adja meg a címet és a Szállítmányozó számlaszáma a szállítási a meghajtók vissza.
5. A feladat létrehozása során a szállítási cím meghajtókon szállításra.
6. Követési szám a importálási feladat részletei tartalmazzák a kézbesítési frissíteni, és küldje el az importálási feladatot.
7. A meghajtók fogadása és feldolgozása az Azure-adatközpontban.
8. A meghajtók mellékelt szolgáltatója, a feladó címe az importálási feladat megadott fiókkal.

> [!NOTE]
> Ossza meg a belföldi Szállítmányozói fiókjára (belül data center ország) helyi szállításhoz 
>
> Ossza meg a nemzetközi Szállítmányozói fiókjára külföldön (data center ország) kívül eső szállításhoz

 ![Ábra 1:Import feladat folyamat](./media/storage-import-export-service/importjob.png)

A részletes adatok importálása, Ugrás:

- [Adatok importálása az Azure-Blobok](storage-import-export-data-to-blobs.md)
- [Adatok importálása az Azure Files szolgáltatásba](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Exportálási feladat belül

> [!IMPORTANT]
> A szolgáltatás csak az Azure-Blobok exportálásának támogatja. Az Azure-fájlok exportálása nem támogatott.

Magas szintű exportálási feladat az alábbi lépésekből áll:

1. Határozza meg az exportálandó adatokat, a meghajtók száma akkor szükséges, a forrás BLOB vagy az adatokat tároló elérési Blob storage-ban.
3. Exportálási feladat létrehozása a forrás tárfiókban található Azure Portalon.
4. Adja meg a forrás BLOB vagy az adatok exportálható tároló elérési.
5. Adja meg a címet és a Szállítmányozó számlaszáma a szállítási a meghajtók vissza.
6. A feladat létrehozása során a szállítási cím meghajtókon szállításra.
7. Követési szám a exportálási feladat részletei tartalmazzák a kézbesítési frissíteni, és küldje el az exportálási feladatot.
8. A meghajtók fogadása és feldolgozása az Azure-adatközpontban.
9. A meghajtó van titkosítva, a BitLocker és a kulcsok az Azure Portalon keresztül érhetők el.  
10. A meghajtók mellékelt szolgáltatója, a feladó címe az importálási feladat megadott fiókkal.

> [!NOTE]
> Ossza meg a belföldi Szállítmányozói fiókjára (belül data center ország) helyi szállításhoz 
>
> Ossza meg a nemzetközi Szállítmányozói fiókjára külföldön (data center ország) kívül eső szállításhoz
  
 ![Ábra 2:Export feladat folyamat](./media/storage-import-export-service/exportjob.png)

Adatok exportálása részletes utasításokért látogasson el [exportál adatokat az Azure-Blobok](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Régiónkénti elérhetőség 

Az Azure Import/Export szolgáltatás támogatja az adatok másolását, és az összes Azure storage-fiók. A felsorolt helyek valamelyikén lemezmeghajtók küldhet el. Ha a tárfiók egy Azure-beli helyen, amely nem szerepel itt, egy másik szállítási címhez tartozó hely áll rendelkezésre a feladat létrehozásakor.

### <a name="supported-shipping-locations"></a>Szállítási helyeken támogatott


|Ország  |Ország  |Ország  |Ország  |
|---------|---------|---------|---------|
|USA keleti régiója    | Észak-Európa        | Közép-India        |US Gov Iowa         |
|USA nyugati régiója     |Nyugat-Európa         | Dél-India        | US DoD – Kelet        |
|USA 2. keleti régiója    | Kelet-Ázsia        |  Nyugat-India        | US DoD – Középső régió        |
|USA nyugati régiója, 2.     | Délkelet-Ázsia        | Közép-Kanada        | Kelet-Kína         |
|USA középső régiója     | Kelet-Ausztrália        | Kelet-Kanada        | Észak-Kína        |
|USA északi középső régiója     |  Délkelet-Ausztrália       | Dél-Brazília        | Az Egyesült Királyság déli régiója        |
|USA déli középső régiója     | Nyugat-Japán        |Korea középső régiója         | Közép-Németország        |
|USA nyugati középső régiója     |  Kelet-Japán       | USA-beli államigazgatás – Virginia        | Északkelet-Németország        |


## <a name="security-considerations"></a>Biztonsági szempontok

A meghajtón található adatok titkosítottak, a BitLocker meghajtótitkosítás gondoskodik. Bár az átvitel során, a titkosítás védi az adatokat.

Az importálási feladatokhoz a meghajtók kétféleképpen titkosítottak.  


- Adja meg a beállítást, ha *dataset.csv* fájlból a meghajtó előkészítése során a WAImportExport eszközt futtatja. 

- A BitLocker titkosítást manuálisan a meghajtón található. Adja meg a titkosítási kulcsot a *driveset.csv* futtatásakor WAImportExport eszköz parancssori meghajtó előkészítése során.


Export-feladatok a a meghajtók, az adatok másolása után a szolgáltatás titkosítja a meghajtót a BitLocker, mielőtt azt vissza. A titkosítási kulcs kapja meg az Azure Portalon keresztül.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Díjszabás

**Meghajtó díj kezelése**

Egy meghajtó kezelési díja az importálás részeként feldolgozott minden olyan meghajtó vagy exportálási feladatot. További részletek a [Azure Import/Export szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage-import-export/).

**A szállítási költségeket**

Amikor Ön az Azure-bA meghajtók, a szállítmányozási kell fizetnie a szállítási költségeket. A Microsoft a meghajtókat, és ad vissza, ha a szállítási költségeket díját a Szállítmányozói fiókjára, amely azt adja meg a feladat létrehozásakor.

**Tranzakciós költségek**

Amikor adatokat importál az Azure Storage-ba, nincsenek nincsenek tranzakciós díjak standard tárolási tranzakciós költségek mellett. A standard szintű kimenő adatforgalmi díjak vonatkoznak a Blob storage-ból exportált adatok. Tranzakciós költségek további információkért lásd: [adatátvitel díjszabási.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható az importálási/exportálási szolgáltatás:
* [Adatok importálása az Azure-Blobok](storage-import-export-data-to-blobs.md)
* [Az Azure-Blobokból az adatok exportálása](storage-import-export-data-from-blobs.md)
* [Adatok importálása az Azure Files](storage-import-export-data-to-files.md)

