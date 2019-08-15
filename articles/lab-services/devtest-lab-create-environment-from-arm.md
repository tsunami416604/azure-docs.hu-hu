---
title: Több virtuális gépre kiterjedő környezetek és a Pásti-erőforrások létrehozása Azure Resource Manager-sablonokkal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre több virtuális gépre kiterjedő környezeteket és a Azure DevTest Labst egy Azure Resource Manager sablonból
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2019
ms.author: spelluru
ms.openlocfilehash: 51c699f9b392be5f2e2bc16b5729d6567ace7f17
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016244"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Több virtuális gépre kiterjedő környezetek és Pásti-erőforrások létrehozása Azure Resource Manager-sablonokkal

Azure DevTest Labs környezetek lehetővé teszik a felhasználók számára, hogy a labor határain belül konzisztens módon telepítsék az összetett infrastruktúrákat. [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) -sablonokkal környezeteket hozhat létre az DevTest Labs erőforrásainak készletével. Ezek a környezetek a Resource Manager-sablonok által létrehozott Azure-erőforrásokat is tartalmazhatnak. 

A [Azure Portal](https://portal.azure.com)használatával könnyedén [hozzáadhat egy virtuális gépet (VM)](devtest-lab-add-vm.md) a laborhoz. A többrétegű webalkalmazások vagy egy SharePoint-farmok esetében azonban olyan mechanizmusra van szükség, amely egyetlen lépésben több virtuális gépet hoz létre. Azure Resource Manager sablonok használatával megadhatja az Azure-megoldás infrastruktúráját és konfigurációját, és ismételten telepítheti a több virtuális gépet konzisztens állapotba. 

Azure Resource Manager sablonok a következő előnyöket is biztosítják:

- Azure Resource Manager a sablonok közvetlenül a GitHubról vagy az Azure Repos forrás-ellenőrzési adattárból tölthetők be.
- A felhasználók létrehozhatnak egy környezetet úgy, hogy kiválasztanak egy konfigurált Azure Resource Manager sablont a Azure Portalból, ugyanúgy, mint más [](devtest-lab-comparing-vm-base-image-types.md)típusú virtuálisgép-bázisokkal.
- Egy Azure Resource Manager sablonból kiépítheti az Azure IaaS-erőforrásokat, valamint a környezetekben lévő virtuális gépeket is.
- A laborban lévő környezetek költségeit nyomon követheti a más típusú adatbázisok által létrehozott egyéni virtuális gépeken kívül is. A rendszer létrehozza a Pásti-erőforrásokat, és megjelenik a Cost Tracking szolgáltatásban. A virtuális gép automatikus leállítása azonban nem vonatkozik a Pásti-erőforrásokra.

Ha többet szeretne megtudni arról, hogy a Resource Manager-sablonok hogyan telepíthetnek, frissíthetnek vagy törölhetnek számos laboratóriumi erőforrást egyetlen műveletben, tekintse meg a [Resource Manager-sablonok használatának előnyei](../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager)című témakört.

> [!NOTE]
> Ha egy Resource Manager-sablont használ a laboratóriumi virtuális gépek létrehozásához, a több virtuális gép vagy egyetlen virtuális gép létrehozása között néhány különbség áll fenn. További információ: [virtuális gép Azure Resource Manager sablonjának használata](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs nyilvános környezetek használata
Azure DevTest Labs rendelkezik olyan [Azure Resource Manager sablonok nyilvános tárházával](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , amelyek használatával környezeteket hozhat létre anélkül, hogy külső GitHub-forráshoz kellene csatlakoznia. Ez a nyilvános tárház hasonló az összes létrehozott labor Azure Portalban elérhető összetevők nyilvános tárházához. A környezeti tárház segítségével gyorsan megkezdheti a néhány bemeneti paraméterrel rendelkező előre létrehozott környezeti sablonokat. Ezek a sablonok zökkenőmentes, első lépéseket biztosítanak a további, a laborokon belüli Pásti-erőforrásokhoz. 

A nyilvános tárházban a DevTest Labs csapata és mások olyan gyakran használt sablonokat hoztak létre és osztottak meg, mint például az Azure Web Apps, a Service Fabric a fürt és a fejlesztői SharePoint-farmok környezete. Ezeket a sablonokat közvetlenül is használhatja, vagy testre szabhatja az igényeinek megfelelően. További információ: [nyilvános környezetek konfigurálása és használata a DevTest Labs](devtest-lab-configure-use-public-environments.md)szolgáltatásban. A saját sablonok létrehozása után a tárházban tárolhatja őket másokkal, vagy beállíthatja saját git-tárházát.

<a name="configure-your-own-template-repositories"></a> 
## <a name="create-your-own-template-repositories"></a>Saját sablon létrehozása

Az infrastruktúra-kóddal és a konfigurációval kapcsolatos ajánlott eljárások egyike a forrás vezérlőelemben kell kezelnie a környezeti sablonokat. Azure DevTest Labs követi ezt a gyakorlatot, és betölti az összes Azure Resource Manager-sablont közvetlenül a GitHubról vagy az Azure Repos-tárházból. Ennek eredményeképpen a teljes kiadási ciklusban használhatja a Resource Manager-sablonokat a tesztkörnyezet és az éles környezet között.

A Azure Resource Manager-sablonok egy adattárban való rendszerezéséhez számos szabályt kell követnie:

- A *azuredeploy. JSON*nevű fő sablont kell megadnia. 
  
- Ha paraméterként megadott paramétereket kíván használni, a *azuredeploy. Parameters. JSON*nevű paramétert kell megadni.
  
  A paramétereket `_artifactsLocation` és `_artifactsLocationSasToken` a parametersLink URI értékét is felhasználhatja, így a DevTest Labs automatikusan kezelhet beágyazott sablonokat. További információ: [beágyazott Azure Resource Manager-sablonok központi telepítése tesztelési környezetekhez](deploy-nested-template-environments.md).
  
- A metaadatokat úgy definiálhatja, hogy megadja a sablon megjelenítendő nevét és leírását egy *metadata. JSON*nevű fájlban, a következőképpen:
  
  ```json
  { 
    "itemDisplayName": "<your template name>", 
    "description": "<description of the template>" 
  }
  ```

![Azure Resource Manager sablon fájljai](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sablon adattárainak hozzáadása a laborhoz

A tárház létrehozása és konfigurálása után a Azure Portal a következő paranccsal adhatja hozzá a laborhoz: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A Labs listából válassza ki a kívánt labort. 
1. A labor **Áttekintés** paneljén válassza a **konfiguráció és szabályzatok**lehetőséget.
   
   ![Konfigurálás és szabályzatok](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)
   
1. A **konfigurációs és házirend** -beállítások listából válassza a **tárak**elemet. A **nyilvános** összetevő tárháza automatikusan létrejön az összes laborhoz, és csatlakozik a [DevTest Labs nyilvános GitHub](https://github.com/Azure/azure-devtestlab)-tárházához.
   
1. A Azure Resource Manager sablon adattárának hozzáadásához válassza a **Hozzáadás**lehetőséget.
   
   ![Nyilvános tárház](./media/devtest-lab-create-environment-from-arm/public-repo.png)
   
1. A **Tárházak** ablaktáblán adja meg a következő adatokat:
   
   - **Név**: Adja meg a laborban használni kívánt tárház nevét.
   - **Git-klón URL-címe**: Adja meg a git HTTPS-klón URL-címét a GitHubról vagy az Azure Repos-ból. 
   - **Ág** (nem kötelező): Adja meg az ág nevét az Azure Resource Manager-sablon definícióinak eléréséhez.
   - **Személyes hozzáférési jogkivonat**: Adja meg az adattár biztonságos eléréséhez használt személyes hozzáférési jogkivonatot.
     - A token Azure reposből való lekéréséhez a profil alatt válassza a **felhasználói beállítások** > **biztonsági** > **személyes hozzáférési tokenek**lehetőséget.
     - Ha szeretné lekérni a tokent a githubról, a profil alatt válassza a **Beállítások** > **fejlesztői beállítások** > **személyes hozzáférési**jogkivonatok lehetőséget.
   - **Mappa elérési útjai**: Adja meg azt a mappa elérési útját, amely az összetevő-definíciók vagy a Azure Resource Manager-sablon definíciói esetében a git-klón URI-hoz képest relatív. 
   
1. Kattintson a **Mentés** gombra.
   
   ![Új tárház hozzáadása](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Miután hozzáadta Azure Resource Manager sablont a laborhoz, a labor felhasználói a sablon használatával hozhatnak létre környezeteket. 

## <a name="configure-access-rights-for-lab-users"></a>A tesztkörnyezet felhasználói hozzáférési jogosultságának konfigurálása

A labor felhasználói alapértelmezés szerint rendelkeznek olvasói szerepkörrel, így nem módosíthatják a környezeti erőforráscsoport erőforrásait. Például nem állíthatják le és nem indíthatják el erőforrásaikat. 

Az alábbi lépéseket követve biztosíthatja, hogy a labor-felhasználók **közreműködő** szerepkörrel szerkesszék a környezetében található erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)a labor **Áttekintés** paneljén válassza a **konfiguráció és házirendek**lehetőséget, majd válassza a **tesztkörnyezet beállításai**lehetőséget.
   
1. A **labor beállításai** ablaktáblán válassza a **közreműködő**lehetőséget, majd válassza a **Mentés** lehetőséget, ha írási engedélyeket szeretne adni a labor felhasználói számára.
   
   ![Tesztkörnyezet felhasználói hozzáférési jogosultságának konfigurálása](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A következő szakasz végigvezeti a környezetek létrehozásán egy Azure Resource Manager sablonból.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Környezetek létrehozása sablonokból a Azure Portal

Miután hozzáadta Azure Resource Manager sablont a laborhoz, a labor felhasználói a következő lépésekkel hozhatnak létre környezeteket a Azure Portalban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
   
1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
   
1. A Labs listából válassza ki a kívánt labort. 
   
1. A labor lapon válassza a **Hozzáadás**lehetőséget.
   
1. Az alapszintű ablaktábla **választása** megjeleníti a használni kívánt alapképeket, valamint a Azure Resource Manager sablonokat. Válassza ki a kívánt Azure Resource Manager sablont.
   
   ![Alap kiválasztása](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
   
1. A **Hozzáadás** ablaktáblán adja meg a környezeti felhasználók számára megjelenítendő **környezeti név** értéket. 
   
   A Azure Resource Manager sablon meghatározza a beviteli mezők többi részét. Ha a sablon *azuredeploy. paraméter. JSON* fájlja az alapértelmezett értékeket határozza meg, akkor a beviteli mezők ezeket az értékeket jelenítik meg. 
   
   A *Secure string*típusú paraméterek esetében használhatja a Azure Key Vault titkos kulcsait. A titkos kulcsok egy kulcstartóban való tárolásával és a laboratóriumi erőforrások létrehozásakor történő használatával kapcsolatos további információkért lásd: [a titkok tárolása Azure Key Vaultban](devtest-lab-store-secrets-in-key-vault.md).  
   
   ![Ablaktábla hozzáadása](./media/devtest-lab-create-environment-from-arm/add.png)
   
   > [!NOTE]
   > A következő paraméterérték nem jelennek meg a bemeneti mezőkben, még akkor is, ha a sablon megadja azokat. Ehelyett az űrlap olyan üres beviteli mezőket jelenít meg, ahol a labor felhasználóknak értékeket kell megadniuk a környezet létrehozásakor.
   > 
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD 
   
1. A környezet létrehozásához válassza a **Hozzáadás** lehetőséget. 
   
   A környezet azonnal elindítja az üzembe helyezést, és a **virtuális gépek** listájában látható állapot jelenik meg. A labor automatikusan létrehoz egy új erőforráscsoportot a Azure Resource Manager sablonban definiált összes erőforrás kiépítéséhez.
   
1. A környezet létrehozása után válassza ki a környezetet a **saját virtuális gépek** listájában, és nyissa meg az erőforráscsoport ablaktáblát, és keresse meg a környezet által kiépített összes erőforrást.
   
   ![Környezeti erőforrások](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   A környezetet úgy is kiterjesztheti, hogy csak a telepített virtuális gépek listáját jelenítse meg.
   
   ![Virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)
   
1. Válassza ki a környezetek bármelyikét az elérhető műveletek megtekintéséhez, például az összetevők alkalmazásához, az adatlemezek csatolásához, az automatikus leállítási idő módosításához és egyebekhez.
   
   ![Környezeti műveletek](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a> 
## <a name="automate-environment-creation-with-powershell"></a>Környezet-létrehozás automatizálása a PowerShell-lel

Lehetséges, hogy a Azure Portal használatával egyetlen környezetet vehet fel egy laborba, de ha egy fejlesztési vagy tesztelési forgatókönyvnek több környezetet kell létrehoznia, az automatikus üzembe helyezés jobb megoldás. 

A folytatás előtt győződjön meg arról, hogy rendelkezik egy Azure Resource Manager sablonnal, amely meghatározza a létrehozandó erőforrásokat. [Adja hozzá és konfigurálja a sablont egy git](#configure-your-own-template-repositories)-tárházban, és [adja hozzá a tárházat a laborhoz](#add-template-repositories-to-the-lab).

A következő minta-szkript létrehoz egy környezetet a laborban. A megjegyzések segítenek jobban megérteni a szkriptet. 

1. Mentse a következő PowerShell-parancsfájlt a merevlemezre *deployenv. ps1*néven. 
  
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   ```powershell
   #Requires -Module Az.Resources
   
   [CmdletBinding()]
   
   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,
   
   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,
   
   # Name of the connected repository in the lab 
   [string] [Parameter(Mandatory=$true)] $RepositoryName,
   
   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,
   
   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,
   
   # The parameters to be passed to the template. Each parameter is prefixed with "-param_". 
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName", 
   # the string in $Params will have the form: -param_TestVMName MyVMName. 
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )
   
   # Sign in to Azure. 
   # Comment out the following statement to completely automate the environment creation. 
   Connect-AzAccount
   
   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
   
   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
           
   # Get information about the lab, such as lab location. 
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
       
   # Get information about the repository in the lab. 
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 
   
   # Get information about the Resource Manager template base for the environment. 
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 
   
   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()
   
   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }
   
   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 
   
   # Now, create or deploy the environment in the lab by using the New-AzResource command. 
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force 
    
   Write-Output "Environment $EnvironmentName completed."
   ```
   
1. Futtassa a parancsfájlt az alábbiak szerint, a SubscriptionId, a LabName, a ResourceGroupName, a RepositoryName, a TemplateName (a git-tárház mappája) és a EnvironmentName megadott értékeinek használatával.
   
   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv" 
   ```

Az Azure CLI használatával erőforrás-kezelői sablonokkal is üzembe helyezhet erőforrásokat. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md).

> [!NOTE]
> Csak a labor tulajdonosi engedélyekkel rendelkező felhasználók hozhatnak létre virtuális gépeket egy Resource Manager-sablonból Azure PowerShell használatával. Ha egy Resource Manager-sablonnal szeretné automatizálni a virtuális gépek létrehozását, és csak felhasználói engedélyekkel rendelkezik, használhatja a CLI parancsot az [Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create)paranccsal.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>A Resource Manager-sablonokra vonatkozó korlátozások a DevTest Labs szolgáltatásban 

A DevTest Labs Resource Manager-sablonjainak használatakor vegye figyelembe ezeket a korlátozásokat:

- A Resource Manager-sablonok nem hivatkozhatnak a legtöbb meglévő erőforrásra. Csak új erőforrásokat hozhatnak létre. Ha olyan Resource Manager-sablonokat használ, amelyek a meglévő erőforrásokra hivatkozó DevTest Labs-en kívül vannak, akkor nem használhatja őket a DevTest Labs szolgáltatásban. Az egyetlen kivétel, hogy hivatkozhat egy meglévő virtuális hálózatra. 
  
- Resource Manager-sablonból létrehozott laboratóriumi virtuális gépekről nem hozhat létre képleteket és egyéni lemezképeket. 
  
- A legtöbb szabályzatot a Resource Manager-sablonok telepítésekor nem értékeli ki a rendszer.
  
  Előfordulhat például, hogy egy olyan labor-szabályzattal rendelkezik, amely szerint a felhasználó csak öt virtuális gépet hozhat létre. A felhasználó azonban telepíthet olyan Resource Manager-sablont, amely több tucat virtuális gépet hoz létre. A nem értékelt házirendek a következők:
  
  - Virtuális gépek száma felhasználónként
    
  - A prémium szintű virtuális gépek száma tesztkörnyezetben felhasználónként
    
  - A prémium szintű lemezek száma tesztkörnyezetben felhasználónként

## <a name="next-steps"></a>További lépések
- Miután létrehozta a virtuális gépet, a virtuális gép felügyeleti paneljén a **Kapcsolódás** lehetőségre kattintva csatlakozhat a virtuális géphez.
- A környezet erőforrásainak megtekintéséhez és kezeléséhez válassza ki a környezetet a **saját virtuális gépek** listájában a laborban. 
- Ismerkedjen meg a [Azure Resource Manager-sablonokkal az Azure Gyorsindítás sablon-galériájában](https://github.com/Azure/azure-quickstart-templates).
