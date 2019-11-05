---
title: Webszolgáltatás naplózása – Azure Machine Learning Studio (klasszikus) | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a Machine Learning Studio (klasszikus) webszolgáltatások naplózását. A naplózás további információkat biztosít az API-k hibakereséséhez.
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
ms.openlocfilehash: 90ee036275e13b209d9f6c37aba6d16ed564cf5d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466956"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások naplózásának engedélyezése
Ez a dokumentum a Machine Learning Studio (klasszikus) webszolgáltatások naplózási képességével kapcsolatos információkat tartalmaz. A naplózási szolgáltatás további információkat tartalmaz, amelyek csak egy adott számú és egy üzeneten felül segítenek a Machine Learning Studio (klasszikus) API-k hívásának hibakeresésében.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Webszolgáltatások naplózásának engedélyezése

Engedélyezi a naplózást a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net) portálján. 

1. Jelentkezzen be a Azure Machine Learning Studio (klasszikus) webszolgáltatások portálra a következő címen: [https://services.azureml.net](https://services.azureml.net). A klasszikus webszolgáltatások esetében a portálra az **új webszolgáltatások** használata a Machine learning Studio (klasszikus) webszolgáltatások lapján, a Machine learning Studio klasszikus verziójában is elérhető.

   ![Új webszolgáltatási élmény hivatkozása](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsorban kattintson az új webszolgáltatáshoz tartozó **webszolgáltatások** lehetőségre, vagy kattintson a klasszikus webszolgáltatások klasszikus webes **szolgáltatásai** lehetőségre.

   ![Új vagy klasszikus webszolgáltatások kiválasztása](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatás esetén kattintson a webszolgáltatás nevére. Klasszikus webszolgáltatás esetén kattintson a webszolgáltatás nevére, majd a következő lapon kattintson a megfelelő végpontra.

4. A felső menüsorban kattintson a **Konfigurálás**elemre.

5. Állítsa be a **naplózás engedélyezése** beállítást a *hiba* (csak a hibák naplózása) vagy az *összes* (teljes naplózáshoz) beállításnál.

   ![Naplózási szint kiválasztása](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Save** (Mentés) gombra.

7. A klasszikus webszolgáltatások esetében hozza létre a **ml-diagnosztika** tárolót.

   Az összes webszolgáltatás naplója egy **ml-diagnosztika** nevű blob-tárolóban marad a webszolgáltatáshoz társított Storage-fiókban. Az új webszolgáltatások esetében ez a tároló jön létre, amikor először fér hozzá a webszolgáltatáshoz. A klasszikus webszolgáltatások esetében létre kell hoznia a tárolót, ha még nem létezik. 

   1. A [Azure Portal](https://portal.azure.com)lépjen a webszolgáltatáshoz társított Storage-fiókra.

   2. A **Blob Service** területen kattintson a **Tárolók** lehetőségre.

   3. Ha a tároló **ml-diagnosztika** nem létezik, kattintson a **+ tároló**elemre, adja meg a tárolónak a "ml-diagnosztika" nevet, és válassza a **hozzáférési típust** "blob"-ként. Kattintson az **OK** gombra.

      ![Hozzon létre egy új tárolót a diagnosztikai naplók tárolásához](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> A klasszikus webszolgáltatások esetében a Machine Learning Studio klasszikus verziójában a webszolgáltatások irányítópultján is van egy kapcsoló a naplózás engedélyezéséhez. Mivel azonban a naplózást most a webszolgáltatások portálon keresztül felügyelik, engedélyeznie kell a naplózást a portálon a jelen cikkben leírtak szerint. Ha már engedélyezte a naplózást a Studióban (klasszikus), akkor a Web Services portálon tiltsa le a naplózást, majd engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezésének hatásai
Ha a naplózás engedélyezve van, a webszolgáltatási végpont diagnosztika és hibák naplózása a felhasználó munkaterületéhez társított Azure Storage **-fiók ml-diagnosztika blob-** tárolójában történik. Ez a tároló tartalmazza az összes webszolgáltatási végponthoz tartozó diagnosztikai információt az ehhez a Storage-fiókhoz társított összes munkaterülethez.

A naplók az Azure Storage-fiók megismeréséhez rendelkezésre álló számos eszköz használatával tekinthetők meg. A legegyszerűbb lehet, ha a Azure Portal a Storage-fiókra kattint **, kattintson a tárolók**elemre, majd kattintson a tároló **ml-diagnosztika**elemre.  

## <a name="log-blob-detail-information"></a>A blob részletes adatainak naplózása
A tárolóban lévő összes blob a diagnosztikai adatokat a következő műveletek pontosan egyikének megfelelően tárolja:

* A batch-execution metódus végrehajtása  
* A kérelem-válasz módszer végrehajtása  
* Kérelem-válasz tároló inicializálása

Az egyes Blobok neve a következő formátumú előtaggal rendelkezik: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ahol a _napló típusa_ a következő értékek egyike:  

* kötegelt  
* pontszám/kérelmek  
* pontszám/init  

