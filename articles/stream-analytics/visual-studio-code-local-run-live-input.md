---
title: A Visual Studio Code használatával helyileg tesztelheti Azure Stream Analytics lekérdezéseket élő stream-bevitelsel
description: Ez a cikk azt ismerteti, hogyan tesztelheti a lekérdezéseket az élő stream-bemeneteken a Visual Studio Code-hoz készült Azure Stream Analytics Tools használatával.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660851"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>A Visual Studio Code használatával helyileg tesztelheti Stream Analytics lekérdezéseket élő stream-bevitelsel

A Visual Studio Code-hoz készült Azure Stream Analytics Tools használatával a Stream Analytics-feladatokat helyileg tesztelheti az élő stream bemenetével. A bemenet olyan forrásból származhat, mint az Azure Event Hubs vagy az Azure IoT Hub. A kimeneti eredmények JSON-fájlként lesznek elküldve a projekt egy **LocalRunOutputs**nevű mappájába.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.net Core SDKt](https://dotnet.microsoft.com/download) , és indítsa újra a Visual Studio Code-ot.

* [Ebből](quick-create-vs-code.md) a rövid útmutatóból megtudhatja, hogyan hozhat létre stream Analytics feladatokat a Visual Studio Code használatával.

## <a name="define-a-live-stream-input"></a>Élő stream-bemenet megadása

1. Kattintson a jobb gombbal a Stream Analytics projekt **bemenetek** mappájára. Ezután válassza az **ASA: bemenet hozzáadása** lehetőséget a helyi menüből.

   ![Bemenet hozzáadása a bemenetek mappából](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   A **CTRL + SHIFT + P** billentyűkombinációval is megnyithatja a parancssort, és megadhatja az **ASA: bemenet hozzáadása**elemet.

   ![Stream Analytics bevitel hozzáadása a Visual Studio Code-ban](./media/quick-create-vs-code/add-input.png)

2. Válassza ki a bemeneti forrás típusát a legördülő listából.

   ![Válassza az IoT hub lehetőséget a bemeneti beállításként](./media/quick-create-vs-code/iot-hub.png)

3. Ha hozzáadta a bemenetet a parancssorból, válassza ki a Stream Analytics lekérdezési parancsfájlt, amely a bemenetet fogja használni. A rendszer automatikusan kitölti a **myASAproj. asaql**fájl elérési útját.

   ![Stream Analytics szkript kiválasztása a Visual Studio Code-ban](./media/quick-create-vs-code/asa-script.png)

4. A legördülő menüben válassza az **Azure-előfizetések kiválasztása** lehetőséget.

    ![Kiválasztás az előfizetések közül](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Konfigurálja az újonnan létrehozott JSON-fájlt. A Codelensben funkcióval karakterláncot adhat meg, kiválaszthatja a legördülő listából, vagy megváltoztathatja a szöveget közvetlenül a fájlban. Az alábbi képernyőképen az **előfizetések közül választhat** , példaként.

   ![A bemenet konfigurálása a Visual Studio Code-ban](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Előzetes verzió bemenete

Győződjön meg arról, hogy a bemeneti adatok beérkeznek, válassza az **adatok előnézete** lehetőséget az élő bemeneti konfigurációs fájlban a felső sorban. Egyes bemeneti adatok egy IoT-hubhoz származnak, és az előnézet ablakban láthatók. Az előnézet néhány másodpercig is eltarthat.

 ![Élő bemenet megtekintése](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Lekérdezések helyi futtatása

Térjen vissza a lekérdezés-szerkesztőhöz, és válassza a **helyi Futtatás**lehetőséget. Ezután válassza az **élő bemenet használata** lehetőséget a legördülő listából.

![A lekérdezés-szerkesztőben válassza a helyi Futtatás lehetőséget.](./media/vscode-local-run/run-locally.png)

![Válassza az "élő bemenet használata" lehetőséget.](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Az eredmény a jobb oldali ablakban jelenik meg, és 3 másodpercenként frissül. A **Futtatás** lehetőség kiválasztásával tesztelheti újra. A **Megnyitás a mappában** lehetőség kiválasztásával megtekintheti az eredmények fájljait a Fájlkezelőben, és megnyithatja őket a Visual Studio Code vagy egy hasonló eszköz használatával. Vegye figyelembe, hogy az eredmény fájljai csak JSON formátumban érhetők el.

A kimenet létrehozásának megkezdéséhez használt alapértelmezett idő **most**értékre van állítva. Az időablakban a **kimenet kezdési időpontja** gomb kiválasztásával testreszabhatja az időt.

![Helyi Futtatás eredményének megtekintése](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok megismerése a Visual Studio Code-ban (előzetes verzió)](visual-studio-code-explore-jobs.md)

* [CI/CD-folyamatok beállítása a NPM-csomag használatával](setup-cicd-vs-code.md)
