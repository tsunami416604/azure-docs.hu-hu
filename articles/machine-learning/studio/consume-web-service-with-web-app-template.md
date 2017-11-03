---
title: "A Machine Learning webszolgáltatásba web app sablonnal felhasználása |} Microsoft Docs"
description: "Azure piactér web app sablon segítségével az Azure Machine Learning a prediktív webszolgáltatás felhasználását."
keywords: "a gépi tanulás webszolgáltatás, operationalization, REST API-t"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Azure Machine Learning webszolgáltatások használata webalkalmazás-sablonnal

Egyszer már a prediktív modell fejlesztett, és telepítve lett webszolgáltatásként az Azure Machine Learning Studio használatával, vagy eszközökkel, például az R vagy Python, hozzáférhet a operationalized modell egy REST API használatával.

Számos módja a REST API-t használnak, és a webes szolgáltatás. Például C#, R vagy Python hozta létre a webszolgáltatás üzembe helyezésekor mintakód használatával írhat egy alkalmazás (elérhető a [Machine Learning Web Services portálra](https://services.azureml.net/quickstart) vagy a webes szolgáltatás irányítópultján a Machine Learning Studióban). Vagy a minta Microsoft Excel-munkafüzet hozott létre az Ön egyszerre is használhatja.

A leggyorsabb és legegyszerűbb módja a webes szolgáltatás eléréséhez keresztül elérhető a Web App sablon, de a [Azure Web App piactér](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Az Azure Machine Learning Web App sablonok
A webes alkalmazás sablonok érhető el az Azure piactéren hozhat létre egy egyéni webalkalmazást, hogy ismeri a webszolgáltatás bemeneti adatok és a kívánt eredmény elérése érdekében. Mindössze annyit kell tennie hozzáférést a webes alkalmazás a webszolgáltatás és az adatokat, és a sablon elvégzi a többit, azaz.

Két sablonok érhetők el:

* [Az Azure ML kérés-válasz szolgáltatás Web App-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML kötegelt végrehajtási szolgáltatás Web App-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Minden sablon létrehoz egy minta ASP.NET alkalmazást, az API-URI és kulcs használatával a webszolgáltatáshoz és az Azure web helyként telepíti. A kérés-válasz szolgáltatás (RR-EKET) sablont hoz létre egy webalkalmazást, amely lehetővé teszi a webszolgáltatás számára, hogy egyetlen eredmény egyetlen sornyi adatot küldeni. A kötegelt végrehajtási szolgáltatás (BES) sablont hoz létre egy webalkalmazást, amely lehetővé teszi, hogy hány sornyi adatot több lekéréséhez küldhet.

Ezek a sablonok használatához nem kódolás szükséges. Csak akkor adja meg, az API-kulcsot és URI, és a sablon épít fel az alkalmazást.

Az API-kulcs és a kérelem URI-azonosítója lekérése egy webszolgáltatás-bővítmény:

1. Az a [Web Services portálra](https://services.azureml.net/quickstart), egy új webszolgáltatás kattintson **webszolgáltatások** tetején. Vagy a klasszikus web service kattintson **klasszikus webszolgáltatások**.
2. Kattintson a használni kívánt webszolgáltatáshoz.
3. Klasszikus webszolgáltatáshoz kattintson a használni kívánt végpont.
4. Kattintson a **felhasználás** tetején.
5. Másolás a **elsődleges** vagy **másodlagos kulcs** és mentse azt.
6. Ha a kérés-válasz szolgáltatás (RR-EKET) sablont hoz létre, másolja a **kérés-válasz** URI és mentse azt. Ha egy kötegelt végrehajtási szolgáltatás (BES) sablont hoz létre, másolja a **kötegelt kérelmekben** URI és mentse azt.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>A kérés-válasz szolgáltatás (RR-EKET) sablon használata
Kövesse az alábbi lépéseket ahhoz, hogy a RR-EKET web app sablon használható az alábbi ábrán is látható.

![Folyamat RR-EKET webes sablon használata][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Lépjen a [Azure-portálon](https://portal.azure.com), **bejelentkezési**, kattintson **új**, és adja meg **Azure ML kérés-válasz szolgáltatás Web App**, majd kattintson a **létrehozása**. 
   
   * Adjon meg egy egyedi nevet a webalkalmazás. A webalkalmazás URL-CÍMÉT a nevét, majd lesz `.azurewebsites.net.` például`http://carprediction.azurewebsites.net.`
   * Válassza ki az Azure-előfizetés és az alatt a webszolgáltatás fut. szolgáltatások.
   * Kattintson a **Create** (Létrehozás) gombra.
     
     ![Webalkalmazás létrehozása][image5]

4. Ha Azure webalkalmazás telepítése befejeződött, kattintson a **URL-cím** az Azure-ban a webalkalmazás-beállítások lapon, vagy adja meg az URL-címet egy webböngészőben. Például: `http://carprediction.azurewebsites.net.`
5. A webes alkalmazás első futtatásakor, meg kell adni a **API Post URL-címe** és **API-kulcs**.
   Adja meg a korábban mentett értékeket (**kérelem URI-azonosítója** és **API-kulcs**, illetve).
     
     Kattintson a **nyújt**.
     
     ![Adja meg a feladás egy vagy több URI és az API-kulcs][image6]

6. A webes alkalmazás megjelenik a **Web App konfigurációs** az aktuális webes szolgáltatás beállításait tartalmazó lapra. Itt módosíthatja a beállításokat, a webes alkalmazás által használt.
   
   > [!NOTE]
   > Itt a szolgáltatás beállításainak módosítása csak akkor változik meg azokat a webalkalmazás. Ez az alapértelmezett beállításokat, a webszolgáltatás nem változik. Például, ha módosítja a **leírás** itt nem módosítja a webes szolgáltatás irányítópultján a Machine Learning Studióban látható leírás.
   > 
   > 
   
    Amikor elkészült, kattintson a **módosítások mentése**, és kattintson a **Ugrás a kezdőlapra**.

7. A kezdőoldalon a webszolgáltatás küldendő értékeket adhat meg. Kattintson a **Submit** amikor elkészült, és az eredményt adja vissza.

Ha vissza szeretne a **konfigurációs** lap megnyitásához válassza a `setting.aspx` a webes alkalmazás lapján. Például: `http://carprediction.azurewebsites.net/setting.aspx.` kérni fogja a írja be újra az API-kulcs - van szüksége, amely a lap eléréséhez, és frissítse a beállításokat.

Állítsa le, indítsa újra, vagy törölje a webalkalmazást, mint bármely más webalkalmazás az Azure portálon. Mindaddig, amíg fut-e otthoni webcímére navigálhat, és adja meg új értékeket.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>A kötegelt végrehajtási szolgáltatás (BES) sablon használata
A BES web app sablon használhatja ugyanúgy, mint az RRS sablon, azzal a különbséggel, hogy a létrehozott webalkalmazás lehetővé teszi több sornyi adatot nyújt, és több eredményeket.

A bemeneti értékek egy kötegelt végrehajtási webszolgáltatáshoz származhatnak az Azure storage vagy a helyi fájl; az eredményeket egy az Azure storage-tároló tárolja.
Igen szüksége lesz egy Azure storage tárolót a webes alkalmazás által visszaadott eredményeit, és kész állapotba hozásához a bemeneti adatok lesz szüksége.

![Folyamat BES webes sablon használata][image2]

1. Kövesse ugyanezt az eljárást, mint a RR-EKET sablon, kivéve, nyissa meg a BES webalkalmazás létrehozása [Azure ML kötegelt végrehajtási szolgáltatás Web App sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) nyissa meg a BES sablont az Azure piactérről elemre kattintva **webalkalmazás létrehozása**.

2. Adja meg a tárolt eredményt, írja be a cél tároló adatokat a webes alkalmazás kezdőlapján. Ha a webes alkalmazás kérheti le a bemenő érték, vagy egy helyi fájl vagy egy Azure storage-tárolót is megadhat.
   Kattintson a **nyújt**.
   
    ![Szolgáltatás adatai][image7]

A web app egy feladat állapota lapon jelenik meg.
A feldolgozás befejezése lesz az eredményeket az Azure blob storage helyének adni. Lehetősége is van, az eredményeket egy helyi fájl letöltése.

## <a name="for-more-information"></a>További információ
További részletek...

* Tekintse meg a Machine Learning Studio, a gépi tanulási kísérlet létrehozása [az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md)
* a gépi tanulási kísérlet egy webszolgáltatás telepítése tudnivalókat [az Azure Machine Learning webszolgáltatás telepítése](publish-a-machine-learning-web-service.md)
* a webes szolgáltatás más módon lásd [hogyan kell használni az Azure Machine Learning Web service](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
