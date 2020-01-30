---
title: Felügyeleti eszköz üzembe helyezése a Windows rendszerű virtuális asztalhoz az egyszerű szolgáltatásnév használatával – Azure
description: A Windows rendszerű virtuális asztal felügyeleti eszközének üzembe helyezése a PowerShell használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
ms.openlocfilehash: e3ea11f4faad204756f9e1296b5190e1f81a5cc0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772796"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Felügyeleti eszköz üzembe helyezése a PowerShell-lel

Ez a cikk bemutatja, hogyan helyezheti üzembe a felügyeleti eszközt a PowerShell használatával.

## <a name="important-considerations"></a>Fontos szempontok

Minden Azure Active Directory-(Azure AD-) bérlő előfizetéséhez a felügyeleti eszköz saját külön üzembe helyezésére van szükség. Ez az eszköz nem támogatja az Azure AD vállalatközi (B2B) forgatókönyveit. 

Ez a felügyeleti eszköz egy minta. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. [A forráskód a githubon érhető el](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Akár ügyfél, akár partner, javasoljuk, hogy szabja testre az eszközt az üzleti igények kielégítése érdekében.

A következő böngészők kompatibilisek a felügyeleti eszközzel:

- Google Chrome 68 vagy újabb verzió
- Microsoft Edge 40,15063 vagy újabb verzió
- Mozilla Firefox 52,0 vagy újabb verzió
- Safari 10 vagy újabb (csak macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezéséhez szükséges tudnivalók

A felügyeleti eszköz telepítése előtt szüksége lesz egy Azure Active Directory (Azure AD) felhasználóra az alkalmazások regisztrálásának létrehozásához és a felügyeleti felhasználói felület telepítéséhez. A felhasználónak a következőket kell tennie:

- Engedéllyel rendelkezik erőforrások létrehozásához az Azure-előfizetésében
- Engedéllyel rendelkezik Azure AD-alkalmazás létrehozásához. Kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)utasításait követve.

A felügyeleti eszköz sikeres telepítéséhez és konfigurálásához először le kell töltenie a következő PowerShell-parancsfájlokat az [RDS-templates GitHub-](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) tárházból, és mentenie kell azokat a helyi gép ugyanazon mappájába.

  - createWvdMgmtUxAppRegistration. ps1
  - updateWvdMgmtUxApiUrl. ps1

A felügyeleti eszköz üzembe helyezése és konfigurálása után javasoljuk, hogy kérje meg a felhasználót, hogy indítsa el a felügyeleti felhasználói felületet, és győződjön meg róla, hogy minden működik. A felügyeleti felhasználói felületet indító felhasználónak szerepkör-hozzárendeléssel kell rendelkeznie, amely lehetővé teszi a Windows rendszerű virtuális asztali bérlő megtekintését vagy szerkesztését.

## <a name="set-up-powershell"></a>A PowerShell beállítása

Első lépésként jelentkezzen be mind az az, mind az Azure AD PowerShell-modulba. A következő módon jelentkezhet be:

1. Nyissa meg a PowerShellt rendszergazdaként, és navigáljon arra a könyvtárra, ahová a PowerShell-parancsfájlokat mentette.
2. Jelentkezzen be az Azure-ba egy olyan fiókkal, amely tulajdonosi vagy közreműködői engedélyekkel rendelkezik azon Azure-előfizetésben, amelyet a felügyeleti eszköz létrehozásához kíván használni a következő parancsmag futtatásával:

    ```powershell
    Login-AzAccount
    ```

3. A következő parancsmag futtatásával jelentkezzen be az Azure AD-be ugyanazzal a fiókkal, amelyet az az PowerShell-modulhoz használt:

    ```powershell
    Connect-AzureAD
    ```

4. Ezt követően navigáljon ahhoz a mappához, ahová a két PowerShell-parancsfájlt mentette az RDS-templates GitHub-tárházból.

Tartsa meg úgy a PowerShell-ablak megnyitását, hogy további PowerShell-parancsmagokat futtasson a bejelentkezéskor.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrációjának létrehozása

Futtassa az alábbi parancsokat az alkalmazás regisztrációjának létrehozásához a szükséges API-engedélyekkel:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy végrehajtotta az Azure AD-alkalmazás regisztrációját, üzembe helyezheti a felügyeleti eszközt.

## <a name="deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezése

Futtassa a következő PowerShell-parancsokat a felügyeleti eszköz üzembe helyezéséhez és az imént létrehozott egyszerű szolgáltatáshoz való hozzárendeléséhez:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

A webalkalmazás létrehozása után hozzá kell adnia egy átirányítási URI-t az Azure AD-alkalmazáshoz, hogy sikeresen bejelentkezzen a felhasználókba.

## <a name="set-the-redirect-uri"></a>Az átirányítási URI beállítása

Futtassa a következő PowerShell-parancsokat a webalkalmazás URL-címének lekéréséhez, és állítsa be hitelesítési átirányítási URI-ként (más néven válasz-URL-ként):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Most, hogy hozzáadta az átirányítási URI-t, az API URL-címét frissítenie kell, hogy a felügyeleti eszköz kommunikálni tudjon az API-backend szolgáltatással.

## <a name="update-the-api-url-for-the-web-application"></a>A webalkalmazás API URL-címének frissítése

Futtassa a következő szkriptet az API URL-cím konfigurációjának frissítéséhez a webalkalmazás-előtérben:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy teljesen konfigurálta a felügyeleti eszköz webalkalmazását, itt az ideje, hogy ellenőrizze az Azure AD-alkalmazást, és adja meg az engedélyt.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Ellenőrizze az Azure AD-alkalmazást, és adja meg a beleegyezett

Az Azure AD-alkalmazás konfigurációjának ellenőrzése és a beleegyező engedély megadása:

1. Nyissa meg az Internet böngészőt, és jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a rendszergazdai fiókjával.
2. A Azure Portal tetején található keresősáv alatt keresse meg **Alkalmazásregisztrációk** , és válassza ki az elemet a **szolgáltatások**területen.
3. Válassza a **minden alkalmazás** lehetőséget, és keresse meg a PowerShell-parancsfájlhoz megadott egyedi alkalmazás-nevet a [Azure Active Directory alkalmazás regisztrációjának létrehozása](#create-an-azure-active-directory-app-registration)lapon.
4. A böngésző bal oldali paneljén válassza a **hitelesítés** lehetőséget, és győződjön meg arról, hogy az átirányítási URI megegyezik a felügyeleti eszköz webalkalmazás URL-címével, ahogy az alábbi képen is látható.
   
   [![a hitelesítési lapot a megadott átirányítási URI-val](media/management-ui-redirect-uri-inline.png)](media/management-ui-redirect-uri-expanded.png#lightbox)

5. A bal oldali panelen válassza az **API-engedélyek** lehetőséget az engedélyek hozzáadásának megerősítéséhez. Ha Ön globális rendszergazda, válassza a **rendszergazdai jóváhagyás megadása `tenantname`** gombra, és kövesse a párbeszédpanelt, és adja meg a szervezete rendszergazdai hozzájárulását.
    
    [![az API-engedélyek lapot](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

Most már megkezdheti a felügyeleti eszköz használatát.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Most, hogy bármikor beállította a felügyeleti eszközt, bármikor elindíthatja, bárhol is. A következőképpen indíthatja el az eszközt:

1. Nyissa meg a webalkalmazás URL-címét egy böngészőben. Ha nem emlékszik az URL-címre, bejelentkezhet az Azure-ba, megkeresheti a felügyeleti eszközre telepített app Service-t, majd kiválaszthatja az URL-címet.
2. Jelentkezzen be a Windows rendszerű virtuális asztali hitelesítő adataival.
   
   > [!NOTE]
   > Ha a felügyeleti eszköz konfigurálása során nem adta meg a rendszergazdai jóváhagyást, akkor a bejelentkező összes felhasználónak meg kell adnia a saját felhasználói hozzájárulásukat az eszköz használatához.

3. Amikor a rendszer kéri, hogy válasszon ki egy bérlői csoportot, válassza ki az **alapértelmezett bérlői csoportot** a legördülő listából.
4. Amikor kiválasztja az **alapértelmezett bérlői csoportot**, egy menü jelenik meg az ablak bal oldalán. Ebben a menüben keresse meg a bérlői csoport nevét, és jelölje ki.
   
   > [!NOTE]
   > Ha egyéni bérlői csoporttal rendelkezik, a legördülő listából válassza a nevet manuálisan.

## <a name="report-issues"></a>Problémák jelentése

Ha a felügyeleti eszközzel vagy más Windowsos virtuális asztali eszközökkel kapcsolatos problémákba ütközik, kövesse az [Azure Resource Manager-távoli asztali szolgáltatások sablonok](https://github.com/Azure/RDS-Templates/blob/master/README.md) című témakör utasításait a githubon való jelentéséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan helyezheti üzembe és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja a Azure Service Healtht a szolgáltatási problémák és az állapot-útmutatók figyelésére. További információ: [set up Service riasztások oktatóanyaga](./set-up-service-alerts.md).
