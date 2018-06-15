---
title: Az Azure Import/Export eszközzel |} Microsoft Docs
description: 'Útmutató: az Import/Export eszköz segítségével készítse elő a merevlemez-meghajtók egy importálási feladatnak, javítsa az importálási feladat, vagy javítsa ki az exportálási feladat.'
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 20a720833842f9579fd4fccaa39e964def48197e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873673"
---
# <a name="using-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz használatával 

Az Azure Import/Export eszköz (WAImportExport.exe) segítségével az Azure Import/Export szolgáltatás feladatok létrehozásához és kezeléséhez így lehetővé teszi nagy adatmennyiségek átvitelét a virtuális gépbe vagy onnan Azure Blob Storage.

Ez a dokumentáció az Azure Import/Export eszköz legújabb verziója van. A klasszikus üzembe helyezési modellel kapcsolatos további információkért lásd: [használata az Azure Import/Export eszköz v1](storage-import-export-tool-how-to-v1.md).

A következő cikkek bemutatják, hogyan számára:  

- Telepítse és állítsa be az Azure Import/Export eszköz.
- Készítse elő a merevlemez-meghajtók egy feladatot, amikor adatokat importál a meghajtók Azure Blob Storage.
- Tekintse át a feladat állapotának másolási naplófájlok. 
- Javítsa az importálási feladat. 
- Javítsa ki az exportálási feladat. 
- Az Azure Import/Export eszköz hibaelhárításához. 

## <a name="next-steps"></a>Következő lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-setup.md)