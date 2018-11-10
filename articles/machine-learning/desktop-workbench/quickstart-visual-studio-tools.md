---
title: Visual Studio Tools for Machine Learning az Azure-ban – rövid útmutató | Microsoft Docs
description: Ez a cikk ismerteti a Visual Studio Tools for Machine Learning használatának első lépéseit, egy kísérlet létrehozásától egy modell tanításán át egy webszolgáltatás üzembe helyezéséig.
services: machine-learning
author: chris-lauren
ms.author: clauren
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, vs-azure
ms.topic: quickstart
ms.date: 11/15/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 37663e5053de8066458364bbfdf824caa154dfb1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227025"
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

A Visual Studio Tools for AI egy fejlesztési bővítmény, amely mélytanulási és AI-megoldások összeállítását, tesztelését és üzembe helyezését teszi lehetővé. Zökkenőmentes integrációt biztosít az Azure Machine Learninggel, többek között egy futtatásielőzmények-nézetet, amely a korábbi betanítások és egyéni mérőszámok teljesítményét részletezi. Mintatallózó nézetet is kínál, amely lehetővé teszi az új projektek tallózását és elindítását a [Microsoft Cognitive Toolkittel (korábban CNTK)](https://www.microsoft.com/cognitive-toolkit), [Google TensorFlow-val](https://www.tensorflow.org) és más mélytanulási keretrendszerekkel. Végül egy tallózót biztosít a számítási célokhoz, amely lehetővé teszi modellek távoli környezetben (például Azure-beli virtuális gépeken vagy GPU-val rendelkező Linux-kiszolgálókon) való betanítási feladatainak elküldését. Emellett könnyített hozzáférést biztosít az [Azure Batch AI előzetes verziójához](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Első lépések 
Első lépésként le kell tölteni és telepíteni kell a [Visual Studiót](https://www.visualstudio.com/downloads/). A Visual Studio megnyitása után hajtsa végre a következő lépéseket:
1. Kattintson a „Tools” (Eszközök) elemre a Visual Studio menüsorán, és válassza az Extensions and Updates (Bővítmények és frissítések) lehetőséget.
2. Kattintson az Online lapra, és válassza a Search Visual Studio Marketplace (Keresés a Visual Studio piacterén) elemet.
3. Keressen a „Visual Studio for AI” kifejezésre. 
3. Kattintson a **Download** (Letöltés) gombra. 
4. A telepítés után indítsa újra a Visual Studiót. 

A Visual Studio újbóli betöltése után a bővítmény engedélyezve lesz. [További tudnivalók a bővítmények kereséséről](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> A Visual Studio Tools for AI használatához a Visual Studio 2015 vagy 2017 Professional vagy Enterprise verziója szükséges. Az Apple OSX verziói nem támogatottak. 


## <a name="exploring-project-samples"></a>Projektminták megismerése
A Visual Studio Tools for AI egy mintatallózóval rendelkezik. A mintatallózóval egyszerűen kereshet mintákat, és ezeket csupán néhány kattintással kipróbálhatja. A tallózó megnyitásához tegye a következőket:   
1. Kattintson a menüsávon az **AI Tools** (MI-eszközök) elemre.
2. Kattintson az Azure Machine Learning Gallery elemre.

Megnyílik egy lap, amely az összes Azure ML-mintát tartalmazza.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Új projekt létrehozása a mintatallózóból 
Különböző minták között böngészhet, és további információkat tudhat meg róluk. Tallózással keresse meg a „Classifying Iris” nevű mintát. Egy ezen a mintán alapuló, új projekt létrehozásához tegye a következőket:
1. A projektmintánál kattintson az **install** (telepítés) gombra, amely egy új párbeszédpanelt nyit meg. 
2. Válassza ki az erőforráscsoportot, a fiókot és a munkaterületet.
3. A projekt típusa maradhat General (Általános).
4. Adja meg a projekt elérési útját és nevét, majd nyomja le az Enter billentyűt. 
5. A megnyíló párbeszédpanelen kattintson a mentés elemre. 

A folyamat végén egy új projekt nyílik meg a Visual Studio egy új példányában. 

> [!TIP]
> Az Azure-erőforrás eléréséhez bejelentkezett állapotban kell lennie. A beágyazott terminálon írja be az „az login” kifejezést, és kövesse az utasításokat. 

## <a name="submitting-experiment-with-the-new-project"></a>Kísérlet elküldése az új projekttel
Amikor a Visual Studióban megnyílt az új projekt, küldjön el egy feladatot egy számítási célnak (helyi vagy Dockerrel rendelkező virtuális gép).
A feladat elküldéséhez a következőt kell tennie: 
1. A megoldáskezelőben kattintson a jobb gombbal az elküldeni kívánt fájlra, majd válassza a **Set as Startup File** (Beállítás indítófájlként) elemet.
2. Válassza ki a projekt nevét, kattintson a jobb egérgombbal, majd válassza a **Submit Job...** (Feladat elküldése) elemet.
3. Egy új párbeszédpanel nyílik meg, amellyel kiválaszthatja a fürtöt (vagy a számítási célt) a szkript végrehajtásához.
4. Kattintson a **Submit** (Küldés) gombra

A feladat elküldését követően a beágyazott terminál megjeleníti a futtatások állapotát.

## <a name="view-list-of-jobs"></a>A feladatok listájának megtekintése
Az elküldött feladatokat a futtatási előzményekben tekintheti át.
1. A **Server Explorerben** kattintson az **AI Tools** elemre.
2. Ezután válassza az **Azure Machine Learning** elemet
3. Kattintson a **Jobs** (Feladatok) menüre.

A Feladattallózó a projekthez tartozó összes elküldött kísérletet megjeleníti. 

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A megnyitott Feladattallózóban kattintson az első elemre.
Így betöltődik a Feladatösszegző, illetve a Naplók és kimenetek panel.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Machine Learning konfigurálása IDE-vel való együttműködésre](./how-to-configure-your-IDE.md)
