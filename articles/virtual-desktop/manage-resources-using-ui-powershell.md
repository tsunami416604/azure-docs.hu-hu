---
title: Felügyeleti eszköz telepítése a Windows virtuális asztalhoz egyszerű szolgáltatás használatával - Azure
description: A Windows virtual desktop felügyeleti eszközének telepítése a PowerShell használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127800"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Felügyeleti eszköz üzembe helyezése a PowerShell használatával

Ez a cikk bemutatja, hogyan telepítheti a felügyeleti eszközt a PowerShell használatával.

## <a name="important-considerations"></a>Fontos szempontok

Minden Azure Active Directory (Azure AD) bérlői előfizetésszüksége van a felügyeleti eszköz saját külön üzembe helyezéséhez. Ez az eszköz nem támogatja az Azure AD Business-to-Business (B2B) forgatókönyveket. 

Ez a felügyeleti eszköz egy példa. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. [A forráskód a GitHubon érhető el.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Akár ügyfél, akár partner, javasoljuk, hogy szabja testre az eszközt, hogy megfeleljen az üzleti igényeinek.

A következő böngészők kompatibilisek a felügyeleti eszközzel:

- Google Chrome 68 vagy újabb
- Microsoft Edge 40.15063 vagy újabb
- Mozilla Firefox 52.0 vagy újabb
- Safari 10 vagy újabb verzió (csak macOS esetén)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Mire van szükség a felügyeleti eszköz telepítéséhez?

A felügyeleti eszköz üzembe helyezése előtt szüksége lesz egy Azure Active Directory (Azure AD) felhasználó ra egy alkalmazás regisztrációjának létrehozásához és a felügyeleti felhasználói felület üzembe helyezéséhez. Ennek a felhasználónak:

- Engedéllyel rendelkezik erőforrások létrehozására az Azure-előfizetésben
- Engedéllyel rendelkezik egy Azure AD-alkalmazás létrehozásához. Az alábbi lépésekkel ellenőrizheti, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a [Szükséges engedélyek](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)című részben található utasításokat követve.

A felügyeleti eszköz sikeres üzembe helyezéséhez és konfigurálásához először le kell töltenie a következő PowerShell-parancsfájlokat az [RDS-Templates GitHub-tárházból,](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) és mentenie kell őket a helyi számítógépen lévő azonos mappába.

  - createWvdMgmtUxAppRegistration.ps1
  - updateWvdMgmtUxApiUrl.ps1

A felügyeleti eszköz telepítése és konfigurálása után javasoljuk, hogy kérje meg a felhasználót, hogy indítsa el a felügyeleti felhasználói felületet, hogy minden rendben legyen. A felügyeleti felhasználói felületet elvezető felhasználónak rendelkeznie kell egy szerepkör-hozzárendeléssel, amely lehetővé teszi számára a Windows virtuális asztal bérlőjének megtekintését vagy szerkesztését.

## <a name="set-up-powershell"></a>A PowerShell beállítása

Első lépések az Az és az Azure AD PowerShell-modulokba való bejelentkezéssel. A bejelentkezés a következőképpen jelentkezik be:

1. Nyissa meg a PowerShellt rendszergazdaként, és keresse meg azt a könyvtárat, amelybe a PowerShell-parancsfájlokat mentette.
2. Jelentkezzen be az Azure-ba egy olyan fiókkal, amely tulajdonosi vagy közreműködői engedélyekkel rendelkezik a felügyeleti eszköz létrehozásához használni kívánt Azure-előfizetéssel a következő parancsmag futtatásával:

    ```powershell
    Login-AzAccount
    ```

3. Futtassa a következő parancsmast az Azure AD-be való bejelentkezéshez ugyanazzal a fiókkal, amelyet az Az PowerShell-modulhoz használt:

    ```powershell
    Connect-AzureAD
    ```

4. Ezt követően keresse meg azt a mappát, amelyben a két PowerShell-parancsfájlt mentette az RDS-Templates GitHub-tárházból.

Tartsa nyitva a powershell-ablakot, amelyet a bejelentkezéshez használt további PowerShell-parancsmagok futtatásához.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory-alkalmazásregisztráció létrehozása

Futtassa a következő parancsokat az alkalmazás regisztrációjának létrehozásához a szükséges API-engedélyekkel:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy befejezte az Azure AD-alkalmazás regisztrációját, üzembe helyezheti a felügyeleti eszközt.

## <a name="deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezése

Futtassa a következő PowerShell-parancsokat a felügyeleti eszköz üzembe helyezéséhez, és társítsa azt az imént létrehozott egyszerű szolgáltatáshoz:
     
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

Miután létrehozta a webalkalmazást, hozzá kell adnia egy átirányítási URI-t az Azure AD-alkalmazáshoz a felhasználók sikeres bejelentkezéséhez.

## <a name="set-the-redirect-uri"></a>Az átirányításURI beállítása

Futtassa a következő PowerShell-parancsokat a webalkalmazás URL-címének lekéréséhez, és állítsa be hitelesítési átirányítási URI -ként (más néven válasz URL-címként):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Most, hogy hozzáadott egy átirányítási URI-t, most frissítenie kell az API URL-címét, hogy a felügyeleti eszköz kommunikálhassa az API-háttérszolgáltatást.

## <a name="update-the-api-url-for-the-web-application"></a>A webalkalmazás API-URL-címének frissítése

Futtassa a következő parancsfájlt az API URL-konfigurációjának frissítéséhez a webalkalmazás előtér-rendszerében:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy már teljesen konfigurálta a felügyeleti eszköz webapp, itt az ideje, hogy ellenőrizze az Azure AD-alkalmazást, és beleegyezését adja meg.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Az Azure AD-alkalmazás ellenőrzése és hozzájárulás ad ássa le a hozzá való hozzájárulást

Az Azure AD-alkalmazás konfigurációjának ellenőrzése és hozzájárulása:

1. Nyissa meg az internetböngészőt, és jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a felügyeleti fiókjával.
2. Az Azure Portal tetején található keresősávból keresse meg az **alkalmazásregisztrációkat,** és válassza ki az elemet a **Szolgáltatások**csoportban.
3. Válassza a **Minden alkalmazás lehetőséget,** és keressen a PowerShell-parancsfájlhoz megadott egyedi alkalmazásnévközött az [Azure Active Directory-alkalmazás regisztrációjának létrehozása](#create-an-azure-active-directory-app-registration)című dokumentumban.
4. A böngésző bal oldalán lévő panelen válassza a **Hitelesítés** lehetőséget, és győződjön meg arról, hogy az átirányítási URI megegyezik a felügyeleti eszköz webalkalmazás-URL-címével, ahogy az az alábbi képen látható.
   
   [![A megadott átirányítási URI-val](media/management-ui-redirect-uri-inline.png) rendelkező hitelesítési lap](media/management-ui-redirect-uri-expanded.png#lightbox)

5. A bal oldali panelen válassza az **API-engedélyeket** az engedélyek hozzáadásának megerősítéséhez. Ha Ön globális rendszergazda, válassza a **Rendszergazda `tenantname` hozzájárulásának megadása** gombot, és kövesse a párbeszédpanel-utasításokat, hogy rendszergazdai jóváhagyást adjon a szervezetnek.
    
    [![Az API-engedélyek lap](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

Most már elkezdheti használni a felügyeleti eszközt.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Most, hogy bármikor beállította a felügyeleti eszközt, bármikor, bárhol elindíthatja. Az eszköz elindítása a következőképpen indítható el:

1. Nyissa meg a webalkalmazás URL-címét egy webböngészőben. Ha nem emlékszik az URL-címre, bejelentkezhet az Azure-ba, megkeresheti a felügyeleti eszközhöz üzembe helyezett alkalmazásszolgáltatást, majd kiválaszthatja az URL-címet.
2. Jelentkezzen be a Windows virtuális asztal hitelesítő adataival.
   
   > [!NOTE]
   > Ha a felügyeleti eszköz konfigurálása során nem adta meg a rendszergazdai jóváhagyást, minden bejelentkező felhasználónak meg kell adnia a saját felhasználói hozzájárulását az eszköz használatához.

3. Amikor a rendszer kéri, hogy válasszon egy bérlői csoportot, válassza az **alapértelmezett bérlői csoport** lehetőséget a legördülő listából.
4. Ha az **Alapértelmezett bérlői csoport**lehetőséget választja, egy menünek kell megjelennie az ablak bal oldalán. Ebben a menüben keresse meg a bérlői csoport nevét, és jelölje ki.
   
   > [!NOTE]
   > Ha egyéni bérlői csoporttal rendelkezik, adja meg a nevet manuálisan, ahelyett, hogy a legördülő listából választana.

## <a name="report-issues"></a>Problémák bejelentése

Ha bármilyen probléma merül fel a felügyeleti eszközzel vagy más Windows virtuális asztali eszközökkel kapcsolatban, kövesse az [Azure Resource Manager távoli asztali szolgáltatásokhoz készült sablonjainak](https://github.com/Azure/RDS-Templates/blob/master/README.md) utasításait, és jelentse azokat a GitHubon.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepítheti és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja az Azure Service Health szolgáltatást a szolgáltatási problémák és az egészségügyi tanácsok figyelésére. További információ: [A Szolgáltatásriasztások beállítása oktatóanyag.](./set-up-service-alerts.md)
