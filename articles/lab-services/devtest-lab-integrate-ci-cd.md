---
title: Az Azure DevTest Labs folyamatba való integrálásához Azure folyamatok folyamatos integrációt és teljesítést |} A Microsoft Docs
description: Ismerje meg, hogyan integrálhatja az Azure DevTest Labs szolgáltatásban az Azure-folyamatok folyamatos integrációs és teljesítési folyamat
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 20ba297d22e26aa8c7e20db300173f12582d257e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224469"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs integrálása az Azure-folyamatok CI/CD-folyamatával

Az *Azure DevTest Labs Tasks* bővítménnyel integrálhatja Azure-folyamatait a folyamatos integráció és a folyamatos teljesítés (CI/CD) létrehozásához és kiadásához Azure DevTest Labs segítségével. A bővítmény több feladatot is telepít, beleértve a következőket: 

- Virtuális gép (VM) létrehozása
- Egyéni lemezkép készítése egy virtuális Gépről
- Virtuális gép törlése 

Ezek a feladatok megkönnyítik például az *arany lemezképű* virtuális gépek gyors üzembe helyezését egy adott tesztelési feladathoz, majd a teszt befejezésekor törlik a virtuális gépet.

Ez a cikk bemutatja, hogyan használhatók Azure DevTest Labs feladatok virtuális gépek létrehozásához és üzembe helyezéséhez, egyéni lemezképek létrehozásához, majd a virtuális gép törléséhez, egyetlen kiadási folyamatként. A feladatokat általában a saját egyéni Build-, tesztelési és üzembe helyezési folyamataiban végezze el.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Előfeltételek

