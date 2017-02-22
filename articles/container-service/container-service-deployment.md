---
title: "Docker-tárolófürt üzembe helyezése az Azure-ban | Microsoft Docs"
description: "Azure Container Service-fürt üzembe helyezése az Azure Portal vagy az Azure Resource Manager-sablon használatával."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Azure  tárolószolgáltatás-fürt üzembe helyezése



Az Azure tárolószolgáltatással gyorsan üzembe helyezhet népszerű nyílt forráskódú tárolófürtözési és vezénylési megoldásokat. Ez a dokumentum részletesen ismerteti, hogyan helyezhető üzembe egy Azure Container Service-fürt az Azure Portal vagy egy Azure Resource Manager gyorsindítási sablon használatával. 

> [!NOTE]
> A Kubernetes támogatása az Azure Container Service-ben jelenleg előzetes verzióban van.

Az Azure Container Service-fürtöket az [Azure CLI 2.0 (előzetes verzió)](container-service-create-acs-cluster-cli.md) vagy az Azure Container Service API-k használatával is üzembe helyezheti.



## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Nyilvános SSH-kulcs**: Ha a portálon vagy valamelyik Azure gyorsindítási sablonnal végzi az üzembe helyezést, meg kell adnia az Azure Container Service virtuális gépeivel történő hitelesítésre szolgáló nyilvános kulcsot. A Secure Shell- (SSH-) kulcsok létrehozásával kapcsolatban lásd az [OS X és Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) rendszerhez készült útmutatót. 

