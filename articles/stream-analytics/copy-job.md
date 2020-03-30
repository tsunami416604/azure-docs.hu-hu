---
title: Azure Stream Analytics-feladatok másolása vagy biztonsági másolata
description: Ez a cikk ismerteti, hogyan másolhatja vagy készíthet biztonsági másolatot egy Azure Stream Analytics-feladatról.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771495"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics-feladatok másolása vagy biztonsági másolata

A telepített Azure Stream Analytics-feladatokmásolása és biztonsági másolata a Visual Studio-kód vagy a Visual Studio használatával. 

## <a name="before-you-begin"></a>Előkészületek
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

* Telepítse [az Azure Stream Analytics bővítményt a Visual Studio-kódhoz](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) vagy az Azure Stream [Analytics-eszközökhöz a Visual Studio-hoz.](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Kattintson az **Azure** ikonra a Visual Studio kódtevékenységsávján, majd bontsa ki a **Stream Analytics** csomópontot. A feladatoknak az előfizetések alatt kell megjelenniük.

   ![A Stream Analytics Explorer megnyitása](./media/vscode-explore-jobs/open-explorer.png)

2. Ha egy feladatot egy helyi projektbe szeretne exportálni, keresse meg az exportálni kívánt feladatot a **Stream Analytics Intézőben** a Visual Studio-kódban. Ezután jelöljön ki egy mappát a projekthez.

    ![ASA-feladat exportálása a Visual Studio-kódban](./media/vscode-explore-jobs/export-job.png)

    A program a projektet a kijelölt mappába exportálja, és hozzáadja az aktuális munkaterülethez.

    ![ASA-feladat exportálása a Visual Studio-kódban](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Ha a feladatot egy másik régióban vagy biztonsági másolatban szeretné közzétenni egy\*másik névvel, válassza a Kijelölés lehetőséget az előfizetések közül a lekérdezésszerkesztőben ( .asaql) **közzétenni kívánt előfizetései közül,** és kövesse az utasításokat.

    ![Közzététel az Azure-ban a Visual Studio-kódban](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Kövesse az [üzembe helyezett Azure Stream Analytics-feladat exportálását egy projektutasításokba.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)

2. Nyissa \*meg az .asaql fájlt a Lekérdezésszerkesztőben, válassza a Küldés az **Azure-ba** lehetőséget a parancsfájlszerkesztőben, és kövesse az utasításokat a feladat közzétételéhez egy másik régióban, vagy egy új név használatával biztonsági mentést.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladat létrehozása a Visual Studio-kód használatával](quick-create-vs-code.md)
* [Rövid útmutató: Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Azure Stream Analytics-feladat üzembe helyezése CI/CD-vel az Azure Pipelines használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
