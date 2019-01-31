---
title: Az Azure Import/Export eszköz használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Import/Export eszköz merevlemezek előkészítése importálási feladatokhoz, importálási feladat javítása vagy exportálási feladat javítása.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 5eaf29623a18f7347ad287e4b8389667f4b4e272
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467560"
---
# <a name="using-the-azure-importexport-tool"></a>Az Azure Import/Export eszköz használata 

Az Azure Import/Export eszköz (WAImportExport.exe) segítségével az Azure Import/Export szolgáltatás feladatok létrehozása és kezelése, hogy nagy mennyiségű adat átvitele, vagy onnan máshová az Azure Blob Storage lehetővé teszi.

Ez a dokumentáció az Azure Import/Export eszköz legújabb verziójának van. A klasszikus üzemi modell használatával kapcsolatos információkért lásd: [használata az Azure Import/Export eszköz v1](storage-import-export-tool-how-to-v1.md).

A következő cikkek bemutatják, hogyan való:  

- Telepítse és állítsa be az Azure Import/Export eszköz.
- Készítse elő a merevlemez-meghajtók egy feladatot, amikor adatokat importál a meghajtók az Azure Blob Storage.
- A feladat állapotának áttekintése a másolási naplófájlok segítségével. 
- Importálási feladat javítása. 
- Exportálási feladat javítása. 
- Az Azure Import/Export eszköz hibaelhárítása. 

## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz telepítése](storage-import-export-tool-setup.md)