- Regisztráljon vagy jelentkezzen be az [Azure DevOps](https://dev.azure.com) -szervezetbe, és [hozzon létre egy projektet](/vsts/organizations/projects/create-project) a szervezeten belül. 
  
- Telepítse a Azure DevTest Labs Tasks bővítményt a Visual Studio Piactérről.
  
  1. Lépjen a [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Válassza **az ingyenes letöltés**lehetőséget.
  1. Válassza ki az Azure DevOps-szervezetét a legördülő listából, majd válassza a **telepítés**lehetőséget. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Sablon létrehozása Azure-beli virtuális gép létrehozásához 

Ez a szakasz azt ismerteti, hogyan hozható létre az Azure-beli virtuális gép igény szerinti létrehozásához használt Azure Resource Manager-sablon.

1. Ha Resource Manager-sablont szeretne létrehozni az előfizetésében, kövesse a [Resource Manager-sablon használata](devtest-lab-use-resource-manager-template.md)című témakörben leírt eljárást.
   
1. Mielőtt létrehozza a Resource Manager-sablon, adja hozzá a [WinRM összetevő](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) a virtuális gép létrehozása során. Az *Azure file Copy* és *a PowerShell a célszámítógépen* való üzembe helyezéséhez WinRM-hozzáférésre van szükség. A Rendszerfelügyeleti webszolgáltatások összetevőnek egy állomásnévnek kell lennie paraméterként, amely a virtuális gép teljes tartományneve (FQDN). 
   
   > [!NOTE]
   > Amikor a Rendszerfelügyeleti webszolgáltatások egy megosztott IP-címet használja, hozzá kell adnia egy NAT-szabályt egy külső port leképezése a WinRM-port. Ha nyilvános IP-címmel hozza létre a virtuális gépet, nincs szüksége a NAT-szabályra.
   
   
1. Mentse a sablont a számítógépre a *CreateVMTemplate. JSON*nevű fájlként.
   
1. A sablonban keresse meg a forrás vezérlőt.

## <a name="create-a-script-to-get-vm-properties"></a>Parancsfájl létrehozása a virtuális gép tulajdonságainak lekéréséhez

Ha olyan feladatokat hajt végre, mint például az *Azure file Copy* vagy a PowerShell a kiadási folyamatban lévő *célszámítógépen* , a következő parancsfájl gyűjti azokat az értékeket, amelyekre telepítenie kell egy alkalmazást egy virtuális gépre. Általában ezeket a feladatokat fogja használni az alkalmazás Azure-beli virtuális gépekre való üzembe helyezéséhez. A feladatok olyan értékeket igényelnek, mint például a virtuális gép erőforráscsoport neve, az IP-cím és a teljes tartománynév.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A parancsfájl létrehozása:

1. Nyisson meg egy szövegszerkesztőt, és illessze be az alábbi parancsfájlt.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Mentse a fájlt egy olyan névvel, mint a *GetLabVMParams. ps1*, és jelentkezzen be a verziókövetés rendszerébe. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Kiadási folyamat létrehozása az Azure Pipelinesban

Új kiadási folyamat létrehozása:

1. Az Azure DevOps-projekt lapon válassza a **folyamatok** > **kiadások** lehetőséget a bal oldali navigációs sávon.
1. Válassza az **új folyamat**lehetőséget.
1. **A sablon kiválasztása**területen görgessen le, és válassza az **üres feladatok**elemet, majd kattintson az **alkalmaz**gombra.

### <a name="add-and-set-variables"></a>Változók hozzáadása és beállítása

A folyamat feladatai a virtuális géphez hozzárendelt értékeket használják, amikor a Azure Portalban létrehozta a Resource Manager-sablont. 

Változók hozzáadása az értékekhez: 

1. A folyamat lapon válassza a **változók** lapot.
   
1. Minden változó esetében válassza a **Hozzáadás** lehetőséget, és adja meg a nevet és az értéket:
   
   |Name (Név)|Value|
   |---|---|
   |*vmName*|A Resource Manager-sablonban hozzárendelt virtuális gép neve|
   |*userName*|A virtuális gép eléréséhez használt Felhasználónév|
   |*jelszó*|A Felhasználónév jelszava. Kattintson a Zárolás ikonra a jelszó elrejtéséhez és védelméhez.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs-beli virtuális gép létrehozása

A következő lépés az, hogy létrehozza az arany rendszerképű virtuális gépet, amelyet a későbbi üzembe helyezésekhez kíván használni. A virtuális GÉPET a *Azure DevTest Labs virtuális gép létrehozása* feladat használatával hozza létre a Azure DevTest Labs-példányon belül.

1. A kiadási folyamat **folyamata** lapon jelölje ki az **1. fázisban** található hiperhivatkozással ellátott szöveget a **szakasz feladatainak megtekintéséhez**, majd válassza ki az **+** **ügynök feladata**melletti plusz jelet. 
   
1. A **feladatok hozzáadása**területen válassza ki **Azure DevTest Labs virtuális gép létrehozása**elemet, majd válassza a **Hozzáadás**lehetőséget. 
   
1. A bal oldali ablaktáblán válassza a **létrehozás Azure DevTest Labs virtuális gép** lehetőséget. 

1. A jobb oldali panelen töltse ki az űrlapot a következőképpen:
   
   |Mező|Value|
   |---|---|
   |**Azure RM-előfizetés**|Válasszon ki egy szolgáltatási kapcsolatot vagy előfizetést az **elérhető Azure-szolgáltatási kapcsolatok** vagy az **elérhető Azure-előfizetések** közül a legördülő menüben, majd válassza az **Engedélyezés** lehetőséget, ha szükséges.<br /><br />**Megjegyzés:** További információ az Azure-előfizetéshez való korlátozott hozzáférésű engedélyek létrehozásáról: [Azure Resource Manager szolgáltatási végpont](/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).|
   |**Labor neve**|Válassza ki annak a meglévő labornak a nevét, amelyben a tesztkörnyezet virtuális gépét létre kívánja hozni.|
   |**Sablon neve**|Adja meg a forráskód-adattárba mentett sablonfájl teljes elérési útját és nevét. A beépített tulajdonságok használatával egyszerűsítheti az elérési utat, például:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Sablon paraméterei**|Adja meg a korábban definiált változók paramétereit:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Kimeneti változók** > **Lab virtuális gép azonosítója**|Adja meg a létrehozott Tesztkörnyezet virtuális gép AZONOSÍTÓjának változóját. Ha az alapértelmezett **labVMId**használja, a következő feladatokban a változót a *$ (labVMId)* értékkel tekintheti meg.<br /><br />Az alapértelmezettől eltérő nevet is létrehozhat, de ne feledje, hogy a következő feladatokban a helyes nevet használja. A labor virtuális gép AZONOSÍTÓját a következő formában írhatja be:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>A DevTest Labs-beli virtuális gép részleteinek összegyűjtése

Hajtsa végre a gyűjtése a DevTest Labs virtuális gép részletei korábban létrehozott parancsfájlt. 

1. A kiadási folyamat **folyamata** lapon jelölje ki az **1. fázisban** található hiperhivatkozással ellátott szöveget a **szakasz feladatainak megtekintéséhez**, majd válassza ki az **+** **ügynök feladata**melletti plusz jelet. 
   
1. A **feladatok hozzáadása**területen válassza a **Azure PowerShell**lehetőséget, majd válassza a **Hozzáadás**lehetőséget. 
   
1. Azure PowerShell **parancsfájl kiválasztása: FilePath** a bal oldali ablaktáblán. 
   
1. A jobb oldali panelen töltse ki az űrlapot a következőképpen:
   
   |Mező|Value|
   |---|---|
   |**Azure-kapcsolattípus**|Válassza a **Azure Resource Manager**lehetőséget.|
   |**Azure előfizetés**|Válassza ki a szolgáltatási vagy előfizetését.| 
   |**Parancsfájl típusa**|Válassza ki a **parancsfájl elérési útját**.|
   |**Parancsfájl elérési útja**|Adja meg a forráskód-adattárba mentett PowerShell-parancsfájl teljes elérési útját és nevét. A beépített tulajdonságok használatával egyszerűsítheti az elérési utat, például:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Parancsfájl argumentumai**|Adja meg az előző feladat által feltöltött *labVmId* változó nevét, például:<br /><br />`-labVmId '$(labVMId)'`|

A parancsfájl gyűjti a szükséges értékeket, és a kiadási folyamaton belül tárolja a környezeti változókat, így a következő lépésekben egyszerűen hivatkozhat rájuk.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Virtuálisgép-rendszerkép létrehozása a DevTest Labs virtuális gépről

A következő feladat az újonnan üzembe helyezett virtuális gép rendszerképének létrehozása a Azure DevTest Labs-példányban. Ezután használhatja a lemezképet, igény szerint hozhat létre a virtuális gép másolatát, ha szeretné végrehajtani egy fejlesztési feladatot, vagy egyes tesztek futtatása. 

1. A kiadási folyamat **folyamata** lapon jelölje ki az **1. fázisban** található hiperhivatkozással ellátott szöveget a **szakasz feladatainak megtekintéséhez**, majd válassza ki az **+** **ügynök feladata**melletti plusz jelet. 
   
1. A **feladatok hozzáadása**területen válassza ki **Azure DevTest Labs egyéni rendszerkép létrehozása**elemet, majd válassza a **Hozzáadás**lehetőséget. 
   
1. A feladat a következőképpen konfigurálja:
   
   |Mező|Value|
   |---|---|
   |**Azure RM-előfizetés**|Válassza ki a szolgáltatási vagy előfizetését.|
   |**Labor neve**|Válassza ki annak a meglévő labornak a nevét, amelyben a rendszerkép létre lesz hozva.|
   |**Egyéni rendszerkép neve**|Adja meg az egyéni rendszerkép nevét.|
   |**Leírás** választható|Adjon meg egy leírást, amely megkönnyíti a megfelelő rendszerkép kiválasztását később.|
   |**Forrás labor VM** > -**forrás Tesztkörnyezet virtuális gép azonosítója**|Ha módosította az LabVMId változó alapértelmezett nevét, adja meg itt. Az alapértelmezett érték **$(labVMId)** .|
   |**Kimeneti változók** > **Egyéni rendszerkép-azonosítója**|Szükség esetén módosíthatja a változó alapértelmezett nevét.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Az alkalmazás üzembe helyezése a DevTest Labs-beli virtuális gépen (opcionális)

Hozzáadhat feladatokat az alkalmazás üzembe helyezéséhez az új DevTest Labs-beli virtuális gépen. Az alkalmazás üzembe helyezéséhez leggyakrabban használt feladatok az *Azure file Copy* és *a PowerShell a célszámítógépen*.

Ezeknek a feladatoknak a paraméterei számára szükséges virtuálisgép-információk tárolása három, **labVmRgName**, **labVMIpAddress**és **labVMFqdn** nevű konfigurációs változóban történik a kiadási folyamaton belül. Ha csak szeretné létrehozása a DevTest Labs virtuális gép és a egy egyéni rendszerképet, az alkalmazás üzembe helyezése nélkül kísérletezhet, kihagyhatja ezt a lépést.

### <a name="delete-the-vm"></a>A virtuális gép törlése

A végső feladat az Azure DevTest Labs-példányban üzembe helyezett virtuális gép törlése. A virtuális gép után hajtsa végre a fejlesztési feladatokat, vagy a telepített virtuális gépen futtatni a teszteket, amelyekre szüksége parancsmagokéval töröljük. 

1. A kiadási folyamat **folyamata** lapon jelölje ki az **1. fázisban** található hiperhivatkozással ellátott szöveget a **szakasz feladatainak megtekintéséhez**, majd válassza ki az **+** **ügynök feladata**melletti plusz jelet. 
   
1. A **feladatok hozzáadása**területen válassza ki **Azure DevTest Labs a virtuális gép törlése**elemet, majd kattintson a **Hozzáadás**gombra. 
   
1. A feladat a következőképpen konfigurálja:
   
   - Az **Azure RM-előfizetés**területen válassza ki a szolgáltatáshoz tartozó-vagy előfizetést. 
   - Ha módosította az LabVMId változó alapértelmezett nevét, akkor adja meg a **Tesztkörnyezet virtuális gép azonosítóját**. Az alapértelmezett érték **$(labVMId)** .
   
### <a name="save-the-release-pipeline"></a>A kiadási folyamat mentése

Az új kiadási folyamat mentése:

1. Válassza ki a kiadási folyamat lapon a **új kiadási folyamat** nevet, és adjon meg egy új nevet a folyamatnak. 
   
1. Válassza a **Mentés** ikont a jobb felső sarokban. 

## <a name="create-and-run-a-release"></a>Kiadás létrehozása és futtatása

Kiadás létrehozása és futtatása az új folyamat használatával:

1. A kiadási folyamat lap jobb felső sarkában válassza a **kiadás létrehozása** lehetőséget. 
   
1. Az **összetevők területen válassza**ki a legújabb buildet, majd válassza a **Létrehozás**lehetőséget.
   
1. Az egyes kiadási fázisokban frissítse a Azure Portal DevTest Labs-példányának nézetét a virtuális gépek létrehozásának, rendszerképének létrehozásának és a virtuális gép törlésének megtekintéséhez.

Az egyéni rendszerkép használatával virtuális gépeket hozhat létre, amikor csak szüksége van rájuk.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [több virtuális gépes környezet létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
- Ismerje meg a további a rövid útmutató Resource Manager-sablonokkal a DevTest Labs automation a [nyilvános DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-quickstart-templates).
- Ha szükséges, tekintse meg az [Azure DevOps hibaelhárítási](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) oldalát.
 
