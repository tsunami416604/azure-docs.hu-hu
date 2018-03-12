---
title: "Az Azure-fájlok központi telepítésének megtervezése |} Microsoft Docs"
description: "További tudnivalók az Azure-fájlok központi telepítésének tervezése során megfontolandó tényezőkről."
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: wgries
ms.openlocfilehash: 017dd79e2d15fdd98ea020c686857d282bad244e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők az iparági szabványos SMB protokollon keresztül. Azure fájlok teljes körűen felügyelt, mert sokkal egyszerűbb, mint a központi telepítésére és felügyeletére a fájlkiszolgáló vagy NAS-eszköz telepítés éles helyzetekben. Ez a cikk foglalkozik a témakörök figyelembe kell venni egy Azure fájlmegosztás üzemi használatra a szervezeten belüli központi telepítésekor.

## <a name="management-concepts"></a>Felügyeleti fogalmak
 A következő ábra szemlélteti a Azure fájlok felügyeleti szerkezet:

![Fájlstruktúra](./media/storage-files-introduction/files-concepts.png)

* **Storage-fiók**: Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Méretezhetőségi és teljesítménycélok).

* **Megosztás**: A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, és a megosztás tud tárolni a fájlokat, legfeljebb 5 TiB teljes kapacitását a fájlmegosztás korlátlan számú.

* **Könyvtár**: Egy választható könyvtár-hierarchia.

* **Fájl**: A megosztásban található fájl. Lehet, hogy a fájl mérete legfeljebb 1 TiB.

* **URL-formátum**: a fájl REST-protokollal végzett Azure fájlmegosztásra vonatkozó fájljai a következő URL-formátummal:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Adatok hozzáférési módszer
Az Azure fájlok ajánlatok két, beépített és kényelmes adat-hozzáférési módszer használható önállóan, vagy egymással, együtt az adatok eléréséhez:

1. **Közvetlen hozzáférés felhő**: bármely Azure fájlmegosztás által lehet csatlakoztatni [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), és/vagy [Linux](storage-how-to-use-files-linux.md) rendelkező az iparági szabványos Server Message Block (SMB) protokoll vagy a fájl REST API-n keresztül. Az SMB olvasási és írási műveleteket ad ki a megosztáson tárolt fájlok készülnek el közvetlenül a fájlmegosztás az Azure-ban. Csak akkor csatlakoztassa egy Azure-ban, az SMB-ügyfél operációs rendszerben támogatja a legalább SMB 2.1-es verzióját. Csatlakoztassa a helyszínen, például egy felhasználó munkaállomáson, a munkaállomás által támogatott SMB-ügyfél támogatnia kell a legalább SMB 3.0 (a titkosítás). Mellett SMB új alkalmazásokat vagy szolgáltatásokat lehet, hogy közvetlenül hozzáférhet a fájlmegosztáshoz fájl REST, amely egy egyszerű és méretezhető alkalmazásprogramozási felületet nyújt szoftverfejlesztői-en keresztül.
2. **Az Azure fájlszinkronizálás** (előzetes verzió): Azure fájl szinkronizálási megosztások replikálható helyszíni Windows Server-kiszolgálók vagy az Azure-ban. A felhasználók elérésére a fájlmegosztás keresztül a Windows Server többek között az SMB vagy NFS-megosztások keresztül. Ez akkor hasznos, forgatókönyvek, amelyben az adatok elért és módosítva távolságra a egy Azure-adatközpontban, többek között a fiókirodák esetében. Előfordulhat, hogy replikálja adatok több Windows Server végpontok közötti például több fiókirodák között. Végezetül adatokat is helyezhető el a az Azure Fileshoz, úgy, hogy minden adatot a kiszolgáló továbbra is elérhetők, de a kiszolgáló nem rendelkezik az adatok teljes másolata. Ehelyett adatok van zökkenőmentesen előtt tartani, amikor a felhasználó megnyitja.

A következő táblázat bemutatja, hogyan az alkalmazások és használhatják a Azure fájlmegosztás:

| | Közvetlen felhőelérést | Azure File Sync |
|------------------------|------------|-----------------|
| Milyen protokollok szeretné használni? | Az Azure Files SMB 2.1, SMB 3.0-s és fájl REST API támogatja. | Hozzáférés az Azure fájlmegosztás bármely támogatott protokollal, a Windows Server (SMB, NFS, ftps-t, stb.) |  
| Hol futnak az alkalmazások és szolgáltatások? | **Az Azure-ban**: Azure fájlok adatait közvetlen hozzáférést biztosít. | **A lassú hálózati helyszíni**: Windows, Linux és macOS ügyfelek helyi helyszíni Windows fájlmegosztást csatlakoztathatnak, az Azure fájlmegosztás gyors gyorsítótár. |
| Milyen hatással lehet a hozzáférés-vezérlési listák van szüksége? | Megosztás- és fájl szintje. | Megosztás, a fájl és a felhasználói szintű. |

## <a name="data-security"></a>Adatbiztonság
Az Azure Files program számos beépített biztosítva az adatok biztonsága:

* Mindkét over tömörített protokollokban titkosítás támogatása: SMB 3.0-titkosítás és a fájl REST HTTPS-KAPCSOLATON keresztül. Alapértelmezés: 
    * Ügyfelek, amelyek támogatják az SMB 3.0-titkosítás adatokat küldeni és fogadni egy titkosított csatornán keresztül.
    * Ügyfelek, amelyek nem támogatják az SMB 3.0-s verziójával is intra-datacenter protokollt használó kommunikációra SMB 2.1 vagy SMB 3.0 titkosítás nélkül. Ne feledje, hogy az ügyfelek nem többek datacenter kommunikálhassanak SMB 2.1 vagy SMB 3.0 titkosítás nélkül.
    * Az ügyfelek a is fájl REST HTTP vagy HTTPS protokollt használó kommunikációra.
