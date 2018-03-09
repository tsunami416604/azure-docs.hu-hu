---
title: "A Machine Learning webszolgáltatásba használják az web app sablon |} Microsoft Docs"
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
ms.author: raymondl
ms.openlocfilehash: f7efa647fa6afc247509cd4a52066c0459f75ca3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Használhat fel az Azure Machine Learning webszolgáltatás web app sablon segítségével

A prediktív modellezése, és a telepítése egy Azure webes szolgáltatás használatával:
- Az Azure Machine Learning Studióban.
- Eszközök, például az R vagy Python. 

Ezt követően a operationalized modell egy REST API használatával végezheti el.

Számos módja a REST API-t használnak, és a webes szolgáltatás. Például írhat egy alkalmazást a C#, R vagy Python hozta létre a webszolgáltatás üzembe helyezésekor mintakód használatával. (A mintakód is elérhető a [Machine Learning webszolgáltatások portal](https://services.azureml.net/quickstart) vagy a webes szolgáltatás irányítópultján a Machine Learning Studióban.) Vagy a minta Microsoft Excel-munkafüzet hozott létre az Ön egyszerre is használhatja.

A leggyorsabb és legegyszerűbb módja a webes szolgáltatás eléréséhez keresztül elérhető a web app sablon, de a [Azure piactér](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Az Azure Machine Learning web app sablonok
A webes alkalmazás sablonok érhető el az Azure piactéren hozhat létre egy egyéni webalkalmazást, hogy ismeri a webszolgáltatás bemeneti adatok és a kívánt eredmény elérése érdekében. Mindössze annyit kell tennie hozzáférést a webes alkalmazás a webszolgáltatás és az adatokat, és a sablon elvégzi a többit, azaz.

Két sablonok érhetők el:

* [Az Azure ML kérés-válasz szolgáltatás Web App-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML kötegelt végrehajtási szolgáltatás Web App-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Minden sablon az ASP.NET mintaalkalmazás API URI használatával hozza létre és a kulcs a webszolgáltatáshoz. A sablon az Azure-bA majd központilag telepíti az alkalmazást, mert a webhelyet. 

A kérés-válasz szolgáltatás (RR-EKET) sablon létrehoz egy webalkalmazást, amelyek segítségével egyetlen sornyi adatot küldeni a webszolgáltatás számára, hogy egyetlen eredmény. A kötegelt végrehajtási szolgáltatás (BES) sablon létrehoz egy webalkalmazást, amelyek segítségével az adatok több lekéréséhez sorok küldése.

Ezek a sablonok használatához nem kódolás szükséges. Csak akkor adja meg, az API-kulcsot és URI, és a sablon épít fel az alkalmazást.

Az API-kulcsot, és az adott webszolgáltatás kérelmi URI:

1. Az a [Web Services portál](https://services.azureml.net/quickstart), jelölje be **webszolgáltatások** tetején. Klasszikus webszolgáltatáshoz, válassza ki vagy **klasszikus webszolgáltatások**.
2. Válassza ki a webszolgáltatás, amelyet el szeretne érni.
3. Klasszikus webszolgáltatáshoz válassza ki a használni kívánt végpont.
4. Válassza ki **felhasználás** tetején.
5. Az elsődleges vagy másodlagos kulcsot másolja ki és mentse azt.
6. Ha egy RR-EKET sablont hoz létre, másolja a **kérés-válasz** URI és mentse azt. Ha BES sablont készít, akkor másolja a **kötegelt kérelmekben** URI és mentse azt.


## <a name="how-to-use-the-request-response-service-template"></a>A kérés-válasz Szolgáltatássablon használata
Kövesse az alábbi lépéseket ahhoz, hogy a RR-EKET web app sablon használható az alábbi ábrán is látható.

![Folyamat RR-EKET webes sablon használata][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **új**, és adja meg **Azure ML kérés-válasz szolgáltatás webalkalmazás**, majd válassza ki **létrehozása**. 
3. Az a **létrehozása** panelen:
   
   * Adjon meg egy egyedi nevet a webalkalmazás. A webalkalmazás URL-CÍMÉT a nevét, majd lesz **. azurewebsites.net**. Példa: **http://carprediction.azurewebsites.net**.
   * Válassza ki az Azure-előfizetés és az alatt a webszolgáltatás fut. szolgáltatások.
   * Kattintson a **Létrehozás** gombra.
     
   ![Webalkalmazás létrehozása][image5]

4. Ha Azure webalkalmazás telepítése befejeződött, válassza ki a **URL-cím** az Azure-ban a webalkalmazás-beállítások lapon, vagy adja meg az URL-címet egy webböngészőben. Adja meg például **http://carprediction.azurewebsites.net**.
5. Amikor első alkalommal futtatja a webalkalmazást, akkor a kéri a **API Post URL-címe** és **API-kulcs**. Adja meg a korábban mentett értékeket (URI és API-kulcsát, illetve kérés). Válassza ki **nyújt**.
     
   ![Adja meg a post URI és API-kulcsát][image6]

6. A webes alkalmazás megjelenik a **Web App konfigurációs** az aktuális webes szolgáltatás beállításait tartalmazó lapra. Itt módosíthatja a beállításokat, amelyek a webalkalmazás számára.
   
   > [!NOTE]
   > Itt a szolgáltatás beállításainak módosítása csak akkor változik meg azokat a webalkalmazás. Ez az alapértelmezett beállításokat, a webszolgáltatás nem változik. Például, ha a szöveget **leírás** itt nem módosítja a webes szolgáltatás irányítópultján a Machine Learning Studióban látható leírás.
   > 
   > 
   
    Amikor elkészült, válassza ki a **módosítások mentése**, majd válassza ki **Ugrás a kezdőlapra**.

7. A kezdőlapról a webszolgáltatás küldendő értékeket adhat meg. Válassza ki **Submit** amikor elkészült, és az eredményt adja vissza.

Ha vissza szeretne a **konfigurációs** lap megnyitásához válassza a **setting.aspx** a webes alkalmazás lapján. Például Ugrás **http://carprediction.azurewebsites.net/setting.aspx**. Írja be újra az API-kulcsot kéri. Meg kell, hogy a lap eléréséhez, és frissítse a beállításokat.

Állítsa le, indítsa újra, vagy törölje a webalkalmazást, mint bármely más webalkalmazás az Azure portálon. Mindaddig, amíg fut, keresse meg a kezdőkönyvtár címét, és adja meg új értékeket.

## <a name="how-to-use-the-batch-execution-service-template"></a>A kötegelt végrehajtási Szolgáltatássablon használata
A BES web app-sablon az RRS-sablonként ugyanúgy használhatja. A különbség, hogy segítségével a létrehozott webalkalmazás küldje el az adatok több sort, és több eredményeket.

A bemeneti értékek egy kötegelt végrehajtási webszolgáltatáshoz Azure Storage vagy a helyi fájl származhatnak. Az eredményeket egy az Azure storage-tároló tárolja. Így kell egy Azure storage-tárolót, amely a webes alkalmazás visszaadja az eredmények tárolásához. Szükség kész állapotba hozásához a bemeneti adatok.

![Folyamat BES webes sablon használata][image2]

1. A BES webalkalmazás, mint az RRS-sablon létrehozása az eljárást kell követnie. Ebben az esetben lépjen, de [Azure ML kötegelt végrehajtási szolgáltatás Web App sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) a BES sablon megnyitásához az Azure piactéren. Válassza ki **webalkalmazás létrehozása**.

2. Adja meg a tárolt eredményt, írja be a cél tároló adatokat a webes alkalmazás kezdőlapján. Is megadhatja, ha a webes alkalmazás kérheti le a bemeneti értékek: egy helyi fájlban vagy egy Azure storage tárolóban.
   Válassza ki **nyújt**.
   
   ![Szolgáltatás adatai][image7]

A web app egy lapon feladat állapotát jeleníti meg. A feladat befejeződött, az eredményeket az Azure Blob storage helyének nyílik meg. Lehetősége is van, az eredményeket egy helyi fájl letöltése.

## <a name="for-more-information"></a>További tudnivalók
További részletek:

* Tekintse meg a Machine Learning Studio, a gépi tanulási kísérlet létrehozása [az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md).
* A gépi tanulási kísérlet egy webszolgáltatás telepítése tudnivalókat [központi telepítése az Azure Machine Learning webszolgáltatás](publish-a-machine-learning-web-service.md).
* A webes szolgáltatás más módon lásd [hogyan kell használni az Azure Machine Learning webszolgáltatás](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
