---
title: Webszolgáltatás naplózása – Azure Machine Learning Studio (klasszikus) | Microsoft dokumentumok
description: Ismerje meg, hogyan engedélyezheti a naplózást a Machine Learning Studio (klasszikus) webes szolgáltatásokhoz. A naplózás további információkat nyújt az API-k hibaelhárításához.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217846"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Naplózás engedélyezése az Azure Machine Learning Studio (klasszikus) webes szolgáltatásaihoz

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez a dokumentum a Machine Learning Studio (klasszikus) webszolgáltatások naplózási képességéről nyújt tájékoztatást. A naplózás a hibaszámon és az üzeneten túl további információkat is tartalmaz, amelyek segíthetnek a Machine Learning Studio (klasszikus) API-kba irányuló hívások hibaelhárításában.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Webszolgáltatás naplózásának engedélyezése

Engedélyezi a naplózást az [Azure Machine Learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net) portáljáról. 

1. Jelentkezzen be az Azure Machine Learning Studio (klasszikus) webszolgáltatások portáljára a webhelyen. [https://services.azureml.net](https://services.azureml.net) Klasszikus webszolgáltatás esetén a portálhoz úgy is eljuthat, hogy a Studio (klasszikus) Machine Learning Studio (klasszikus) webszolgáltatások lapján az **Új webszolgáltatások élménye** elemre kattint.

   ![Új webszolgáltatások felhasználói hivatkozása](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsorban kattintson a **WebServices** elemre egy új webszolgáltatáshoz, vagy kattintson a **Klasszikus webszolgáltatások** elemre egy klasszikus webszolgáltatáshoz.

   ![Új vagy klasszikus webszolgáltatások kiválasztása](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatás esetén kattintson a webszolgáltatás nevére. Klasszikus webszolgáltatás esetén kattintson a webszolgáltatás nevére, majd a következő lapon kattintson a megfelelő végpontra.

4. A felső menüsorban kattintson a **Beállítás gombra.**

5. Állítsa a **Naplózás engedélyezése** beállítást *Hibára* (csak a hibák naplózásához) vagy *az Összes* (teljes naplózáshoz) értékre.

   ![Naplózási szint kiválasztása](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Mentés** gombra.

7. Klasszikus webes szolgáltatások hozlétre a **ml-diagnosztikai** tároló.

   Minden webszolgáltatás-naplók egy **ml-diagnosztika** nevű blob tárolóban tárolják a webszolgáltatáshoz társított tárfiókban. Új webszolgáltatások esetén ez a tároló a webszolgáltatás első elérésekor jön létre. Klasszikus webszolgáltatások, létre kell hoznia a tárolót, ha még nem létezik. 

   1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a webszolgáltatáshoz társított tárfiókot.

   2. A **Blob Service** területen kattintson a **Tárolók** lehetőségre.

   3. Ha a tartály **ml-diagnosztika** nem létezik, kattintson **a +Container**, adja meg a tároló nevét "ml-diagnosztika", és válassza ki az **Access típusát** a "Blob". Kattintson az **OK** gombra.

      ![Új tároló létrehozása a diagnosztikai naplók tárolásához](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Klasszikus webszolgáltatás esetén a Machine Learning Studio (klasszikus) Web Services irányítópultja is rendelkezik egy kapcsolóval a naplózás engedélyezéséhez. Mivel azonban a naplózás kezelése a WebServices portálon keresztül történik, engedélyeznie kell a naplózást a portálon keresztül a jelen cikkben leírtak szerint. Ha már engedélyezte a naplózást a Studio (klasszikus) alkalmazásban, akkor tiltsa le a naplózást, és engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezésének hatásai
Ha a naplózás engedélyezve van, a webszolgáltatás végpontjából származó diagnosztika és hibák a rendszer a rendszer a **ml-diagnosztikai** blob tárolóba kerül az Azure Storage-fiókban, amely a felhasználó munkaterületéhez kapcsolódik. Ez a tároló tartalmazza az összes diagnosztikai információt a webszolgáltatás végpontjai a tárfiókhoz társított összes munkaterülethez.

A naplók az Azure Storage-fiók felfedezéséhez rendelkezésre álló számos eszköz bármelyikével megtekinthetők. A legegyszerűbb az Azure Portalon lévő tárfiókhoz való navigálás, a **Tárolók**elemre, majd a tároló **ml-diagnosztikájára**való kattintás.  

## <a name="log-blob-detail-information"></a>A blob részleteinek naplózása
A tárolóban lévő minden blob pontosan az alábbi műveletek egyikének diagnosztikai adatait tartalmazza:

* A Kötegelt végrehajtás módszer végrehajtása  
* A kérelem-válasz módszer végrehajtása  
* Kérés-válasz tároló inicializálása

Az egyes blobok neve a következő előtaggal rendelkezik: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ahol _a naplótípusa_ az alábbi értékek egyike:  

* kötegelt  
* pontszám/kérések  
* pontszám/init  

