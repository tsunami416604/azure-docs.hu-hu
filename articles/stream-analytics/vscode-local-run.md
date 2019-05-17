---
title: Tesztelje helyben a Visual Studio Code (előzetes verzió) használatával az Azure Stream Analytics-lekérdezések
description: Ez a cikk ismerteti, hogyan tesztelhet helyileg az Azure Stream Analytics Tools for Visual Studio Code-ot.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827950"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Stream Analytics-lekérdezések Visual Studio Code használatával helyben tesztelése

Az Azure Stream Analytics tools for Visual Studio Code használatával a Stream Analytics-feladatok helyileg mintaadatokkal tesztelheti.

Ezzel [rövid](quick-create-vs-code.md) megtudhatja, hogyan hozhat létre egy Stream Analytics-feladatot a Visual Studio Code használatával.

## <a name="run-queries-locally"></a>Lekérdezések futtatása helyileg

Az Azure Stream Analytics-bővítmény a Visual Studio Code használatával a Stream Analytics-feladatok helyileg mintaadatokkal tesztelheti.

1. A Stream Analytics-feladat létrehozása után **Ctrl + Shift + P** a parancskatalógus megnyitásához. Írja be, és válassza ki **ASA: Bemenet hozzáadása**.

    ![A Visual Studio code-ban ASA bemenet hozzáadása](./media/vscode-local-run/add-input.png)

2. Válassza ki **helyi bemeneti**.

    ![A Visual Studio code-ban ASA helyi bemenet hozzáadása](./media/vscode-local-run/add-local-input.png)

3. Válassza ki **+ új helyi bemenet**.

    ![Adjon hozzá egy új ASA helyi adjon meg a Visual Studio code-ban](./media/vscode-local-run/add-new-local-input.png)

4. Adja meg a lekérdezésben használt ugyanazt a bemeneti alias.

    ![Adjon hozzá egy új ASA helyi bemeneti áljel](./media/vscode-local-run/new-local-input-alias.png)

5. Az a **LocalInput_DefaultLocalStream.json** adja meg a fájl elérési útja, ahol a helyi adatok fájl megtalálható-e.

    ![Helyi fájl elérési útját adja meg a Visual studióban](./media/vscode-local-run/local-file-path.png)

6. A Lekérdezésszerkesztő adja vissza, és válassza ki **helyileg történő futtatása**.

    ![Helyileg jelölje be a Futtatás a Lekérdezésszerkesztőben](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)](quick-create-vs-code.md)

* [Ismerkedés az Azure Stream Analytics-feladatok a Visual Studio Code-dal (előzetes verzió)](vscode-explore-jobs.md)
