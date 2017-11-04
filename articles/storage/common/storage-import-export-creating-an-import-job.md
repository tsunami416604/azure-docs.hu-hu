---
title: "Az Azure Import/Export importálási feladat létrehozása |} Microsoft Docs"
description: "Útmutató a Microsoft Azure Import/Export szolgáltatás importálás létrehozásához."
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: 
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: d373d2a0e601f2796719fc5efb8761f276ab24d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Az importálási feladat az Azure Import/Export szolgáltatás létrehozása

A Microsoft Azure Import/Export szolgáltatás REST API használatával az importálási feladat létrehozása a következő lépésekből áll:

-   Felkészülés az Azure Import/Export eszköz rendelkező meghajtókat.

-   A helyet, ahová a meghajtó szállítási megszerezni.

-   Az importálási feladat létrehozása.

-   A szállítási a Microsoftnak a meghajtók támogatott szolgáltatónként szolgáltatáson keresztül.

-   Az importálási feladat frissítése a szállítási adatokkal.

 Lásd: [adatok átviteléhez a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](storage-import-export-service.md) áttekintését a Import/Export szolgáltatás és egy az oktatóanyag bemutatja, hogyan használható a [Azure-portálon](https://portal.azure.com/) létrehozása Importálás kezeléséhez, és exportálni a feladatokat.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Az Azure Import/Export eszközzel meghajtó előkészítése

A meghajtók előkészítése az importálási feladat lépésekre azonos e hoz létre a jobvia a portálon, vagy a REST API-n keresztül.

Az alábbiakban van meghajtó előkészítése rövid áttekintést. Tekintse meg a [Azure Import-ExportTool hivatkozás](storage-import-export-tool-how-to-v1.md) részletes utasításokért. Az Azure Import/Export eszközt letöltheti [Itt](http://go.microsoft.com/fwlink/?LinkID=301900).

A meghajtó előkészítése foglal magában:

-   Az adatok, importálandók azonosítása.

-   A célként megadott blobot, amely a Windows Azure Storage azonosítása.

-   Az Azure Import/Export eszköz használatával másolja az adatokat legalább egy merevlemez-meghajtókat.

 Az Azure Import/Export eszköz is létrehoz a jegyzékfájlt a meghajtókhoz előkészítettként van. A jegyzékfájl tartalmazza:

-   Egy feltöltési és a leképezései ezeket a fájlokat a blobok szánt fájlok felsorolása.

-   Az egyes fájlok szegmenst ellenőrzőösszegeket.

-   A metaadatok és a tulajdonságok minden egyes blob társítandó vonatkozó információk.

-   A művelet neve megegyezik egy már meglévő blob a tárolóban lévő blob, amely feltöltődik-e listáját. A lehetséges értékek: a) a blob felülírja a fájlt, a b) megtartja a meglévő blob és a skip feltölteni a fájlt, a c) hozzáfűzése egy utótagot a nevét, hogy más fájlok nem ütköznek.

## <a name="obtaining-your-shipping-location"></a>A szállítási hely beszerzése

Mielőtt létrehozna egy importálási feladat, be kell szereznie a szállítási hely nevét és címét meghívásával a [lista helyek](/rest/api/storageimportexport/listlocations) műveletet. `List Locations`helyek és a levelezési címek listáját adja vissza. Jelöljön ki egy helyet a visszaadott listából, és küldje el a merevlemez-meghajtók adott címre. Használhatja a `Get Location` közvetlenül beszerzése a szállítási cím, egy adott helyre vonatkozó műveletet.

 A szállítási raktár beszerzése az alábbi lépésekkel:

-   Azonosítsa a tárfiók helyének nevét. Ez az érték található a **hely** található a tárfiók **irányítópult** az Azure portál vagy a service management API művelettel lekérdezett [Storage-fiók beszerzése Tulajdonságok](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   A hely, amelyhez feldolgozni ezt a tárfiókot meghívásával beolvasása a `Get Location` műveletet.

-   Ha a `AlternateLocations` helyének tulajdonság tartalmaz maga a hely, akkor használja ezt a helyet kapcsolatát. Ellenkező esetben hívható meg a `Get Location` műveletet a másodlagos helyek. Az eredeti helyre ideiglenesen le lehet, hogy a következő karbantartási.

## <a name="creating-the-import-job"></a>Az importálási feladat létrehozása
Az importálási feladat létrehozása, hívja meg a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. Meg kell adnia a következő információkat:

-   A feladat nevét.

-   A tárfiók nevét.

-   A szállítási hely neve, az előző lépésben beszerzett.

-   A feladat típusa (importálás).

-   A válaszcím, ahol a meghajtók kell-e küldeni az importálási feladat befejeződése után.

-   A feladat meghajtók listája. Minden olyan meghajtó meg kell adni a következő adatokat, a meghajtó előkészítési lépés során kapott:

    -   A meghajtó azonosítója

    -   A BitLocker-kulcsot

    -   Az Alkalmazásjegyzék-fájl relatív elérési út a merevlemez-meghajtón

    -   A Base16 kódolású jegyzékfájl MD5 kivonatoló

## <a name="shipping-your-drives"></a>A meghajtók szállítási
Kell küldje el az előző lépésben beszerzett címre meghajtó, és a csomag követési számának meg kell adnia az Import/Export szolgáltatás.

> [!NOTE]
>  A meghajtók, amely előállít egy azonosítószám a csomag támogatott szolgáltatónként szolgáltatáson keresztül kell küldje el.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Az importálási feladat naplóküldése adatainak frissítése
Miután a nyilvántartási szám, hívja az [frissítése feladat tulajdonságai](/api/storageimportexport/jobs#Jobs_Update) a szállítási szolgáltatónként nevét, a projekt a nyomon követési száma és a vivőjel-számát visszatérési szállítási frissítési művelete. Opcionálisan megadhat meghajtók és a szállítási dátumot is.

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
