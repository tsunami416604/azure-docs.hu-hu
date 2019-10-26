---
title: Azure Stream Analytics lekérdezések tesztelése a Visual Studio Code-ban
description: Ez a cikk azt ismerteti, hogyan lehet a lekérdezéseket helyileg tesztelni a Visual Studio Code-hoz készült Azure Stream Analytics eszközökkel.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924974"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>A Visual Studio Code-ban helyileg tesztelheti Stream Analytics lekérdezéseket

A Visual Studio Code-hoz készült Azure Stream Analytics Tools segítségével helyileg tesztelheti a Stream Analytics-feladatokat a mintaadatok használatával.

Ebből a rövid útmutatóból megtudhatja [, hogyan](quick-create-vs-code.md) hozhat létre egy stream Analytics feladatot a Visual Studio Code használatával.

## <a name="prerequisites"></a>Előfeltételek
* Telepítse a [.net Core SDK](https://dotnet.microsoft.com/download)-t.
* Indítsa újra a Visual Studio Code-ot.
 
## <a name="run-queries-locally"></a>Lekérdezések helyi futtatása

A Visual Studio Code-hoz készült Azure Stream Analytics-bővítmény segítségével helyileg tesztelheti a Stream Analytics-feladatokat a mintaadatok használatával.

1. Miután létrehozta a Stream Analytics feladatot, a Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt. Ezután írja be és válassza az **ASA: bemenet hozzáadása**elemet.

    ![ASA-bemenet hozzáadása a Visual Studio Code-ban](./media/vscode-local-run/add-input.png)

2. Válassza a **helyi bevitel**lehetőséget.

    ![ASA helyi bemenet hozzáadása a Visual Studio Code-ban](./media/vscode-local-run/add-local-input.png)

3. Válassza az **+ új helyi bemenet**lehetőséget.

    ![Új ASA helyi bevitel hozzáadása a Visual Studio Code-ban](./media/vscode-local-run/add-new-local-input.png)

4. Adja meg a lekérdezésben használt beviteli aliast.

    ![Új ASA helyi bemeneti alias hozzáadása](./media/vscode-local-run/new-local-input-alias.png)

5. A **LocalInput_DefaultLocalStream. JSON** fájlban adja meg a fájl elérési útját, ahol a helyi adatfájl található.

    ![Adja meg a helyi fájl elérési útját a Visual Studióban](./media/vscode-local-run/local-file-path.png)

6. Térjen vissza a lekérdezés-szerkesztőhöz, és válassza a **helyi Futtatás**lehetőséget.

    ![A lekérdezési szerkesztőben válassza a helyi Futtatás lehetőséget.](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)](quick-create-vs-code.md)

* [Azure Stream Analytics feladatok megismerése a Visual Studio Code-ban (előzetes verzió)](vscode-explore-jobs.md)
