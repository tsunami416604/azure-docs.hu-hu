---
title: A Machine Learning webszolgáltatások naplózása |} Microsoft Docs
description: Útmutató a Machine Learning webszolgáltatások naplózásának engedélyezéséről. Naplózási nyújt további információkat az API-k hibaelhárítás elősegítése érdekében.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 4e1545c8fd05795c683b24c029376a3d1e6d85b8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835831"
---
# <a name="enable-logging-for-machine-learning-web-services"></a>A naplózás engedélyezése a Machine Learning webszolgáltatásokhoz
Ez a dokumentum tájékoztatást nyújt a Machine Learning webszolgáltatások a naplózási szolgáltatás. Naplózási nyújt további információkat, csak olyan hiba száma és egy üzenetet, amely segíthet a hívások a Machine Learning API-k elhárításában.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Az adott webszolgáltatás naplózásának engedélyezése

A naplózás engedélyezése a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon. 

1. Jelentkezzen be az Azure Machine Learning webszolgáltatások portálon, a [ https://services.azureml.net ](https://services.azureml.net). A klasszikus webszolgáltatáshoz is kaphat a portálra kattintva **új webes felhasználói élményt** a Machine Learning webszolgáltatások oldalon a Machine Learning Studióban.

   ![Új webes felhasználói élményt hivatkozás](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsávban kattintson **webszolgáltatások** egy új a webes szolgáltatás, vagy kattintson a **klasszikus webszolgáltatások** klasszikus webszolgáltatás.

   ![Válassza ki az új vagy a klasszikus webszolgáltatások](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatás kattintson a webszolgáltatás nevét. A klasszikus webszolgáltatáshoz kattintson a webszolgáltatás nevét, és a következő lapon kattintson a megfelelő végpont.

4. A felső menüsávban kattintson **konfigurálása**.

5. Állítsa be a **naplózás engedélyezése** lehetőséggel *hiba* (csak a hibák bejelentkezni) vagy *összes* (a teljes körű naplózás).

   ![Válassza ki a naplózási szint](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Save** (Mentés) gombra.

7. Klasszikus webszolgáltatások, hozza létre a **ml-diagnosztika** tároló.

   Minden web service naplóit őrizze meg a következő nevű blobtárolóban **ml-diagnosztika** a webszolgáltatás társított tárfiókban. Az új webszolgáltatások ebben a tárolóban van jön létre, a webszolgáltatás elérésére. Klasszikus webszolgáltatások kell létrehozni a tárolót, ha még nem létezik. 

   1. Az a [Azure-portálon](https://portal.azure.com), keresse fel a webes szolgáltatáshoz tartozó tárfiók.

   2. A **Blob szolgáltatás**, kattintson a **tárolók**.

   3. Ha a tároló **ml-diagnosztika** nem létezik, kattintson a **+ tároló**, adjon a tároló a neve "ml-diagnosztika", és válassza ki a **hozzáférési típus** mint "Blob". Kattintson az **OK** gombra.

      ![Válassza ki a naplózási szint](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Az adott klasszikus webszolgáltatás a Web Services irányítópultján a Machine Learning Studióban is rendelkezik egy kapcsoló naplózás engedélyezéséhez. Azonban naplózási most már felügyelt, a Web Services portálon keresztül, mert szeretné a portálon keresztül naplózását, ebben a cikkben leírtak szerint. Ha már engedélyezte Studio van bejelentkezve, a Web Services portálon, tiltsa le a naplózást, és engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezése hatásait
Ha naplózása engedélyezve van, a diagnosztikai és a webszolgáltatás végpontja hibákat naplózza a **ml-diagnosztika** munkaterület a felhasználó kapcsolódik az Azure-Tárfiók a blob-tároló. Ez a tároló összes a webszolgáltatás végpontjainak tároló fiókhoz társított összes munkaterületek tartozó összes diagnosztikai adatokat tartalmazza.

A naplók bármely több rendelkezésre álló eszközök segítségével megismerheti az Azure-Tárfiók használatával tekintheti meg. A legegyszerűbb lehet, hogy a lépjen a tárfiókhoz, az Azure portálon kattintson a **tárolók**, majd kattintson a tároló **ml-diagnosztika**.  

## <a name="log-blob-detail-information"></a>Naplóadatok blob részletei
Minden egyes blob a tárolóban pontosan a következő műveletek egyikét tartozó diagnosztikai adatokat tartalmazza:

* A Batch-végrehajtási metódus egy végrehajtása  
* A kérés-válasz metódus egy végrehajtása  
* A kérés-válasz tároló inicializálása

Minden egyes blob neve a következő előtag van: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ha _típusú naplózása_ a következő értékek egyike:  

* kötegelt  
* pontszám/kérelem  
* pontszám/init  

