---
title: Műterméktár hozzáadása a laborhoz az Azure DevTest Labs ben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy műtermék-tárházat a laborhoz az Azure DevTest labsben.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770245"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Műterméktár hozzáadása a laborhoz a DevTest Labs-ben
DevTest Labs lehetővé teszi, hogy adjon meg egy összetevőt hozzá kell adni a virtuális gép létrehozásakor a virtuális gép létrehozásakor, vagy a virtuális gép létrehozása után. Ez a műtermék lehet egy eszköz vagy egy alkalmazás, amely a virtuális gépre szeretne telepíteni. A műtermékek egy GitHub- vagy Azure DevOps Git-tárházból betöltött JSON-fájlban vannak definiálva.

A DevTest Labs által karbantartott [nyilvános műterméktár](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)számos közös eszközt biztosít Windows és Linux rendszerhez. A tárházra mutató hivatkozás automatikusan hozzáadódik a tesztkörnyezethez. Létrehozhat saját műtermék-tárházat olyan speciális eszközökkel, amelyek nem érhetők el a nyilvános műterméktárban. Az egyéni összetevők létrehozásáról az [Egyéni összetevők létrehozása című](devtest-lab-artifact-author.md)témakörben olvashat.

Ez a cikk az egyéni må±termÃ©k használatával az egyéni műtermék-tárház hozzáadásáról, az Azure Resource Management-sablonok és az Azure PowerShell használatával. PowerShell- vagy CLI-parancsfájlok írásával automatizálhatja a műterméktártár hozzáadása a laborhoz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha hozzá szeretne adni egy adattárat a laborhoz, először is fontos információkat kell leszereznie a tárházból. Az alábbi szakaszok ismertetik, hogyan szerezheti be a Szükséges információkat a **GitHubon** vagy az **Azure DevOps-on**üzemeltetett adattárakhoz.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-tárház klónozási URL-címének és személyes hozzáférési jogkivonatának beszereznie

1. Nyissa meg a GitHub-tárház kezdőlapját, amely a műtermék- vagy erőforrás-kezelősablon-definíciókat tartalmazza.
2. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
3. Ha az URL-címet a vágólapra szeretné másolni, jelölje ki a **HTTPS klón url gombját.** Mentse az URL-címet későbbi használatra.
4. A GitHub jobb felső sarkában jelölje ki a profilképet, majd válassza a **Beállítások**lehetőséget.
5. A bal oldali **Személyes beállítások** menüben válassza a **Fejlesztői beállítások lehetőséget**.
6. Válassza a **személyes hozzáférési jogkivonatok lehetőséget** a bal oldali menüben.
7. Válassza **az Új jogkivonat létrehozása**lehetőséget.
8. Az **Új személyes hozzáférési jogkivonat** lap **Token leírása**csoportban adjon meg egy leírást. Fogadja el az alapértelmezett elemeket a **Hatókörkiválasztása**csoportban, majd válassza **a Jogkivonat létrehozása**lehetőséget.
9. Mentse a generált jogkivonatot. A token később használható.
10. Zárja be a GitHubot.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Az Azure Repos klónozott URL-címének és a személyes hozzáférési jogkivonatának beszereznie
1. Nyissa meg a csoportgyűjtemény kezdőlapját (például `https://contoso-web-team.visualstudio.com`), és válassza ki a projektet.
2. A projekt kezdőlapján válassza a **Kód**lehetőséget.
3. A klón URL-címének megtekintéséhez a projekt **kódlapján** válassza a **Klónozás**lehetőséget.
4. Mentse az URL-címet. Az URL-címet később használhatja.
5. Személyes hozzáférési jogkivonat létrehozásához a felhasználói fiók legördülő menüjében válassza a **Saját profil lehetőséget.**
6. A profilinformációs lapon válassza a **Biztonság**lehetőséget.
7. A **Személyes > biztonsági jogkivonatok** lapon válassza a **+ Új jogkivonat**lehetőséget.
8. Új **személyes hozzáférési jogkivonat létrehozása** lapon:
   1. Adja meg a jogkivonat **nevét.**
   2. A **Szervezet** listában válassza a **Minden akadálymentes szervezet lehetőséget.**
   3. A **Lejárati (UTC)** listában válassza ki a **90 napot**vagy az egyénileg meghatározott lejárati időszakot.
   4. Válassza a **Teljes hozzáférés** lehetőséget a hatókörökhöz.
   5. Kattintson a **Létrehozás** gombra.
