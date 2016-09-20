<properties
   pageTitle="Azure tárolószolgáltatás-fürt üzembe helyezése | Microsoft Azure"
   description="Azure tárolószolgáltatás-fürt üzembe helyezése az Azure Portal, az Azure CLI vagy a PowerShell használatával."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, tárolók, mikroszolgáltatások, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Azure  tárolószolgáltatás-fürt üzembe helyezése

Az Azure tárolószolgáltatással gyorsan üzembe helyezhet népszerű nyílt forráskódú tárolófürtözési és vezénylési megoldásokat. Az Azure tárolószolgáltatás lehetővé teszi, hogy DC/OS- és Docker Swarm-fürtöket helyezzen üzembe Azure Resource Manager-sablonok vagy az Azure portál használatával. A fürtöket az Azure virtuálisgép-skálázási készleteivel helyezheti üzembe. A fürtök igénybe vehetik az Azure hálózati és tárolási szolgáltatásait. Az Azure tárolószolgáltatás eléréséhez Azure-előfizetés szükséges. Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

E dokumentum részletes útmutatása alapján üzembe helyezhet egy Azure tárolószolgáltatás-fürtöt az [Azure portál](#creating-a-service-using-the-azure-portal), az [Azure parancssori felülete (CLI)](#creating-a-service-using-the-azure-cli) vagy az [Azure PowerShell modul](#creating-a-service-using-powershell) használatával.  

## Szolgáltatás létrehozása az Azure Portal használatával

Jelentkezzen be az Azure Portalra, válassza az **Új** lehetőséget, és az Azure Marketplace-en keresse meg az **Azure Container Service** elemet.

![1. üzemelő példány létrehozása](media/acs-portal1.png)  <br />

Válassza ki az **Azure Container Service** elemet, és kattintson a **Létrehozás** gombra.

![2. üzemelő példány létrehozása](media/acs-portal2.png)  <br />

Adja meg a következő információkat:

- **Felhasználónév**: ezt a felhasználónevet fogja használni a fiók az Azure tárolószolgáltatás-fürt összes virtuális gépén és a virtulálisgép-skálázási készletekben.
- **Előfizetés**: válasszon ki egy Azure-előfizetést.
- **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
- **Hely**: válassza ki azt az Azure-régiót, amelyben az Azure Container Service-t üzembe kívánja helyezni.
- **Nyilvános SSH kulcs**: adja meg az Azure Container Service virtuális gépeinek hitelesítésére szolgáló nyilvános kulcsot. Nagyon fontos, hogy a kulcs ne tartalmazzon sortörést, és hogy szerepeljen benne az „ssh-rsa” előtag és a „felhasználónév@tartomány” utótag. A következőhöz hasonlóan kell kinéznie: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azurefelhasználó@linuxvm**. A Secure Shell- (SSH-) kulcsok létrehozására vonatkozó útmutatásért tekintse meg a [Linuxra]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) és a [Windowsra]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) vonatkozó cikkeket.

Kattintson az **OK** gombra, amikor készen áll a folytatásra.

![3. üzemelő példány létrehozása](media/acs-portal3.png)  <br />

Válassza ki a vezénylés típusát. A következő lehetőségek közül választhat:

- **DC/OS**: DC/OS fürt üzembe helyezése.
- **Swarm**: Docker Swarm-fürt üzembe helyezése.

Kattintson az **OK** gombra, amikor készen áll a folytatásra.

![4. üzemelő példány létrehozása](media/acs-portal4.png)  <br />

Adja meg a következő információkat:

- **Főkiszolgálók száma**: a főkiszolgálók száma a fürtben.
- **Ügynökök száma**: a Docker Swarm esetében ez lesz az ügynökök kezdeti száma az ügynökskálázási készletben. A DC/OS esetében ez lesz az ügynökök kezdeti száma a privát skálázási készletekben. Ezenkívül létrejön egy nyilvános méretkészlet, amely az ügynökök előre meghatározott számát tartalmazza. Az ebben a nyilvános méretkészletben található ügynökök számát az határozza meg, hogy hány főkiszolgáló jött létre a fürtön. Egy nyilvános ügynök tartozik egy főkiszolgálóhoz, és két nyilvános ügynök három vagy öt főkiszolgálóhoz.
- **Ügynök-virtuálisgép mérete**: az ügynök-virtuálisgépek mérete.
- **DNS-előtag**: globálisan egyedi név, amely a szolgáltatás teljes tartományneveiben a főrész előtagja lesz.

Kattintson az **OK** gombra, amikor készen áll a folytatásra.

![5. üzemelő példány létrehozása](media/acs-portal5.png)  <br />

Kattintson az **OK** gombra az érvényesítés befejezése után.

![6. üzemelő példány létrehozása](media/acs-portal6.png)  <br />

Az üzembe helyezés elindításához kattintson a **Létrehozás** gombra.

![7. üzemelő példány létrehozása](media/acs-portal7.png)  <br />

Ha úgy döntött, hogy rögzíti az üzembe helyezést az Azure Portalon, megtekintheti annak állapotát.

![8. üzemelő példány létrehozása](media/acs-portal8.png)  <br />

Az üzembe helyezés befejezése után az Azure tárolószolgáltatás-fürt használatra kész.

## Szolgáltatáspéldány létrehozása az Azure CLI használatával

