---
title: "Az Azure Import/Export eszközzel - 1-es verzió |} Microsoft Docs"
description: "Útmutató: az Import/Export eszköz segítségével készítse elő a merevlemez-meghajtók egy importálási feladatnak, javítsa az importálási feladat, vagy javítsa ki az exportálási feladat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/15/2017
ms.author: muralikk
ms.openlocfilehash: 4ce2273cc0dcc456c2edc8c5dd2fc22496f20380
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Az Azure Import/Export eszközzel (klasszikus üzembe helyezési modellel)

Az Azure Import/Export eszköz (WAImportExport.exe) segítségével az Azure Import/Export szolgáltatás feladatok létrehozásához és kezeléséhez így lehetővé teszi nagy adatmennyiségek átvitelét a virtuális gépbe vagy onnan Azure Blob Storage.

Ez a dokumentáció a klasszikus üzembe helyezési modellel, az Azure Import/Export eszköz szolgál. Az eszköz legújabb verziójának használatával kapcsolatos információkért lásd: [az Azure Import/Export eszközzel](../storage-import-export-tool-how-to.md).

A következő cikkek bemutatják, hogyan számára:

- Telepítse és állítsa be az Import/Export eszköz.
- Készítse elő a merevlemez-meghajtók egy feladatot, amikor adatokat importál a meghajtók Azure Blob Storage.
- Tekintse át a feladat állapotának másolási naplófájlok. 
- Javítsa az importálási feladat. 
- Javítsa ki az exportálási feladat. 
- Végezzen hibaelhárítást az Azure Import/Export eszköz, abban az esetben, ha a probléma volt a folyamat során. 

## <a name="next-steps"></a>Következő lépések

* [A WAImportExport eszköz beállítása](../storage-import-export-tool-how-to.md)