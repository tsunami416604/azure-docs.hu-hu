---
title: Az Azure Stream Analytics tesztelése helyileg lekérdezi az élő közvetítés bemenetét a Visual Studio-kód használatával
description: Ez a cikk bemutatja, hogyan tesztelheti a lekérdezéseket helyileg az élő streambemenettel szemben az Azure Stream Analytics-eszközök a Visual Studio-kódhoz használatával.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660851"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>A Test Stream Analytics helyi legfelhasználhatósági lekérdezéseket használ a Visual Studio-kód használatával

Az Azure Stream Analytics Tools for Visual Studio Code segítségével helyileg tesztelheti a Stream Analytics-feladatokat az élő közvetítés bemenetével szemben. A bemenet származhat olyan forrásból, mint az Azure Event Hubs vagy az Azure IoT Hub. A kimeneti eredményeket a program JSON-fájlként küldi el a projekt **LocalRunOutputs nevű mappájába.**

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.NET Core SDK-t,](https://dotnet.microsoft.com/download) és indítsa újra a Visual Studio Code alkalmazást.

* [Ezzel a rövid útmutatóval](quick-create-vs-code.md) megtudhatja, hogyan hozhat létre Stream Analytics-feladatot a Visual Studio-kód használatával.

## <a name="define-a-live-stream-input"></a>Élő közvetítés bemenetének definiálása

1. Kattintson a jobb gombbal a **Bemenetek** mappára a Stream Analytics projektben. Ezután válassza **az ASA: Add Input** a helyi menüből.

   ![Bemenet hozzáadása a Bemenetek mappából](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   A **Ctrl+Shift+P** billentyűkombinációval megnyithatja a parancspalettát, és beírhatja az **ASA: Bevitel hozzáadása parancsot.**

   ![Stream Analytics-bevitel hozzáadása a Visual Studio-kódban](./media/quick-create-vs-code/add-input.png)

2. Válasszon egy bemeneti forrástípust a legördülő listából.

   ![Az IoT-központ kiválasztása beviteli lehetőségként](./media/quick-create-vs-code/iot-hub.png)

3. Ha hozzáadta a bemenetet a parancspalettáról, válassza ki a Stream Analytics lekérdezési parancsfájlt, amely a bemenetet fogja használni. Meg kell automatikusan kitölteni a fájl elérési útját **myASAproj.asaql**.

   ![Stream Analytics-parancsfájl kiválasztása a Visual Studio-kódban](./media/quick-create-vs-code/asa-script.png)

4. Válassza **a Kijelölés az Azure-előfizetések közül** a legördülő menüből.

    ![Válasszon az előfizetések közül](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Konfigurálja az újonnan létrehozott JSON-fájlt. A CodeLens szolgáltatással karakterláncot írhat be, legördülő listából választhat, vagy közvetlenül a fájlban módosíthatja a szöveget. Az alábbi képernyőképen példaként **a Kijelölés az Előfizetések közül** látható.

   ![Bemenet konfigurálása a Visual Studio-kódban](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Előzetes bemenet

Győződjön meg arról, hogy a bemeneti adatok érkeznek, válassza **az Adatok előnézete** az élő bemeneti konfigurációs fájlban a felső sorban. Egyes bemeneti adatok egy IoT-központból származik, és az előnézeti ablakban jelenik meg. Az előnézet megjelenése néhány másodpercet is igénybe vehet.

 ![Élő bevitel előnézete](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Lekérdezések futtatása helyileg

Térjen vissza a lekérdezésszerkesztőhöz, és válassza a **Helyi futtatás**lehetőséget. Ezután válassza az **Élő bevitel használata lehetőséget** a legördülő listából.

![A "Helyi futtatás" kiválasztása a lekérdezésszerkesztőben](./media/vscode-local-run/run-locally.png)

![Válassza az "Élő bevitel használata" lehetőséget](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Az eredmény a jobb oldali ablakban jelenik meg, és 3 másodpercenként frissül. A **Futtatás** lehetőséget választva újra tesztelheti a tesztet. A **Megnyitás mappában** lehetőséget választva megtekintheti az eredményfájlokat a Fájlkezelőben, és megnyithatja őket a Visual Studio-kóddal vagy egy olyan eszközzel, mint az Excel. Ne feledje, hogy az eredményfájlok csak JSON formátumban érhetők el.

A feladat létrehozásának megkezdéséhez szükséges alapértelmezett idő a **Most**értékre van állítva. Az idő testreszabásához válassza az eredményablak **Kimenetkezdési időpontja** gombját.

![Helyi futtatáseredmény megtekintése](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>További lépések

* [Fedezze fel az Azure Stream Analytics-feladatokat a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-explore-jobs.md)

* [CI/CD-folyamatok beállítása az npm csomag használatával](setup-cicd-vs-code.md)
