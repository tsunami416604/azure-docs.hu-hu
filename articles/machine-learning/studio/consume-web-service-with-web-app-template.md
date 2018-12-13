---
title: Webalkalmazás-sablon – az Azure Machine Learning Studio-Studio-webszolgáltatás felhasználása |} A Microsoft Docs
description: Az Azure Marketplace-en webalkalmazás-sablonok segítségével az Azure Machine Learning prediktív webszolgáltatás felhasználása.
keywords: webszolgáltatás, operacionalizálás, REST API-t, a machine learning
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 6a6da1af937338637cd875abe4e9d539b2e2b6e2
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273884"
---
# <a name="consume-an-azure-machine-learning-studio-web-service-by-using-a-web-app-template"></a>Az Azure Machine Learning Studio webszolgáltatás felhasználhatják a webalkalmazás-sablonok használatával

A prediktív modellek fejlesztése, és helyezze üzembe, az Azure web service használatával:
- Az Azure Machine Learning Studióban.
- Eszközök, például az R vagy Python. 

Ezt követően a szolgáltatáscsomagot modell elérheti, ha egy REST API-val.

Nincsenek számos módon a REST API felhasználására, és a hozzáférés a webszolgáltatáshoz. Ha például írhat egy alkalmazást C#, R vagy Python használatával, a webszolgáltatás üzembe helyezésekor kapott mintakódot. (A mintakód megtalálható a [Machine Learning webszolgáltatások portálján](https://services.azureml.net/quickstart) vagy a Machine Learning Studio webszolgáltatási irányítópultján található.) Vagy egy időben az Ön számára létrehozott Microsoft Excel-munkafüzetmintát is használhatja.

A leggyorsabb és legegyszerűbb módszert kínálja a webszolgáltatás elérésére keresztül a webalkalmazás-sablonokat érhető el, de a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/web-applications/all/).



## <a name="azure-machine-learning-web-app-templates"></a>Azure Machine Learning web app-sablonok
A webalkalmazás-sablonokat elérhető az Azure piactéren, hogy ismeri a webszolgáltatás bemeneti adatokat és várt eredmény egyéni webalkalmazásokat hozhat létre. Mást nem kell tennie a webes alkalmazás hozzáférést adhat a webszolgáltatás és az adatokat, és a sablon végzi.

A két sablon érhetők el:

