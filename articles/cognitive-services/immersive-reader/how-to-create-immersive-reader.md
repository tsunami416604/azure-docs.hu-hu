---
title: Erőforrás létrehozása a Modern olvasóhoz
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre egy új, magával ragadó reader-erőforrást egyéni altartománnyal, majd konfigurálhatja az Azure AD-t az Azure-bérlőben.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330719"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Magával ragadó olvasói erőforrás létrehozása és az Azure Active Directory-hitelesítés konfigurálása

Ebben a cikkben egy parancsfájlt biztosítunk, amely létrehoz egy magával ragadó olvasó-erőforrást, és konfigurálja az Azure Active Directory (Azure AD) hitelesítést. Minden alkalommal, amikor egy magával ragadó olvasó erőforrás jön létre, akár ezzel a parancsfájllal, vagy a portálon, azt is be kell állítani az Azure AD-engedélyekkel. Ez a szkript segít ebben.

A parancsfájl célja, hogy hozza létre és konfigurálja az összes szükséges Immersive Reader és az Azure AD-erőforrások az Ön számára minden egy lépésben. Azonban csak konfigurálhatja az Azure AD-hitelesítést egy meglévő Immersive Reader-erőforráshoz, ha például előfordulhat, hogy már létrehozott egyet az Azure Portalon.

Egyes ügyfelek számára szükség lehet több Immersive Reader erőforrás létrehozására, a fejlesztés hez és a termeléshez, vagy talán több különböző régióhoz, ahol a szolgáltatás telepítve van. Ezekben az esetekben, akkor gyere vissza, és használja a parancsfájlt többször hozzon létre a különböző immersive Reader erőforrásokat, és azokat konfigurálva az Azure AD-engedélyeket.

A szkript célja, hogy rugalmas legyen. Először megkeresi a meglévő Immersive Reader és az Azure AD-erőforrásokat az előfizetésben, és csak szükség szerint hozza létre őket, ha még nem léteznek. Ha ez az első alkalom, hogy létrehoz egy magával ragadó reader erőforrást, a szkript mindent megtesz, amire szüksége van. Ha azt szeretné, hogy csak az Azure AD konfigurálásához egy meglévő, a portálon létrehozott Immersive Reader-erőforráshoz, azt is megteszi. Azt is fel lehet használni, hogy hozzon létre és konfigurálja több Alámerülés Reader források.

## <a name="set-up-powershell-environment"></a>PowerShell-környezet beállítása

1. Kezdje az [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)megnyitásával. Győződjön meg arról, hogy a felhőhéj a Bal felső `pwsh`legördülő menüben vagy a beírásban a PowerShell lesz állítva.

1. Másolja és illessze be a következő kódrészletet a rendszerhéjba.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Futtassa `Create-ImmersiveReaderResource`a függvényt, adja meg a paramétereket.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Paraméter | Megjegyzések |
    | --- | --- |
    | SubscriptionName |A Magával ragadó olvasóerőforráshoz használandó Azure-előfizetés neve. Az erőforrás létrehozásához előfizetéssel kell rendelkeznie. |
    | ResourceName nevű erőforrásáról |  Alfanumerikusnak kell lennie, és tartalmazhat "-" értéket, feltéve, hogy a "-" nem az első vagy az utolsó karakter. A hossz nem haladhatja meg a 63 karaktert.|
    | ResourceSubtartomány |A magával ragadó olvasó erőforráshoz egyéni altartomány szükséges. Az aldomaint az SDK használja, amikor a Immersive Reader szolgáltatást hívja az Olvasó elindításához. Az altartománynak globálisan egyedinek kell lennie. Az altartománynak alfanumerikusnak kell lennie, és tartalmazhat "-" értéket, feltéve, hogy a "-" nem az első vagy az utolsó karakter. A hossz nem haladhatja meg a 63 karaktert. Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceSKU |Beállítások: `S0`. Látogasson el a [Cognitive Services díjszabási oldalára,](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) ahol többet tudhat meg az egyes elérhető termékváltozatokról. Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceLocation |Lehetőségek: `eastus` `eastus2`, `southcentralus` `westus`, `westus2` `australiaeast`, `southeastasia` `centralindia`, `japaneast` `northeurope`, `uksouth` `westeurope`, , , , , , . Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceGroupName |Az erőforrások az előfizetéseken belüli erőforráscsoportokban jönnek létre. Adja meg egy meglévő erőforráscsoport nevét. Ha az erőforráscsoport még nem létezik, egy ilyen nevű új jön létre. |
    | ResourceGroupLocation (Erőforráscsoport helye) |Ha az erőforráscsoport nem létezik, meg kell adnia egy helyet, ahol létre hozhatja a csoportot. A helyek listájának megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a futtassa `az account list-locations` Használja a visszaadott eredmény *névtulajdonságát* (szóközök nélkül). Ez a paraméter nem kötelező, ha az erőforráscsoport már létezik. |
    | AADAppDisplayName |Az Azure Active Directory-alkalmazás megjelenítendő neve. Ha egy meglévő Azure AD-alkalmazás nem található, egy új, ezzel a névvel jön létre. Ez a paraméter nem kötelező, ha az Azure AD-alkalmazás már létezik. |
    | AADAppIdentifieruri között |Az Azure AD alkalmazás URI-ja. Ha egy meglévő Azure AD-alkalmazás nem található, egy újat hoz létre ezzel az URI-val. Például: `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |A létrehozott jelszó, amely et később a hitelesítéshez fog használni, amikor token beolvasásakor elindítja a Magával ragadó olvasót. A jelszónak legalább 16 karakter hosszúnak kell lennie, legalább 1 különleges karaktert kell tartalmaznia, és legalább 1 numerikus karaktert kell tartalmaznia. |

1. Másolja a JSON-kimenetet egy szövegfájlba későbbi használatra. A kimenetnek a következőkre kell hasonlítania.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Node.js rövid útmutatót,](./quickstart-nodejs.md) hogy mi mást tehet a Magával ragadó Reader SDK segítségével Node.js
* Tekintse meg a [Python oktatóanyag,](./tutorial-python.md) hogy mi mást tehet ünk a magával ragadó Reader SDK python használatával
* Tekintse meg a [Swift bemutató,](./tutorial-ios-picture-immersive-reader.md) hogy mi mást tehetünk a magával ragadó Reader SDK swift
* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)




