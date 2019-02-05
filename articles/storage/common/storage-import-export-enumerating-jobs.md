---
title: Az Azure Import/Export-feladatok összes |} MicrosoftDocs
description: Ismerje meg, hogyan összes, az Azure Import/Export szolgáltatás feladatok az előfizetéshez.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726667"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás feladatok számbavétele
Egy adott előfizetés összes feladatok számbavétele, hívja meg a [listázhatók a feladatok](/rest/api/storageimportexport/jobs) műveletet. `List Jobs` a feladatok, valamint a következő attribútumok listáját adja vissza:

-   Milyen típusú feladatot (importálás vagy exportálás)

-   A jelenlegi feladatállapotban

-   A feladat a társított storage-fiók

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
