---
title: Azure Stream Analytics feladatok megismerése a Visual Studio Code-ban
description: Ez a cikk bemutatja, hogyan exportálhat egy Azure Stream Analytics feladatot egy helyi projektbe, hogyan listázhatja a feladatokat, és megtekintheti a feladatok entitásait.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 00705e40ca17959701af325ed52a4c3754d35122
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039058"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Ismerkedés a Azure Stream Analytics a Visual Studio Code (előzetes verzió) szolgáltatással

A Visual Studio Code-bővítmény Azure Stream Analytics a fejlesztők számára a Stream Analytics-feladatok kezelésének egyszerű élményét nyújtja. Windows, Mac és Linux rendszereken is használható. A Azure Stream Analytics bővítménnyel a következőket teheti:

- Feladatok [létrehozása](quick-create-vs-code.md), elindítása és leállítása
- Meglévő feladatok exportálása helyi projektbe
- Feladatok listázása és feladat-entitások megtekintése

## <a name="export-a-job-to-a-local-project"></a>Feladatok exportálása helyi projektbe

Ha a feladatot egy helyi projektbe szeretné exportálni, keresse meg az exportálni kívánt feladatot a Visual Studio Code **stream Analytics Explorerben** . Ezután válasszon ki egy mappát a projekt számára. A program a kiválasztott mappába exportálja a projektet, és a Visual Studio Code-ból folytathatja a feladatok kezelését. A Visual [Studio code stream Analytics](quick-create-vs-code.md)feladatok kezelésével kapcsolatos további információkért tekintse meg a Visual Studio Code gyors útmutatóját.

![ASA-feladatok exportálása a Visual Studio Code-ban](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Feladatok listázása és a feladatok megtekintése

A feladat nézettel a Visual studióból Azure Stream Analytics feladatokkal dolgozhat.


1. Kattintson az **Azure** ikonra a Visual Studio Code tevékenység sávján, majd bontsa ki **stream Analytics csomópontot**. A feladatoknak az előfizetések alatt kell megjelenniük.

   ![Stream Analytics Explorer megnyitása](./media/vscode-explore-jobs/open-explorer.png)

2. A feladatok csomópontjának kibontásával megnyithatja és megtekintheti a feladatok lekérdezését, konfigurációját, bemeneteit, kimeneteit és funkcióit. 

3. Kattintson a jobb gombbal a feladatra, majd válassza a feladatok nézet **megnyitása a portálon** csomópontot a Azure Portal feladatok nézetének megnyitásához.

   ![A feladatok nézet megnyitása a portálon](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)](quick-create-vs-code.md)
