---
title: Az Azure DevTest Labs integrálása az Azure-folyamatokba
description: Ismerje meg, hogyan integrálhatja az Azure DevTest Labs-t az Azure Pipelines folyamatos integrációs és kézbesítési folyamatába
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293215"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Az Azure DevTest Labs integrálása az Azure Pipelines CI/CD-folyamatába

Az Azure *DevTest Labs tasks* bővítményhasználatával integrálhatja az Azure Pipelines folyamatos integrációját és folyamatos kézbesítését (CI/CD) az Azure DevTest Labs-szel. A bővítmény több feladatot telepít, többek között a következőket: 

- Virtuális gép (VM) létrehozása
- Egyéni rendszerkép létrehozása virtuális gépből
- Virtuális gép törlése 

Ezek a feladatok megkönnyítik például egy *aranyszínű* virtuális gép gyors üzembe helyezését egy adott tesztfeladathoz, majd törlik a virtuális gép, amikor a teszt befejeződött.

Ez a cikk bemutatja, hogyan azure DevTest Labs feladatok létrehozása és üzembe helyezése egy virtuális gép, hozzon létre egy egyéni lemezképet, majd törölje a virtuális gép, minden egy kiadási folyamatként. A feladatokat általában a saját egyéni build, tesztelés és üzembe helyezés során hajthatja végre.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Előfeltételek

