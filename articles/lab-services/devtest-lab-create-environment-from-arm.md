---
title: Több virtuális gépes környezet és PaaS-erőforrások létrehozása Azure Resource Manager-sablonokkal |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre több virtuális gépes környezet és PaaS-erőforrásokat az Azure DevTest Labs szolgáltatásban az Azure Resource Manager-sablon
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
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: ebe5c65f701c0a1c7c02182800a35bfbeed5b0be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181384"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Több virtuális gépes környezet és PaaS-erőforrások létrehozása Azure Resource Manager-sablonokkal

A [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lehetővé teszi, hogy könnyen [egyszerre egy virtuális gép hozzáadása egy laborhoz](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Azonban ha a környezet több virtuális gépet tartalmaz, minden virtuális gép külön-külön léteznie kell. Például egy többrétegű webalkalmazást vagy a SharePoint-farm forgatókönyvek esetén olyan mechanizmus szükséges ahhoz, hogy egyetlen lépésben több virtuális gép létrehozásához. Az Azure Resource Manager-sablonok használatával ezután határozza meg az infrastruktúra és az Azure-megoldás konfigurációját, és ismételten üzembe több virtuális gépet egy konzisztens állapotba. Ez a funkció az alábbi előnyöket nyújtja:

- Az Azure Resource Manager-sablonok töltődnek be közvetlenül a (a GitHub vagy az Azure fejlesztési és üzemeltetési szolgáltatás Git) a verziókövetési tárházzal.
- Miután konfigurálta a felhasználók létrehozna egy környezetet az Azure Portalon egy Azure Resource Manager-sablon kiválasztásával, mint a más típusú [Virtuálisgép-adatbázisok](./devtest-lab-comparing-vm-base-image-types.md).
- Azure PaaS-erőforrások kiépítése egy Azure Resource Manager-sablon IaaS virtuális gépek mellett a környezetben.
- A környezetek költségei a labor létrehozása mellett egyéb típusú bázisok által létrehozott egyedi virtuális gépek követhető nyomon.
- PaaS-erőforrásokhoz jönnek létre, és megjelenik követését; virtuális gép automatikus leállítása azonban nem vonatkozik a PaaS-erőforrásokhoz.

További információ a több [Resource Manager-sablonok használatának előnyei](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) telepíteni, frissíteni vagy törölni az összes egyetlen művelettel labor erőforrás.

> [!NOTE]
> Resource Manager-sablon alapjául való használatakor további labor virtuális gépek létrehozása, néhány különbségek vannak, ne feledje, hogy Multi- vagy egyetlen – virtuális gép létrehozásakor. [A virtuális gépek az Azure Resource Manager-sablonnal](devtest-lab-use-resource-manager-template.md) nagyobb részletesen ismertetjük a különbségeket ismerteti.
>

## <a name="devtest-labs-public-environments"></a>DevTest Labs nyilvános környezetben
Az Azure DevTest Labs rendelkezik egy [nyilvános tárházban az Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , amelyek segítségével való csatlakozáshoz külső GitHub forrás saját magának nélkül hozhat létre környezeteket. Ez a tárház például az Azure Web Apps, a Service Fabric-fürt és a fejlesztési környezet SharePoint-Farm gyakran használt sablont tartalmaz. Ez a funkció az összetevők a nyilvános tárház, amely tartalmaz minden Ön által létrehozott laborban hasonlít. A környezet tárház használatának gyors megkezdése minimális bemeneti paraméterei, zökkenőmentes első lépések felhasználói élményt biztosíthat a PaaS-erőforrásokhoz labs belül az előre létrehozott környezeti sablonokkal teszi lehetővé. További információkért lásd: [konfigurálása és használata a nyilvános környezetben a DevTest Labs szolgáltatásban](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Saját sablon tárházak konfigurálása
Az ajánlott eljárásokat az infrastruktúra mint kód és konfigurációs a kódot, egyik környezeti sablonokkal verziókövetési rendszerben kell kezelni. Az Azure DevTest Labs ezzel a gyakorlattal követi, és az összes Azure Resource Manager-sablonok közvetlenül tölt be a GitHub vagy az Azure fejlesztési és üzemeltetési szolgáltatás Git-tárházak. Ennek eredményeképpen a Resource Manager-sablonok között a teljes kiadási ciklus a tesztkörnyezetből az éles környezetbe használható.

Tekintse meg a DevTest Labs csapat által létrehozott sablonok az [nyilvános GitHub-adattárból](https://github.com/Azure/azure-devtestlab/tree/master/Environments). A nyilvános tárházban megtekintheti, hogy közvetlenül használni, vagy testre is szabhatja őket, hogy illeszkedjen az igényeihez mások által megosztott sablonok. Miután létrehozta a sablont, tárolja a tárházban, ossza meg másokkal. A saját Git-tárház környezeteket a felhőben használható sablonokkal is állíthatja. 

Van néhány a szabályok egy adattárból az Azure Resource Manager-sablonok rendszerezésére kövesse:

- Névvel kell rendelkeznie a fő sablonfájl `azuredeploy.json`. 

    ![Kulcs Azure Resource Manager-sablonfájlokat](./media/devtest-lab-create-environment-from-arm/master-template.png)

- A paraméter-fájlban definiált paraméterértékek használni kívánt, ha az alkalmazásparaméter-fájlt névvel kell rendelkeznie `azuredeploy.parameters.json`.
- A paraméterek használhatók `_artifactsLocation` és `_artifactsLocationSasToken` hozhatnak létre az parametersLink URI értéket, így a DevTest Labs automatikusan a beágyazott sablonok kezelése. További információkért lásd: [módját az Azure DevTest Labs megkönnyíti a beágyazott erőforrás-kezelő sablon-üzembehelyezések tesztelési környezetben](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Metaadatok, adja meg a sablon megjelenítendő neve és leírása lehet definiálni. A metaadatok nevű fájlba kell `metadata.json`. A következő példa metaadatfájl mutatja be a megjelenítendő név és leírás megadása: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

A következő lépések végigvezetik egy tárház hozzáadása a labor, az Azure portal használatával. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.   
1. A laborgyakorlat **áttekintése** ablaktáblán válassza előbb **Konfigurace a zásady**.

    ![Konfigurálás és szabályzatok](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Az a **Konfigurace a zásady** beállítások listáról válassza ki **Tárházak**. A **Tárházak** panel felsorolja a tárházban, amely a labor lettek hozzáadva. A tárházhoz `Public Repo` automatikusan jön létre minden labs, és csatlakozik a [DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-devtestlab) , amely tartalmazza a felhasználás több VM-összetevők.

    ![Nyilvános adattár](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Válassza ki **Add +** hozzáadása az Azure Resource Manager-sablon adattár.
1. Ha a második **Tárházak** panel megnyílik, adja meg a szükséges információkat a következő:
    - **Név** – adja meg a tárház nevet, amelyen a tesztkörnyezetben.
    - **Git clone URL-cím** – adja meg a GIT HTTPS clone URL-cím a GitHub vagy az Azure DevOps-szolgáltatásokkal.  
    - **Ág** – adja meg az Azure Resource Manager-sablon definíciók eléréséhez ág nevét. 
    - **Személyes hozzáférési tokent** – a személyes hozzáférési jogkivonat segítségével biztonságosan érhetik el a tárházat. Válassza ki a beszerezni a jogkivonatot az Azure DevOps-szolgáltatásokkal,  **&lt;Sajátneve >> saját profil > Biztonság > nyilvános hozzáférési jogkivonat**. A token beszerzése a Githubról, válassza az avatar kiválasztásával követni **beállítások > nyilvános hozzáférési jogkivonat**. 
    - **Mappa elérési útjának** – az egyik a két beviteli mezőt, adja meg a mappa elérési útja, amely - / - perjellel kezdődik, és a Git-klón URI-t vagy képest a összetevő definíciók (első szövegbeviteli mező) vagy az Azure Resource Manager-sablon definíciók .   
    
        ![Nyilvános adattár](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. A kötelező mezőket kerülnek, és adja át az ellenőrzés után válassza ki a **mentése**.

Ez a szakasz végigvezeti környezetek létrehozása az Azure Resource Manager-sablon.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Környezet létrehozása a Resource Manager-sablont az Azure portal használatával

Ha a labor létrehozása az Azure Resource Manager-sablon tárház lett konfigurálva, a labor felhasználók létrehozna egy környezetet, az alábbi lépéseket követve az Azure portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.   
1. A labor ablaktábláján válassza ki a **Add +**.
1. A **vyberte bázi** ablaktáblán megjelennek azok az alaprendszerképek lekéréshez használhatja az Azure Resource Manager-sablonok, a lista elején. Válassza ki a kívánt Azure Resource Manager-sablont.

    ![Alap kiválasztása](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Az a **Hozzáadás** panelen adja meg a **környezet neve** értéket. A környezet neve, a felhasználók számára a lab-ben jelenik meg. Az Azure Resource Manager-sablon határozza meg a fennmaradó beviteli mezőket. Ha az alapértelmezett értékek vannak definiálva a sablonban vagy a `azuredeploy.parameter.json` fájl jelen, az alapértelmezett értékek jelennek meg ezek a beviteli mezők. Paraméter típusa *biztonságos karakterlánc*, az az Azure key vaultban tárolt titkos kulcsokat is használhat. Mentése folyamatban van a titkos kulcsok a key vaultban, és használja őket, labor-erőforrások létrehozásakor kapcsolatos további információkért lásd: [Store titkos kulcsok Azure Key vaultban](devtest-lab-store-secrets-in-key-vault.md).  

    ![Hozzáadása panel](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Nincsenek több paraméterértékeket – még akkor is, ha a megadott - jelennek meg, az üres értékeket. Ezért ha a felhasználók ezeket az értékeket rendel egy Azure Resource Manager-sablonban szereplő paraméterekkel, DevTest Labs nem jeleníti meg az értékeket. Ehelyett üres szövegbeviteli mezők jelennek meg, labor felhasználókat kell adnia egy értéket a környezet létrehozásakor.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Válassza ki **Hozzáadás** a környezet létrehozásához. A környezet elindul, azonnal az állapot megjelenítése a kiépítés a **a virtual machines** listája. Az Azure Resource Manager-sablonban definiált összes erőforrás kiépítéséhez a Lab automatikusan létrejön egy új erőforráscsoportot.
1. A környezet létrehozása után válassza ki a környezetet a **a virtual machines** listában az erőforrás-csoport panel megnyitásához, és keresse meg a környezetben üzembe helyezett erőforrásokat.
    
    ![A virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   A környezet egyszerűen üzembe helyezett virtuális gépek listájának megtekintése a környezetben is kiterjesztheti.
    
    ![A virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Kattintson bármelyik a környezeteket a rendelkezésre álló műveletek – például az összetevőket, adatlemez, változó automatikus leállítási ideje és egyéb kapcsolódó alkalmazása megtekintéséhez.

    ![Környezet műveletek](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>A környezetek üzembe helyezésének automatizálása
Az Azure DevTest Labs lehetővé teszi a használatára egy [Azure Resource Management Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) olyan környezetet hozhat létre a tesztkörnyezetben erőforrások vannak beállítva. Ezekben a környezetekben, amelyek a Resource Manager-sablonok használatával lehet létrehozni Azure-erőforrások is tartalmazhat. DevTest Labs-környezetek engedélyezése a felhasználók számára könnyen központi telepítése összetett infrastruktúrák konzisztens módon belül a labor létrehozása. Jelenleg környezet hozzáadása egy laborhoz, az Azure portal használatával akkor megvalósítható, ha egyszer hozza létre, de egy fejlesztési és a egy tesztelési helyzetet, ahol több létrehozások fordulhat elő, az automatikus központi telepítési lehetővé teszi, hogy egy továbbfejlesztett felületet.

Befejeződött a következő lépéseket a [konfigurálása saját sablon tárházak](#configure-your-own-template-repositories) szakaszt, mielőtt továbblépne: 

1. A Resource Manager-sablon, amely meghatározza a létrehozott erőforrások létrehozásához. 
2. Állítsa be a Resource Manager-sablon a Git-tárház. 
3. A Git-tárház csatlakozhat a tesztkörnyezetben. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése a PowerShell-parancsprogram
A PowerShell-parancsfájl mentése a következő szakaszban a merevlemezre (például: deployenv.ps1), és futtassa a szkriptet a SubscriptionId, ResourceGroupName, LabName, RepositoryName, az értékek megadása után TemplateName (mappa), a Git-tárházban, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Példaszkript
Itt látható a minta parancsfájl olyan környezetet hozhat létre a tesztkörnyezetben. A megjegyzéseket, a parancsfájl segít jobban megérteni a parancsfájl. 

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzureRmAccount

# Select the subscription that has the lab.  
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzureRmADUser -UserPrincipalName (Get-AzureRmContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzureRmResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzureRmResource command. 
New-AzureRmResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

Azure CLI segítségével is erőforrások Resource Manager-sablonok üzembe helyezése. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Csak egy tesztlabor tulajdonosi engedélyekkel rendelkező felhasználói virtuális gépeket hozhat létre a Resource Manager-sablon Azure PowerShell használatával. Ha szeretné automatizálni a virtuális gép létrehozása Resource Manager-sablon használatával, és csak felhasználói engedélyek, használhatja a [ **az lab vm létrehozása** parancsot a parancssori felületen](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Általános korlátozások 

DevTest Labs szolgáltatásban létrehozott Resource Manager-sablon használatakor, fontolja meg ezek a korlátozások:

- Bármely létrehozása Resource Manager-sablon nem hivatkozhatnak olyan meglévő erőforrások; csak új erőforrások hivatkozhat. Emellett ha rendelkezik egy meglévő Resource Manager-sablon általában DevTest Labs-en kívül üzembe helyezett, és a meglévő erőforrások mutató hivatkozásokat tartalmaz, akkor nem használható a tesztkörnyezetben.

   Az egyetlen kivétel az, hogy Ön **is** hivatkozhat egy meglévő virtuális hálózatot. 

- A labor virtuális gépeket, amelyek egy Resource Manager-sablon alapján létrehozott képletek nem hozhatók létre. 

- Egyéni rendszerképek nem hozható létre a virtuális gépeket, amelyek egy Resource Manager-sablon alapján létrehozott tesztkörnyezet. 

- A legtöbb házirendek nem értékeli ki a Resource Manager-sablonok központi telepítésekor.

   Például hogy előfordulhat, hogy egy tesztkörnyezet szabályzat megadása, hogy a felhasználó csak öt virtuális gépeket hozhat létre. Azonban a felhasználó telepíthet egy Resource Manager-sablon, amely több tucat virtuális gépeket hoz létre. Házirendeket, amelyek nem értékeli ki a következők:

   - Virtuális gépek száma felhasználónként
   - Prémium szintű virtuális gépek labor felhasználónkénti száma
   - Prémium szintű lemezek labor felhasználónkénti száma


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Környezeti erőforrás csoport hozzáférési jogosultságok labor felhasználók konfigurálása

Labor felhasználók üzembe helyezhetnek egy Resource Manager-sablon. Azonban alapértelmezés szerint rendelkezik olvasó hozzáférési jogosultságokat, ami azt jelenti, hogy az erőforrások egy környezet erőforráscsoportban sem tudják módosítani. Nem lehet például azok leállítani, vagy indítsa el az erőforrásaikat.

Kövesse az alábbi lépéseket, hogy a labor felhasználók közreműködői hozzáférési jogokat. Ezt követően azok Resource Manager-sablon üzembe helyezése, ha azok lesz szerkesztheti az erőforrásokat, a környezetben. 


1. A laborgyakorlat **áttekintése** ablaktáblán válassza előbb **Konfigurace a zásady**.
1. Válassza ki **labor beállítások**.
1. A Lab beállítások panelen válassza ki **közreműködői** írási engedélyeket a labor-felhasználók számára.

    ![Lab felhasználói hozzáférési jogok konfigurálása](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések
* Virtuális gép létrehozása után csatlakozhat a virtuális gép kiválasztásával **Connect** a Virtuálisgép-kezelés panel.
* Erőforrás megtekintése és kezelése egy környezetben a környezetet a kiválasztásával a **a virtual machines** a labor listájában. 
* Fedezze fel a [Azure Resource Manager-sablonok Azure gyorsindítási sablon katalógusból](https://github.com/Azure/azure-quickstart-templates).
