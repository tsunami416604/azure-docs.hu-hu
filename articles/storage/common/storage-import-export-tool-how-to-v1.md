---
title: Az Azure importálási/exportálási eszközének használata – v1 | Microsoft dokumentumok
description: Megtudhatja, hogy az Importálás/exportálás eszközzel hogyan készíthet idáig merevlemezeket egy importálási feladatra, hogyan javíthatja ki az importálási feladatot, illetve hogyan javíthatja ki az exportálási feladatot.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: aaceee65ae91a22dc658d185a874e2040b1e0f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75975670"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Az Azure importálási/exportálási eszköz használata (klasszikus telepítési modell)

Az Azure Import/Export Tool (WAImportExport.exe) segítségével az Azure import/export szolgáltatás hoz létre és kezel, így nagy mennyiségű adatot vihet át az Azure Blob Storage-ba vagy azAzure Blob Storage-ból.

Ez a dokumentáció az Azure importálási/exportálási eszköz klasszikus üzembe helyezési modelljéhez. Az eszköz legújabb verziójának használatáról az [Azure importálási/exportálási eszköz használata című](../storage-import-export-tool-how-to.md)témakörben talál további információt.

A következő cikkek bemutatják, hogyan kell:

- Telepítse és állítsa be az Importálás/exportálás eszközt.
- Készítse elő a merevlemezeket egy olyan feladatra, ahol adatokat importál a meghajtókról az Azure Blob Storage-ba.
- Tekintse át egy feladat állapotát a Naplófájlok másolásával.
- Importfeladat javítása.
- Exportálási feladat javítása.
- Az Azure importálási/exportálási eszköz hibaelhárítása, ha a folyamat során probléma merült fel.

## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](../storage-import-export-tool-how-to.md)
