---
title: Webszolgáltatás naplózása – Azure Machine Learning Studio (klasszikus) | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a Machine Learning Studio (klasszikus) webszolgáltatások naplózását. Naplózás nyújt további információt talál az API-k hibaelhárítása.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217846"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások naplózásának engedélyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez a dokumentum a Machine Learning Studio (klasszikus) webszolgáltatások naplózási képességével kapcsolatos információkat tartalmaz. A naplózási szolgáltatás további információkat tartalmaz, amelyek csak egy adott számú és egy üzeneten felül segítenek a Machine Learning Studio (klasszikus) API-k hívásának hibakeresésében.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Webszolgáltatások naplózásának engedélyezése

Engedélyezi a naplózást a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net) portálján. 

1. Jelentkezzen be a Azure Machine Learning Studio (klasszikus) webszolgáltatások portálra a következő címen: [https://services.azureml.net](https://services.azureml.net). A klasszikus webszolgáltatások esetében az új webMachine Learning Studio **szolgáltatások** használata a Studióban (klasszikus) webszolgáltatások lapján is elérhetővé teheti a portált.

   ![Új webes szolgáltatási környezetet biztosít hivatkozás](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsorban kattintson az új webszolgáltatáshoz tartozó **webszolgáltatások** lehetőségre, vagy kattintson a klasszikus webszolgáltatások klasszikus webes **szolgáltatásai** lehetőségre.

   ![Válassza ki az új vagy a klasszikus webszolgáltatások](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatásként kattintson a webes szolgáltatás nevét. Klasszikus webszolgáltatás kattintson a webes szolgáltatás nevét és a következő lapon kattintson a megfelelő végpontra.

4. A felső menüsorban kattintson a **Konfigurálás**elemre.

5. Állítsa be a **naplózás engedélyezése** beállítást a *hiba* (csak a hibák naplózása) vagy az *összes* (teljes naplózáshoz) beállításnál.

   ![Válassza ki a naplózási szint](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Save** (Mentés) gombra.

7. A klasszikus webszolgáltatások esetében hozza létre a **ml-diagnosztika** tárolót.

   Az összes webszolgáltatás naplója egy **ml-diagnosztika** nevű blob-tárolóban marad a webszolgáltatáshoz társított Storage-fiókban. Az új webszolgáltatásoknál Ez a tároló jön létre az első alkalommal fér hozzá a web service. Klasszikus webszolgáltatásoknál kell létrehozni a tárolót, ha még nem létezik. 

   1. A [Azure Portal](https://portal.azure.com)lépjen a webszolgáltatáshoz társított Storage-fiókra.

   2. A **Blob Service** területen kattintson a **Tárolók** lehetőségre.

   3. Ha a tároló **ml-diagnosztika** nem létezik, kattintson a **+ tároló**elemre, adja meg a tárolónak a "ml-diagnosztika" nevet, és válassza a **hozzáférési típust** "blob"-ként. Kattintson az **OK** gombra.

      ![Hozzon létre egy új tárolót a diagnosztikai naplók tárolásához](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> A klasszikus webszolgáltatások esetében a Machine Learning Studio (klasszikus) webszolgáltatások irányítópultján is van egy kapcsoló a naplózás engedélyezéséhez. Azonban naplózási most már felügyelt, a Web Services portálon keresztül, mert, engedélyeznie kell a portálon keresztül naplózása ebben a cikkben leírtak szerint. Ha már engedélyezte a naplózást a Studióban (klasszikus), akkor a Web Services portálon tiltsa le a naplózást, majd engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezése hatásait
Ha a naplózás engedélyezve van, a webszolgáltatási végpont diagnosztika és hibák naplózása a felhasználó munkaterületéhez társított Azure Storage **-fiók ml-diagnosztika blob-** tárolójában történik. Ez a tároló összes a webszolgáltatás-végpontok a storage-fiókhoz társított összes munkaterülethez tartozó összes diagnosztikai adatokat tárolja.

A naplókban is megtekinthetők, a számos eszközt biztosít Ismerkedés az Azure Storage-fiók bármelyikével. A legegyszerűbb lehet, ha a Azure Portal a Storage-fiókra kattint **, kattintson a tárolók**elemre, majd kattintson a tároló **ml-diagnosztika**elemre.  

## <a name="log-blob-detail-information"></a>Blob részletes adatait
A tárolóban lévő minden egyes blob pontosan a következő műveletek egyikét tartozó diagnosztikai adatokat tartalmazza:

* A Batch-végrehajtási metódus végrehajtásának  
* A kérés-válasz metódus végrehajtásának  
* Kérés-válasz tároló inicializálása

Minden egyes blob neve van egy előtagot a következő formátumot követi: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ahol a _napló típusa_ a következő értékek egyike:  

* kötegelt  
* pontszám/kérelmek  
* pontszám/init  