- Regisztráljon vagy jelentkezzen be az [Azure DevOps-szervezetbe,](https://dev.azure.com) és [hozzon létre egy projektet](/vsts/organizations/projects/create-project) a szervezetben. 
  
- Telepítse az Azure DevTest Labs feladatok bővítményt a Visual Studio Piactérről.
  
  1. Nyissa meg az [Azure DevTest Labs-feladatok at.](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
  1. Válassza **a Ingyenes beszerezhetése**lehetőséget.
  1. Válassza ki az Azure DevOps-szervezetet a legördülő menüből, és válassza a **Telepítés lehetőséget.** 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Hozza létre a sablont egy Azure virtuális gép létrehozásához 

Ez a szakasz bemutatja, hogyan hozhatja létre az Azure Resource Manager sablont, amely segítségével hozzon létre egy Azure virtuális gép igény szerint.

1. Ha erőforrás-kezelősablont szeretne létrehozni az előfizetésében, kövesse az [Erőforrás-kezelő használata sablon eljárását.](devtest-lab-use-resource-manager-template.md)
   
1. Az Erőforrás-kezelő sablon létrehozása előtt adja hozzá a [WinRM-összetevőt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) a virtuális gép létrehozásának részeként. A központi telepítési feladatok, például *az Azure File Copy* és a *PowerShell a célgépeken* winrm-hozzáférésre van szükségük. A rendszerfelügyeleti rendszerösszetevő paraméterként egy állomásnevet igényel, amely a virtuális gép teljesen minősített tartományneve (FQDN). 
   
   > [!NOTE]
   > Ha a WinRM-et megosztott IP-címmel használja, hozzá kell adnia egy NAT-szabályt, amely leképez egy külső portot a WinRM-porthoz. Nincs szüksége a NAT-szabályra, ha nyilvános IP-címmel hozza létre a virtuális gép.
   
   
1. Mentse a sablont a számítógépre *CreateVMTemplate.json*nevű fájlként.
   
1. Adja be a sablont a forrásvezérlő rendszerbe.

## <a name="create-a-script-to-get-vm-properties"></a>Parancsfájl létrehozása a virtuális gép tulajdonságainak bekésezéséhez

Amikor feladatlépéseket futtat, például *az Azure File Copy* vagy a *PowerShell a célgépeken* a kiadási folyamatban, a következő parancsfájl azokat az értékeket gyűjti, amelyek egy alkalmazás virtuális gépre való üzembe helyezéséhez szükséges. Általában ezeket a feladatokat használja az alkalmazás üzembe helyezéséhez egy Azure-beli virtuális gépre. A feladatok hoz értékeket, például a virtuális gép erőforráscsoport nevét, IP-címét és teljes tartománynév.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A parancsfájl létrehozása:

1. Nyisson meg egy szövegszerkesztőt, és illessze be a következő parancsfájlt.
   
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

1. Mentse a fájlt egy olyan névvel, mint *a GetLabVMParams.ps1*, és adja be a forrásvezérlő rendszerbe. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Kiadási folyamat létrehozása az Azure Pipelinesban

Új kiadási folyamat létrehozása:

1. Az Azure DevOps-projektlapján válassza a Bal oldali navigációs **sáv folyamatok kiadásait.** > **Releases**
1. Válassza a **New pipeline** (Új folyamat) lehetőséget.
1. A **Sablon kiválasztása**csoportban görgessen le, és válassza az Üres **feladat**lehetőséget, majd válassza az **Alkalmaz**lehetőséget.

### <a name="add-and-set-variables"></a>Változók hozzáadása és beállítása

A folyamatfeladatok a virtuális géphez rendelt értékeket használják, amikor létrehozta az Erőforrás-kezelő sablont az Azure Portalon. 

Változók hozzáadása az értékekhez: 

1. A folyamatlapon válassza a **Változók** lapot.
   
1. Minden változóhoz válassza a **Hozzáadás** lehetőséget, és adja meg a nevet és az értéket:
   
   |Név|Érték|
   |---|---|
   |*vmName*|Az Erőforrás-kezelő sablonban hozzárendelt virtuális gép neve|
   |*Felhasználónév*|Felhasználónév a virtuális gép eléréséhez|
   |*alaphelyzetbe állítása*|A felhasználónév jelszava. A jelszó elrejtéséhez és biztonságossá tétele érdekében válassza a lakat ikont.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs virtuális gép létrehozása

A következő lépés az aranylemezvirtuális gép létrehozása a jövőbeli telepítésekhez. A virtuális gép az Azure DevTest Labs-példányon belül az *Azure DevTest Labs virtuálisgép-létrehozása* feladat használatával hoz létre.

1. A kiadási folyamat folyamat **lapján** jelölje ki a **View stage tasks**hiperhivatkozással ellátott szöveget **+** az **Agent job** **1.** 
   
1. A **Feladatok hozzáadása csoportban**válassza az **Azure DevTest Labs Virtuálisgép létrehozása**lehetőséget, és válassza a **Hozzáadás**lehetőséget. 
   
1. Válassza **az Azure DevTest Labs virtuális gép létrehozása lehetőséget** a bal oldali ablaktáblában. 

1. A jobb oldali panelen töltse ki az űrlapot az alábbi módon:
   
   |Mező|Érték|
   |---|---|
   |**Azure RM-előfizetés**|Válasszon egy szolgáltatáskapcsolatot vagy előfizetést a **rendelkezésre álló Azure-szolgáltatási kapcsolatok** vagy **a rendelkezésre álló Azure-előfizetések** a legördülő menüben, és válassza **a Jogosult,** ha szükséges.<br /><br />**Megjegyzés:** Az Azure-előfizetéssel való korlátozottabb engedélykapcsolat létrehozásáról az [Azure Resource Manager szolgáltatásvégpont](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)című témakörben talál további információt.|
   |**Labor neve**|Válassza ki egy meglévő tesztkörnyezet nevét, amelyben a tesztkörnyezet virtuális gépjön létre.|
   |**Sablon neve**|Adja meg a forráskódtárba mentett sablonfájl teljes elérési útját és nevét. A beépített tulajdonságok kal egyszerűsítheti az elérési utat, például:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Sablon paraméterei**|Adja meg a korábban definiált változók paramétereit:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Kimeneti változók** > **Lab vm-azonosítója**|Adja meg a létrehozott tesztkörnyezet virtuálisgép-azonosító változóját. Ha az alapértelmezett **labVMId,hivatkozhat**a változó a későbbi feladatok *$(labVMId)*.<br /><br />Az alapértelmezettnévnél eltérő nevet is létrehozhat, de a későbbi feladatokban ne felejtse el a megfelelő nevet használni. A Lab vm-azonosítót a következő formában írhatja:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>A DevTest Labs virtuális gép részleteinek összegyűjtése

A DevTest Labs virtuális gép részleteinek összegyűjtéséhez hajtsa végre a korábban létrehozott parancsfájlt. 

1. A kiadási folyamat folyamat **lapján** jelölje ki a **View stage tasks**hiperhivatkozással ellátott szöveget **+** az **Agent job** **1.** 
   
1. A **Feladatok hozzáadása csoportban**válassza az **Azure PowerShell**lehetőséget, és válassza **a Hozzáadás**lehetőséget. 
   
1. Válassza az **Azure PowerShell-parancsfájl: FilePath** a bal oldali ablaktáblában. 
   
1. A jobb oldali panelen töltse ki az űrlapot az alábbi módon:
   
   |Mező|Érték|
   |---|---|
   |**Azure-kapcsolat típusa**|Válassza az **Azure Resource Manager**lehetőséget.|
   |**Azure-előfizetés**|Válassza ki a szolgáltatási kapcsolatot vagy előfizetést.| 
   |**Parancsfájl típusa**|Válassza a **Parancsfájl elérési útját**.|
   |**Parancsfájl elérési útja**|Adja meg a teljes elérési útját és nevét a PowerShell-parancsfájl, amely mentett a forráskód tárházba. A beépített tulajdonságok kal egyszerűsítheti az elérési utat, például:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Parancsfájl argumentumai**|Adja meg az előző feladat által feltöltött *labVmId* változó nevét, például:<br /><br />`-labVmId '$(labVMId)'`|

A parancsfájl összegyűjti a szükséges értékeket, és a kiadási folyamaton belül környezeti változókban tárolja őket, így a következő lépésekben könnyen hivatkozhat rájuk.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Virtuálisgép-lemezkép létrehozása a DevTest Labs virtuális gépről

A következő feladat az újonnan üzembe helyezett virtuális gép lemezképének létrehozása az Azure DevTest Labs-példányban. Ezután a lemezkép segítségével igény szerint hozhat létre másolatot a virtuális gépről, amikor fejlesztési feladatot szeretne végrehajtani, vagy néhány tesztet futtatni szeretne. 

1. A kiadási folyamat folyamat **lapján** jelölje ki a **View stage tasks**hiperhivatkozással ellátott szöveget **+** az **Agent job** **1.** 
   
1. A **Feladatok hozzáadása csoportban**válassza az **Azure DevTest Labs Egyéni lemezkép létrehozása**lehetőséget, és válassza a **Hozzáadás**lehetőséget. 
   
1. Állítsa be a feladatot az alábbiak szerint:
   
   |Mező|Érték|
   |---|---|
   |**Azure RM-előfizetés**|Válassza ki a szolgáltatási kapcsolatot vagy előfizetést.|
   |**Labor neve**|Válassza ki annak a meglévő tesztkörnyezetnek a nevét, amelyben a rendszerkép létrejön.|
   |**Egyéni kép neve**|Adja meg az egyéni kép nevét.|
   |**Leírás** (nem kötelező)|Adjon meg egy leírást, hogy később könnyen kiválaszthassa a megfelelő képet.|
   |**Forráslabor** > **vm-forrástesztjának virtuálisgép-azonosítója**|Ha módosította a LabVMId változó alapértelmezett nevét, írja be ide. Az alapértelmezett érték **$(labVMId)**.|
   |**Kimeneti változók** > **egyéni képazonosítója**|Szükség esetén módosíthatja a változó alapértelmezett nevét.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Az alkalmazás üzembe helyezése a DevTest Labs virtuális gépre (nem kötelező)

Az alkalmazás üzembe helyezéséhez az új DevTest Labs virtuális gép üzembe helyezéséhez feladatokat adhat hozzá. Az alkalmazás központi telepítéséhez általában az *Azure File Copy* és a PowerShell a *célgépeken*használható feladatok.

A feladatok paramétereihez szükséges virtuális gép adatait három **labVmRgName**, **labVMIpAddress**és **labVMFqdn** nevű konfigurációs változó tárolja a kiadási folyamaton belül. Ha csak egy DevTest Labs virtuális gép és egy egyéni lemezkép létrehozásával szeretne kísérletezni anélkül, hogy egy alkalmazást üzembe helyezne, kihagyhatja ezt a lépést.

### <a name="delete-the-vm"></a>A virtuális gép törlése

A végső feladat az Azure DevTest Labs-példányban üzembe helyezett virtuális gép törlése. Általában törölje a virtuális gép a fejlesztési feladatok végrehajtása után, vagy futtassa a szükséges teszteket az üzembe helyezett virtuális gép. 

1. A kiadási folyamat folyamat **lapján** jelölje ki a **View stage tasks**hiperhivatkozással ellátott szöveget **+** az **Agent job** **1.** 
   
1. A **Feladatok hozzáadása csoportban**válassza az **Azure DevTest Labs Delete VM (Virtuális gép törlése)** lehetőséget, és válassza **a Hozzáadás**lehetőséget. 
   
1. Állítsa be a feladatot az alábbiak szerint:
   
   - Az **Azure RM-előfizetés**csoportban válassza ki a szolgáltatáskapcsolatot vagy előfizetést. 
   - Lab **vm-azonosító**esetén, ha megváltoztatta a LabVMId változó alapértelmezett nevét, írja be ide. Az alapértelmezett érték **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>A kiadási folyamat mentése

Az új kiadási folyamat mentése:

1. Válassza ki az **Új kiadási folyamat** nevet a kiadási folyamat lapján, és adjon meg egy új nevet a folyamatnak. 
   
1. Válassza **a** Mentés ikont a jobb felső sarokban. 

## <a name="create-and-run-a-release"></a>Kiadás létrehozása és futtatása

Kiadás létrehozása és futtatása az új folyamat használatával:

1. Válassza a Kiadási folyamat jobb felső részén válassza a **Kiadás létrehozása** lehetőséget a kiadási folyamat lapon. 
   
1. Az **Eltérések csoportban**jelölje ki a legújabb buildet, majd kattintson a **Létrehozás gombra.**
   
1. Minden kiadási szakaszban frissítse a DevTest Labs-példány nézetét az Azure Portalon a virtuális gép létrehozásának, a lemezkép létrehozásának és a virtuális gép törlésének megtekintéséhez.

Az egyéni lemezkép segítségével virtuális gépeket hozhat létre, amikor csak szüksége van rájuk.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogy [miként hozhat létre többvirtuális gépes környezeteket az Erőforrás-kezelő sablonokkal.](devtest-lab-create-environment-from-arm.md)
- Fedezzen fel további rövid útmutatóerőforrás-kezelősablonokat a DevTest Labs automatizálásához a [nyilvános DevTest Labs GitHub-tárból.](https://github.com/Azure/azure-quickstart-templates)
- Ha szükséges, tekintse meg az [Azure DevOps hibaelhárítási](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) lap.
 
