---
title: Többvirtuális gépes környezetek és PaaS-erőforrások létrehozása sablonokkal
description: Megtudhatja, hogyan hozhat létre többvirtuális gépes környezeteket és PaaS-erőforrásokat az Azure DevTest Labs ben egy Azure Resource Manager-sablonból
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169635"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal

Az Azure DevTest Labs környezetek lehetővé teszik a felhasználók számára, hogy a labor határain belül konzisztens módon telepítsék az összetett infrastruktúrákat. [Az Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md) használatával erőforrásokat a DevTest Labs erőforrásokkal rendelkező környezeteket hozhat létre. Ezek a környezetek tartalmazhatnak olyan Azure-erőforrásokat, amelyeket a Resource Manager-sablonok létrehozhatnak.

Az [Azure Portal](https://portal.azure.com)használatával könnyedén [hozzáadhat egyszerre egy virtuális gépet egy](devtest-lab-add-vm.md) laborhoz. Azonban forgatókönyvek, például a többrétegű webalkalmazások vagy a SharePoint-farm kell egy mechanizmust, hogy hozzon létre több virtuális gépet egy lépésben. Az Azure Resource Manager-sablonok használatával meghatározhatja az Azure-megoldás infrastruktúráját és konfigurációját, és ismételten több virtuális gépet helyezhet üzembe konzisztens állapotban.

Az Azure Resource Manager-sablonok a következő előnyöket is biztosítják:

- Az Azure Resource Manager-sablonok közvetlenül a GitHub- vagy Az Azure Repos forrásvezérlő tárházból töltődnek be.
- A felhasználók létrehozhatnak egy környezetet, ha kiválasztanak egy konfigurált Azure Resource Manager-sablont az Azure Portalról, ugyanúgy, mint más típusú [virtuálisgép-bázisok.](devtest-lab-comparing-vm-base-image-types.md)
- Azure PaaS-erőforrásokat, valamint IaaS virtuális gépeket egy Azure Resource Manager-sablonból hozhat ki egy környezetben.
- Nyomon követheti a környezetek költségét a laborban, a más típusú bázisok által létrehozott egyedi virtuális gépek mellett. A PaaS-erőforrások létrejönnek, és megjelennek a költségkövetésben. A virtuális gép automatikus leállítása azonban nem vonatkozik a PaaS-erőforrásokra.

Ha többet szeretne megtudni az Erőforrás-kezelő sablonjainak egyetlen műveletben történő üzembe helyezéséhez, frissítéséhez vagy törléséhez szükséges előnyeiről, olvassa el [az Erőforrás-kezelő sablonok használatának előnyei című témakört.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Ha egy Erőforrás-kezelő sablont használ bázisként a tesztkörnyezet virtuális gépeinek létrehozásához, van néhány különbség több virtuális gép vagy egy virtuális gép létrehozása között. További információ: [A virtuális gépek Azure Resource Manager-sablonjának használata.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>A DevTest Labs nyilvános környezetének használata
Az Azure DevTest Labs rendelkezik egy [nyilvános tárház az Azure Resource Manager-sablonok,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) amely segítségével környezetek létrehozása anélkül, hogy egy külső GitHub-forráshoz saját maga. Ez a nyilvános tárház hasonló a nyilvános tárház a műtermékek, amelyek az Azure Portalon minden egyes létrehozott környezetben elérhető. A környezeti tárház lehetővé teszi, hogy gyorsan elkezdhetőbeaz előre megírt környezeti sablonokkal, amelyek kevés bemeneti paraméterrel rendelkeznek. Ezek a sablonok zökkenőmentes, első lépések élményt nyújtanak a PaaS-erőforrások laborokon belüli élményéhez.

A nyilvános tárházban a DevTest Labs csapata és mások olyan gyakran használt sablonokat hoztak létre és osztottak meg, mint az Azure Web Apps, a Service Fabric Cluster és a fejlesztői SharePoint Farm-környezet. Ezeket a sablonokat közvetlenül használhatja, vagy igény szerint testreszabhatja őket. További információ: [A nyilvános környezetek konfigurálása és használata a DevTest Labs alkalmazásban](devtest-lab-configure-use-public-environments.md)című témakörben talál. Miután létrehozta a saját sablonokat, tárolhatja őket ebben a tárházban, hogy megossza őket másokkal, vagy állítsa be a saját Git-tárház.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Saját sablontárolók létrehozása

Az infrastruktúra-kódként és a konfiguráció-kódként beállítással kapcsolatos gyakorlati tanácsok egyikeként a forrásvezérlésben kell kezelnia a környezeti sablonokat. Az Azure DevTest Labs követi ezt a gyakorlatot, és betölti az összes Azure Resource Manager-sablont közvetlenül a GitHub- vagy Azure-adattárházakból. Ennek eredményeképpen az Erőforrás-kezelő sablonok at a teljes kiadási ciklusban, a tesztkörnyezettől az éles környezetig használhatja.

Az Azure Resource Manager-sablonok tárházban való rendszerezéséhez számos követendő szabály létezik:

- El kell neveznie a fő sablonfájlt *az azuredeploy.json fájlnak.*

- Ha paraméterfájlban definiált paraméterértékeket szeretne használni, a paraméterfájlnak *azuredeploy.parameters.json*nevűnek kell lennie.

  Használhatja a paramétereket, `_artifactsLocation` és `_artifactsLocationSasToken` a paramétereklink URI-érték, amely lehetővé teszi a DevTest Labs automatikusan kezelheti a beágyazott sablonokat. További információ: [Egymásba ágyazott Azure Resource Manager-sablonok telepítése a környezetek teszteléséhez.](deploy-nested-template-environments.md)

- Metaadatok megadásával megadhatja a sablon megjelenítendő nevét és leírását a *metadata.json*nevű fájlban az alábbiak szerint:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Kulcs Azure Resource Manager sablonfájlok](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sablontárolók hozzáadása a laborhoz

Miután létrehozta és konfigurálta a tárházat, hozzáadhatja a laborhoz az Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki a kívánt labort.
1. A tesztkörnyezet **áttekintése** ablaktáblán válassza a **Konfiguráció és házirendek**lehetőséget.

   ![Konfigurálás és szabályzatok](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. A **Konfigurációs és házirendek** beállításai listából válassza az **Adattárak**lehetőséget. A **nyilvános összetevő-tárház** automatikusan létrejön az összes laborhoz, és csatlakozik a [DevTest Labs nyilvános GitHub-tárházához.](https://github.com/Azure/azure-devtestlab)

1. Az Azure Resource Manager-sablontár hozzáadásához válassza a **Hozzáadás lehetőséget.**

   ![Nyilvános tárhét](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Az **Adattárak** ablaktáblán adja meg a következő adatokat:

   - **Név**: Adja meg a tesztkörnyezetben használandó tárház nevét.
   - **Git klón URL:Adja**meg a Git HTTPS klón URL-jét a GitHubról vagy az Azure Repos.Git clone URL: Enter the Git HTTPS clone URL from GitHub or Azure Repos.
   - **Ág** (nem kötelező): Adja meg a fiók nevét az Azure Resource Manager-sablondefiníciók eléréséhez.
   - **Személyes hozzáférési jogkivonat:** Adja meg a személyes hozzáférési jogkivonatot, amely a tárház biztonságos eléréséhez használatos.
     - A token azure-tárcsákból való bekéréséhez a profil alatt válassza a **Felhasználói beállítások** > **biztonsági** > **személyes hozzáférési jogkivonatait.**
     - A token bekéseléséhez a GitHubtól, a profil alatt válassza a **Beállítások** > **fejlesztői beállítások** > **Személyes hozzáférési tokeneket.**
   - **Mappaelérési utak:** Adja meg a mappa elérési útját, amely a Git klón URI-hoz képest a műtermék-definíciók vagy az Azure Resource Manager sablondefiníciók.

1. Kattintson a **Mentés** gombra.

   ![Új tárház hozzáadása](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Miután hozzáadott egy Azure Resource Manager-sablont a laborhoz, a tesztkörnyezet-felhasználók a sablon használatával hozhatnak létre környezeteket.

## <a name="configure-access-rights-for-lab-users"></a>Hozzáférési jogok konfigurálása a tesztkörnyezet felhasználóiszámára

A Labor-felhasználók alapértelmezés szerint **Olvasó** szerepkört kapnak, így nem módosíthatják a környezeti erőforráscsoport erőforrásait. Például nem tudják leállítani vagy elindítani az erőforrásaikat.

Ha azt szeretné, hogy a tesztkörnyezet felhasználói **szerepkört** kapjanak, hogy szerkeszthessék az erőforrásokat a környezetükben, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)a labor **áttekintő** ablaktábláján válassza a **Konfiguráció és szabályzatok**lehetőséget, majd a **Lab-beállítások**lehetőséget.

1. A **Labor beállítások** ablaktáblán válassza a **Közreműködő**lehetőséget, majd a **Mentés** lehetőséget, ha írási engedélyeket szeretne adni a tesztkörnyezet-felhasználóknak.

   ![A laborfelhasználói hozzáférési jogok konfigurálása](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A következő szakasz bemutatja a környezetek létrehozása egy Azure Resource Manager sablonból.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Környezetek létrehozása sablonokból az Azure Portalon

Miután hozzáadott egy Azure Resource Manager-sablont a laborhoz, a tesztkörnyezet-felhasználók az alábbi lépésekkel hozhatnak létre környezeteket az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.

1. A tesztkörnyezet lapján válassza a **Hozzáadás**lehetőséget.

1. Az **Alap kiválasztása** ablaktábla megjeleníti az alaplemezképeket, amelyeket használhat, először az Azure Resource Manager-sablonok at. Válassza ki a kívánt Azure Resource Manager-sablont.

   ![Alap kiválasztása](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. A **Hozzáadás** ablaktáblán adjon meg egy **környezetnév-értéket** a környezet felhasználói számára.

   Az Azure Resource Manager sablon határozza meg a többi bemeneti mezők. Ha a sablon *azuredeploy.parameter.json* fájl határozza meg az alapértelmezett értékeket, a bemeneti mezők ezeket az értékeket jeleníti meg.

   A paraméterek típusú *biztonságos karakterlánc,* használhatja az Azure Key Vault titkos kulcsokat. A titkos kulcsok kulcstárolóban való tárolásáról és azok használatáról a laborerőforrások létrehozásakor című témakörben olvashat: [Tárolja a titkos kulcsokat az Azure Key Vaultban című témakörben.](devtest-lab-store-secrets-in-key-vault.md)  

   ![Ablaktábla hozzáadása](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > A következő paraméterértékek nem jelennek meg a beviteli mezőkben, még akkor sem, ha a sablon megadja őket. Ehelyett az űrlap üres beviteli mezőket jelenít meg, ahol a tesztkörnyezet-felhasználóknak értékeket kell megadniuk a környezet létrehozásakor.
   >
   > - GEN-EGYEDI
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KULCS
   > - GEN-JELSZÓ

1. A környezet létrehozásához válassza a **Hozzáadás** lehetőséget.

   A környezet azonnal megkezdi a kiépítést, és az állapot megjelenik a **Saját virtuális gépek** listában. A labor automatikusan létrehoz egy új erőforráscsoportot az Azure Resource Manager-sablonban definiált összes erőforrás kiépítéséhez.

1. A környezet létrehozása után válassza ki a környezetet a **Saját virtuális gépek** listában az erőforráscsoport ablaktáblájának megnyitásához és a környezet által kiosztott összes erőforrás tallózásához.

   ![Környezeti erőforrások](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   A környezet kibontásával csak a környezetben kiosztott virtuális gépek listáját tekintheti meg.

   ![Saját virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Válassza ki bármelyik környezetet a rendelkezésre álló műveletek megtekintéséhez, például az összetevők alkalmazásához, az adatlemezek csatolásához, az automatikus leállítási idő módosításához stb.

   ![Környezetvédelmi intézkedések](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>A környezet létrehozásának automatizálása a PowerShell segítségével

Az Azure Portal használatával egyetlen környezet hozzáadása a laborhoz, de ha egy fejlesztési vagy tesztelési forgatókönyv több környezetet kell létrehoznia, az automatikus üzembe helyezés jobb élmény.

Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik egy Azure Resource Manager sablon, amely meghatározza a létrehozandó erőforrásokat. [Adja hozzá és konfigurálja a sablont egy Git-tárházban,](#configure-your-own-template-repositories)és [adja hozzá a tárházat a tesztkörnyezethez.](#add-template-repositories-to-the-lab)

A következő mintaparancsfájl létrehoz egy környezetet a laborban. A megjegyzések segítenek jobban megérteni a szkriptet.

1. Mentse a következő powershell-parancsfájlmintát a merevlemezre *deployenv.ps1*néven.

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

1. Futtassa a parancsfájlt az alábbiak szerint, a SubscriptionId, a LabName, a ResourceGroupName, a RepositoryName, a TemplateName (a Git-tárház mappájában) és az EnvironmentName speciális értékeivel.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Az Azure CLI használatával erőforrásokat is üzembe helyezhet az Erőforrás-kezelő sablonokkal. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és Az Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md)című témakörben olvashat.

> [!NOTE]
> Csak egy labortulajdonosi engedélyekkel rendelkező felhasználó hozhat létre virtuális gépeket egy Erőforrás-kezelő sablonból az Azure PowerShell használatával. Ha erőforrás-kezelő sablonnal szeretné automatizálni a virtuális gépek létrehozását, és csak felhasználói engedélyekkel rendelkezik, használhatja a CLI parancsot, amely [labor virtuális gép létrehozása](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Az Erőforrás-kezelő sablonjainak korlátozásai a DevTest Labs programban

Vegye figyelembe ezeket a korlátozásokat, amikor a DevTest Labs erőforrás-kezelősablonjait használja:

- Az Erőforrás-kezelő sablonjai nem hivatkozhatnak a legtöbb meglévő erőforrásra. Csak új erőforrásokat hozhatnak létre. Ha a DevTest Labs en kívül használt Erőforrás-kezelő sablonokkal rendelkezik, amelyek meglévő erőforrásokra hivatkoznak, nem használhatja őket a DevTest Labs-ben. Az egyetlen kivétel, hogy egy meglévő virtuális hálózatra hivatkozhat.

- Nem hozhat létre képleteket vagy egyéni lemezképeket az Erőforrás-kezelő sablonból létrehozott tesztkörnyezet-virtuális gépekből.

- A legtöbb szabályzat nem kiértékelése az Erőforrás-kezelő sablonok telepítésekor.

  Például előfordulhat, hogy egy tesztkörnyezet szabályzat, amely a felhasználó hozhat létre csak öt virtuális gépek. A felhasználó azonban üzembe helyezhet egy Erőforrás-kezelő sablont, amely több tucat virtuális gépet hoz létre. A nem kiértékelt házirendek a következők:

  - Virtuális gépek száma felhasználónként

  - Prémium szintű virtuális gépek száma laborfelhasználónként

  - Prémium szintű lemezek száma laborfelhasználónként

## <a name="next-steps"></a>További lépések
- Miután létrehozott egy virtuális gép, csatlakozhat a virtuális gép hez a **virtuális gép** felügyeleti ablaktábláján válassza a Csatlakozás lehetőséget.
- Az erőforrások at tekintheti meg és kezelheti egy környezetben a környezetben a **Saját virtuális gépek** listában a laborban.
- Fedezze fel az [Azure Resource Manager-sablonokat az Azure gyorsútmutató sablongyűjteményéből.](https://github.com/Azure/azure-quickstart-templates)
