---
title: Ismerkedés az Azure Stream Analytics-feladatok a Visual Studio Code-dal (előzetes verzió)
description: Ez a cikk bemutatja, hogyan exportálhatja az Azure Stream Analytics-feladat egy helyi projektet, a listázhatók a feladatok és a feladat entitások megtekintése.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827800"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Ismerkedés az Azure Stream Analytics Visual Studio Code-dal (előzetes verzió)

Az Azure Stream Analytics Visual Studio Code-bővítmény könnyen használható környezetet biztosít a fejlesztők számára a Stream Analytics-feladatok kezelésére. Windows, Mac és Linux rendszereken használható. Az Azure Stream Analytics-bővítmény segítségével:

- [Hozzon létre](quick-create-vs-code.md), elindítása és leállítása feladat
- Meglévő feladatok exportálása helyi-projektbe
- Feladatok listázása és entitások feladat megtekintése

## <a name="export-a-job-to-a-local-project"></a>Feladatok exportálása helyi-projektbe

Feladatok exportálása helyi projektbe, keresse meg a feladat az exportálni kívánt a **Stream Analytics Explorer** Visual Studio Code-ban. Ezután válassza ki a mappát a projekt számára. A projekt van exportálva, jelölje be a mappához, és továbbra is kezelheti a feladat a Visual Studio Code-ból. A Stream Analytics-feladatok kezelése a Visual Studio Code segítségével további információkért tekintse meg a Visual Studio Code [rövid](quick-create-vs-code.md).

![A Visual Studio Code ASA-feladat exportálása](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Feladatok listázása és entitások feladat megtekintése

A feladat nézet segítségével dolgozhat a Visual Studióból az Azure Stream Analytics-feladatok.


1. Kattintson a **Azure** ikon a Visual Studio Code tevékenységsávon majd **Stream Analytics csomópont**. A feladatok a előfizetéseknél kell megjelennie.

   ![Nyissa meg a Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Bontsa ki a projekt csomópontra, megnyithatja és megtekintheti a feladat lekérdezés, konfigurációs, bemenetek, kimenetek és funkciók. 

3. Kattintson a jobb gombbal a projekt csomópontra, majd válassza a **portálon nyissa meg a feladat nézet** csomóponton a feladat nézet megnyitása az Azure Portalon.

   ![Nyissa meg a feladat megtekintése a portálon](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)](quick-create-vs-code.md)
