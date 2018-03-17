---
title: "Az Azure Machine Learning klasszikus webszolgáltatás átképezési hibaelhárítása |} Microsoft Docs"
description: "Határozza meg és javítsa ki a gyakori problémák közben, amikor az Azure Machine Learning webszolgáltatás van mind a modellben."
services: machine-learning
documentationcenter: 
author: garyericson
manager: hjerez
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 486f66e3d864a172ba301d017c12406ebafc4824
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Az Azure Machine Learning klasszikus webszolgáltatás átképezési hibaelhárítása
## <a name="retraining-overview"></a>Megőrzési áttekintése
Egy prediktív kísérletté pontozási webszolgáltatásként központi telepítésekor a rendszer egy olyan statikus modell. Amint az elérhetővé válik az új adatok, vagy ha a fogyasztó API rendelkezik saját adataikat, a modellnek támogatnia kell a retrained lehet. 

A megőrzési folyamat egy klasszikus webszolgáltatás részletes útmutatást lásd: [újratanítása Machine Learning modellek szoftveres](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Megőrzési folyamat
Ha a webszolgáltatás újratanítása van szüksége, hozzá kell adnia néhány további eleme:

* A tanítási kísérletet alapján telepített webes szolgáltatás. A kísérlet rendelkeznie kell egy **webes szolgáltatás kimeneti** modul kimenetével csatlakoztatva a **tanítási modell** modul.  
  
    ![A webes szolgáltatás kimeneti csatolása a tanítási modell.][image1]
* A pontozási webszolgáltatás hozzáadott új végpont.  A végpont programozott módon, a újratanítása Machine Learning modellek szoftveres hivatkozott mintakód is hozzáadhat a témakör vagy az Azure Machine Learning webszolgáltatások portálon keresztül.

A minta C#-kódban a képzés webszolgáltatás API súgó oldalról segítségével majd modell működik. Kiértékelték az eredményeket, és elégedett őket, miután a betanított modell használatával a hozzáadott új végpont webszolgáltatás pontozási frissítenie.

A hely minden a helyére a fő lépéseken, a modell újratanítása érdekében el kell végeznie a következők:

1. A képzési webszolgáltatás hívására: A tekintendő, amely számára a kötegelt végrehajtási szolgáltatás (BES), nem a kérelem válasz szolgáltatás (RR-EKET). Az API Súgó oldalon példakód C# segítségével a hívást. 
2. Az értékek keresése a *BaseLocation*, *RelativeLocation*, és *SasBlobToken*: ezeket az értékeket a rendszer adja vissza a kimenet a képzés webes szolgáltatás hívása. 
   ![a kimenet a megőrzési minta és az BaseLocation RelativeLocation és SasBlobToken értékeket jelenít meg.][image6]
3. A pontozási webszolgáltatásból a hozzáadott végpont frissítéséhez az új betanított modell: a felvett újonnan betanított modell pontozási modell a tanítási webszolgáltatás új végpont használatával programozott módon, a Machine Learning újratanítása modellek megadott mintakód frissítéséhez.

## <a name="common-obstacles"></a>Közös akadályok
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Ellenőrizze, hogy rendelkezik-e a megfelelő javítás URL-címe
A javítás URL-címet használ a pontozási webszolgáltatás hozzáadott új pontozási végponthoz társított egy kell lennie. Számos módon a javítás URL-cím beszerzése:

**1. lehetőség: programozottan**

A megfelelő javítás URL-cím beolvasása:

1. Futtassa a [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) példakód.
2. AddEndpoint kimenetében található a *HelpLocation* értékét, és másolja az URL-címet.
   
   ![A addEndpoint minta kimenet HelpLocation.][image2]
3. Illessze be az URL-címet egy böngészőben nyissa meg a webszolgáltatás súgóhivatkozások az olyan.
4. Kattintson a **frissítés erőforrás** jelenítse meg a javítás Súgó hivatkozásra.

**2. lehetőség: A portál használatához az Azure Machine Learning webszolgáltatások**

1. Jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portálon.
2. Kattintson a **webszolgáltatások** vagy **klasszikus webszolgáltatások** tetején.
4. A pontozási webszolgáltatás dolgozunk kattintson (az alapértelmezett nevet: a webszolgáltatás nem módosítható, ha azt lejár "[pontozás Exp.]").
5. Kattintson a **+ új**.
6. A végpont hozzáadása után kattintson a végpont neve.
7. A a **javítás** URL-t, kattintson a **API súgó** javítási súgó lapjának megnyitásához.

> [!NOTE]
> Ha hozzáadta a végpont a képzés webszolgáltatás helyett a prediktív webszolgáltatás, kapni fog a következő hiba kattintva a **frissítés erőforrás** hivatkozás: "Elnézést kérünk, de ez a funkció nem nem támogatott vagy nem érhető el Ebben a környezetben. Ez a webszolgáltatás nem frissíthető erőforrással rendelkezik. Azt a kényelmetlenségért elnézését kérjük és a munkafolyamat javítása dolgozik."
> 
> 

A javítás súgólap a javítás URL-címet tartalmaz kell használnia, és segítségével neki példakódot tartalmaz.

![Javítás URL-címe.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Ellenőrizze, hogy a helyes pontozási végpont frissítése
* A javítás nem a képzés webszolgáltatás: A javítási műveletet kell végrehajtani a pontozási webszolgáltatáson.
* A javítás nem az alapértelmezett végpont a webszolgáltatás: az új pontozási webes végpontot, amelyhez hozzáadta a a javítási műveletet kell végrehajtani.

Ellenőrizheti, hogy melyik webes szolgáltatás, a Web Services portál felkeresésével megtalálható a végpont. 

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a képzés webszolgáltatás kíván hozzáadni. Ha megfelelően telepítette, a képzési és a prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatások. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
> 
> 

1. Jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portálon.
2. Kattintson a **webszolgáltatások** vagy **klasszikus webszolgáltatások**.
3. Válassza ki a prediktív webszolgáltatás.
4. Győződjön meg arról, hogy az új végpont a webszolgáltatás lett hozzáadva.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Ellenőrizze, hogy a munkaterület ugyanabban a régióban, mint amelyen a webszolgáltatás
1. Jelentkezzen be [Learning Studio a gép](https://studio.azureml.net/).
2. A lap tetején kattintson a legördülő listából válassza ki a munkaterület.

   ![Machine learning régió felhasználói felületén.][image4]

3. Győződjön meg arról, hogy a munkaterület megtalálható a régió.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
