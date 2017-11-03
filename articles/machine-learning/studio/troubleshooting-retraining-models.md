---
title: "Az Azure Machine Learning klasszikus webszolgáltatás átképezési hibaelhárítása |} Microsoft Docs"
description: "Határozza meg és javítsa ki a gyakori problémák közben, amikor az Azure Machine Learning webszolgáltatás van mind a modellben."
services: machine-learning
documentationcenter: 
author: VDonGlover
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 85cf9175bb4a5f253c7b47b2edc3ac8b00616ba2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Az Azure Machine Learning klasszikus webszolgáltatás átképezési hibaelhárítása
## <a name="retraining-overview"></a>Megőrzési áttekintése
Egy prediktív kísérletté pontozási webszolgáltatásként központi telepítésekor a rendszer egy olyan statikus modell. Amint az elérhetővé válik az új adatok, vagy ha a fogyasztó API rendelkezik saját adataikat, a modellnek támogatnia kell a retrained lehet. 

A megőrzési folyamat klasszikus webszolgáltatás részletes útmutatást lásd: [újratanítása Machine Learning modellek szoftveres](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Megőrzési folyamat
Ha a webszolgáltatás újratanítása van szüksége, hozzá kell adnia néhány további eleme:

* A tanítási kísérletet alapján telepített webes szolgáltatás. A kísérlet rendelkeznie kell egy **webes szolgáltatás kimeneti** modul kimenetével csatlakoztatva a **tanítási modell** modul.  
  
    ![A webes szolgáltatás kimeneti csatolása a tanítási modell.][image1]
* A pontozási webszolgáltatás hozzáadott új végpont.  A végpont programozott módon, a újratanítása Machine Learning modellek szoftveres hivatkozott mintakód is hozzáadhat a témakör vagy a klasszikus Azure portálon keresztül.

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

**2. lehetőség: A klasszikus Azure portál használata**

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Nyissa meg a Machine Learning lapját. ![Gép leaning lapján.][image4]
3. A munkaterület neve, majd kattintson **webszolgáltatások**.
4. Kattintson a pontozási webszolgáltatás dolgozik. (Ha nem módosította az alapértelmezett nevet: a webes szolgáltatás, akkor fejeződik [pontozási Exp.] a.)
5. Kattintson a **végpont hozzáadása**.
6. A végpont hozzáadása után kattintson a végpont neve. Kattintson a **frissítés erőforrás** javítási súgó lapjának megnyitásához.

> [!NOTE]
> Ha hozzáadta a végpont a képzés webszolgáltatás helyett a prediktív webszolgáltatás, kapni fog a következő hiba kattintva a **frissítés erőforrás** hivatkozás: Sajnos, de ez a funkció nem támogatott vagy nem érhető el ebben a környezetben. Ez a webszolgáltatás nem frissíthető erőforrással rendelkezik. Azt a kényelmetlenségért elnézését kérjük, és a munkafolyamat javítása dolgoznak.
> 
> 

![Új endpoint irányítópult.][image3]

A javítás súgólap a javítás URL-címet tartalmaz kell használnia, és segítségével neki példakódot tartalmaz.

![Javítás URL-címe.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Ellenőrizze, hogy a helyes pontozási végpont frissítése
* A javítás nem a képzés webszolgáltatás: A javítási műveletet kell végrehajtani a pontozási webszolgáltatáson.
* A javítás nem az alapértelmezett végpont webszolgáltatás: az új pontozási webes végpontot, amelyhez hozzáadta a a javítási műveletet kell végrehajtani.

Ellenőrizheti, hogy melyik webes szolgáltatás, a klasszikus Azure portál felkeresésével megtalálható a végpont. 

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a képzés webszolgáltatás kíván hozzáadni. Ha megfelelően telepítette, a képzési és a prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatások. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
> 
> 

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Nyissa meg a Machine Learning lapját. ![Machine learning-munkaterület felhasználói felületén.][image4]
3. A munkaterület kiválasztása.
4. Kattintson a **webszolgáltatások**.
5. Válassza ki a prediktív webszolgáltatás.
6. Győződjön meg arról, hogy az új végpont a webszolgáltatás lett hozzáadva.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Ellenőrizze a munkaterület, amely a webszolgáltatás annak érdekében, hogy a helyes régióban található
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Válassza ki a Machine Learning a menüből.
   ![Machine learning régió felhasználói felületén.][image4]
3. Ellenőrizze a munkaterület helyét.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