* [Az Azure Machine Learning-kérés-válasz szolgáltatás webalkalmazás-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Az Azure ML kötegelt végrehajtási szolgáltatás webalkalmazás-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Mindegyik sablon az API URI használatával hoz létre egy ASP.NET-mintaalkalmazás és kulcsával a webszolgáltatást. A sablon majd üzembe helyezi az alkalmazást, mint egy webhelyet az Azure-bA. 

A kérés-válasz szolgáltatás (RRS) sablont hoz létre egy webalkalmazást, amely segítségével egyetlen sor az adatok küldése a web Service egyszeri eredmény. A Batch Execution Service (BES) sablont hoz létre egy webalkalmazást, amely segítségével az adatok több eredményt sorok számát küldése.

Ezekkel a sablonokkal kódolása nem szükséges. Csak akkor adja meg, az API-kulcsát és URI-t, és a sablon hozza létre az alkalmazás az Ön számára.

Az API-kulcs beszerzése és a kérés URI-ja egy webszolgáltatás:

1. Az a [Web Services portál](https://services.azureml.net/quickstart)válassza **webszolgáltatások** tetején. Vagy válasszon ki egy klasszikus webszolgáltatás **klasszikus webszolgáltatások**.
2. Válassza ki a webszolgáltatás, amelyet el szeretne érni.
3. A klasszikus webszolgáltatások válassza ki a végpont, amelyet el szeretne érni.
4. Válassza ki **felhasználás** tetején.
5. Másolja az elsődleges vagy másodlagos kulcsot, és mentse.
6. Ha egy RRS-sablont hoz létre, másolja a **kérés-válasz** URI-t, és mentse. A BES-sablont hoz létre, másolja a **kérések** URI-t, és mentse.


## <a name="how-to-use-the-request-response-service-template"></a>A kérés-válasz szolgáltatás sablon használata
Kövesse az alábbi lépéseket az RRS webalkalmazás-sablon, használatához, az alábbi ábrán látható módon.

![Folyamat RRS webes sablon használata][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **új**, keresése és kiválasztása **Azure ML kérés-válasz szolgáltatás webalkalmazás**, majd válassza ki **létrehozás**. 
3. Az a **létrehozás** panelen:
   
   * Adjon meg egy egyedi nevet a webalkalmazás. A web app URL-címe lesz a nevét, majd **. azurewebsites.net**. Például **http://carprediction.azurewebsites.net**.
   * Válassza ki az Azure-előfizetés és a szolgáltatások, amelyek alapján a webszolgáltatás fut.
   * Kattintson a **Létrehozás** gombra.
     
   ![Webalkalmazás létrehozása][image5]

4. Ha az Azure webalkalmazás telepítése befejeződött, válassza ki a **URL-cím** az Azure-ban a webalkalmazás-beállítások lapon, vagy adja meg az URL-CÍMÉT egy webböngészőben. Adja meg például **http://carprediction.azurewebsites.net**.
5. Amikor a webalkalmazás első alkalommal futtatja, a kéri a **API Post URL-Címének** és **API-kulcs**. Adja meg a korábban mentett értékeket (URI-t és az API-kulcsát, illetve kérés). Válassza ki **elküldése**.
     
   ![Adja meg a post URI és az API-kulcsát][image6]

6. A webes alkalmazás megjelenik a **webalkalmazás konfigurációjának** az aktuális webes szolgáltatás beállításait tartalmazó oldalt. Itt módosíthatja a beállításait, a webes alkalmazás által használt.
   
   > [!NOTE]
   > Ide a beállítások módosítása csak módosítja őket a webalkalmazás számára. Ez az alapértelmezett beállításokat, a webszolgáltatás nem változik. Például, ha a szöveg módosítása **leírás** itt nem módosítja a Machine Learning Studio webszolgáltatási irányítópultján jelenik meg a leírást.
   > 
   > 
   
    Ha elkészült, válassza ki a **módosítások mentése**, majd válassza ki **lépjen a kezdőlapra**.

7. A kezdőlapon a webszolgáltatás küldendő értékeket adhat meg. Válassza ki **küldés** amikor elkészült, és az eredményt visszaad.

Ha azt szeretné, hogy térjen vissza a **konfigurációs** lapon kattintson a **setting.aspx** a webalkalmazás oldalán. Ha például nyissa meg **http://carprediction.azurewebsites.net/setting.aspx**. Azt az API-kulcsot újra meg kell adnia. Az oldal eléréséhez, és frissítse a beállításokat, amelyek van szüksége.

Állítsa le, indítsa újra, vagy törölje a webalkalmazást, mint bármely más webes alkalmazást az Azure Portalon. Mindaddig, amíg fut, tallózással keresse meg az otthoni URL-címet, és adja meg az új értékeket.

## <a name="how-to-use-the-batch-execution-service-template"></a>A kötegelt végrehajtási szolgáltatás sablon használata
A BES webalkalmazás-sablon ugyanúgy az RRS-sablonként is használhatja. A különbség az, hogy a létrehozott webalkalmazás több sornyi adatot nyújt, és több eredményeket használhatja.

A bemeneti értékek a kötegelt végrehajtási webszolgáltatáshoz Azure Storage vagy a helyi fájl származhatnak. Az eredmények tárolása az Azure storage-tárolókat. Tehát szüksége lesz egy Azure storage-tároló, amely tárolja az eredményeket, amely a web app adja vissza. Emellett szüksége, a bemeneti adatok készen álljon.

![Folyamat BES webes sablon használata][image2]

1. Kövesse ugyanazt az eljárást a BES webalkalmazás, mint az RRS-sablon létrehozásához. Azonban ebben az esetben ugorjon [Azure ML kötegelt végrehajtási szolgáltatás webalkalmazás-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) a BES sablon megnyitásához az Azure piactéren. Válassza ki **webes alkalmazás létrehozása**.

2. Adja meg, ahol az eredményeket tárolni szeretné, adja meg a cél tároló információkat a webalkalmazás kezdőlapján. Is adja meg, ahol a webalkalmazás beszerezheti a bemeneti értékek: helyi fájlból vagy egy Azure storage-tárolóba.
   Válassza ki **elküldése**.
   
   ![Szolgáltatás adatai][image7]

A web app feladat állapotát tartalmazó oldalt jelenít meg. A feladat befejeződött, az eredményeket az Azure Blob storage helyének kap. Lehetősége is van, az eredmények letöltése egy helyi fájlba.

## <a name="for-more-information"></a>További tudnivalók
További információ:

* Tekintse meg a machine learning-kísérlet létrehozása a Machine Learning Studióban [az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md).
* A machine learning-kísérlet webszolgáltatásként üzembe helyezése, olvassa el [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).
* Tekintse meg a hozzáférés a webszolgáltatáshoz való egyéb módon [használata az Azure Machine Learning webszolgáltatás](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
