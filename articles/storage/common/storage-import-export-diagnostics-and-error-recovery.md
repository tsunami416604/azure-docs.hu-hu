---
title: "Az Azure importálási/exportálási feladatok diagnosztika és a hiba helyreállítási |} Microsoft Docs"
description: "Megtudhatja, hogyan engedélyezi a részletes naplózás a Microsoft Azure Import/Export szolgáltatás feladatok."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnosztika és a hiba helyreállítási az Azure importálási/exportálási feladatok
Minden meghajtó feldolgozott az Azure Import/Export szolgáltatás hibanaplót a kapcsolódó tárfiók hoz létre. Részletes naplózás engedélyezése úgy, hogy a `LogLevel` tulajdonságot `Verbose` meghívásakor a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) vagy [frissítés Feladattulajdonság](/rest/api/storageimportexport/jobs#Jobs_Update) műveletek.

 Alapértelmezés szerint írja a naplókat nevű tárolót `waimportexport`. Megadhatja egy másik nevet úgy, hogy a `DiagnosticsPath` tulajdonság meghívásakor a `Put Job` vagy `Update Job Properties` műveletek. A naplók a következő elnevezés szabály szerint blokkblobként tárolja: `waies/jobname_driveid_timestamp_logtype.xml`.

 Az URI-feladat a naplók meghívásával kérheti le a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet. A részletes naplózás URI-JÁNAK eredmény abban az esetben a `VerboseLogUri` tulajdonság minden meghajtó, amíg a hibanapló URI-JÁNAK eredmény abban az esetben a `ErrorLogUri` tulajdonság.

A naplózási adatok segítségével a következő problémák azonosításához.

## <a name="drive-errors"></a>Meghajtó hibák

A következő elemek besorolt meghajtó hibák:

-   Való hozzáférés, vagy a jegyzékfájl olvasása

-   Helytelen a BitLocker-kulcsok

-   A meghajtó olvasási/írási hibák

## <a name="blob-errors"></a>A BLOB hibák

A következő elemek besorolt blob hibák:

-   Hibás vagy ütköző blob vagy -nevek

-   Hiányzó fájlok

-   A BLOB nem található

-   Csonkolt fájlok (a fájlok a lemezen kisebb, mint a jegyzékben megadott)

-   Sérült fájlt tartalom (importálási feladatok észlelt egy MD5 ellenőrzőösszeg eltérése)

-   (Az MD5 ellenőrzőösszeg eltérése az észlelt) sérült blob metaadatait és tulajdonság fájlok

-   A blob tulajdonságai és/vagy metaadatfájlokban hibás sémát

Előfordulhat, ha egyes részei egy importálási vagy exportálási feladat nem fejeződött be sikeresen, a teljes feladat még elvégzése közben. Ebben az esetben feltölteni, vagy töltse le a hiányzó rész az adatok hálózaton keresztül, vagy létrehozhat egy új feladatot, amely az adatok átvitelét. Tekintse meg a [Azure Import/Export eszköz hivatkozás](storage-import-export-tool-how-to-v1.md) hogyan javítsa ki az adatok hálózaton keresztül.

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
