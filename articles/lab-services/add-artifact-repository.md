---
title: Az összetevőtárban hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg az összetevőtárban hozzáadása az Azure DevTest labs szolgáltatásban létrehozott tesztkörnyezet.
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
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 42b59c8da9e66b0f749d067e3df867911c7108e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106831"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>A DevTest Labs szolgáltatásban létrehozott tesztkörnyezet egy összetevőtárban hozzáadása
DevTest Labs lehetővé teszi, hogy adjon meg egy időben, a virtuális gép létrehozása, vagy a virtuális gép létrehozása után egy virtuális Gépet hozzáadni kívánt összetevőt. Az összetevő egy eszköz és a egy virtuális Gépre telepíteni kívánt alkalmazás lehet. Összetevők egy JSON-fájlt a betöltött GitHub vagy a VSTS Git-adattárból vannak definiálva. 

A [nyilvános összetevőtárral](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), DevTest Labs által karbantartott, számos gyakori eszközöket biztosít a Windows és Linux is. Ez a tárház mutató hivatkozás automatikusan hozzáadódik a labor. Az eszközöket, amelyek nem érhetők el a nyilvános összetevőtárral hozhat létre saját összetevőtárban. Egyéni összetevők létrehozásával kapcsolatos további információkért lásd: [egyéni összetevők létrehozása](devtest-lab-artifact-author.md).

