---
title: Az Azure importálási és exportálási szolgáltatáshoz importálási feladat létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a Microsoft Azure Import/Export szolgáltatás importálását.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 3c6adc3f27e49facec124401f03a036269b7c7a9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524447"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás importálási feladat létrehozása

A Microsoft Azure Import/Export szolgáltatás REST API használatával importálási feladat létrehozása az alábbi lépésekből áll:

-   Felkészülés az Azure Import/Export eszközzel rendelkező meghajtókat.

-   A hely, amelyhez a meghajtó szállításra való beszerzéséről.

-   Az importálási feladat létrehozása.

-   A szállítási a Microsoftnak a meghajtók támogatott szolgáltató szolgáltatáson keresztül.

-   Az importálási feladat frissítése a a szállítás részleteiről.

 Lásd: [adatok átvitele a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](storage-import-export-service.md) áttekintését, az Import/Export szolgáltatás és a egy oktatóanyag, amely azt ismerteti, hogyan használható a [az Azure portal](https://portal.azure.com/) hozhat létre és importálási kezelése és a feladatok exportálása.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Az Azure Import/Export eszközzel együtt meghajtók előkészítése

Meghajtók előkészítése importálási feladatokhoz a lépések ugyanazok, hogy hoz létre a jobvia a portálon, vagy a REST API-n keresztül.

Az alábbi, a meghajtó előkészítése rövid áttekintése. Tekintse meg a [Azure Import-ExportTool referencia](storage-import-export-tool-how-to-v1.md) részletes utasításokért. Az Azure Import/Export eszközt letöltheti [Itt](http://go.microsoft.com/fwlink/?LinkID=301900).

A meghajtó előkészítése foglalja magában:

-   Az importálandó adatok azonosítása.

-   A cél a Windows Azure Storage blobok azonosítása.

-   Az Azure Import/Export eszköz használata az adatok másolása egy vagy több merevlemez-meghajtókat.

 Az Azure Import/Export eszköz is generál a jegyzékfájlt a meghajtókhoz, az előkészítés során. A jegyzékfájlt tartalmaz:

-   Egy enumerálás feltöltési és a blobokhoz az érintett fájlok a leképezések az összes fájlt.

-   Az egyes fájlok szegmenseinek ellenőrzőösszegek.

-   A metaadat- és tulajdonságfájljainak minden egyes blob társítandó vonatkozó adatokat.

-   Ha egy blob feltöltése folyamatban van, a neve megegyezik egy meglévő blobot a tárolóban elvégzendő listája. A lehetséges értékek: a) a blob felülírja a fájlt a, b) tartani a meglévő blobra, majd feltölti a skip, c) hozzáfűzése egy utótagot a nevét, hogy más fájlok nem ütköznek.

## <a name="obtaining-your-shipping-location"></a>A szállítási címhez tartozó hely beszerzése

Importálási feladat létrehozása előtt be kell szereznie egy szállítási hely nevét és címét meghívásával a [lista helyek](/rest/api/storageimportexport/listlocations) műveletet. `List Locations` helyek és a levelezési címét listáját adja vissza. Válasszon egy helyet a visszaadott listában, és arra a címre rögzített meghajtók kiszállítása. Is használhatja a `Get Location` művelet egy konkrét hely a szállítási cím közvetlenül beszerzése.

 A szállítási címhez tartozó hely beszerzése az alábbi lépésekkel:

-   Azonosítsa a helyet a tárfiók nevére. Ez az érték alatt található a **hely** a storage-fiókban található **irányítópult** az Azure portal vagy a service management API művelet használatával lekérdezett [Storage-fiók beszerzése Tulajdonságok](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   A hely, amelyhez ez a tárfiók feldolgozni meghívásával lekérése a `Get Location` műveletet.

-   Ha a `AlternateLocations` helye tulajdonsága tartalmaz magát a helyet, akkor már használja ezt a helyet. Ellenkező esetben hívja a `Get Location` a másodlagos helyek újra a műveletet. Az eredeti helyre ideiglenesen megtakarítása karbantartás céljából.

## <a name="creating-the-import-job"></a>Az importálási feladat létrehozása
Az importálási feladat létrehozásához hívja a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. Adja meg a következő információkat kell:

-   A feladat nevét.

-   A tárfiók neve.

-   A szállítási hely neve, az előző lépésben beszerzett.

-   Feladat típusa (importálás).

-   A válaszcím, ahol a meghajtók kell küldeni az importálási feladat befejezése után.

-   A feladat meghajtók listája. Minden meghajtó meghajtó előkészítési lépés során kapott a következőket kell tartalmaznia:

    -   A meghajtó azonosítója

    -   A BitLocker-kulcs

    -   Az Alkalmazásjegyzék-fájl relatív elérési út a merevlemezen

    -   A Base16 kódolású Alkalmazásjegyzék-fájl MD5-kivonat

## <a name="shipping-your-drives"></a>A szállítási meghajtó
Meg kell meghajtók kiszállítása a a címet az előző lépésben beszerzett, és az Import/Export szolgáltatás meg kell adnia a csomag a nyomkövetési azonosító szám.

> [!NOTE]
>  Meg kell egy támogatott szolgáltató szolgáltatással, amely a csomag biztosít egy nyomkövetési azonosító szám meghajtók kiszállítása.

## <a name="updating-the-import-job-with-your-shipping-information"></a>A szállítási adatait az importálási feladat frissítése
Miután a nyomkövetési azonosító szám, hívja a [feladat tulajdonságainak frissítése](/api/storageimportexport/jobs#Jobs_Update) szállítási szállító nevét, a feladat a nyomkövetési azonosító szám és a Szállítmányozó számlaszáma a visszaszállítási frissítésére szolgáló művelet. Opcionálisan megadhatja a meghajtókat és a szállítási címhez tartozó dátumot is a számot.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
