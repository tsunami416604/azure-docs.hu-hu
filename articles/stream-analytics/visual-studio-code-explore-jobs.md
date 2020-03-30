---
title: Fedezze fel az Azure Stream Analytics-feladatokat a Visual Studio-kódban
description: Ez a cikk bemutatja, hogyan exportálhat egy Azure Stream Analytics-feladatot egy helyi projektbe, listázhatja a feladatokat és tekintheti meg a feladatentitásokat.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479240"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Az Azure Stream Analytics felfedezése a Visual Studio-kóddal (előzetes verzió)

Az Azure Stream Analytics for Visual Studio Code bővítmény könnyű élményt nyújt a fejlesztőknek a Stream Analytics-feladatok kezeléséhez. Ezt fel lehet használni a Windows, Mac és Linux. Az Azure Stream Analytics-bővítménysegítségével a következőket teheti:

- [Feladatok létrehozása,](quick-create-vs-code.md)indítása és leállítása
- Meglévő feladatok exportálása helyi projektbe
- Feladatok listázása és feladatentitások megtekintése

## <a name="export-a-job-to-a-local-project"></a>Feladat exportálása helyi projektbe

Ha egy feladatot egy helyi projektbe szeretne exportálni, keresse meg az exportálni kívánt feladatot a **Stream Analytics Intézőben** a Visual Studio-kódban. Ezután jelöljön ki egy mappát a projekthez. A program exportálja a projektet a kijelölt mappába, és a feladatot továbbra is kezelheti a Visual Studio-kódból. A Stream Analytics-feladatok Visual Studio-kód dal kezeléséről a Visual Studio-kód [rövid útmutatójában](quick-create-vs-code.md)talál további információt.

![ASA-feladat exportálása a Visual Studio-kódban](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Feladat listázása és feladatentitások megtekintése

A feladatnézet segítségével a Visual Studio Azure Stream Analytics-feladataival kommunikálhat.


1. Kattintson az **Azure** ikonra a Visual Studio kódtevékenységsávján, majd bontsa ki a **Stream Analytics csomópontot.** A feladatoknak az előfizetések alatt kell megjelenniük.

   ![A Stream Analytics Explorer megnyitása](./media/vscode-explore-jobs/open-explorer.png)

2. Bontsa ki a feladat csomópont, megnyithatja és megtekintheti a feladat lekérdezés, konfiguráció, bemenetek, kimenetek és függvények. 

3. Kattintson a jobb gombbal a feladatcsomópontra, és válassza a Feladatnézet megnyitása a **portálcsomópontban** lehetőséget a feladatnézet megnyitásához az Azure Portalon.

   ![Feladatnézet megnyitása a portálon](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics felhőfeladat létrehozása a Visual Studio-kódban (előzetes verzió)](quick-create-vs-code.md)
