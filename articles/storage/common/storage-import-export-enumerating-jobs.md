---
title: Az Azure Import/Export-feladatok összes |} MicrosoftDocs
description: Ismerje meg, hogyan összes, az Azure Import/Export szolgáltatás feladatok az előfizetéshez.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520880"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás feladatok számbavétele
Egy adott előfizetés összes feladatok számbavétele, hívja meg a [listázhatók a feladatok](/rest/api/storageimportexport/jobs#Jobs_List) műveletet. `List Jobs` a feladatok, valamint a következő attribútumok listáját adja vissza:

-   Milyen típusú feladatot (importálás vagy exportálás)

-   A jelenlegi feladatállapotban

-   A feladat a társított storage-fiók

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
