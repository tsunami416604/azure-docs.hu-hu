---
title: Az Azure Import/Export eszköz használata – v1 |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Import/Export eszköz merevlemezek előkészítése importálási feladatokhoz, importálási feladat javítása vagy exportálási feladat javítása.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 90b5697da0f134ccb3c9ddb66b5da138c8849189
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523410"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Az Azure Import/Export eszközzel (klasszikus üzembe helyezési modell)

Az Azure Import/Export eszköz (WAImportExport.exe) segítségével az Azure Import/Export szolgáltatás feladatok létrehozása és kezelése, hogy nagy mennyiségű adat átvitele, vagy onnan máshová az Azure Blob Storage lehetővé teszi.

Ez a dokumentáció a klasszikus üzemi modell az Azure Import/Export eszköz van. További információ az eszköz legújabb verzióját használja: [az Azure Import/Export eszközzel](../storage-import-export-tool-how-to.md).

A következő cikkek bemutatják, hogyan való:

- Telepítse és állítsa be az Import/Export eszköz.
- Készítse elő a merevlemez-meghajtók egy feladatot, amikor adatokat importál a meghajtók az Azure Blob Storage.
- A feladat állapotának áttekintése a másolási naplófájlok segítségével. 
- Importálási feladat javítása. 
- Exportálási feladat javítása. 
- Az Azure Import/Export eszköz hibaelhárítása, abban az esetben folyamata során probléma volt. 

## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz telepítése](../storage-import-export-tool-how-to.md)