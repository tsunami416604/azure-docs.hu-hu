---
title: Visual Studio Code Tools for Machine Learning az Azure-ban – rövid útmutató | Microsoft Docs
description: Ez a cikk ismerteti a Visual Studio Code Tools for Machine Learning használatának első lépéseit, egy kísérlet létrehozásától egy modell tanításán át egy webszolgáltatás üzembe helyezéséig.
services: machine-learning
author: chris-lauren
ms.author: clauren
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ROBOTS: NOINDEX
ms.openlocfilehash: f767da10b3607cb24c981b3eaffe988eff1f4f9e
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261190"
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

A Visual Studio Code Tools for AI egy fejlesztési bővítmény, amely mélytanulási és AI-megoldások összeállítását, tesztelését és üzembe helyezését teszi lehetővé. Zökkenőmentes integrációt biztosít az Azure Machine Learninggel, többek között egy futtatásielőzmények-nézetet, amely a korábbi betanítások és egyéni mérőszámok teljesítményét részletezi. Mintatallózó nézetet is kínál, amely lehetővé teszi az új projektek tallózását és elindítását a [Microsoft Cognitive Toolkittel (korábban CNTK)](https://docs.microsoft.com/cognitive-toolkit/), [Google TensorFlow-val](https://www.tensorflow.org) és más mélytanulási keretrendszerekkel. Végül egy tallózót biztosít a számítási célokhoz, amely lehetővé teszi modellek távoli környezetben (például Azure-beli virtuális gépeken vagy GPU-val rendelkező Linux-kiszolgálókon) való betanítási feladatainak elküldését. 
 
## <a name="getting-started"></a>Első lépések 
Első lépésként le kell tölteni és telepíteni kell a [Visual Studio Code](https://code.visualstudio.com/Download)-ot. Miután megnyitotta a Visual Studio Code-ot, hajtsa végre a következő lépéseket:
1. Kattintson a bővítmény ikonra a tevékenységsávon. 
2. Keressen a „Visual Studio Code Tools for AI” kifejezésre. 
3. Kattintson a **Telepítés** gombra. 
4. A telepítést követően kattintson az **Újbóli betöltés** gombra. 

A Visual Studio Code újbóli betöltése után a bővítmény engedélyezve lesz. [További tudnivalók a bővítmények telepítéséről](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Projektminták megismerése
A Visual Studio Code Tools for AI egy mintatallózóval rendelkezik. A mintatallózóval egyszerűen kereshet mintákat, és ezeket csupán néhány kattintással kipróbálhatja. A tallózó megnyitásához tegye a következőket:   
1. Nyissa meg a parancskatalógust (Nézet > **Parancskatalógus** vagy **Ctrl+Shift+P**).
2. Írja be az „AI Sample” (MI-minta) kifejezést. 
3. A rendszer felajánlja javaslatként az „AI: Open Azure ML Sample Explorer” (MI: Azure ML-mintatallózó megnyitása) parancsot. Ezt válassza ki, majd nyomja le az Enter billentyűt. 

Vagy kattintson a mintatallózó ikonjára.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Új projekt létrehozása a mintatallózóból 
Különböző minták között böngészhet, és további információkat tudhat meg róluk. Tallózással keresse meg a „Classifying Iris” nevű mintát. Egy ezen a mintán alapuló, új projekt létrehozásához tegye a következőket:
1. Kattintson a telepítés gombra a projektmintán, majd a rendszer kérdéseket tesz fel, hogy végigvezesse az új projekt létrehozásának lépésein. 
2. Adjon egy nevet a projektnek (például: „Iris”).
3. Válasszon egy mappaelérési utat a projekt létrehozásához, majd nyomja le az Enter billentyűt. 
4. Válasszon ki egy meglévő munkaterületet, és nyomja le az Enter billentyűt.

Ezután a rendszer létrehozza a projektet.

> [!TIP]
> Az Azure-erőforrás eléréséhez bejelentkezett állapotban kell lennie. A beágyazott terminálon írja be az „az login” kifejezést, és kövesse az utasításokat. 

## <a name="submitting-experiment-with-the-new-project"></a>Kísérlet elküldése az új projekttel
A Visual Studio Code-ban megnyílik egy új projekt, egy feladatot pedig elküldünk különböző számítási céloknak (helyi és Dockerrel rendelkező virtuális gép).
A Visual Studio Code Tools for AI több módot is biztosít egy kísérlet elküldésére. 
1. Kattintson a jobb gombbal a helyi menüre, majd válassza az **AI: Submit Job** (MI: feladat elküldése) lehetőséget.
2. A parancskatalógusban válassza az „AI: Submit Job” (MI: feladat elküldése) parancsot.
3. A parancsot közvetlenül az Azure CLI használatával is futtathatja, a beágyazott terminál segítségével.

Nyissa meg az iris_sklearn.py fájlt, kattintson a jobb gombbal, és válassza az **AI: Submit Job** (MI: feladat elküldése) parancsot.
1. Válassza ki az „Azure Machine Learning” platformot.
2. Válassza ki a „Docker-Python” futtatási konfigurációt.

> [!NOTE]
> Ha első alkalommal küld el egy feladatot, a rendszer a következő értesítést küldi: „No Machine Learning configuration found, creating...” (Nem található Machine Learning-konfiguráció, létrehozás folyamatban...). Megnyílik egy JSON-fájl, amit mentsen a **Ctrl+S** billentyűkombinációval.

A feladat elküldését követően a beágyazott terminál megjeleníti a futtatások állapotát. 

## <a name="view-list-of-jobs"></a>A feladatok listájának megtekintése
A feladatokat az elküldésüket követően a futtatási előzményekben tekintheti át.
1. Nyissa meg a parancskatalógust (Nézet > **Parancskatalógus** vagy **Ctrl+Shift+P**).
2. Írja be az „AI List” (MI lista) kifejezést.
3. A rendszer felajánlja javaslatként az „AI: List Jobs” parancsot. Ezt válassza ki, majd nyomja le az Enter billentyűt.

Megnyílik a feladatok listanézete, amelyben az összes futó elem és azok kapcsolódó adatai láthatók.

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A megnyitott Feladatlistában kattintson az első elemre.
Az egyes feladatok eredményeinek mélyebb szintű megismerése érdekében kattintson a felső **feladatazonosítóra** a részletes adatok megjelenítéséhez. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Machine Learning konfigurálása IDE-vel való együttműködésre](./how-to-configure-your-IDE.md)
