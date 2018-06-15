---
title: Listázza az összes, az Azure importálási/exportálási feladatok |} MicrosoftDocs
description: Megtudhatja, hogyan listázza az összes előfizetés az Azure Import/Export szolgáltatás feladatok.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873701"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás feladatok számbavétele
Felsorolni az előfizetés levő összes feladatnak, hívja meg a [lista feladatok](/rest/api/storageimportexport/jobs#Jobs_List) műveletet. `List Jobs`feladatok, valamint a következő attribútumok listáját adja vissza:

-   A feladat (importálása vagy exportálása) típusa

-   A feladat jelenlegi állapota

-   A feladat társított storage-fiók

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
