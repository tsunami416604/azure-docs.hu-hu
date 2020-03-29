---
title: Azure Stream Analytics-feladat exportálása Azure Resource Manager-sablon
description: Ez a cikk ismerteti, hogyan exportálhatja az Azure Resource Manager-sablont az Azure Stream Analytics-feladathoz.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968954"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Azure Stream Analytics-feladat exportálása Azure Resource Manager-sablon

[Az Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md) lehetővé teszik az infrastruktúra kódként való megvalósítását. A sablon egy JavaScript-objektumjelölési (JSON) fájl, amely meghatározza az erőforrások infrastruktúráját és konfigurációját. Megadhatja az üzembe helyezandó erőforrásokat és az erőforrások tulajdonságait.

Az Azure Stream Analytics-feladat újratelepítése az Azure Resource Manager sablon exportálásával.

## <a name="open-a-job-in-vs-code"></a>Feladat megnyitása a VS-kódban

A sablonok exportálása előtt meg kell nyitnia egy meglévő Stream Analytics-feladatot a Visual Studio-kódban. 

Ha egy feladatot egy helyi projektbe szeretne exportálni, keresse meg az exportálni kívánt feladatot az Azure Portalon a **Stream Analytics Intézőben.** A **Lekérdezés** lapon válassza a **Megnyitás a Visual Studio programban**lehetőséget. Ezután válassza a **Visual Studio-kód lehetőséget.**

![Stream Analytics-feladat megnyitása a Visual Studio-kódban](./media/resource-manager-export/open-job-vs-code.png)

A Stream Analytics-feladatok Visual Studio-kód dal kezeléséről a [Visual Studio-kód rövid útmutatójában](quick-create-vs-code.md)talál további információt.

## <a name="compile-the-script"></a>A parancsfájl fordítása 

A következő lépés a feladatparancsfájl fordítása egy Azure Resource Manager-sablonba. A parancsfájl fordítása előtt győződjön meg arról, hogy a feladat legalább egy bemenettel és egy kimenettel rendelkezik. Ha nincs konfigurálva bemenet vagy kimenet, először konfigurálnia kell a bemenetet és a kimenetet.

1. A Visual Studio-kódban keresse meg a feladat *Transformation.asaql* fájlját.

   ![Transformation.asaql fájl a Visual Studio-kódban](./media/resource-manager-export/transformation-asaql.png)

1. Kattintson a jobb gombbal a *Transformation.asaql* fájlra, és válassza a menü **ASA: Fordításparancsfájl** parancsát.

1. Figyelje meg, hogy egy **központi telepítés** mappa jelenik meg a Stream Analytics-feladat munkaterületen.

1. Fedezze fel a *JobTemplate.json* fájlt, amely az Azure Resource Management sablon üzembe helyezéséhez használt.

## <a name="complete-the-parameters-file"></a>A paraméterfájl befejezése

Ezután töltse ki az Azure Resource Management sablon paraméterfájlját.

1. Nyissa meg a Visual Studio-kódban a Stream Analytics-feladat-munkaterület **Telepítés** mappájában található *JobTemplate.parameters.json* fájlt.

1. Figyelje meg, hogy a bemeneti és kimeneti kulcsok null értékűek. Cserélje le a null értékeket a bemeneti és kimeneti erőforrások tényleges hozzáférési kulcsaira.

1. Mentse a paraméterfájlt.

## <a name="deploy-using-templates"></a>Telepítés sablonok használatával

Készen áll az Azure Stream Analytics-feladat üzembe helyezésére az előző szakaszban létrehozott Azure Resource Manager-sablonok használatával.

Egy PowerShell-ablakban futtassa a következő parancsot. Ügyeljen arra, hogy reaplce a *ResourceGroupName*, *TemplateFile*, és *TemplateParameterFile* a tényleges erőforráscsoport nevét, és a teljes fájl elérési útjait a *JobTemplate.json* és *JobTemplate.parameters.json* fájlokat a **központi telepítés mappában** a feladat munkaterület.

Ha nincs konfigurálva az Azure PowerShell, kövesse az [Azure PowerShell-modul telepítése című](https://docs.microsoft.com/powershell/azure/install-Az-ps)témakör lépéseit.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>További lépések

* [Tesztelje az Azure Stream Analytics-feladatokat helyileg élő bevitellel a Visual Studio-kód használatával](visual-studio-code-local-run-live-input.md)

* [Fedezze fel az Azure Stream Analytics-feladatokat a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-explore-jobs.md)