Az Azure tárolószolgáltatás-példány parancssorban történő létrehozásához Azure előfizetésre van szükség. Ha nem rendelkezik előfizetéssel, regisztrálhat az [ingyenes próbaverzióra](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Ezen felül [telepítenie](../xplat-cli-install.md) és [konfigurálnia](../xplat-cli-connect.md) kell az Azure CLI-t is.

DC/OS- vagy Docker Swarm-fürt üzembe helyezéséhez válassza az alábbi GitHub-sablonok egyikét. A két sablon az alapértelmezett vezénylési típus kivételével azonos.

* [DC/OS-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Győződjön meg róla, hogy az Azure CLI csatlakoztatva van egy Azure-előfizetéshez. Ehhez futtassa az alábbi parancsot:

```bash
azure account show
```
Ha a parancs nem ad vissza Azure-fiókot, használja az alábbi parancsot a CLI Azure-ba történő beléptetéséhez.

```bash
azure login -u user@domain.com
```

Ezt követően állítsa be az Azure CLI-eszközeit az Azure Resource Manager használatára.

```bash
azure config mode arm
```

Hozzon létre egy Azure-erőforráscsoportot és egy tárolószolgáltatás-fürtöt az alábbi paranccsal, ahol:

- A **RESOURCE_GROUP** a szolgáltatáshoz használni kívánt erőforráscsoport neve.
- A **HELY** az az Azure-régió, ahol az erőforráscsoportot és az Azure Container Service-t üzembe szeretné helyezni.
- A **SABLON_URI_AZONOSÍTÓJA** az üzembe helyezési fájl helye. Vegye figyelembe, hogy ennek nyersfájlnak kell lennie, nem a GitHub felhasználói felületre mutató elemnek. Az URL-cím megkereséséhez válassza ki az azuredeploy.json fájlt a GitHubon, majd kattintson a **Raw** gombra.

> [AZURE.NOTE] A parancs futtatásakor a rendszerhéj kérni fogja az üzembe helyezési paraméterek értékeit.

```bash
# sample deployment

azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### A sablon paramétereinek megadása

Ebben a parancsverzióban a paramétereket interaktív módon kell megadni. Ha paramétereket szeretne megadni, például egy JSON-formátumú karakterláncot, akkor használja a `-p` kapcsolót. Példa:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Másik megoldásként megadhat egy JSON-formátumú paraméterfájlt is az `-e` kapcsoló használatával:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Ha szeretne megtekinteni egy példát a paraméterfájlra, keresse meg az `azuredeploy.parameters.json` nevű fájlt a GitHubon az Azure tárolószolgáltatás-sablonokkal.

## Szolgáltatáspéldány létrehozása a PowerShell használatával

Azure tárolószolgáltatás-fürtöt a PowerShell használatával is üzembe helyezhet. Ez a dokumentum az [Azure PowerShell modul](https://azure.microsoft.com/blog/azps-1-0/) 1.0-s verziója alapján készült.

DC/OS- vagy Docker Swarm-fürt üzembe helyezéséhez válassza ki az alábbi sablonok egyikét. A két sablon az alapértelmezett vezénylési típus kivételével azonos.

* [DC/OS-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Mielőtt az Azure-előfizetésében létrehozna egy fürtöt, győződjön meg arról, hogy a PowerShell-munkamenet bejelentkezett az Azure-ba. Ezt a `Get-AzureRMSubscription` paranccsal teheti meg:

```powershell
Get-AzureRmSubscription
```

Az Azure-ba való bejelentkezéshez használja a `Login-AzureRMAccount` parancsot:

```powershell
Login-AzureRmAccount
```

Ha az üzembe helyezést új erőforráscsoportban végzi, először létre kell hoznia az erőforráscsoportot. Új erőforráscsoport létrehozásához használja a `New-AzureRmResourceGroup` parancsot, és adjon meg egy erőforráscsoport-nevet és célrégiót:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Miután létrehozott egy erőforráscsoport, a fürtöt az alábbi paranccsal hozhatja létre. A kívánt sablonhoz tartozó URI-t a `-TemplateUri` paraméterben kell megadni. A parancs futtatásakor a rendszerhéj kérni fogja az üzembe helyezési paramétereket.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### A sablon paramétereinek megadása

Ha már használta a PowerShellt, biztosan tudja, hogy a parancsmag elérhető paraméterei között a mínuszjel (-) beírásával, majd a TAB billentyű lenyomásával válthat. Ez a funkció a sablonban megadott saját paraméterekkel is működik. Amint beírja a sablon nevét, a parancsmag beolvassa a sablont, elemzi a paramétereket, és dinamikusan hozzáadja a parancshoz a sablon paramétereit. Ez jelentősen megkönnyíti a sablon-paraméterértékek megadását. Ha megfeledkezik egy kötelező paraméterértékről, a PowerShell kérni fogja azt.

Az alábbiakban a teljes parancs látható paraméterekkel együtt. Az erőforrások neveinél saját értékeket is megadhat.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Következő lépések

Most, hogy működő fürtje van, tekintse meg ezeket a dokumentumokat a kapcsolatra és a felügyeletre vonatkozó részletekért:

- [Csatlakozás Azure tárolószolgáltatási fürthöz](container-service-connect.md)
- [Az Azure Container Service és a DC/OS használata](container-service-mesos-marathon-rest.md)
- [Az Azure Container Service és a Docker Swarm használata](container-service-docker-swarm.md)



<!--HONumber=sep16_HO1-->