* Titkosítási nyugalmi ([Azure Storage szolgáltatás titkosítási](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): az összes storage-fiók alapértelmezés szerint engedélyezve van a Storage szolgáltatás titkosítási (SSE). Kulcsok teljes körűen felügyelt adatok nyugalmi van titkosítva. Titkosítási nyugalmi nem tárolási költségek növelheti vagy csökkentheti a teljesítményt. 
* A titkosított adatok az átvitel közbeni választható rendszerkövetelményt: kiválasztásakor Azure fájlok elutasítja a hozzáférést az adatokat nem titkosított csatornákon keresztül. Pontosabban csak HTTPS és SMB 3.0 titkosítást kapcsolatokkal engedélyezettek. 

    > [!Important]  
    > Az adatok biztonságos átvitelét igénylő, akkor a régebbi SMB-ügyfelek nem képes kommunikálni az SMB 3.0-s titkosítás sikertelen lesz. Lásd: [csatolható fel a Windows](storage-how-to-use-files-windows.md), [Linux csatlakoztatási](storage-how-to-use-files-linux.md), [macOS csatlakoztassa](storage-how-to-use-files-mac.md) további információt.

A maximális biztonság érdekében javasolt mindig mindkét titkosítási nyugalmi engedélyezése és az adatok az átvitel közbeni titkosítás engedélyezése, amikor az adatok elérésére használt a modern ügyfelek. Például ha kell csatlakoztatni egy megosztást a Windows Server 2008 R2 virtuális gép, amely csak az SMB 2.1 támogatja, akkor engedélyeznie kell a tárfiók nem titkosított forgalom óta SMB 2.1 nem támogatja a titkosítást.

Azure fájlszinkronizálás az Azure-fájlmegosztás eléréséhez használnak, ha mindig használjuk HTTPS és SMB 3.0 titkosítással szinkronizálni az adatokat, hogy a Windows-kiszolgálók, függetlenül attól, hogy megköveteljék a adatokat nyugalmi titkosítását.

## <a name="data-redundancy"></a>Adatredundancia
Az Azure Files három redundancia lehetőségeket támogat: helyileg redundáns tárolás (LRS), a zóna redundáns tárolás (ZRS) és a georedundáns tárolás (GRS). A következő szakaszok ismertetik a különböző redundancia lehetőségek közötti eltéréseket:

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Redundáns tárolási zóna
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Georedundáns tárolás
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Növekedési adatmintát
Napjainkban a maximális egy Azure fájlmegosztás mérete 5 TiB, amelynek részét alkotják az megosztás pillanatképeket. A jelenlegi korlátozás miatt érdemes megfontolni a várt adatmennyiség-növekedés egy Azure fájlmegosztás telepítésekor. Vegye figyelembe, hogy egy Azure Storage-fiók tárolhat több megosztás minden megosztás között tárolt 500 TiB összesen.

Több Azure fájlmegosztásokat Windows egyetlen fájlkiszolgáló-vel való szinkronizálásának Azure fájlszinkronizálás lehetőség. Ez lehetővé teszi, hogy győződjön meg arról, hogy a régebbi, nagyon nagy fájlmegosztások, hogy előfordulhat, hogy a helyszíni be Azure fájlszinkronizálás tehető. Ellenőrizze a [Azure fájl szinkronizálási központi telepítésének tervezésében](storage-files-planning.md) további információt.

## <a name="data-transfer-method"></a>Adatátvitel módja
Egy meglévő fájl adatait megosztani, például egy helyszíni fájlmegosztás, Azure-fájlok átviteli tömeges sok könnyen lehetőség áll rendelkezésre. Néhány népszerű néhányat a meglévők közül a következők (a teljesség):

* **Az Azure fájlszinkronizálás**: egy Azure-fájlmegosztásban (a "Felhővégpontnak") és a Windows-könyvtár névtér (a "Server Endpoint") között első szinkronizálási részeként Azure fájlszinkronizálás replikálja minden adatot a meglévő fájlmegosztásról az Azure Fileshoz.
* **[Az Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: az Azure Import/Export szolgáltatás lehetővé teszi a biztonságos átvitelére a nagy mennyiségű adat be egy Azure fájlmegosztás által szállítási merevlemez-meghajtók számára egy Azure-adatközpontban. 
* **[A Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy az alkalmazáshoz tartozó Windows és Windows Server jól ismert másolási eszközzel. A Robocopy adatok átviteléhez az Azure-fájlok helyileg a fájlmegosztás csatlakoztatása és a csatlakoztatott hely majd segítségével a Robocopy parancsot a célként használható.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy egy parancssori segédprogram, és az Azure-fájlok, valamint az Azure Blob Storage tárolóban, az adatok másolása az optimális teljesítménnyel egyszerű parancsok használatával. AzCopy Windows és Linux rendszerekhez érhető el.

## <a name="next-steps"></a>További lépések
* [Egy fájl Azure szinkronizálási központi telepítésének tervezése](storage-sync-files-planning.md)
* [Az Azure Files telepítése](storage-files-deployment-guide.md)
* [Az Azure File-szinkronizálás telepítése](storage-sync-files-deployment-guide.md)