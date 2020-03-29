---
title: Az Azure adatátviteli lehetőségei kis, alacsony vagy közepes hálózati sávszélességű adatkészletekhez| Microsoft dokumentumok
description: Megtudhatja, hogyan választhat azure-megoldást adatátvitelre, ha alacsony vagy közepes hálózati sávszélességgel rendelkezik a környezetében, és kis adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397277"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Kis méretű adathalmazok átvitele kis vagy közepes hálózati sávszélességen
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha a környezetben alacsony vagy közepes hálózati sávszélesség van, és kis adatkészletek átvitelét tervezi. A cikk ismerteti az ajánlott adatátviteli lehetőségeket és a megfelelő kulcsfontosságú képességmátrixot ebben a forgatókönyvben.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintéséhez válassza [az Azure-adattovábbítási megoldás kiválasztása című témakört.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Forgatókönyv leírása

A kis adatkészletek a gb-k sorrendjében néhány TB-re vonatkozó adatméretekre vonatkoznak. Az alacsony és közepes hálózati sávszélesség 45 Mb/s (T3 kapcsolat az adatközpontban) és 1 Gbps között történik.

- Ha csak néhány fájlt továbbít, és nem kell automatizálnia az adatátvitelt, fontolja meg a grafikus felülettel ellátott eszközöket.
- Ha nem megfelelő a rendszerfelügyelet, fontolja meg a parancssori vagy programozott/parancsfájlkezelő eszközöket.

## <a name="recommended-options"></a>Ajánlott beállítások

Az ebben a forgatókönyvben ajánlott lehetőségek a következők:

- **Grafikus felület eszközök,** például az Azure Storage Explorer és az Azure Storage az Azure Portalon. Ezek egyszerű módot kínálnak az adatok megtekintésére és néhány fájl gyors átvitelére.

    - **Azure Storage Explorer** – Ez a platformfüggetlen eszköz lehetővé teszi az Azure storage-fiókok tartalmának kezelését. Lehetővé teszi blobok, fájlok, várólisták, táblák és Azure Cosmos DB-entitások feltöltését, letöltését és kezelését. Használja a Blob storage-szal a blobok és mappák kezeléséhez, valamint blobok feltöltéséhez és letöltéséhez a helyi fájlrendszer és a Blob storage, illetve a tárfiókok között.
    - **Azure Portal** – Az Azure Storage az Azure Portalon webalapú felületet biztosít a fájlok felderítéséhez és az új fájlok egyenkénti feltöltéséhez. Ez egy jó lehetőség, ha nem szeretné telepíteni az eszközöket, vagy parancsokat kiadni, hogy gyorsan vizsgálja meg a fájlokat, vagy egyszerűen csak feltölteni egy maroknyi újat.

- **Parancsfájlok készítése/programozott eszközök,** például az AzCopy/PowerShell/Azure CLI és az Azure Storage REST API-k.

    - **AzCopy** – Ezzel a parancssori eszközzel egyszerűen másolhat adatokat az Azure Blobs, files és table storage optimális teljesítményével. AzCopy támogatja az egyidejűséget és a párhuzamosságot, valamint a másolási műveletek folytatását megszakítás esetén.
    - **Azure PowerShell** – A rendszerfelügyelettel kényelmes felhasználók számára az Azure PowerShell Azure Storage moduljával továbbíthatja az adatokat.
    - **Azure CLI** – Ezzel a platformfüggetlen eszközzel kezelheti az Azure-szolgáltatásokat, és adatokat tölthet fel az Azure Storage-ba.
    - **Azure Storage REST API-k/SDK-k** – Egy alkalmazás létrehozásakor fejlesztheti az alkalmazást az Azure Storage REST API-k/SDK-k, és használja az Azure-ügyfélkönyvtárak kínált több nyelven.


## <a name="comparison-of-key-capabilities"></a>A legfontosabb képességek összehasonlítása

Az alábbi táblázat összefoglalja a kulcsfontosságú képességek közötti különbségeket.

| Szolgáltatás | Azure Storage Explorer | Azure portál | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API-k vagy SDK-k |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Rendelkezésre állás | Letöltés és telepítés <br>Önálló eszköz | Webalapú feltárási eszközök az Azure Portalon | Parancssori eszköz |Programozható felületek .NET, Java, Python, JavaScript, C++, Go, Ruby és PHP nyelven |
| Grafikus felület | Igen | Igen | Nem | Nem |
| Támogatott platformok | Windows, Mac, Linux | Webalapú |Windows, Mac, Linux |Összes platform |
| Engedélyezett Blob-tárolási műveletek<br>blobok és mappák esetén | Feltöltés<br>Letöltés<br>Kezelés | Feltöltés<br>Letöltés<br>Kezelés |Feltöltés<br>Letöltés<br>Kezelés | Igen, testreszabható |
| Engedélyezett Data Lake Gen1 tároló<br>fájlok és mappák műveletei | Feltöltés<br>Letöltés<br>Kezelés | Nem |Feltöltés<br>Letöltés<br>Kezelés                   | Nem |
| Engedélyezett fájltárolási műveletek<br>fájlokhoz és könyvtárakhoz | Feltöltés<br>Letöltés<br>Kezelés | Feltöltés<br>Letöltés<br>Kezelés   |Feltöltés<br>Letöltés<br>Kezelés | Igen, testreszabható |
| Engedélyezett táblatárolási műveletek<br>táblázatokhoz |Kezelés | Nem |Táblatámogatás az AzCopy v7-ben |Igen, testreszabható|
| Engedélyezett várólista-tárolás | Kezelés | Nem  |Nem | Igen, testreszabható|


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [vihet át adatokat az Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)rel.
- [Adatok áthelyezése az AzCopyval](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

