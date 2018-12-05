---
title: Az Azure Container Instances (ACI) – az Azure Machine Learning webszolgáltatások üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe egy betanított modellt webszolgáltatásként, amely az Azure Container Instances (ACI) Azure Machine Learning szolgáltatással. Ez a cikk bemutatja a modell üzembe helyezése az aci-ban három különböző módon. A kód és a vezérlő elnevezési részei a központi telepítésben található sorok számát különböznek.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 11/06/2018
ms.openlocfilehash: 4d525fdcbaa85eecee903ed83ee903d55810cbce
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876593"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Webszolgáltatások üzembe helyezése az Azure Container Instances szolgáltatásban 

Telepítheti a betanított modellt webszolgáltatásként, amely a [Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (aci) Szolgáltatásban, [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), az IoT él eszközt, vagy [programmable gate arrays (FPGA) mezőbe](concept-accelerate-with-fpgas.md) 

ACI általában az AKS olcsóbb és 4 – 6 sornyi kódot is beállítható. ACI tökéletes lehetőség központi telepítések teszteléséhez. Később, a modellek és webszolgáltatások nagy léptékben méretezhető, éles használatra kész, is [üzembe helyezheti őket az AKS](how-to-deploy-to-aks.md).

Ez a cikk bemutatja a modell üzembe helyezése az aci-ban három különböző módon. A kód és a vezérlő elnevezési részei a központi telepítésben található sorok számát különböznek. A metódus a lehető legkevesebb kódot és a metódus a legtöbb kóddal és vezérlőelem az ACI lehetőségek közül választhat:

* Üzembe helyezés a modell fájl használatával `Webservice.deploy()` 
* Üzembe helyezés a regisztrált modell használatával `Webservice.deploy_from_model()`
* A lemezkép használatával regisztrált modell üzembe helyezése `Webservice.deploy_from_image()`

>[!NOTE]
> Ebben a cikkben kód tesztelés az Azure Machine Learning SDK-val 1.0.2-es verzióját

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [Azure Machine Learning a rövid útmutató – első lépések](quickstart-get-started.md).

- Az Azure Machine Learning szolgáltatás munkaterület objektum

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=loadWorkspace)]

- Modell üzembe helyezéséhez. Ebben a dokumentumban szereplő példák a végrehajtásával létrehozott modellt használja a "[a modell betanítását](tutorial-train-models-with-aml.md)" oktatóanyag. Ha nem használja ezt a modellt, módosíthatja a lépések a modell neve.  Is kell írnia a saját pontozó szkript futtatásához a modellt.


## <a name="configure-an-image"></a>Kép beállítása

Állítsa be a Docker-rendszerképet, amely a modell-fájlok tárolására szolgál.
1. Hozzon létre egy pontozó szkriptre (score.py) [ezen utasításokat követve](tutorial-deploy-models-with-aml.md#create-scoring-script).

    > [!IMPORTANT]
    > A pontozó szkript az ügyfeleknek elküldött adatokat fogad, és átadja a modell pontozása. A dokumentum a adatszerkezet, amely a parancsfájlt és a modell várható. Ez a dokumentáció kellene, azzal megkönnyítheti létrehozását, hogy az ügyfél használata a web service.

1. Hozzon létre egy környezetben fájlt (myenv.yml) [ezen utasításokat követve](tutorial-deploy-models-with-aml.md#create-environment-file).

1. Két fájlt használja a Docker-rendszerképet konfigurálhatja a Python SDK használatával a következőképpen:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configImage)]

> [!NOTE]
> GPU-gyorsítás használó kép létrehozásakor az GPU alaprendszerképet kell használni a Microsoft Azure-szolgáltatásokra csak.

## <a name="configure-the-aci-container"></a>Az ACI-tároló konfigurálása

Az ACI-tároló konfigurálása szerint adja meg a processzorok számát és a GB memóriával történő üzembe helyezéshez az ACI-tároló. Az alapértelmezett egymagos és 1 GB memória elég sok modellek esetén. Ha úgy gondolja, hogy szüksége több később hozza létre újra a lemezképet, és telepítse újra a szolgáltatást.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

## <a name="register-a-model"></a>Regisztrálja a modellt

> Hagyja ki ezt az előfeltételt, ha Ön [üzembe helyezése a egy modellfájl](#deploy-from-model-file) (`Webservice.deploy()`).

Regisztrálja a modellt használandó [Webservice.deploy_from_model](#deploy-from-registered-model) vagy [Webservice.deploy_from_image](#deploy-from-image). Vagy ha már rendelkezik egy regisztrált modell lekérni most.


### <a name="retrieve-a-registered-model"></a>A regisztrált modell beolvasása
Ha az Azure Machine Learning segítségével a modell betanítását, a modell előfordulhat, hogy már regisztrálva van a munkaterülethez.  Például az utolsó lépés a [modell létrehozása útmutató betanításához](tutorial-train-models-with-aml.md) regisztrálva a modell.  Majd kérheti le a regisztrált modell üzembe helyezéséhez.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=retrieveModel)]
  
