---
title: Hozzon létre egy feladatot az Azure Import/Export exportálási |} Microsoft Docs
description: Útmutató a Microsoft Azure Import/Export szolgáltatás exportálási feladat létrehozásához.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3fb3f2af5e5cebcac21f4372bc9d9dc9ee837202
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás exportálási feladat létrehozása
A Microsoft Azure Import/Export szolgáltatás REST API használatával exportálási feladat létrehozása a következő lépésekből áll:

-   A bináris objektumok exportálása kiválasztása.

-   Az beszerzése szállítási helyről.

-   Az exportálási feladat létrehozása.

-   A szállítási üres meghajtó Microsoft támogatott szolgáltatónként szolgáltatáson keresztül.

-   Az exportálási feladat frissítése a csomag információval.

-   Vissza a Microsoft a meghajtók küld.

 Lásd: [a Windows Azure Import/Export szolgáltatás adatok átviteléhez a Blob Storage segítségével](storage-import-export-service.md) áttekintését a Import/Export szolgáltatás és egy az oktatóanyag bemutatja, hogyan használható a [Azure-portálon](https://portal.azure.com/) létrehozása és kezelése az importálás és exportálni a feladatokat.

## <a name="selecting-blobs-to-export"></a>Blobok exportálása kiválasztása
 Exportálási feladat létrehozásához szüksége lesz a tárfiók exportálni kívánt BLOB listáját tartalmazzák. Válassza ki az exportálandó blobok néhány módja van:

-   Egy blob relatív elérési út használatával egyetlen blob és a pillanatképek összes kiválasztása.

-   Egy blob relatív elérési út használatával válassza ki a pillanatképek nélkül egyetlen blob.

-   Egy blob relatív elérési utat és egy pillanatkép idő segítségével válassza ki a egyetlen pillanatkép.

-   Egy blob előtagja segítségével válassza ki az összes BLOB és a pillanatfelvételeket a megadott előtaggal.

-   Exportálhatja a blobok és a pillanatképek a tárfiókban.

 Blobok exportálása megadásával kapcsolatos további információkért lásd: a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet.

## <a name="obtaining-your-shipping-location"></a>A szállítási hely beszerzése
Exportálási feladat létrehozása, előtt be kell szereznie a szállítási hely nevét és címét meghívásával a [első hely](https://portal.azure.com) vagy [lista helyek](/rest/api/storageimportexport/listlocations) műveletet. `List Locations` helyek és a levelezési címek listáját adja vissza. Jelöljön ki egy helyet a visszaadott listából, és küldje el a merevlemez-meghajtók adott címre. Használhatja a `Get Location` közvetlenül beszerzése a szállítási cím, egy adott helyre vonatkozó műveletet.

A szállítási raktár beszerzése az alábbi lépésekkel:

-   Azonosítsa a tárfiók helyének nevét. Ez az érték található a **hely** található a tárfiók **irányítópult** az Azure portál vagy a service management API művelettel lekérdezett [Storage-fiók beszerzése Tulajdonságok](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   A hely által biztosított feldolgozni ezt a tárfiókot meghívásával beolvasása a `Get Location` műveletet.

-   Ha a `AlternateLocations` helyének tulajdonság tartalmaz maga a hely, akkor használja ezt a helyet kapcsolatát. Ellenkező esetben hívható meg a `Get Location` műveletet a másodlagos helyek. Az eredeti helyre ideiglenesen le lehet, hogy a következő karbantartási.

## <a name="creating-the-export-job"></a>Az exportálási feladat létrehozása
 Az exportálási feladat létrehozása, hívja meg a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. Meg kell adnia a következő információkat:

-   A feladat nevét.

-   A tárfiók neve.

-   A szállítási hely neve, az előző lépésben beszerzett.

-   A feladat típusa (Exportálás).

-   A válaszcím, ahol a meghajtók kell-e küldeni az exportálási feladat befejeződése után.

-   A blobok (vagy blob előtagok) exportálható listáját.

## <a name="shipping-your-drives"></a>A meghajtók szállítási
 Ezután használhatja az Azure Import/Export eszköz annak meghatározásához, hány meghajtót szeretne küldeni, a blobok választja, exportálva és a meghajtó mérete alapján. Tekintse meg a [Azure Import/Export eszköz hivatkozás](storage-import-export-tool-how-to-v1.md) részleteiről.

 A meghajtók csomagban csomagot, majd küldje el azokat a korábbi lépésben kapott címre. Megjegyzés: a csomag a következő lépéshez követési száma.

> [!NOTE]
>  A meghajtók, amely előállít egy azonosítószám a csomag támogatott szolgáltatónként szolgáltatáson keresztül kell küldje el.

## <a name="updating-the-export-job-with-your-package-information"></a>Az exportálási feladat frissítése a csomag információval
 Miután a nyilvántartási szám, hívja az [frissítés Feladattulajdonság](/rest/api/storageimportexport/jobs#Jobs_Update) művelet frissíti a vivőjel nevét, és nyomon követését a projekt száma. Opcionálisan megadhat a számát, a címet, és a szállítási dátumot is.

## <a name="receiving-the-package"></a>A csomag fogadása
 Az exportálási feladat feldolgozása után a meghajtók visszatér, a titkosított adatok. Kérheti le a BitLocker-kulcsot a meghajtókhoz meghívásával a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet. Majd fel is oldhatja a meghajtót, a kulcs használatával. A meghajtó jegyzékfájl egyes fájlokat a meghajtó, valamint az eredeti blob cím az egyes fájlok listáját tartalmazza.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
