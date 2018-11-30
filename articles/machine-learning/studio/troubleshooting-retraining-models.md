---
title: A Machine Learning Studio klasszikus webszolgáltatások – Azure átképezési hibaelhárítása |} A Microsoft Docs
description: Azonosítsa és javítsa ki a gyakori problémák észlelt.%12%0, ha meg vannak átképezési a az Azure Machine Learning Studio webszolgáltatás-modell.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 1105b81d0f8ba80bd76bcdf140fe79b9e8a7102d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307202"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-studio-classic-web-service"></a>A klasszikus Azure Machine Learning Studio webszolgáltatás átképezési hibaelhárítása
## <a name="retraining-overview"></a>Megőrzési áttekintése
Amikor telepít egy prediktív kísérletet pontozási webszolgáltatásként, egy statikus modellt. Az új adatok válnak elérhetővé, vagy ha a fogyasztói API-rendelkezik saját adataikat, a modell kell lennie retrained. 

Klasszikus webszolgáltatás megőrzési folyamat részletes útmutatást lásd: [Retrain Machine Learning modellek programozott módon](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Megőrzési folyamat
Ha a webszolgáltatás újratanítása van szüksége, hozzá kell adnia néhány további információt:

* A betanítási kísérlet alapján telepített webes szolgáltatás. Rendelkeznie kell a kísérletet a **webes szolgáltatás kimeneti** modul kimenete csatlakozik a **tanítási modell** modul.  
  
    ![A web service kimeneti csatolása a tanítási modell.][image1]
* A pontozási webszolgáltatás hozzáadott új végpont.  A végpont programozott módon azzal a mintakóddal, a Machine Learning modellek szoftveres Átképezése hivatkozott programozott módon is hozzáadhat a témakör vagy az Azure Machine Learning webszolgáltatások portálján.

Ezután használhatja a minta C# API súgóoldalt a betanítási webszolgáltatás újratanítása modell a kódot. Rendelkezik értékeli ki az eredményeket, és a velük teljesültek, frissítse a betanított modell pontozási webszolgáltatás a hozzáadott új végpont használatával.

A hely minden a helyére a fő lépések szükségesek a modell újratanítása a következők:

1. Az oktatási webes szolgáltatás hívása: A hívás nem, a Batch Execution Service (BES), nem a Request Response Service (RRS). Használhatja a minta C# kódot az API help oldalon a híváshoz. 
2. Keresse meg a tartozó értékeket a *BaseLocation*, *RelativeLocation*, és *SasBlobToken*: ezeket az értékeket a rendszer adja vissza a kimenetben a betanítási webszolgáltatás hívás. 
   ![a kimenet a megőrzési minta és a BaseLocation RelativeLocation és SasBlobToken értékek megjelenítése.][image6]
3. A hozzáadott a pontozási webszolgáltatás-végpont frissítése az új betanított modell: azzal a mintakóddal, programozott módon, a Machine Learning modellek szoftveres Átképezése megadott frissítése az új végpont a pontozási modelljével az újonnan betanított modell hozzáadott a Képzési webszolgáltatás.

## <a name="common-obstacles"></a>Gyakori akadályai
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Ellenőrizze, hogy van-e a megfelelő JAVÍTÁSI URL-címe
A JAVÍTÁSI URL-címet használ a pontozási webszolgáltatás hozzáadott új pontozási végponthoz társított egy kell lennie. Számos módon a JAVÍTÁSI URL-cím:

**1. lehetőség: programozottan**

A megfelelő JAVÍTÁSI URL-Címének lekéréséhez:

1. Futtassa a [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) mintakódot.
2. Az AddEndpoint kimenetében található a *HelpLocation* értékét, és másolja az URL-címet.
   
   ![A kimenetben addEndpoint minta HelpLocation.][image2]
3. Illessze be az URL-címet egy böngészőben nyissa meg egy lapot, amely biztosítja a webszolgáltatás súgóhivatkozások.
4. Kattintson a **erőforrás frissítése** nyissa meg a patch Súgó hivatkozásra.

**2. lehetőség: Az Azure Machine Learning Web Services portál használata**

1. Jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portálon.
2. Kattintson a **webszolgáltatások** vagy **klasszikus webszolgáltatások** tetején.
4. Kattintson a pontozási webszolgáltatás dolgozik (Ha nem módosítja az alapértelmezett nevet, a webszolgáltatás, azt megszűnik az "[pontozás Exp.]").
5. Kattintson a **+ új**.
6. A végpont hozzáadása után kattintson a végpont neve.
7. Alatt a **javítás** URL-CÍMÉT, kattintson a **API Help** nyissa meg a javítási segítséget.

> [!NOTE]
> Ha a végpont a betanítási Web Service helyett a prediktív webszolgáltatás hozzáadta, a következő hibaüzenetet kap, kattintva a **erőforrás frissítése** hivatkozás: "hiba, de ez a funkció nem támogatott vagy nem érhető el Ebben a környezetben. A webszolgáltatás nem rendelkezik nem frissíthető erőforrásokkal. Hogy kérünk az okozott kellemetlenségekért, és dolgozunk azon, hogy ez a munkafolyamat javítására."
> 
> 

A PATCH súgóoldalt tartalmazza, a JAVÍTÁSI URL-címet kell használnia, és segítségével meghívására mintakódot biztosít.

![Javítási URL-címe.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Ellenőrizze, hogy a megfelelő pontozási végpont frissítése
* A képzési webszolgáltatás javítás nem: A javítási műveletet kell végrehajtani a pontozási webszolgáltatásban.
* Az alapértelmezett végpont a web service a javítás nem: A javítási műveletet kell végrehajtani az új pontozási webszolgáltatás végpontja hozzáadott a.

Ellenőrizheti, hogy melyik webes szolgáltatás, a végpont a Web Services portál meglátogatása szerint be van kapcsolva. 

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a betanítási webszolgáltatás ad hozzá. Ha megfelelően telepítette, egy tanítási és a egy prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatást. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
> 
> 

1. Jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portálon.
2. Kattintson a **webszolgáltatások** vagy **a klasszikus webszolgáltatások**.
3. Válassza ki a prediktív webszolgáltatásait.
4. Az új végpont hozzáadásának ellenőrzéséhez a webszolgáltatást.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Ellenőrizze, hogy a munkaterület a web service ugyanabban a régióban
1. Jelentkezzen be a [Machine Learning Studióba](https://studio.azureml.net/).
2. A képernyő felső részén kattintson a legördülő listából válassza ki a munkaterületeket.

   ![Machine learning régió felhasználói felületén.][image4]

3. Ellenőrizze a régiót, amelyben a munkaterület megtalálható.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
