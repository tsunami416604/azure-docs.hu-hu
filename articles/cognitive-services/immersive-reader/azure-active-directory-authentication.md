---
title: Azure Active Directory (Azure AD) hitelesítés
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre egy új, teljes értékű olvasó-erőforrást egy egyéni altartománnyal, majd hogyan konfigurálhatja az Azure AD-t az Azure-bérlőben.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d51c27b90113679c1547f2d030459a03cc22c80c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299807"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>A Azure Active Directory-(Azure AD-) hitelesítés használata a magától ellátott olvasó szolgáltatással

A következő fejezetekben a Azure Cloud Shell-környezet vagy a Azure PowerShell használatával hozzon létre egy új, magára ejtő olvasó-erőforrást egy egyéni altartománnyal, majd konfigurálja az Azure AD-t az Azure-bérlőben. A kezdeti konfiguráció befejezése után az Azure AD-t hívja meg egy hozzáférési token beszerzéséhez, amely hasonló ahhoz, ahogyan a magával ragadó olvasói SDK használatakor el fog végezni. Ha elakad, az egyes szakaszokban a Azure PowerShell parancsaihoz elérhető összes lehetőségre mutató hivatkozásokat talál.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Önmagára kiolvasó erőforrás létrehozása egyéni altartománnyal

1. Először nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ezután [válasszon ki egy előfizetést](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Következő lépésként [hozzon létre egy önmagára kiolvasó erőforrást](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) egy egyéni altartománnyal.

   >[!NOTE]
   > Az altartomány nevét a rendszer a magától elolvasó SDK-ban használja az olvasó launchAsync függvénnyel való indításakor.

   – A SkuName lehet F0 (ingyenes szint) vagy S0 (standard szint, a nyilvános előzetes verzióban is ingyenes). A S0 szintjének száma magasabb, a hívások számának havi kvótája pedig nem megengedett.

   – A hely a következők bármelyike lehet: `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`, `westeurope`

   – A CustomSubdomainName globálisan egyedinek kell lennie, és nem tartalmazhat speciális karaktereket, például: ".", "!", ",".


   ```azurepowershell-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Ha a művelet sikeres, az erőforrás- **végpontnak** az erőforrás egyedi altartománynevét kell megjelenítenie.

   Itt rögzítjük az újonnan létrehozott erőforrás-objektumot egy **$Resource** változóba, mivel később az erőforráshoz való hozzáférés megadásakor lesz használatban.


   >[!NOTE]
   > Ha erőforrást hoz létre a Azure Portalban, a "Name" erőforrást a rendszer egyéni altartományként használja. Az altartomány nevét a portálon tekintheti meg az erőforrás-áttekintés oldalon, és megkeresheti az altartományt az ott felsorolt végponton, például `https://[SUBDOMAIN].cognitiveservices.azure.com/`. Emellett később is megtekintheti, ha az SDK-val való integráláshoz be kell szereznie az altartományt.

   Ha az erőforrás a portálon lett létrehozva, akkor már [meglévő erőforrást is beszerezhet](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) .

   ```azurepowershell-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Szerepkör kiosztása egy egyszerű szolgáltatáshoz

Most, hogy az erőforráshoz tartozó egyéni altartománnyal rendelkezik, hozzá kell rendelnie egy szerepkört egy egyszerű szolgáltatáshoz.

1. Először [hozzon létre egy Azure ad-alkalmazást](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > A hitelesítő jogkivonatok beszerzéséhez a rendszer a "Client Secret" néven is ismert jelszót fogja használni.

   ```azurepowershell-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Itt rögzítjük az újonnan létrehozott Azure AD App-objektumot egy **$aadApp** változóba a következő lépésben való használatra.

2. Ezután [létre kell hoznia egy egyszerű szolgáltatásnevet](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) az Azure ad-alkalmazáshoz.

   ```azurepowershell-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Itt rögzítjük az újonnan létrehozott szolgáltatásnév-objektumot egy **$Principal** változóba a következő lépésben való használatra.


3. Az utolsó lépés a ["Cognitive Services user" szerepkör társítása](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) az egyszerű szolgáltatáshoz (hatóköre az erőforrás). Szerepkör hozzárendelésével a szolgáltatás egyszerű hozzáférést biztosít ehhez az erőforráshoz. Az előfizetés több erőforrásához is biztosíthatja ugyanazt a szolgáltatást.

   ```azurepowershell-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Ezt a lépést végre kell hajtani minden Ön által létrehozott, magával ragadó olvasó-erőforráshoz. Ha például több erőforrást hoz létre a különböző globális régiók számára, akkor ezeket a lépéseket minden egyes regionális erőforráshoz el kell végeznie, hogy az Azure AD-hitelesítés mindegyikhez használható legyen. Ha később új erőforrást hoz létre, akkor ezt a szerepkör-hozzárendelési lépést is végre kell hajtania az új erőforráshoz.


## <a name="obtain-an-azure-ad-token"></a>Azure AD-jogkivonat beszerzése

Ebben a példában a jelszót az egyszerű szolgáltatásnév hitelesítésére használja az Azure AD-jogkivonat beszerzéséhez.

1. A **TenantId**beszerzése:
   ```azurepowershell-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Token beszerzése:
   ```azurepowershell-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > A alámerülés olvasó SDK a token AccessToken tulajdonságát használja, például $token. AccessToken. A részletekért tekintse meg az SDK- [referenciát](reference.md) és a kód [mintáit](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) .

Másik lehetőségként az egyszerű szolgáltatás hitelesítése tanúsítvánnyal végezhető el. Az egyszerű szolgáltatásnév mellett a felhasználói rendszerbiztonsági tag is támogatott, ha egy másik Azure AD-alkalmazáson keresztül delegált engedélyekkel rendelkezik. Ebben az esetben a jelszavak vagy tanúsítványok helyett a felhasználókat a rendszer a tokenek beszerzése során a kétfaktoros hitelesítésre kéri.

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Node. js-oktatóanyagot](./tutorial-nodejs.md) , amelyből megtudhatja, hogy a Node. js használatával milyen egyéb műveleteket végezhet el az olvasói SDK-val
* Tekintse meg a [Python-oktatóanyagot](./tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg a [Swift-oktatóanyagot](./tutorial-ios-picture-immersive-reader.md) , amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)