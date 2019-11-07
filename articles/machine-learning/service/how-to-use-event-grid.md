---
title: Eseményvezérelt gépi tanulási munkafolyamatok létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható az Event Grid és a Azure Machine Learning az Event-vezérelt megoldások engedélyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622230"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Eseményvezérelt gépi tanulási munkafolyamatok létrehozása (előzetes verzió)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) támogatja a Azure Machine learning szolgáltatás eseményeit. Használhatja például a Futtatás befejezését, a modell regisztrációját, a modell üzembe helyezését és az adateltolódás észlelését egy munkaterületre hatókörön belül.

További információ: [Azure Machine learning integráció a Event Grid](concept-event-grid-integration.md) és az [Azure Machine learning Event Grid-sémával](/azure/event-grid/event-schema-machine-learning).

A Event Grid használatával engedélyezze a gyakori forgatókönyveket, például a következőket:

* Folyamatok újraképzésének indítása
* Azure Machine Learning különböző végpontokra irányuló folyamatos átviteli események

## <a name="prerequisites"></a>Előfeltételek

* Közreműködő vagy tulajdonosi hozzáférés az Azure Machine learning szolgáltatás munkaterületéhez, amelyekhez eseményeket fog létrehozni.
* Válasszon eseménykezelő-végpontot, például egy webhookot vagy egy Event hubot. További információ: [eseménykezelők](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Ha a Azure Event Gridt vagy Machine Learningt a 1 2019. november előtt használta, lehetséges, hogy újra regisztrálnia kell az erőforrás-szolgáltatókat, mielőtt a jelen dokumentumban ismertetett lépéseket végrehajtja. A szolgáltatók újbóli regisztrálásához kövesse az alábbi lépéseket:

1. Lépjen a Azure Portalra, és válassza az __előfizetések__lehetőséget. Válassza ki a használni kívánt előfizetést.
1. Válassza az __erőforrás-szolgáltatók__lehetőséget, majd keressen rá a __EventGrid__.
1. Válassza ki a __Microsoft. EventGrid__ bejegyzést, majd válassza az __újbóli regisztráció__lehetőséget.

    ![erőforrás-szolgáltató újbóli regisztrálása](media/how-to-use-event-grid/re-register-resource-provider.png)

1. Keresse meg a __MachineLearningServices__, válassza ki a bejegyzést, majd válassza az __újbóli regisztráció__lehetőséget.

> [!TIP]
> Ha nem rendelkezik a fenti lépések elvégzéséhez szükséges engedélyekkel, kérje meg az előfizetés rendszergazdáját, hogy hajtsa végre őket.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Gépi tanulási események konfigurálása a Azure Portal használatával

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és lépjen a Azure Machine learning munkaterületére.

1. A bal oldali sávon válassza az __események__ lehetőséget, majd válassza az **esemény-előfizetések**lehetőséget. 

    ![Select-events-in-Workspace. png](media/how-to-use-event-grid/select-event.png)

1. Válassza ki a használni kívánt eseménytípus típusát. Az alábbi képernyőképen például a __regisztrált modell__van kiválasztva, a __modell üzembe helyezése__, a __Futtatás befejeződött__és az __adatkészlet-eltolódás észlelhető__:

    ![hozzáadási esemény típusa](media/how-to-use-event-grid/add-event-type.png)

1. Válassza ki azt a végpontot, amelyen közzé kívánja tenni az eseményt. A következő képernyőképen az __Event hub__ a kiválasztott végpont:

    ![Select-Event-Handler](media/how-to-use-event-grid/select-event-handler.png)

Miután megerősítette a kijelölést, kattintson a __Létrehozás__gombra. A konfigurálás után ezeket az eseményeket a rendszer leküldi a végpontnak.

## <a name="set-up-azure-event-grid-using-cli"></a>Azure Event Grid beállítása a parancssori felület használatával

Telepítheti a legújabb [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t, vagy használhatja az Azure-előfizetésének részeként biztosított Azure Cloud Shell is.

A Event Grid bővítmény telepítéséhez használja az alábbi parancsot a parancssori felületről:

```azurecli-interactive
az add extension --name eventgrid
```

Az alábbi példa bemutatja, hogyan választhat ki egy Azure-előfizetést, majd hozzon létre egy új esemény-előfizetést a Azure Machine Learninghoz:

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

## <a name="sample-scenarios"></a>Használati példák

### <a name="use-a-logic-app-to-send-email-alerts"></a>E-mail értesítések küldése logikai alkalmazás használatával

Az összes eseményre vonatkozó e-mailek konfigurálásához használja a [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/) . Testre szabhatja a feltételeket, és megadhatja a címzetteket, hogy az együttműködés és a tájékoztatás lehetővé váljon a csapatok között.

1. A Azure Portal nyissa meg a Azure Machine Learning munkaterületet, és válassza a bal oldali sáv események lapját. Innen válassza a __Logic apps__lehetőséget. 

    ![Select-Logic-AP](media/how-to-use-event-grid/select-logic-ap.png)

1. Jelentkezzen be a logikai alkalmazás felhasználói felületére, és válassza a Machine Learning szolgáltatás lehetőséget a témakör típusaként. 

    ![Select-topic-Type](media/how-to-use-event-grid/select-topic-type.png)

1. Válassza ki, hogy mely esemény (ek) ra kell értesítést kapni. Például a következő képernyőkép- __RunCompleted__.

    ![Select-Event-runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. Emellett szűrőket is hozzáadhat, hogy csak a logikai alkalmazást aktiválja az eseménytípus egy részhalmazán. A következő képernyőképen a __/datadriftID/Runs/__ __előtag-szűrőjét__ használja a rendszer.

    ![szűrés – események](media/how-to-use-event-grid/filtering-events.png)

1. Ezután adjon hozzá egy lépést az esemény felhasználásához és az e-mailek kereséséhez. Az események fogadására több különböző levelezési fiók is használható. Megadhatja a feltételeket is, amikor e-mail riasztást küld.

    ![e-mail – művelet kiválasztása](media/how-to-use-event-grid/select-email-action.png)

1. Válassza az __E-mail küldése__ lehetőséget, és adja meg a paramétereket. A tárgyban megadhatja az __esemény típusát__ és a __témakört__ , hogy segítsen szűrni az eseményeket. A munkaterületre mutató hivatkozást is tartalmazhat, ha az üzenet törzsében fut. 

    ![konfigurálás – e-mail – törzs](media/how-to-use-event-grid/configure-email-body.png)

1. A művelet mentéséhez válassza a **Mentés másként** lehetőséget az oldal bal oldali sarkában. A megjelenő jobb oldali sávon erősítse meg a művelet létrehozását.

    ![Confirm-Logic-app-Create](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Logikai alkalmazás használata a munkafolyamatok átképzésének elindításához az adateltolódás esetén

A modellek elavultak az idő múlásával, és nem maradnak hasznosak abban a kontextusban, amelyben a fut. Az egyik módszer, ha meg szeretné tudni, hogy a modell újratanításához szükséges idő az adateltolódás észlelése. 

Ez a példa azt mutatja be, hogyan használható az Event Grid egy Azure Logic App-alkalmazással az újraképzés elindításához. A példa egy Azure Data Factory folyamatot indít el, amikor adateltolódás történik a modell betanítása és az adatkészletek kiszolgálása között.

Mielőtt elkezdené, hajtsa végre a következő műveleteket:

* Adatkészlet-figyelő beállítása a munkaterület [adateltolódásának észleléséhez]( https://aka.ms/datadrift)
* Közzétett [Azure Data Factory folyamat](https://docs.microsoft.com/azure/data-factory/)létrehozása.

Ebben a példában egy egyszerű Data Factory folyamatot használunk a fájlok blob-tárolóba történő másolásához és egy közzétett Machine Learning folyamat futtatásához. További információ erről a forgatókönyvről: [Machine learning lépés beállítása Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF – mlpipeline – fázis](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Kezdje a logikai alkalmazás létrehozásával. Lépjen a [Azure Portalra](https://portal.azure.com), keresse meg a Logic apps, majd kattintson a Létrehozás gombra.

    ![Keresés – Logic-app](media/how-to-use-event-grid/search-for-logic-app.png)

1. Adja meg a kért információkat. A felhasználói élmény egyszerűsítése érdekében használja ugyanazt az előfizetést és erőforráscsoportot, mint a Azure Data Factory folyamat és a Azure Machine Learning munkaterület.

    ![beállítás-Logic-app-for-ADF](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Miután létrehozta a logikai alkalmazást, válassza ki, __hogy mikor történjen egy Event Grid erőforrás esemény__. 

    ![Select-Event-Grid-trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Jelentkezzen be, és adja meg az esemény részleteit. Állítsa az __erőforrás nevét__ a munkaterület nevére. Állítsa az __DatasetDriftDetected__az __esemény típusára__ .

    ![bejelentkezés és Hozzáadás – esemény](media/how-to-use-event-grid/login-and-add-event.png)

1. Adjon hozzá egy új lépést, és keressen rá __Azure Data Factory__. Válassza __a folyamat futtatásának létrehozása__lehetőséget. 

    ![létrehozás – adfpipeline – Futtatás](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Jelentkezzen be, és adja meg a közzétett Azure Data Factory folyamat futtatását.

    ![-ADF-folyamat meghatározása](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Mentse és hozza létre a logikai alkalmazást a lap bal felső részén található **Mentés** gombbal. Az alkalmazás megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) munkaterületére, majd kattintson az **események**elemre.

    ![Logic-app-webhook megjelenítése](media/how-to-use-event-grid/show-logic-app-webhook.png)

Ekkor a rendszer a folyamatban lévő adatfeldolgozási folyamatot indítja el a drift bekövetkeztekor. Tekintse meg az adatdrift futtatásával és a Machine learning folyamatával kapcsolatos adatokat az [Új munkaterület portálon](https://ml.azure.com). 

![munkaterület megtekintése](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>A Azure Functions használata a modellek címkék alapján történő üzembe helyezéséhez

Az Azure Machine Learning Model objektum olyan paramétereket tartalmaz, amelyekkel az üzemelő példányok elhelyezhetők, például a modell neve, verziója, címkéje és tulajdonsága. A modell regisztrációs eseménye elindíthat egy végpontot, és használhat egy Azure-függvényt egy modell üzembe helyezéséhez a paraméterek értéke alapján.

Példaként tekintse meg a [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) adattárat, és kövesse a **readme** fájlban található lépéseket.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az elérhető eseményekről, tekintse meg az [Azure Machine learning esemény sémáját](/azure/event-grid/event-schema-machine-learning) .