9. Az új jogkivonat megjelenik a **személyes hozzáférési jogkivonatok** listájában. Válassza **a Jogkivonat másolása**lehetőséget, majd mentse a token értékét későbbi használatra.
10. Folytassa a Labor csatlakoztatása a tárház szakaszhoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz lépéseket tartalmaz egy må±termÃ©k-tárház hozzáadása az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **További szolgáltatások**lehetőséget, majd a Szolgáltatások listájából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a labort.
4. Válassza a bal oldali menü **Konfiguráció és házirendek parancsát.**
5. Válassza a bal oldali menü **Külső erőforrások** csoport Ban válassza az **Adattárak** lehetőséget.
6. Válassza a **+ Hozzáadás lehetőséget** az eszköztáron.

    ![A Tárház hozzáadása gomb](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Az **Adattárak** lapon adja meg a következő információkat:
   1. **Név**. Adja meg a tárház nevét.
   2. **Git klón URL**. Adja meg a Git HTTPS klón URL-jét, amelyet korábban másolt a GitHubról vagy az Azure DevOps-szolgáltatásokból.
   3. **Ág**. A definíciók beírásához adja meg az ágat.
   4. **Személyes hozzáférési jogkivonat**. Adja meg a személyes hozzáférési jogkivonatot, amelyet korábban kapott a GitHubról vagy az Azure DevOps-szolgáltatásokból.
   5. **Mappaelérési utak**. Adjon meg legalább egy mappaelérési utat a műtermék- vagy Erőforrás-kezelő sablondefinícióit tartalmazó klónozó URL-címhez képest. Ha alkönyvtárat ad meg, győződjön meg arról, hogy a perjelet a mappa elérési útjába helyezi.

        ![Adattárak területe](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablon használata
Az Azure Resource Management (Azure Resource Manager) sablonok Olyan JSON-fájlok, amelyek leírják a létrehozni kívánt erőforrásokat az Azure-ban. Ezekről a sablonokról további információt az [Azure Resource Manager-sablonok készítése című témakörben talál.](../azure-resource-manager/templates/template-syntax.md)

Ez a szakasz egy összetevő-tárház hozzáadása egy labor egy Azure Resource Manager-sablon használatával.  A sablon létrehozza a tesztkörnyezetet, ha még nem létezik.

### <a name="template"></a>Sablon
A cikkben használt mintasablon a következő információkat gyűjti a paramétereken keresztül. A legtöbb paraméter intelligens alapértelmezett értékekkel rendelkezik, de néhány értéket meg kell adni. Meg kell adnia a labor nevét, a må±termÃ©k-tárház URI-ját és a tárház biztonsági jogkivonatát.

- Labor neve.
- A műterméktár a DevTest Labs felhasználói felületén (UI) lévő műterméktár nevének megjelenítése. Az alapértelmezett érték `Team Repository`a következő: .
- uri-t a tárházba `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`(Példa: vagy .
- Elágazás a tárházban, amely összetevőket tartalmaz. Az alapértelmezett érték `master`a következő: .
- Az összetevőket tartalmazó mappa neve. Az alapértelmezett érték `/Artifacts`a következő: .
- A tárház típusa. Az engedélyezett `VsoGit` `GitHub`értékek vagy .
- A tárház hozzáférési jogkivonata.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>A sablon üzembe helyezése
Többmódon is üzembe helyezheti a sablont az Azure-ban, és az erőforrás létrehozása, ha nem létezik, vagy frissített, ha létezik. További részletek a következő cikkekben:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/templates/deploy-cli.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal](../azure-resource-manager/templates/deploy-portal.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)

Menjünk előre, és tekintse meg, hogyan telepítheti a sablont a PowerShellben. A sablon üzembe helyezéséhez használt parancsmagok környezetfüggőek, így a jelenlegi bérlő és az aktuális előfizetés használatos. Használja [a Set-AzContext-t](/powershell/module/az.accounts/set-azcontext) a sablon üzembe helyezése előtt, ha szükséges, a környezet módosításához.

Először hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. Ha a használni kívánt erőforráscsoport már létezik, hagyja ki ezt a lépést.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Ezután hozzon létre egy központi telepítést az erőforráscsoportba a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)használatával. Ez a parancsmag alkalmazza az erőforrás-módosításokat az Azure-ra. Egy adott erőforráscsoporthoz több erőforrás-telepítés is eladható. Ha több alkalommal telepíti ugyanahhoz az erőforráscsoporthoz, győződjön meg arról, hogy az egyes központi telepítések neve egyedi.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

A New-AzResourceGroupDeployment sikeres futtatása után a parancs olyan fontos információkat ad ki, mint a létesítési állapot (sikeresnek kell lennie) és a sablon kimenetei.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ez a szakasz egy minta PowerShell-parancsfájlt biztosít, amely egy må±termÃ©k-tároló hozzáadása egy laborhoz. Ha nem rendelkezik Az Azure PowerShell, olvassa el [az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview?view=azps-1.2.0) részletes utasításokat a telepítéshez.

### <a name="full-script"></a>Teljes szkript
Itt van a teljes script, beleértve néhány részletes üzenetek és megjegyzések:

**Új-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása
A következő példa bemutatja a parancsfájl futtatását:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Paraméterek
A minta PowerShell-parancsfájl ebben a cikkben a következő paramétereket veszi:

| Paraméter | Leírás |
| --------- | ----------- |
| Laborneve | A labor neve. |
| ArtifactRepositoryName | Az új műtermék-tárház neve. A parancsfájl véletlenszerű nevet hoz létre a respository számára, ha nincs megadva. |
| ArtifactRepositoryDisplayName | A műtermék-tárház megjelenítendő neve. Ez a név jelenik meg azhttps://portal.azure.com) Azure Portalon ( egy tesztkörnyezet összes összetevő-tárházának megtekintésekor. |
| RepositoryUri | Uri-t a tárházba. `https://github.com/<myteam>/<nameofrepo>.git` Példák: `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`vagy .|
| RepositoryBranch | Az ág, amelyben műtermékfájlok találhatók. Alapértelmezés szerint a "master". |
| FolderPath (Mappaelérési út) | Az a mappa, amely alatt az összetevők megtalálhatók. Alapértelmezésszerint '/Műtermékek' |
| PersonalAccessToken | Biztonsági jogkivonat a GitHub vagy a VSOGit tárház eléréséhez. A személyes hozzáférési jogkivonat bekérésével kapcsolatos utasításokat az előfeltételek című szakaszban találja. |
| SourceType | Akár VSOGit vagy GitHub-tárház. |

A tárház maga is szüksége van egy belső nevet az azonosításhoz, amely eltér az Azure Portalon látható megjelenítendő névtől. Nem látja a belső nevet az Azure Portalon, de az Azure REST API-k vagy az Azure PowerShell használatakor láthatja. A parancsfájl nevet ad, ha nincs a parancsfájlunk felhasználója által megadott.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>A parancsfájlban használt PowerShell-parancsok

| PowerShell-parancs | Megjegyzések |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ez a parancs a tesztkörnyezet részleteinek, például a helyének a leésre szolgál. |
| [Új-AzResource](/powershell/module/az.resources/new-azresource) | Nincs külön parancs a műtermék-tárolók hozzáadásához. Az általános [New-AzResource](/powershell/module/az.resources/new-azresource) parancsmag végzi el a feladatot. Ennek a parancsmagnak a **ResourceId** vagy a **ResourceName** és **a ResourceType** pár ra van szüksége a létrehozandó erőforrás típusának ismeretében. Ez a mintaparancsfájl az erőforrás nevét és az erőforrástípus-párt használja. <br/><br/>Figyelje meg, hogy a må±termÃ©k-tároló forrását ugyanazon a helyen és a laborban ugyanazon az erőforráscsoport alatt hozza létre.|

A parancsfájl új erőforrást ad hozzá az aktuális előfizetéshez. A [Get-AzContext](/powershell/module/az.accounts/get-azcontext) segítségével tekintse meg ezt az információt. A [Set-AzContext](/powershell/module/az.accounts/set-azcontext) segítségével állítsa be az aktuális bérlőt és előfizetést.

Az erőforrás nevének és erőforrástípus-adatainak felderítése a legjobb módja a [Test Drive Azure REST API-k](https://azure.github.io/projects/apis/) webhelyének használata. Tekintse meg a [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) szolgáltatót a DevTest Labs-szolgáltató elérhető REST API-jainak megtekintéséhez. A parancsfájl a következő erőforrás-azonosítót teszi lehetővé.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Az erőforrástípus minden, amely az URI-ban a "szolgáltatók" után szerepel, kivéve a göndör zárójelben felsorolt elemeket. Az erőforrás neve mindent, ami a göndör zárójelben látható. Ha egynél több elem várható az erőforrás nevéhez, válassza el az egyes elemeket egy perjellel, ahogy tettük.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>További lépések
- [Kötelező összetevők megadása a laborhoz az Azure DevTest Labsben](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása a DevTest Labs virtuális gépéhez](devtest-lab-artifact-author.md)
- [Műtermék-hibák diagnosztizálása a laborban](devtest-lab-troubleshoot-artifact-failure.md)