### <a name="register-a-model-file"></a>Egy modellfájl regisztrálása

Ha a modell máshol lett létrehozva, továbbra is regisztrálhatja egyszerűen a munkaterületre.  Regisztrálja a modellt, a modellfájl (`sklearn_mnist_model.pkl` ebben a példában) kell lennie az aktuális munkakönyvtárba. Ezután regisztrálni ezt a fájlt nevű modellként `sklearn_mnist` a munkaterület- `Model.register()`.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=registerModel)]

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>1. lehetőség: A modellfájl üzembe helyezése

Modell-fájlból telepíteni kódot ír, de is kínál a lehető legkevesebb összetevők elnevezési szabályozhatja a lehető legkevesebb szükséges. Ez a beállítás egy modellfájl kezdődik, és az Ön számára a munkaterületre regisztrálja azt.  Azonban nem a modell neve, és társítsa a címkék és a egy leírást.  

Ez a beállítás az SDK-metódussal, Webservice.deploy() használja.  

**Becsült időtartam**: üzembe helyezése körülbelül 6 – 7 perc szükséges.

1. Győződjön meg arról, hogy a jelentésmodell fájlját a helyi munkakönyvtárból.

1. Nyissa meg az előfeltételként szükséges modellfájl score.py és módosítása a `init()` szakaszt:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. A modellfájl üzembe.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option1Deploy)]

1. Mostantól [a webszolgáltatás teszteléséhez](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>2. lehetőség: A regisztrált modell üzembe helyezése

Regisztrált modellfájl telepíteni néhány további sornyi kód szükséges, és lehetővé teszi bizonyos kimenetek elnevezésén felett. Ez a beállítás kényelmes módja a már regisztrált modellek üzembe helyezéséhez.  Azonban nem adjon nevet a Docker-rendszerképet.  

Ez a beállítás az SDK-metódussal, Webservice.deploy_from_model() használja.

**Becsült időtartam**: Ezzel a lehetőséggel üzembe helyezése körülbelül 8 perc szükséges.

1. Futtassa a kódot, a Docker-tároló és az ACI-tároló konfigurálását, és adja meg a regisztrált modell.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option2Deploy)]

1. Mostantól [a webszolgáltatás teszteléséhez](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>3. lehetőség: Lemezkép alapján üzembe helyezése

Modell üzembe helyezése regisztrált (`model`) használatával `Webservice.deploy_from_image()`. Ez a módszer lehetővé teszi a Docker-rendszerkép létrehozása külön-külön és üzembe helyezhesse a rendszerképből.

1. Hozhat létre, és regisztrálja a Docker-rendszerkép alatt a munkaterület használata `ContainerImage.create()`

    Ez a módszer segít jobban szabályozhatja a kép egy külön lépésben létrehozásával.  A regisztrált modell (`model`) a rendszerkép tartalmazza.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3CreateImage)]

    **Becsült időtartam**: körülbelül 3 perc alatt.

1. A szolgáltatás használatával, a Docker-rendszerkép üzembe helyezése `Webservice.deploy_from_image()`

    A kép most üzembe helyezése az aci Szolgáltatásban.  
        
    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]
 
    **Becsült időtartam**: körülbelül 3 perc alatt.

Ez a módszer létrehozásához és az összetevők a központi telepítésben lévő elnevezési legtöbb felett biztosít.

Most tesztelheti a webszolgáltatást.

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>A webszolgáltatás teszteléséhez

A webes szolgáltatás nem ugyanaz, függetlenül attól, amely módszerrel történt.  Előrejelzés lekéréséhez használja a `run` metódus a szolgáltatás.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=testService)]


## <a name="update-the-web-service"></a>A web service frissítése

A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új rendszerkép használata a web service:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Amikor frissít egy képet, a webszolgáltatás nem frissül automatikusan. Minden egyes szolgáltatás, amely az új lemezképet használni kívánt manuálisan kell frissítenie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem a webes szolgáltatás használatához, törlése, így nem kell díjat fizetniük.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=deleteService)]


## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [felhasználás a gépi Tanulási modellek webszolgáltatásként üzembe helyezett](how-to-consume-web-service.md).
* Ismerje meg, hogyan [telepítése az Azure Kubernetes Service](how-to-deploy-to-aks.md) nagyobb méretezési csoport üzembe helyezéséhez. 
