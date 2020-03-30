---
title: Azure Stream Analytics-feladat tesztelése helyileg mintaadatokkal a Visual Studio-kód használatával
description: Ez a cikk ismerteti, hogyan tesztelheti a lekérdezéseket helyileg mintaadatokkal az Azure Stream Analytics-eszközök a Visual Studio-kód használatával.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486468"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>A Test Stream Analytics helyi lekérdezéseket lekérdezése imént a Visual Studio-kód használatával

Az Azure Stream Analytics-eszközök a Visual Studio Code segítségével tesztelheti a Stream Analytics-feladatokhelyi mintaadatokkal. A lekérdezés eredményeit a JSON-fájlokban a projekt **LocalRunOutputs** mappájában találja.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.NET core SDK-t,](https://dotnet.microsoft.com/download) és indítsa újra a Visual Studio Code alkalmazást.

* Ezzel a [rövid útmutatóval](quick-create-vs-code.md) megtudhatja, hogyan hozhat létre Stream Analytics-feladatot a Visual Studio-kód használatával.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

Először elő kell készítenie a mintabemeneti adatfájlokat. Ha már van néhány mintaadatfájl a számítógépen, kihagyhatja ezt a lépést, és továbbléphet a következőre.

1. Kattintson **az Adatok előnézete gombra** a bemeneti konfigurációs fájlban a felső sorban. Egyes bemeneti adatok az IoT Hubból lesznek behívva, és megjelennek az előnézeti ablakban. Ne feledje, hogy ez eltarthat egy ideig.

2. Az adatok bemutatása után kattintson a **Mentés másként** gombra az adatok helyi fájlba mentéséhez.

 ![Élő bevitel előnézete](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Helyi bemenet definiálása

1. Kattintson **a input.json fájlra** a Stream Analytics-projekt Bemenetek mappájában. Ezután válassza **a Helyi bevitel hozzáadása** a felső sorban lehetőséget.

    ![Helyi bemenet hozzáadása a projektből](./media/quick-create-vs-code/add-input-from-project.png)

    A **Ctrl+Shift+P billentyűkombinációval** megnyithatja a parancspalettát, és megadhatja az **ASA: Bevitel hozzáadása parancsot.**

   ![Stream Analytics-bevitel hozzáadása a VS-kódban](./media/quick-create-vs-code/add-input.png)

2. Válassza a **Helyi bevitel lehetőséget.**

    ![AsA helyi bemenet hozzáadása a Visual Studio kódjában](./media/vscode-local-run/add-local-input.png)

3. Válassza a **+ Új helyi bevitel lehetőséget.**

    ![Új HELYI ASA-bemenet hozzáadása a Visual Studio-kódhoz](./media/vscode-local-run/add-new-local-input.png)

4. Adja meg a lekérdezésben használt bemeneti aliast.

    ![Új ASA helyi bemeneti alias hozzáadása](./media/vscode-local-run/new-local-input-alias.png)

5. Az újonnan létrehozott **LocalInput_Input.json** fájlban adja meg azt a fájlelérési utat, amelyben a helyi adatfájl található.

    ![Helyi fájl elérési útjának megadása a Visual Studióban](./media/vscode-local-run/local-file-path.png)

6. A bemeneti adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** lehetőséget. Az adatok szerializálási típusát a rendszer automatikusan észleli, ha annak JSON vagy CSV. A választóval **táblázat** vagy **nyers** formátumban tekintheti meg az adatokat. Az alábbi táblázat táblázat formátumú adatokat **mutat be:**

     ![Helyi adatok előnézete táblázatformátumban](./media/vscode-local-run/local-file-preview-table.png)

    Az alábbi táblázat egy példa a **Nyers formátumú adatokra:**

    ![Helyi adatok előnézete nyers formátumban](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Lekérdezések futtatása helyileg

Térjen vissza a lekérdezésszerkesztőhöz, és válassza a **Helyi futtatás**lehetőséget. Ezután válassza a **Helyi bevitel használata lehetőséget** a legördülő listából.

![Helyi futtatás kiválasztása a lekérdezésszerkesztőben](./media/vscode-local-run/run-locally.png)

![Helyi bemenet használata](./media/vscode-local-run/run-locally-use-local-input.png)

Az eredmény a jobb oldali ablakban jelenik meg. A **Futtatás** gombra kattintva újra tesztelheti a tesztet. A **Megnyitás a mappában** lehetőséget választva megtekintheti az eredményfájlokat a fájlkezelőben, és további eszközöket is használhat. Ne feledje, hogy az eredményfájlok csak JSON formátumban érhetők el.

![Helyi futtatáseredmény megtekintése](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>További lépések

* [Tesztelje az Azure Stream Analytics-feladatokat helyileg élő bevitellel a Visual Studio-kód használatával](visual-studio-code-local-run-live-input.md)

* [Fedezze fel az Azure Stream Analytics-feladatokat a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-explore-jobs.md)
