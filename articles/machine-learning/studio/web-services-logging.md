---
title: Webalkalmazás-szolgáltatás-naplózás – Azure Machine Learning Studióban |} A Microsoft Docs
description: Ismerje meg a Machine Learning webszolgáltatások naplózásának engedélyezése. Naplózás nyújt további információt talál az API-k hibaelhárítása.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 80a5ec64a2afd2367acaedd6e44ffe1a21e9c622
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474385"
---
# <a name="enable-logging-for-azure-machine-learning-studio-web-services"></a>Az Azure Machine Learning Studio-webszolgáltatások naplózásának engedélyezése
Ez a dokumentum információt nyújt a Machine Learning web Services naplózási képesség. Naplózás további információt, csak egy hiba száma és a egy üzenet, amely segíthet a hívásokat a Machine Learning API-k hibaelhárítása tartalmaz.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Webszolgáltatások naplózásának engedélyezése

A naplózás engedélyezése a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon. 

1. Jelentkezzen be az Azure Machine Learning Web Services portálon, a [ https://services.azureml.net ](https://services.azureml.net). Klasszikus webszolgáltatás, akkor is használhatja a portál kattintva **új webes szolgáltatási környezetet biztosít** a Machine Learning webszolgáltatások lapon, a Machine Learning Studióban.

   ![Új webes szolgáltatási környezetet biztosít hivatkozás](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsávon kattintson **webszolgáltatások** egy új a webes szolgáltatás, vagy kattintson a **klasszikus webszolgáltatások** klasszikus webszolgáltatás.

   ![Válassza ki az új vagy a klasszikus webszolgáltatások](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatásként kattintson a webes szolgáltatás nevét. Klasszikus webszolgáltatás kattintson a webes szolgáltatás nevét és a következő lapon kattintson a megfelelő végpontra.

4. A felső menüsávon kattintson **konfigurálása**.

5. Állítsa be a **naplózás engedélyezése** beállítást *hiba* (csak a hibák bejelentkezni) vagy *összes* (a teljes körű naplózás).

   ![Válassza ki a naplózási szint](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Save** (Mentés) gombra.

7. Klasszikus webszolgáltatásoknál, hozzon létre a **ml-diagnostics** tároló.

   Az összes webes szolgáltatás naplókat őrizze meg nevű blob-tárolót **ml-diagnostics** a web service társított storage-fiókban. Az új webszolgáltatásoknál Ez a tároló jön létre az első alkalommal fér hozzá a web service. Klasszikus webszolgáltatásoknál kell létrehozni a tárolót, ha még nem létezik. 

   1. Az a [az Azure portal](https://portal.azure.com), ugorjon a web service társított storage-fiókhoz.

   2. A **Blob Service** területen kattintson a **Tárolók** lehetőségre.

   3. Ha a tároló **ml-diagnosztika** nem létezik, kattintson a **+ tároló**, adjon a tárolót a neve "ml-diagnosztika", és válassza ki a **típus eléréséhez** mint "Blob". Kattintson az **OK** gombra.

      ![Válassza ki a naplózási szint](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Klasszikus webszolgáltatás a Machine Learning Studio, a webes szolgáltatások irányítópultján naplózásának engedélyezése egy kapcsoló is rendelkezik. Azonban naplózási most már felügyelt, a Web Services portálon keresztül, mert, engedélyeznie kell a portálon keresztül naplózása ebben a cikkben leírtak szerint. Ha már engedélyezte a naplózást a Studióban, a Web Services portálon letiltani a naplózást, majd engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezése hatásait
Ha naplózás engedélyezve van, a diagnosztikai és a web service-végpont hibák bejelentkezett a **ml-diagnosztika** a felhasználó munkaterületét csatolva az Azure Storage-fiókban lévő blobtárolóba. Ez a tároló összes a webszolgáltatás-végpontok a storage-fiókhoz társított összes munkaterülethez tartozó összes diagnosztikai adatokat tárolja.

A naplókban is megtekinthetők, a számos eszközt biztosít Ismerkedés az Azure Storage-fiók bármelyikével. A legegyszerűbb lehet, lépjen a tárfiókhoz az Azure Portalon, kattintson a **tárolók**, majd kattintson a tároló **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Blob részletes adatait
A tárolóban lévő minden egyes blob pontosan a következő műveletek egyikét tartozó diagnosztikai adatokat tartalmazza:

* A Batch-végrehajtási metódus végrehajtásának  
* A kérés-válasz metódus végrehajtásának  
* Kérés-válasz tároló inicializálása

Minden egyes blob neve van egy előtagot a következő formátumot követi: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ahol _napló típusa_ van a következő értékek egyikét:  

* kötegelt  
* pontszám/kérelmek  
* pontszám/init  

