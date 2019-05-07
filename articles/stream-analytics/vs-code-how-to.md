---
title: Ismerkedés az Azure Stream Analytics Visual Studio Code-dal (előzetes verzió)
description: Ez a cikk bemutatja, hogyan Azure Stream Analytics-feladat exportálása helyi projektbe, listázhatók a feladatok és a feladat entitások megtekintése, és a Stream Analytics-feladat egy CI/CD-folyamat beállítása.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079210"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Ismerkedés az Azure Stream Analytics Visual Studio Code-dal (előzetes verzió)

Az Azure Stream Analytics Visual Studio Code-bővítmény könnyen használható környezetet biztosít a fejlesztők számára a Stream Analytics-feladatok kezelésére. Az Azure Stream Analytics-bővítmény segítségével:

- [Hozzon létre](quick-create-vs-code.md), elindítása és leállítása feladat
- Meglévő feladatok exportálása helyi-projektbe
- Lekérdezések futtatása helyileg
- CI/CD-folyamat beállítása

## <a name="export-a-job-to-a-local-project"></a>Feladatok exportálása helyi-projektbe

Feladatok exportálása helyi projektbe, keresse meg a feladat az exportálni kívánt a **Stream Analytics Explorer** a Visual Studio code-ban. Ezután válassza ki a mappát a projekt számára. A projekt van exportálva, jelölje be a mappához, és továbbra is kezelheti a feladat a Visual Studio Code-ból. A Stream Analytics-feladatok kezelése a Visual Studio Code segítségével további információkért tekintse meg a Visual Studio Code [rövid](quick-create-vs-code.md).

![A Visual Studio Code ASA-feladat exportálása](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Lekérdezések futtatása helyileg

Az Azure Stream Analytics-bővítmény a Visual Studio Code használatával a Stream Analytics-feladatok helyileg mintaadatokkal tesztelheti.

1. A Stream Analytics-feladat létrehozása után **Ctrl + Shift + P** a parancs raklap megnyitásához. Írja be, és válassza ki **ASA: Bemenet hozzáadása**.

    ![A Visual Studio code-ban ASA bemenet hozzáadása](./media/vs-code-how-to/add-input.png)

2. Válassza ki **helyi bemeneti**.

    ![A Visual Studio code-ban ASA helyi bemenet hozzáadása](./media/vs-code-how-to/add-local-input.png)

3. Válassza ki **+ új helyi bemenet**.

    ![Adjon hozzá egy új ASA helyi adjon meg a Visual Studio code-ban](./media/vs-code-how-to/add-new-local-input.png)

4. Adja meg a lekérdezésben használt ugyanazt a bemeneti alias.

    ![Adjon hozzá egy új ASA helyi bemeneti áljel](./media/vs-code-how-to/new-local-input-alias.png)

5. Az a **LocalInput_DefaultLocalStream.json** adja meg a fájl elérési útja, ahol a helyi adatok fájl megtalálható-e.

    ![Helyi fájl elérési útját adja meg a Visual studióban](./media/vs-code-how-to/local-file-path.png)

6. A Lekérdezésszerkesztő adja vissza, és válassza ki **helyileg történő futtatása**.

    ![Helyileg jelölje be a Futtatás a Lekérdezésszerkesztőben](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>CI/CD-folyamat beállítása

Folyamatos integráció és üzembe helyezés az Azure Stream Analytics-feladatok használatával engedélyezheti a **asa-CI/CD eszközökkel** NPM-csomag. Az NPM-csomag az eszközöket biztosít a Stream Analytics Visual Studio Code-projektek az automatikus központi telepítés. Használat Windows, macOS és Linux rendszereken a Visual Studio Code telepítése nélkül.

Miután [letölti a csomagot](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), használja az alábbi parancsot a kimenetben az Azure Resource Manager-sablonok. Ha a **outputPath** nincs megadva, a sablonok kerülnek a **telepítés** mappát a projekt **bin** mappát.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics felhőalapú feladat létrehozása a Visual Studio Code (előzetes verzió)](quick-create-vs-code.md)