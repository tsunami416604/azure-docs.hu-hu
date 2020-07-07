---
title: Azure Stream Analytics feladatok másolása vagy biztonsági mentése
description: Ez a cikk a Azure Stream Analytics feladatok másolását és biztonsági mentését ismerteti.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 258fc30f3e4a4ba374ada89c03b824ff5e27718e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612265"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics feladatok másolása vagy biztonsági mentése

A Visual Studio Code vagy a Visual Studio használatával másolhatja vagy biztonsági másolatot készíthet az üzembe helyezett Azure Stream Analytics-feladatokról. A feladatok másik régióba való másolása nem másolja az utolsó kimeneti időpontot. Ezért a másolt feladatok indításakor nem használható a [**legutóbbi leállítási**](https://docs.microsoft.com/azure/stream-analytics/start-job#start-options) beállítás.

## <a name="before-you-begin"></a>Előkészületek
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítse [Azure stream Analytics bővítményt a Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) vagy a [Azure stream Analytics Tools for Visual Studio alkalmazáshoz](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Kattintson az **Azure** ikonra a Visual Studio Code tevékenység sávján, majd bontsa ki **stream Analytics** csomópontot. A feladatoknak az előfizetések alatt kell megjelenniük.

   ![Stream Analytics Explorer megnyitása](./media/vscode-explore-jobs/open-explorer.png)

2. Ha a feladatot egy helyi projektbe szeretné exportálni, keresse meg az exportálni kívánt feladatot a Visual Studio Code **stream Analytics Explorerben** . Ezután válasszon ki egy mappát a projekt számára.

    ![ASA-feladatok exportálása a Visual Studio Code-ban](./media/vscode-explore-jobs/export-job.png)

    A projektet a rendszer a kiválasztott mappába exportálja, és hozzáadja az aktuális munkaterülethez.

    ![ASA-feladatok exportálása a Visual Studio Code-ban](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Ha más néven szeretné közzétenni a feladatot egy másik régióban vagy biztonsági másolatban, válassza a **kiválasztás az előfizetések közül** lehetőséget a lekérdezés-szerkesztőben (. asaql) való közzétételhez, \* és kövesse az utasításokat.

    ![Közzététel az Azure-ban a Visual Studio Code-ban](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Kövesse az [üzembe helyezett Azure stream Analyticsi feladat exportálása projekt utasításait](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Nyissa meg a \* . asaql fájlt a Lekérdezéstervezőben, válassza az **elküldés az Azure** -ba lehetőséget a parancsfájl-szerkesztőben, és kövesse az utasításokat a feladat egy másik régióba vagy biztonsági másolatba való közzétételéhez egy új néven.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio Code használatával](quick-create-vs-code.md)
* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Azure Stream Analytics-feladat üzembe helyezése CI/CD-vel az Azure Pipelines használatával](stream-analytics-tools-visual-studio-cicd-vsts.md)
