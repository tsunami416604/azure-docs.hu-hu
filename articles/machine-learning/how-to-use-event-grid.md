---
title: Eseményvezérelt gépi tanulási munkafolyamatok létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az eseményrácsot az Azure Machine Learning szolgáltatással az eseményvezérelt megoldások engedélyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129739"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Eseményvezérelt gépi tanulási munkafolyamatok létrehozása (előzetes verzió)

[Az Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) támogatja az Azure Machine Learning-eseményeket. Előfizethet és felhasználhatja az olyan eseményeket, mint a futtatási állapot megváltozott, a futtatás befejezése, a modell regisztrációja, a modell telepítése és az adateltolódás észlelése egy munkaterületen belül.

Az eseménytípusokról az [Azure Machine Learning-integráció](concept-event-grid-integration.md) az Event Griddel és az [Azure Machine Learning eseményrács-sémával](/azure/event-grid/event-schema-machine-learning)című témakörben talál.

Az Eseményrács használatával olyan gyakori eseteket engedélyez, mint például:

* E-mailek küldése üzemi hiba esetén és futtatás befejezésekor
* Az azure-függvény használata a modell regisztrálása után
* Események streamelése az Azure Machine Learningből különböző végpontokba
* A rendszer eltibóvás észlelésekor elindítja a hibaindító tanfolyamatt

> [!NOTE] 
> Jelenleg a runStatusChanged események csak akkor aktiválódnak, ha a futtatási állapot **sikertelen**
>

## <a name="prerequisites"></a>Előfeltételek
* Közreműködői vagy tulajdonosi hozzáférés az Azure Machine Learning-munkaterülethez, amelyhez eseményeket hoz létre.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Az EventGrid konfigurálása az Azure Portal használatával

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com) és nyissa meg az Azure Machine Learning-munkaterületet.

1. A bal oldali sávon válassza az __Események__ lehetőséget, majd válassza **az Esemény-előfizetések**lehetőséget. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Válassza ki a felhasználandó eseménytípust. Például a következő képernyőkép a Model registered , __Model deployed__, Run completed , and Dataset drift detected __(Modell regisztrálva__, Telepített modell , __Futtatás befejeződött__és __adatkészlet-eltolódás__) lehetőséget választotta:

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Válassza ki azt a végpontot, amelynek közzé szeretné tenni az eseményt. A következő képernyőképen az __Eseményközpont__ a kiválasztott végpont:

    ![kijelölés-eseménykezelő](./media/how-to-use-event-grid/select-event-handler.png)

Miután megerősítette a kijelölést, kattintson a __Létrehozás gombra.__ A konfiguráció után ezek az események a végpontra kerülnek.


### <a name="configure-eventgrid-using-the-cli"></a>Az EventGrid konfigurálása a CLI használatával

Telepítheti a legújabb [Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)vagy használhatja az Azure Cloud Shellt, amely az Azure-előfizetés részeként biztosított.

Az Event Grid bővítmény telepítéséhez használja a CLI következő parancsát:

```azurecli-interactive
az add extension --name eventgrid
```

A következő példa bemutatja, hogyan választhat ki egy Azure-előfizetést, és hogyan hoz létre egy új esemény-előfizetést az Azure Machine Learninghez:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>Események szűrése

Az események beállításakor szűrőket alkalmazhat csak az eseményindítókra az adott eseményadatokon. Az alábbi példában a futtatási állapot módosított események esetén futtatási típusok szerint szűrhet. Az esemény csak akkor aktiválódik, ha a feltételek teljesülnek. Tekintse meg az [Azure Machine Learning eseményrács sémája,](/azure/event-grid/event-schema-machine-learning) hogy az esemény adatok szűrése. 

1. Nyissa meg az Azure Portalon, válasszon ki egy új előfizetést vagy egy meglévőt. 

1. Jelölje ki a Szűrők lapot, és görgessen le a Speciális szűrők lapra. A **Kulcs** és **érték mezőben** adja meg a szűrni kívánt tulajdonságtípusokat. Itt láthatja, hogy az esemény csak akkor aktiválódik, ha a futtatástípusa folyamatfuttatás vagy folyamatlépés-futtatás.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="események szűrése":::

## <a name="sample-scenarios"></a>Mintaforgatókönyvek

### <a name="use-a-logic-app-to-send-email-alerts"></a>E-mailes értesítések küldése logikai alkalmazással

