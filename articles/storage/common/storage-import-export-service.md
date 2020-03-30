---
title: Adatok átvitele az Azure Storage-ba és onnan az Azure Storage-ba | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre importálási és exportálási feladatokat az Azure Portalon az Azure Storage-ba és az Azure Storage-ból történő adatátvitelhez.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282443"
---
# <a name="what-is-azure-importexport-service"></a>Mi az Azure importálási/exportálási szolgáltatása?

Az Azure import/export szolgáltatás segítségével biztonságosan importálhat nagy mennyiségű adatot az Azure Blob storage-ba és az Azure Files-ba lemezmeghajtók azure-adatközpontba szállításával. Ez a szolgáltatás is használható adatok átvitelére az Azure Blob storage lemezmeghajtókra, és a szállítás a helyszíni helyekre. Egy vagy több lemezmeghajtóról származó adatok importálhatók az Azure Blob storage-ba vagy az Azure Files-ba.

Adja meg saját lemezmeghajtóit, és továbbítsa az adatokat az Azure importálási/exportálási szolgáltatásával. A Microsoft által biztosított lemezmeghajtókat is használhatja.

Ha a Microsoft által biztosított lemezmeghajtók használatával szeretne adatokat átvinni, az [Azure Data Box Disk](../../databox/data-box-disk-overview.md) segítségével importálhat adatokat az Azure-ba. A Microsoft rendelésenként legfeljebb 5 titkosított SSD-meghajtót (SSD) szállít az adatközpontba egy regionális szolgáltatón keresztül. Gyorsan konfigurálhatja a lemezmeghajtókat, átmásolhatja az adatokat usb 3.0-s kapcsolaton keresztül lemezmeghajtókra, és visszaküldheti a lemezmeghajtókat az Azure-ba. További információ: [Azure Data Box Disk overview](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Azure importálási/exportálási használati esetek

Fontolja meg az Azure Import/Export szolgáltatás használatát, ha az adatok hálózaton keresztüli feltöltése vagy letöltése túl lassú, vagy a további hálózati sávszélesség beszerzése költségmegfizethetetlen. A szolgáltatás használata a következő esetekben:

* **Adatáttelepítés a felhőbe:** Nagy mennyiségű adatot helyezhet át gyorsan és költséghatékonyan az Azure-ba.
* **Tartalomterjesztés**: Gyorsan küldhet adatokat az ügyfélwebhelyekre.
* **Biztonsági mentés:** Készítsen biztonsági mentést a helyszíni adatokról az Azure Storage-ban tárolandó adatokról.
* **Adat-helyreállítás:** Nagy mennyiségű tárolt adatok helyreállítása a tárolóban, és azokat a helyszíni helyre.

## <a name="importexport-components"></a>Összetevők importálása/exportálása

Az Import/Export szolgáltatás a következő összetevőket használja:

* **Importálási/exportálási szolgáltatás:** Ez az Azure Portalon elérhető szolgáltatás segít a felhasználónak az adatimportálási (feltöltési) és exportálási (letöltési) feladatok létrehozásában és nyomon követésében.  

* **WAImportExport eszköz**: Ez egy parancssori eszköz, amely a következőket teszi:
  * Előkészíti a szállított lemezmeghajtókat importálásra.
  * Megkönnyíti az adatok másolását a meghajtóra.
  * A meghajtón lévő adatoktitkosítása az AES 128 bites BitLocker segítségével. A BitLocker-kulcs védelméhez külső kulcsvédőt használhat.
  * Az importálás létrehozása során használt meghajtónapló-fájlokat hozza létre.
  * Segít azonosítani az exportálási feladatokhoz szükséges meghajtók számát.

> [!NOTE]
> A WAImportExport eszköz két verzióban érhető el, az 1-es és a 2-es verzióban. Javasoljuk, hogy használja:
>
> * 1-es verzió az Azure Blob storage-ba történő importáláshoz/exportáláshoz.
> * 2-es verzió az adatok Azure-fájlokba történő importálásához.
>
> A WAImportExport eszköz csak 64 bites Windows operációs rendszerrel kompatibilis. A támogatott operációsrendszer-verziókesetében keresse fel [az Azure importálási/exportálási követelményeit.](storage-import-export-requirements.md#supported-operating-systems)

* **Lemezmeghajtók:** SSD-k vagy merevlemez-meghajtók (HDD-k) szállíthatók az Azure adatközpontba. Importálási feladat létrehozásakor az adatokat tartalmazó lemezmeghajtókat szállítja. Exportálási feladat létrehozásakor üres meghajtókat szállít az Azure-adatközpontba. Bizonyos lemeztípusok esetén nyissa meg a [Támogatott lemeztípusok at.](storage-import-export-requirements.md#supported-hardware)

## <a name="how-does-importexport-work"></a>Hogyan működik az Import/Export funkció?

Az Azure importálási/exportálási szolgáltatás lehetővé teszi az azure blobokba és az Azure-fájlokba történő adatátvitelt a feladatok létrehozásával. Az Azure Portal vagy az Azure Resource Manager REST API használatával munkahelyeket hozhat létre. Minden feladat egyetlen tárfiókhoz van társítva.

A feladatok lehetnek importálási vagy exportálási feladatok. Az importálási feladat lehetővé teszi az adatok importálását az Azure Blobs vagy az Azure-fájlokba, mivel az exportálási feladat lehetővé teszi az adatok exportálását az Azure Blobsból. Importálási feladat esetén az adatokat tartalmazó meghajtókat szállítja. Amikor létrehoz egy exportálási feladatot, üres meghajtókat szállít egy Azure-adatközpontba. Minden esetben feladatonként legfeljebb 10 lemezmeghajtót szállíthat.

### <a name="inside-an-import-job"></a>Importálási feladaton belül

Magas szinten az importálási feladat a következő lépéseket foglalja magában:

1. Határozza meg az importálandó adatokat, a szükséges meghajtók számát, az Azure storage-beli adatok célblob-helyét.
2. A WAImportExport eszközzel adatokat másolhat lemezmeghajtókra. Titkosítsa a lemezmeghajtókat a BitLocker segítségével.
3. Hozzon létre egy importálási feladatot a céltárfiókban az Azure Portalon. Töltse fel a meghajtó naplófájljait.
4. Adja meg a feladó címét és a szállítói számlaszámot a meghajtók visszaküldéséhez.
5. A lemezmeghajtókat a munkahelyteremtés során megadott szállítási címre szállítsa.
6. Frissítse a szállítási követési számot az importálási feladat részleteiben, és küldje el az importálási feladatot.
7. A meghajtók fogadása és feldolgozása az Azure adatközpontban.
8. A meghajtókat a mobilszolgáltatói fiókhasználatával szállítjuk az importálási feladatban megadott visszaküldési címre.

> [!NOTE]
> Helyi (adatközponti ország/régió) küldemények esetén kérjük, ossza meg a belföldi szállítmányozói fiókját.
>
> Külföldi (adatközpont-országon/-régión kívüli) küldemények esetén kérjük, ossza meg nemzetközi szállítmányozói fiókját.

 ![1. ábra:Feladatfolyamat importálása](./media/storage-import-export-service/importjob.png)

Az adatimportálással kapcsolatos részletes útmutatásért látogasson el a következő útmutatóra:

* [Adatok importálása Az Azure Blobs](storage-import-export-data-to-blobs.md)
* [Adatok importálása az Azure-fájlokba](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Exportálási feladaton belül

> [!IMPORTANT]
> A szolgáltatás csak az Azure Blobok exportálását támogatja. Az Azure-fájlok exportálása nem támogatott.

Magas szinten az exportálási feladat a következő lépéseket foglalja magában:

1. Határozza meg az exportálandó adatokat, a szükséges meghajtók számát, a forrásblobokat vagy az adatok tárolóelérési útjait a Blob storage-ban.
2. Hozzon létre egy exportálási feladatot a forrástár-fiókban az Azure Portalon.
3. Adja meg az exportálandó adatok forrásblobjait vagy tárolóelérési útjait.
4. Adja meg a feladó címét és a szállítói számlaszámot a meghajtók visszaküldéséhez.
5. A lemezmeghajtókat a munkahelyteremtés során megadott szállítási címre szállítsa.
6. Frissítse a szállítási követési számot az exportálási feladat részleteiben, és küldje el az exportálási feladatot.
7. A meghajtók fogadása és feldolgozása az Azure adatközpontban.
8. A meghajtók titkosítva vannak a BitLocker-szel, és a kulcsok az Azure Portalon keresztül érhetők el.  
9. A meghajtókat a mobilszolgáltatói fiókhasználatával szállítjuk az importálási feladatban megadott visszaküldési címre.

> [!NOTE]
> Helyi (adatközponti ország/régió) küldemények esetén kérjük, ossza meg a belföldi szállítmányozói fiókját.
>
> Külföldi (adatközpont-országon/-régión kívüli) küldemények esetén kérjük, ossza meg nemzetközi szállítmányozói fiókját.
  
 ![2. ábra:Feladatfolyamat exportálása](./media/storage-import-export-service/exportjob.png)

Az adatok exportálásával kapcsolatos részletes útmutatásért nyissa meg [az Adatok exportálása az Azure Blobs ból (Export Data from Azure Blobs) (Adatok exportálása az Azure Blobsból) (Adatok exportálása az Azure Blobsból) (Adatok exportálása az Azure Blobsból) (Adatok exportálása](storage-import-export-data-from-blobs.md)az

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure importálási/exportálási szolgáltatás a minden Azure storage-fiókba és az összes Azure storage-fiókból történő másolást támogatja. A lemezmeghajtókat a felsorolt helyek egyikére is szállíthatja. Ha a tárfiók egy Azure-helyen, amely nincs megadva itt, egy másik szállítási hely van megadva, amikor létrehozza a feladatot.

### <a name="supported-shipping-locations"></a>Támogatott szállítási helyek

|Ország/régió  |Ország/régió  |Ország/régió  |Ország/régió  |
|---------|---------|---------|---------|
|USA keleti régiója    | Észak-Európa        | Közép-India        |US Gov Iowa         |
|USA nyugati régiója     |Nyugat-Európa         | Dél-India        | US DoD – Kelet        |
|USA 2. keleti régiója    | Kelet-Ázsia        |  Nyugat-India        | US DoD – Középső régió        |
|USA nyugati régiója, 2.     | Délkelet-Ázsia        | Közép-Kanada        | Kelet-Kína         |
|USA középső régiója     | Kelet-Ausztrália        | Kelet-Kanada        | Észak-Kína        |
|USA északi középső régiója     |  Délkelet-Ausztrália       | Dél-Brazília        | Az Egyesült Királyság déli régiója        |
|USA déli középső régiója     | Nyugat-Japán        |Dél-Korea középső régiója         | Közép-Németország        |
|USA nyugati középső régiója     |  Kelet-Japán       | USA-beli államigazgatás – Virginia        | Északkelet-Németország        |

## <a name="security-considerations"></a>Biztonsági szempontok

A meghajtón lévő adatok titkosítása AES 128 bites BitLocker meghajtótitkosítással történik. Ez a titkosítás védi az adatokat, amíg az átvitel alatt.

Az importálási feladatok esetében a meghajtók kétféleképpen vannak titkosítva.  

* Adja meg a beállítást a *dataset.csv* fájl használatakor a WAImportExport eszköz futtatása közben a meghajtó előkészítése során.

* Engedélyezze a BitLocker titkosítást manuálisan a meghajtón. A WAImportExport eszköz parancssorának futtatásakor adja meg a titkosítási kulcsot a *meghajtó.csv* meghajtóban. A BitLocker titkosítási kulcs további védelmet biztosít egy külső kulcsvédő (más néven a Microsoft által kezelt kulcs) vagy egy ügyfél által kezelt kulcs használatával. További információt a [BitLocker-kulcs védelme érdekében](storage-import-export-encryption-key-portal.md)az ügyfél által kezelt kulcs használata című témakörben talál.

Az exportálási feladatok esetében az adatok meghajtókra másolása után a szolgáltatás titkosítja a meghajtót a BitLocker használatával, mielőtt visszaküldené. A titkosítási kulcs az Azure Portalon keresztül érhető el. A meghajtót fel kell oldani a WAImporExport eszközzel a kulcs használatával.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Díjszabás

**A meghajtó kezelési díja**

Az importálási vagy exportálási feladat részeként feldolgozott minden egyes meghajtóhoz meghajtókezelési díjat kell fizetni. Tekintse meg az [Azure import/export díjszabásának](https://azure.microsoft.com/pricing/details/storage-import-export/)részleteit.

**Szállítási költségek**

Amikor meghajtókat szállít az Azure-ba, a szállítási költséget a szállítmányozónak kell kifizetnie. Amikor a Microsoft visszaküldi Önnek a meghajtókat, a szállítási költséget a létrehozáskor megadott szolgáltatói fiókra terhelik.

**Tranzakciós költségek**

[A szokásos tárolási tranzakciós díj](https://azure.microsoft.com/pricing/details/storage/) az importálás és az adatok exportálása során is felszámításra vonatkozik. Az azure storage-ból történő exportáláskor a normál kimenő forgalom díjai is alkalmazhatók a tárolási tranzakciós díjakkal együtt. A kimenő forgalom költségeiről az [Adatátviteli díjak című](https://azure.microsoft.com/pricing/details/data-transfers/)témakörben talál.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan használhatja az Importálás/exportálás szolgáltatást a következőkre:

* [Adatok importálása Az Azure Blobs](storage-import-export-data-to-blobs.md)
* [Adatok exportálása az Azure Blobs ból](storage-import-export-data-from-blobs.md)
* [Adatok importálása Azure-fájlokba](storage-import-export-data-to-files.md)
