---
title: Hozzon létre egy feladatot az Azure Import/Export export |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a Microsoft Azure Import/Export szolgáltatás exportálási feladat.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 935af10c2ebcdc5273671ed058fdf72099059da3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475618"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás exportálási feladat létrehozása
A Microsoft Azure Import/Export szolgáltatás REST API használatával exportálási feladat létrehozása az alábbi lépésekből áll:

-   Az exportálandó blobok kiválasztása.

-   A szállítási címhez tartozó hely beszerzése.

-   Az exportálási feladat létrehozása.

-   A szállítási támogatott szolgáltató szolgáltatáson keresztül a Microsoft az üres meghajtókon.

-   Az exportálási feladat frissítése a csomaginformációkat használva.

-   A meghajtók kap vissza a Microsofttól.

 Lásd: [a Windows Azure Import/Export szolgáltatás az adatok átvitele a Blob Storage használatával](storage-import-export-service.md) áttekintheti az Import/Export szolgáltatás és a egy oktatóanyag, amely azt ismerteti, hogyan használhatja az a [az Azure portal](https://portal.azure.com/) hozhat létre és Importálás kezelése és a feladatok exportálása.

## <a name="selecting-blobs-to-export"></a>Exportálandó blobok kiválasztása
 Exportálási feladat létrehozása, szüksége lesz, amely az Ön tárfiókjából exportálandó blobok listáját adja meg. Válassza ki az exportálandó blobok néhány módja van:

-   Egy blob relatív elérési út segítségével válassza ki egy blob és az összes hozzá tartozó pillanatképek.

-   Egy blob relatív elérési út segítségével válassza ki a pillanatképek nélkül egyetlen blobhoz.

-   A relatív blob elérési útja és a egy pillanatkép idő segítségével válassza ki egy egységes pillanatképet.

-   Egy blob előtag segítségével válassza ki a blobok és a pillanatképek a megadott előtaggal.

-   Exportálhatja a blobok és a pillanatképek a storage-fiókban.

 Exportálandó blobok megadásával kapcsolatos további információkért lásd: a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet.

## <a name="obtaining-your-shipping-location"></a>A szállítási címhez tartozó hely beszerzése
Exportálási feladat létrehozása előtt be kell szereznie egy szállítási hely nevét és címét meghívásával a [beolvasása hely](https://portal.azure.com) vagy [lista helyek](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) műveletet. `List Locations` helyek és a levelezési címét listáját adja vissza. Válasszon egy helyet a visszaadott listában, és arra a címre rögzített meghajtók kiszállítása. Is használhatja a `Get Location` művelet egy konkrét hely a szállítási cím közvetlenül beszerzése.

A szállítási címhez tartozó hely beszerzése az alábbi lépésekkel:

-   Azonosítsa a helyet a tárfiók nevére. Ez az érték alatt található a **hely** a storage-fiókban található **irányítópult** az Azure portal vagy a service management API művelet használatával lekérdezett [Storage-fiók beszerzése Tulajdonságok](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Elérhető feldolgozni ezt a tárfiókot meghívásával helyének beolvasásához a `Get Location` műveletet.

-   Ha a `AlternateLocations` helye tulajdonsága tartalmaz magát a helyet, akkor már használja ezt a helyet. Ellenkező esetben hívja a `Get Location` a másodlagos helyek újra a műveletet. Az eredeti helyre ideiglenesen megtakarítása karbantartás céljából.

## <a name="creating-the-export-job"></a>Az exportálási feladat létrehozása
 Az exportálási feladat létrehozásához hívja a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. Adja meg a következő információkat kell:

-   A feladat nevét.

-   A tárfiók neve.

-   A szállítási hely neve, az előző lépésben beszerzett.

-   Feladat típusa (Exportálás).

-   A válaszcím, ahol a meghajtók kell küldeni az exportálási feladat befejezése után.

-   Exportálandó blobok (vagy blob előtagok) listája.

## <a name="shipping-your-drives"></a>A szállítási meghajtó
 Ezután használhatja az Azure Import/Export eszköz meghatározásához, hány meghajtót szeretne küldeni, a blobok az exportálandó kiválasztott és a meghajtó mérete alapján. Tekintse meg a [Azure Import/Export eszköz referencia](storage-import-export-tool-how-to-v1.md) részleteiről.

 Csomagot a meghajtók, egyetlen csomagban, és küldje őket a korábbi lépésben kapott címre. Vegye figyelembe a nyomkövetési azonosító szám a csomag a következő lépéshez.

> [!NOTE]
>  Meg kell egy támogatott szolgáltató szolgáltatással, amely a csomag biztosít egy nyomkövetési azonosító szám meghajtók kiszállítása.

## <a name="updating-the-export-job-with-your-package-information"></a>Az exportálási feladat frissítése a csomaginformációkat használva
 Miután a nyomkövetési azonosító szám, hívja a [feladat tulajdonságainak](/rest/api/storageimportexport/jobs#Jobs_Update) művelet frissíti a szállító nevét és követési szám a feladathoz. Opcionálisan megadhatja a meghajtók, a feladó címe és a szállítási címhez tartozó dátumot is számát.

## <a name="receiving-the-package"></a>A csomag fogadása
 Az exportálási feladat feldolgozása után a meghajtók visszatér, a titkosított adatok. Kérheti le a BitLocker-kulcsot a meghajtókhoz meghívásával a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet. Ezután fel is oldhatja a meghajtó a kulccsal. A meghajtó jegyzékfájl egyes meghajtókon tartalmazza azokat a fájlokat a meghajtó, valamint az eredeti blob-címet az összes fájl esetében.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