* **Egyszerű szolgáltatás ügyfél-azonosítója és kulcsa** (csak Kubernetes esetében): Az egyszerű szolgáltatások létrehozásával kapcsolatos további információkért és útmutatóért lásd: [Tudnivalók a Kubernetes-fürthöz tartozó egyszerű szolgáltatásról](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Fürt létrehozása az Azure Portalon
1. Jelentkezzen be az Azure Portalra, válassza az **Új** lehetőséget, és az Azure Marketplace-en keresse meg az **Azure Container Service** elemet.

    ![Az Azure Container Service a Marketplace-en](media/container-service-deployment/acs-portal1.png)  <br />

2. Válassza ki az **Azure Container Service** elemet, és kattintson a **Létrehozás** gombra.

    ![Tárolószolgáltatás létrehozása](media/container-service-deployment/acs-portal2.png)  <br />

3. Adja meg a következő információkat:

    * **Felhasználónév**: Az Azure Container Service-fürt összes virtuális gépén és a virtuálisgép-méretezési csoportokban használt fiókhoz tartozó felhasználónév.
    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
    * **Hely**: válassza ki azt az Azure-régiót, amelyben az Azure Container Service-t üzembe kívánja helyezni.
    * **Nyilvános SSH-kulcs**: adja meg az Azure Container Service virtuális gépeinek hitelesítésére szolgáló nyilvános kulcsot. Fontos, hogy a kulcs ne tartalmazzon sortörést, és hogy szerepeljen benne az `ssh-rsa` előtag. Az `username@domain` utótag nem kötelező. A kulcsnak a következőhöz hasonlóan kell kinéznie: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Kattintson az **OK** gombra, amikor készen áll a folytatásra.

    ![Alapbeállítások](media/container-service-deployment/acs-portal3.png)  <br />

5. Válassza ki a vezénylés típusát. A következő lehetőségek közül választhat:

  * **DC/OS**: DC/OS fürt üzembe helyezése.
  * **Swarm**: Docker Swarm-fürt üzembe helyezése.
  * **Kubernetes**: Kubernetes-fürt üzembe helyezése


6. Kattintson az **OK** gombra, amikor készen áll a folytatásra.

    ![Vezénylő kiválasztása](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Ha a **Kubernetes** lehetőséget választja a legördülő menüben, akkor meg kell adnia az egyszerű szolgáltatás ügyfél-azonosítóját és az egyszerű szolgáltatás titkos ügyfélkulcsát. További információ: [Tudnivalók az Kubernetes-fürthöz tartozó egyszerű szolgáltatásról](container-service-kubernetes-service-principal.md).

    ![A Kubernetes egyszerű szolgáltatásának megadása](media/container-service-deployment/acs-portal10.png)  <br />

7. Az **Azure Container Service** beállításpanelén adja meg a következő információkat:

    * **Főkiszolgálók száma**: a főkiszolgálók száma a fürtben. Ha a Kubernetes lehetőséget választotta, a főkiszolgálók száma alapértelmezés szerint 1.
    * **Ügynökök száma**: a Docker Swarm és a Kubernetes esetében ez az érték az ügynökök kezdeti száma az ügynökök méretezési csoportjában. A DC/OS esetében ez az ügynökök kezdeti száma a privát méretezési csoportokban. Ezenkívül létrejön egy nyilvános méretezési csoport a DC/OS számára, amely az ügynökök előre meghatározott számát tartalmazza. Az ebben a nyilvános méretkészletben található ügynökök számát az határozza meg, hogy hány főkiszolgáló jött létre a fürtön: egy nyilvános ügynök tartozik egy főkiszolgálóhoz, és két nyilvános ügynök három vagy öt főkiszolgálóhoz.
    * **Ügynök-virtuálisgép mérete**: az ügynök-virtuálisgépek mérete.
    * **DNS-előtag**: globálisan egyedi név, amely a szolgáltatás teljes tartományneveiben a főrész előtagja lesz.
    * **Virtuálisgép-diagnosztika**: Egyes választott vezénylők számára engedélyezheti a virtuálisgép-diagnosztikát.

8. Kattintson az **OK** gombra, amikor készen áll a folytatásra.

    ![Container Service-beállítások](media/container-service-deployment/acs-portal5.png)  <br />

9. Kattintson az **OK** gombra az érvényesítés befejezése után.

    ![Ellenőrzés](media/container-service-deployment/acs-portal6.png)  <br />

10. Tekintse át a feltételeket. Az üzembe helyezés elindításához kattintson a **Vásárlás** gombra.

    ![Vásárlás](media/container-service-deployment/acs-portal7.png)  <br />

    Ha úgy döntött, hogy rögzíti az üzembe helyezést az Azure Portalon, megtekintheti annak állapotát.

    ![Üzembe helyezés állapota](media/container-service-deployment/acs-portal8.png)  <br />

Az üzembe helyezés több percet is igénybe vehet. Ezután az Azure Container Service-fürt készen áll a használatra.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Fürt létrehozása gyorsindítási sablon használatával
Az Azure gyorsindítási sablonok használatával fürtök helyezhetők üzembe az Azure Container Service-ben. A megadott gyorsindítási sablonok módosíthatók további vagy speciális Azure-konfigurációk belefoglalásával. Az Azure Container Service-fürtök Azure gyorsindítási sablonokkal való létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Az alábbi lépésekkel egy sablon és az Azure CLI 2.0-s (előzetes) verziójával helyezhet üzembe egy fürtöt (lásd a [telepítési és beállítási utasításokat](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Windows rendszer használata esetén hasonló lépésekkel helyezhet üzembe sablont az Azure PowerShell használatával. A lépéseket lásd a szakasz későbbi részében. Emellett a [portálon](../azure-resource-manager/resource-group-template-deploy-portal.md) keresztül vagy egyéb módszerekkel is üzembe helyezhet sablonokat.

1. DC/OS-, Docker Swarm- vagy Kubernetes-fürt üzembe helyezéséhez válassza az alábbi GitHub-sablonok egyikét. A DC/OS- és a Swarm-sablonok az alapértelmezett vezénylési típus kivételével azonosak.

    * [DC/OS-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Jelentkezzen be az Azure-fiókjába (`az login`), és győződjön meg róla, hogy az Azure CLI csatlakoztatva van az Azure-előfizetéséhez. Az alapértelmezett előfizetés megtekintéséhez futtassa az alábbi parancsot:

    ```azurecli
    az account show
    ```
    
    Ha több előfizetéssel rendelkezik, és másik alapértelmezett előfizetést szeretne beállítani, futtassa az `az account set --subscription` parancsot, és adja meg az előfizetés azonosítóját vagy nevét.

3. Az ajánlott eljárás egy új erőforráscsoport használata az üzemelő példányhoz. Erőforráscsoport létrehozásához használja az `az group create` parancsot, és adjon meg egy erőforráscsoport-nevet és -helyet: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Hozzon létre egy JSON-fájlt, amely tartalmazza a sablon szükséges paramétereit. Töltse le az `azuredeploy.parameters.json` nevű paraméterfájlt, amely az `azuredeploy.json` Azure Container Service-sablonhoz tartozik a GitHubon. Adja meg a fürt szükséges paraméterértékeit. 

    A [DC/OS-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) használatához például adja meg a `dnsNamePrefix` és `sshRSAPublicKey` paraméterértékeit. Tekintse meg a `azuredeploy.json` leírásait és az egyéb paraméterekre vonatkozó beállításokat.  
 

5. Hozzon létre egy Container Service-fürtöt az üzembehelyezési paraméterfájl alábbi paranccsal történő átadásával, ahol:

    * A **RESOURCE_GROUP** az előző lépés során létrehozott erőforráscsoport neve.
    * A **DEPLOYMENT_NAME** (választható) az üzemelő példány választott neve.
    * A **SABLON_URI_AZONOSÍTÓJA** az üzembe helyezési fájl (`azuredeploy.json`) helye. Az URI-nak a nyersfájlnak kell lennie, nem a GitHub felhasználói felületére mutató elemnek. Az URI megkereséséhez válassza ki az `azuredeploy.json` fájlt a GitHubon, majd kattintson a **Raw** gombra.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    A paramétereket JSON-formátumú sztringként is megadhatja a parancssoron. Használjon az alábbihoz hasonló parancsot:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > Az üzembe helyezés több percet is igénybe vehet.
    > 

### <a name="equivalent-powershell-commands"></a>Egyenértékű PowerShell-parancsok
A PowerShell használatával is üzembe helyezhet Azure Container Service-fürtöket. Ez a dokumentum az [Azure PowerShell modul](https://azure.microsoft.com/blog/azps-1-0/) 1.0-s verziója alapján készült.

1. DC/OS-, Docker Swarm- vagy Kubernetes-fürt üzembe helyezéséhez válassza az alábbi sablonok egyikét. A DC/OS- és a Swarm-sablonok az alapértelmezett vezénylési típus kivételével azonosak.

    * [DC/OS-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Mielőtt az Azure-előfizetésében létrehozna egy fürtöt, győződjön meg arról, hogy a PowerShell-munkamenet bejelentkezett az Azure-ba. Ezt a `Get-AzureRMSubscription` paranccsal teheti meg:

    ```powershell
    Get-AzureRmSubscription
    ```

3. Az Azure-ba való bejelentkezéshez használja a `Login-AzureRMAccount` parancsot:

    ```powershell
    Login-AzureRmAccount
    ```

4. Az ajánlott eljárás egy új erőforráscsoport használata az üzemelő példányhoz. Erőforráscsoport létrehozásához használja a `New-AzureRmResourceGroup` parancsot, és adjon meg egy erőforráscsoport-nevet és célrégiót:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Miután létrehozott egy erőforráscsoport, a fürtöt az alábbi paranccsal hozhatja létre. A kívánt sablonhoz tartozó URI-t a `-TemplateUri` paraméterben kell megadni. A parancs futtatásakor a rendszerhéj kéri az üzembehelyezési paramétereket.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>A sablon paramétereinek megadása
Ha már használta a PowerShellt, biztosan tudja, hogy a parancsmag elérhető paraméterei között a mínuszjel (-) beírásával, majd a TAB billentyű lenyomásával válthat. Ez a funkció a sablonban megadott saját paraméterekkel is működik. Amint beírja a sablon nevét, a parancsmag beolvassa a sablont, elemzi a paramétereket, és dinamikusan hozzáadja a parancshoz a sablon paramétereit. Ez jelentősen megkönnyíti a sablon-paraméterértékek megadását. Ha megfeledkezik egy kötelező paraméterértékről, a PowerShell kérni fogja azt.

Az alábbiakban a teljes parancs látható paraméterekkel együtt. Az erőforrások neveinél saját értékeket is megadhat.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Következő lépések
Most, hogy működő fürtje van, tekintse meg ezeket a dokumentumokat a kapcsolatra és a felügyeletre vonatkozó részletekért:

* [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md)
* [Az Azure Container Service és a DC/OS használata](container-service-mesos-marathon-rest.md)
* [Az Azure Container Service és a Docker Swarm használata](container-service-docker-swarm.md)
* [Az Azure Container Service és a Kubernetes használata](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->