Használja ki [az Azure Logic Apps-t](https://docs.microsoft.com/azure/logic-apps/) az összes esemény e-mailjeinek konfigurálásához. Testreszabás feltételekkel, és adja meg a címzettek, hogy az együttműködés és a tudatosság a csapatok együtt dolgoznak.

1. Az Azure Portalon nyissa meg az Azure Machine Learning-munkaterületet, és válassza ki az események lapot a bal oldali sávon. Itt válassza a __Logikai alkalmazások lehetőséget.__ 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Jelentkezzen be a Logic App felhasználói felületére, és válassza a Machine Learning szolgáltatás témakörtípusként. 

    ![select-topic-típus](./media/how-to-use-event-grid/select-topic-type.png)

1. Válassza ki, hogy melyik esemény(ek)ről szeretne értesítést kapni. Például a következő képernyőkép __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Használhatja a szűrési módszert a fenti szakaszban, vagy szűrők hozzáadása csak a logikai alkalmazás aktiválásához az eseménytípusok egy részhalmazán. A következő képernyőképen a __/datadriftID/runs/__ __előtagszűrőt__ használja a rendszer.

    ![szűrőesemények](./media/how-to-use-event-grid/filtering-events.png)

1. Ezután adjon hozzá egy lépést az esemény felhasználásához és az e-mailek kereséséhez. Az események fogadására számos különböző e-mail fiók használható. Azt is beállíthatja, hogy mikor kell e-mail értesítést küldeni.

    ![select-email-művelet](./media/how-to-use-event-grid/select-email-action.png)

1. Válassza __az E-mail küldése__ lehetőséget, és töltse ki a paramétereket. A tárgyban az __eseménytípust__ és a __témakört__ is felveheti az események szűréséhez. Az üzenet törzsében futtatható futtatások munkaterületi lapjára mutató hivatkozást is megadhat. 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. A művelet mentéséhez válassza a lap bal sarkán a **Mentés másként** lehetőséget. A megjelenő jobb oldali sávról erősítse meg a művelet létrehozását.

    ![megerősítés-logika-alkalmazás-létrehozás](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Logikai alkalmazás használata átképzési munkafolyamatok aktiválásához adateltolódás esetén

A modellek idővel elavultak, és nem maradnak hasznosak abban a környezetben, amelyben fut. Az egyik módja annak, hogy megállapítsa, hogy itt az ideje, hogy újratanítsa a modell észlelése adateltolódás. 

Ebben a példában bemutatja, hogyan használhatja az eseményrácsot egy Azure Logic Alkalmazással az átképzés aktiválásához. A példa elindítja az Azure Data Factory folyamat, amikor az adatok eltolódása között történik a modell betanítása és az adatkészletek kiszolgálása.

Mielőtt elkezdené, hajtsa végre a következő műveleteket:

* Adatkészlet-figyelő beállítása a [munkaterületadat-eltolódásának észlelésére]( https://aka.ms/datadrift)
* Közzétett [Azure Data Factory-folyamat létrehozása.](https://docs.microsoft.com/azure/data-factory/)

Ebben a példában egy egyszerű Data Factory-folyamat fájlok másolására szolgál egy blob store és egy közzétett Machine Learning-folyamat futtatásához. Ebben a forgatókönyvben további információt a [Machine Learning-lépés](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) beállítása az Azure Data Factoryban című témakörben talál.

![adf-mlcsővezeték-szakasz](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Kezdje a logikai alkalmazás létrehozásával. Nyissa meg az [Azure Portalon,](https://portal.azure.com)keresse meg a Logikai alkalmazásokat, és válassza a Létrehozás lehetőséget.

    ![keresés-logika-alkalmazás](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Adja meg a kért adatokat. A felhasználói élmény egyszerűsítése érdekében használja ugyanazt az előfizetési és erőforráscsoportot, mint az Azure Data Factory Pipeline és az Azure Machine Learning munkaterület.

    ![beállítás-logika-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Miután létrehozta a logikai alkalmazást, válassza a __When An Event Grid erőforrásesemény eseményének bekövetkeztekor__lehetőséget. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Jelentkezzen be, és töltse ki az esemény részleteit. Állítsa be az __erőforrás nevét__ a munkaterület nevére. Állítsa az __eseménytípust__ __DatasetDriftDetected (DatasetDriftDetected .__

    ![bejelentkezési és add-esemény](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adjon hozzá egy új lépést, és keressen az __Azure Data Factory__. Válassza __a Folyamatfuttatás létrehozása__lehetőséget. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Jelentkezzen be, és adja meg a futtatandó közzétett Azure Data Factory-folyamatot.

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Mentse és hozza létre a logikai alkalmazást a lap bal felső részén található **Mentés** gombbal. Az alkalmazás megtekintéséhez nyissa meg a munkaterületet az [Azure Portalon,](https://portal.azure.com) és kattintson az **Események gombra.**

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Most az adat-gyári folyamat aktiválódik, ha sodródás történik. Az [új munkaterületi portálon](https://ml.azure.com)megtekintheti az adateltolódás im2-es futtatásának és a gépi tanulási folyamatnak a részleteit. 

![munkaterületen lévő nézet](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Címkéken alapuló modell üzembe helyezése az Azure Functions használatával

Az Azure Machine Learning modellobjektum olyan paramétereket tartalmaz, amelyeket a modell név, a verzió, a címke és a tulajdonság alapján hozhat el. A modell regisztrációs esemény egy végpontot válthat ki, és egy Azure-függvény használatával üzembe helyezhet egy modellt a paraméterek értéke alapján.

Például tekintse meg [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) a tárházat, és kövesse a readme fájl **lépéseit.**

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az elérhető eseményekről, tekintse meg az [Azure Machine Learning eseménysémáját](/azure/event-grid/event-schema-machine-learning)
