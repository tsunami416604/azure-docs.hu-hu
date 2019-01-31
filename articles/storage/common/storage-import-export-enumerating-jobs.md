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
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462920"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás feladatok számbavétele
Egy adott előfizetés összes feladatok számbavétele, hívja meg a [listázhatók a feladatok](/rest/api/storageimportexport/jobs#Jobs_List) műveletet. `List Jobs` a feladatok, valamint a következő attribútumok listáját adja vissza:

-   Milyen típusú feladatot (importálás vagy exportálás)

-   A jelenlegi feladatállapotban

-   A feladat a társított storage-fiók

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