Ez a cikk információt nyújt az egyéni az összetevőtárban hozzáadása az Azure portal, az Azure Resource Management-sablonokat és az Azure PowerShell használatával. Automatizálhatja az összetevőtárban hozzáadása egy laborhoz PowerShell vagy parancssori felület parancsfájlok írásával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
A labor ad hozzá egy adattár, először kérje le kulcsfontosságú adatokat az adattárból. Az alábbi szakaszok ismertetik a szükséges adatok lévő üzemeltetett tárházakhoz készült **GitHub** vagy **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-tárház clone URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Nyissa meg a kezdőlapon GitHub-adattár, amely az összetevő vagy a Resource Manager-sablon definíciókat tartalmazza.
2. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
3. Az URL-címet a vágólapra másolásához jelölje ki a **HTTPS clone URL-cím** gombra. Mentse az URL-cím későbbi használatra.
4. GitHub jobb felső sarkában, a profilkép, majd válassza ki és **beállítások**.
5. Az a **személyes beállítások** a bal oldalon válassza a menü **fejlesztői beállítások**.
6. Válassza ki **személyes hozzáférési jogkivonatok** a bal oldali menüben.
7. Válassza ki **új jogkivonat létrehozása**.
8. Az a **új személyes hozzáférési tokent** lap **jogkivonat leírása**, adjon meg egy leírást. Fogadja el az alapértelmezett elemek **hatókörök kiválasztása**, majd válassza ki **azonosító létrehozása**.
9. A létrehozott jogkivonatának mentéset. A jogkivonat későbbi használatra.
10. Zárja be a Githubon.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Az Azure-Adattárakkal klón első URL-CÍMÉT és a személyes hozzáférési jogkivonat
1. Lépjen a kezdőlapra, a csapat gyűjtemény (például https://contoso-web-team.visualstudio.com), majd válassza ki a projekthez.
2. Válassza ki a projekt kezdőlapját, **kód**.
3. A klón URL-cím megtekintéséhez a projekt **kód** lapon válassza ki **klónozott**.
4. Mentse az URL-címet. Az URL-cím később használni.
5. A személyes hozzáférési tokent létrehozásához a felhasználói fiók legördülő menüben válassza **saját profil**.
6. A profil-információ lapon válassza ki **biztonsági**.
7. Az a **biztonsági** lapon jelölje be **Hozzáadás**.
8. Az a **személyes hozzáférési jogkivonat létrehozása** oldalon:
   1. Adjon meg egy **leírás** a jogkivonat.
   2. Az a **lejár a** listáról válassza ki **180 nap**.
   3. Az a **fiókok** listáról válassza ki **az összes elérhető fiókok**.
   4. Válassza ki a **minden hatókör** lehetőséget.
   5. Válassza ki **Token létrehozása**.
9. Megjelenik az új jogkivonatot a **személyes hozzáférési jogkivonatok** listája. Válassza ki **másolási Token**, majd mentse a token értéket későbbi használatra.
10. A csatlakozás továbbra is a labor a tárház szakaszra.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz lépéseit az összetevőtárban hozzáadása egy laborhoz az Azure Portalon. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **további szolgáltatások**, majd válassza ki **DevTest Labs** a szolgáltatások listájából.
3. Tesztkörnyezetek listájában jelölje ki a labor. 
4. Válassza ki **Konfigurace a zásady** a bal oldali menüben.
5. Válassza ki **Tárházak** alatt **külső erőforrások** szakaszban a bal oldali menüben.
6. Válassza ki **+ Hozzáadás** az eszköztáron.

    ![Adattár hozzáadása gombbal](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Az a **Tárházak** csoportjában adja meg a következő információkat:
   1. **Név**. Adja meg a tárház nevét.
   2. **Git Clone URL-cím**. Adja meg a Git HTTPS clone URL-cím, amely korábban vágólapra másolt a GitHub vagy a az Azure DevOps-szolgáltatásokkal.
   3. **Ág**. A definíciók lekéréséhez adja meg az ágat.
   4. **Személyes hozzáférési tokent**. Adja meg a személyes hozzáférési tokent korábban a GitHub vagy a az Azure DevOps-szolgáltatásokkal.
   5. **Mappa elérési útjának**. Adjon meg legalább egy mappa elérési útja a clone URL-cím, amely tartalmazza az összetevő vagy a Resource Manager-sablon definíciók viszonyítva. Alkönyvtár megadásakor ellenőrizze, hogy a perjel szerepel a mappa elérési útját.

        ![Tárházak terület](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablonjainak használata
Az Azure Resource Management (Azure Resource Manager) sablonok az Azure-ban, hogy a létrehozni kívánt erőforrásokat leíró JSON-fájlokat is. Ezekkel a sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

Ez a szakasz lépéseit az összetevőtárban hozzáadása egy laborhoz Azure Resource Manager-sablon használatával.  A sablon létrehozza a labor létrehozása, ha még nem létezik. 

### <a name="template"></a>Sablon
A mintasablon a cikk ezt használja a következő adatokat gyűjt a paraméterekkel. A paraméterek a legtöbb intelligens alapértelmezett beállítások, de van néhány olyan értékek, meg kell adni. Meg kell adnia a labor nevét, URI-azonosítóját az összetevőtárban, és a biztonsági jogkivonat adattár. 

- Labor nevét.
- A DevTest Labs felhasználói felületen (UI) az összetevőtárban megjelenített neve. Az alapértelmezett érték: `Team Repository`.
- A tárház URI-t (Példa: `https://github.com/<myteam>/<nameofrepo>.git` vagy `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- A tárház, amely tartalmazza az összetevők ágában. Az alapértelmezett érték: `master`.
- Összetevőket tartalmazó mappa nevét. Az alapértelmezett érték: `/Artifacts`.
- Az adattár típusa. Engedélyezett értékek a következők `VsoGit` vagy `GitHub`.
- A tárház hozzáférési jogkivonatát. 

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
A sablon üzembe helyezése az Azure és az erőforrás hozta létre, ha még nem létezik, vagy frissíteni, ha létezik néhány módja van. További információkért lásd: a következő cikkeket:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md)

Lépjen tovább, és tekintse meg a PowerShell-sablon üzembe helyezése. A sablon üzembe helyezéséhez használt parancsmagok környezetfüggő,, így az aktuális bérlő és a jelenlegi előfizetésében. Használat [Set-AzContext](/powershell/module/az.accounts/set-azcontext) a sablon üzembe helyezése, ha szükséges, módosíthatja a környezet előtt.

Először is hozzon létre egy erőforrás csoport [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Ha az már a használni kívánt erőforráscsoport már létezik, hagyja ki ezt a lépést.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Ezután hozza létre a központi telepítést, az erőforrás csoport használatával [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Ez a parancsmag az erőforrás-módosítások vonatkozik az Azure-bA. Több erőforráscsoport központi telepítések lehet tenni bármely adott erőforráscsoportban. Ha többször ugyanabban az erőforráscsoportban végzi, győződjön meg arról, minden egyes központi telepítés neve nem egyedi.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

New-AzResourceGroupDeployment sikeres futtatása után a parancs kimenete a fontos információkat, például a kiépítési állapota (sikeres volt a kell lennie), és a sablon kimeneteit.
 
## <a name="use-azure-powershell"></a>Azure PowerShell használatával 
Ez a szakasz egy minta PowerShell-parancsfájlt, amely segítségével az összetevőtárban hozzáadása egy laborhoz biztosít. Ha nem rendelkezik Azure PowerShell-lel, tekintse meg [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview?view=azps-1.2.0) részletes útmutatást a telepítéshez.

### <a name="full-script"></a>Teljes szkript
Itt látható a teljes szkript, beleértve néhány részletes üzenetek és a Megjegyzések:

**New-DevTestLabArtifactRepository.ps1**:

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
See https://azure.microsoft.com/en-us/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

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
Az alábbi példa bemutatja, hogyan a parancsfájl futtatásához: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Paraméterek
Ez a cikk a minta PowerShell-parancsprogram a következő paramétereket fogadja:

| Paraméter | Leírás | 
| --------- | ----------- | 
| LabName | A labor neve. |
| ArtifactRepositoryName | Az új összetevőtárban neve. A parancsfájl létrehozza a tárház egy véletlenszerű nevet, ha az nincs megadva. |
| ArtifactRepositoryDisplayName | Az összetevőtárban megjelenített neve. Azt a nevet, amely megjelenik-e az Azure Portalon (https://portal.azure.com) labor összes összetevő-tárházat megtekintésekor. |
| RepositoryUri | A tárház URI-t. Példák: `https://github.com/<myteam>/<nameofrepo>.git` vagy `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | A fiókiroda melyik összetevőben fájlok találhatók. Alapértelmezés szerint a "master". | 
| FolderPath | Az mappa, amely összetevők helyen található. Alapértelmezés szerint a "/ összetevők |
| PersonalAccessToken | Biztonsági jogkivonatot a GitHub vagy VSOGit tárház eléréséhez. Az Előfeltételek című útmutatást személyes hozzáférési jogkivonat beszerzése |
| forrás típusa | Összetevő-e VSOGit vagy a GitHub-adattárban. |

A tárház magának kell egy belső nevet azonosítása, amely eltér, hogy a megjelenített név, amely az Azure Portalon látható. Nem látja a belső nevét az Azure portal használatával, de látja az Azure REST API-k vagy az Azure PowerShell használata esetén. A parancsfájl ad meg egy nevet, ha nincs megadva a parancsfájl a felhasználó által.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>A szkriptben használt PowerShell-parancsok

| PowerShell-paranccsal | Megjegyzések |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ez a parancs segítségével például a helyét a tesztlabor kaphasson. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Nem tartozik a megadott parancs összetevő tárházak hozzáadásához. Az általános [New-AzResource](/powershell/module/az.resources/new-azresource) parancsmag does a feladatot. Ez a parancsmag van szüksége, vagy a **ResourceId** vagy a **ResourceName** és **ResourceType** pár tudni, hogy a létrehozni kívánt erőforrás típusát. Ez a példaszkript használja az erőforrás nevét és az erőforrás típusának pár. <br/><br/>Figyelje meg, hogy létrehozásakor összetevő tárház forrásának ugyanazon a helyen, és ugyanazt az erőforráscsoportot, a labor létrehozása alatt.|

A parancsfájl hozzáad egy új erőforrást az aktuális előfizetéshez. Használat [Get-AzContext](/powershell/module/az.accounts/get-azcontext) ezek az információk megtekintéséhez. Használat [Set-AzContext](/powershell/module/az.accounts/set-azcontext) az aktuális bérlő és az előfizetés beállításához.

A legjobb módja az erőforrás nevét és az erőforrás típussal kapcsolatos információk felderítésére az [Test Drive Azure REST API-k](https://azure.github.io/projects/apis/) webhelyén. Tekintse meg a [DevTest Labs – a 2016-05-15](https://aka.ms/dtlrestapis) -szolgáltatót, az elérhető REST API-kat a DevTest Labs-szolgáltatóhoz. A parancsfájl-felhasználók a következő erőforrás-azonosítója. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Az erőforrás típusa pedig minden felsorolt kivételével a kapcsos zárójelben szereplő elemek az URI "providers" után. Az erőforrásnév mindennél fontosabb a kapcsos zárójelben látható. Ha egynél több elem várhatóan az erőforrásnév, külön minden elem perjelre, ahogy eddig még. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>További lépések
- [Adja meg a kötelező összetevők a tesztkörnyezethez az Azure DevTest Labs szolgáltatásban](devtest-lab-mandatory-artifacts.md)
- [A DevTest Labs szolgáltatásban virtuális gép egyéni összetevők létrehozása](devtest-lab-artifact-author.md)
- [A lab-ben az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)

