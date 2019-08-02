---
title: Windows rendszerű virtuális asztali bérlő és gazdagép-készlet létrehozása – Azure
description: A bérlői és a gazdagép-készletezési problémák elhárítása és megoldása a Windows rendszerű virtuális asztali bérlői környezet beállítása során.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 9e58c3bfc0e74f9aac61085608a843954b0923c0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249558"
---
# <a name="tenant-and-host-pool-creation"></a>Bérlői és gazdagépcsoport létrehozása

Ez a cikk a Windows rendszerű virtuális asztali bérlő és a kapcsolódó munkamenet-gazdagépek infrastruktúrájának kezdeti beállítása során felmerülő problémákat ismerteti.

## <a name="provide-feedback"></a>Visszajelzés küldése

Jelenleg nem veszünk fel támogatási eseteket, amíg a Windows rendszerű virtuális asztal előzetes verzióban érhető el. Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>A Windows 10 Enterprise több munkamenetet ábrázoló rendszerképének beszerzése

A Windows 10 Enterprise multi-session rendszerképek használatához nyissa meg az Azure Marketplace-t **, válassza** > a**Microsoft Windows 10** > és [a Windows 10 Enterprise rendszerű virtuális asztalok előzetes verzióját, a 1809-es verziót](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Képernyőkép: a Windows 10 Enterprise for Virtual desktops előzetes verziója, 1809-es verzió.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows rendszerű virtuális asztali bérlő létrehozása

Ez a szakasz a Windows rendszerű virtuális asztali bérlő létrehozásakor felmerülő esetleges problémákat ismerteti.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználónak nincs engedélye a kezelési szolgáltatás lekérdezésére

![Képernyőkép a PowerShell-ablakról, amelyben a felhasználó nem rendelkezik jogosultsággal a kezelési szolgáltatás lekérdezéséhez.](media/UserNotAuthorizedNewTenant.png)

Nyers hiba – példa:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Okozhat** A bejelentkezett felhasználó nem rendelte hozzá a TenantCreator szerepkört a Azure Active Directory.

**Javítsa ki** Kövesse a TenantCreator- [alkalmazás szerepkörének a Azure Active Directory-bérlőben](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)lévő felhasználóhoz való hozzárendelésével kapcsolatos utasításokat. Az utasításokat követve egy felhasználó lesz hozzárendelve a TenantCreator szerepkörhöz.

![Képernyőkép a hozzárendelt TenantCreator-szerepkörről.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows rendszerű virtuális asztali munkamenetgazda-alapú virtuális gépek létrehozása

A munkamenet-gazda virtuális gépek több módon is létrehozhatók, de a Távoli asztali szolgáltatások/Windows rendszerű virtuális asztali csapatok csak a Azure Resource Manager sablonnal kapcsolatos virtuális gépek kiépítési problémáit támogatják. A Azure Resource Manager sablon az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en és [](https://github.com/)a githubon érhető el.

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémák a Windows virtuális asztal használatával – Azure Marketplace-ajánlat kiépítése

A Windows rendszerű virtuális asztal – alkalmazáskészlet sablonjának kiépítése az Azure piactéren érhető el.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hiba: A GitHubról való hivatkozás használatakor megjelenik az "ingyenes fiók létrehozása" üzenet.

![Képernyőkép egy ingyenes fiók létrehozásához.](media/be615904ace9832754f0669de28abd94.png)

**1. ok:** Nincsenek aktív előfizetések az Azure-ba való bejelentkezéshez, vagy a használt fiók nem rendelkezik engedéllyel az előfizetések megtekintéséhez.

**1. javítás:** Jelentkezzen be egy olyan fiókkal, amely közreműködői hozzáféréssel rendelkezik (legalább) ahhoz az előfizetéshez, amelyben a munkamenet-gazdagép virtuális gépei telepítve lesznek.

**2. ok:** A használt előfizetés egy Microsoft Cloud szolgáltatói (CSP-) bérlő részét képezi.

**2. javítás:** Nyissa meg a GitHub helyét az **új Windowsos virtuális asztali címkészlet létrehozásához és** kiépítéséhez, és kövesse az alábbi utasításokat:

1. Kattintson a jobb gombbal az **üzembe helyezés az Azure** -ba lehetőségre, és válassza a **hivatkozás címe másolása**lehetőséget.
2. Nyissa meg a **jegyzettömböt** , és illessze be a hivatkozást.
3. A # karakter előtt szúrja be a CSP végfelhasználói bérlői nevét.
4. Nyissa meg az új hivatkozást egy böngészőben, és a Azure Portal betölti a sablont.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>A Azure Resource Manager sablon és a PowerShell kívánt állapot-konfigurációs (DSC) hibái

A Azure Resource Manager-sablonok és a PowerShell DSC nem sikeres központi telepítésének hibakereséséhez kövesse az alábbi utasításokat.

1. Tekintse át az üzemelő példány hibáit a Azure Resource Manager használatával történő [központi telepítési műveletek megtekintésével](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Ha nem találhatók hibák az üzemelő példányban, tekintse át a tevékenység naplójának hibáit a tevékenységek [megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)az erőforrásokon végzett naplózási műveleteknél.
3. A hiba észlelése után használja a hibaüzenetet és az erőforrásokat az [Azure telepítési hibáinak elhárításához Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) a probléma megoldásához.
4. Törölje az előző központi telepítés során létrehozott erőforrásokat, majd próbálkozzon újra a sablon üzembe helyezésével.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hiba: A telepítés nem sikerült...\<. állomásnév >/JoinDomain

![A telepítés sikertelen képernyőkép.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Nyers hiba – példa:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. ok:** A virtuális gépek tartományhoz való csatlakoztatásához megadott hitelesítő adatok helytelenek.

**1. javítás:** Tekintse meg a "helytelen hitelesítő adatok" hibaüzenetet, ha a virtuális gépek nem csatlakoznak a tartományhoz a [munkamenet-gazdagép](troubleshoot-vm-configuration.md)virtuálisgép-konfigurációjában.

**2. ok:** A tartománynév nem oldható fel.

**2. javítás:** Tekintse meg a "tartománynév nem oldható fel" hibaüzenetet, ha a virtuális gépek nem csatlakoznak a tartományhoz a [munkamenet-gazdagép](troubleshoot-vm-configuration.md)virtuálisgép-konfigurációjában.


### <a name="error-your-deployment-failedunauthorized"></a>Hiba: A telepítés nem sikerült. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Okozhat** Az Ön által használt előfizetés olyan típus, amely nem fér hozzá a szükséges funkciókhoz abban a régióban, ahol az ügyfél üzembe helyezését kísérli meg. Például az MSDN, az ingyenes vagy az oktatási előfizetések megjeleníthetik ezt a hibát.

**Javítsa ki** Módosítsa az előfizetés típusát vagy régióját olyanra, amely hozzáférhet a szükséges funkciókhoz.

### <a name="error-vmextensionprovisioningerror"></a>Hiba: VMExtensionProvisioningError

![A központi telepítés képernyőképe sikertelen volt, mert a terminál kiépítési állapota nem sikerült.](media/7aaf15615309c18a984673be73ac969a.png)

**1. ok:** Átmeneti hiba a Windows rendszerű virtuális asztali környezettel.

**2. ok:** Átmeneti hiba történt a kapcsolatban.

**Javítsa ki** A PowerShell használatával történő bejelentkezéssel ellenőrizze, hogy a Windows rendszerű virtuális asztali környezet kifogástalan állapotú-e. Fejezze be a virtuális gép regisztrációját manuálisan a [gazdagép létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)-lel.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hiba: A megadott rendszergazdai Felhasználónév nem engedélyezett

![A központi telepítés képernyőképe nem sikerült, mert a megadott rendszergazda nem engedélyezett.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Nyers hiba – példa:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Okozhat** A megadott jelszó tiltott alkarakterláncokat tartalmaz (adminisztrátor, rendszergazda, gyökér).

**Javítsa ki** Frissítse a felhasználónevet, vagy használjon más felhasználókat.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hiba: A virtuális gép hibát jelzett a bővítmény feldolgozásakor

![Az erőforrás-művelet képernyőképe a terminál kiépítési állapotával fejeződött be a telepítés során.](media/49c4a1836a55d91cd65125cf227f411f.png)

Nyers hiba – példa:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Okozhat** A PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférést a virtuális gépen.

**Javítsa ki** Ellenőrizze, hogy a Felhasználónév és a jelszó rendszergazdai hozzáféréssel rendelkezik-e a virtuális gépen, majd futtassa újra a Azure Resource Manager sablont.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hiba: DeploymentFailed – a (z) FirstSessionHost PowerShell DSC-konfiguráció hibával fejeződött be

![A központi telepítés képernyőképe sikertelen, mert a PowerShell DSC-konfiguráció "FirstSessionHost" hibával fejeződött be.](media/64870370bcbe1286906f34cf0a8646ab.png)

Nyers hiba – példa:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Okozhat** A PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférést a virtuális gépen.

**Javítsa ki** Ellenőrizze, hogy a megadott Felhasználónév és jelszó rendszergazdai hozzáféréssel rendelkezik-e a virtuális gépen, majd futtassa újra a Azure Resource Manager sablont.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: DeploymentFailed – InvalidResourceReference

Nyers hiba – példa:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Okozhat** Az erőforráscsoport nevének egy részét a sablon által létrehozott egyes erőforrásokhoz használja a rendszer. A meglévő erőforrásoknak megfelelő név miatt a sablon egy másik csoportból is választhat egy meglévő erőforrást.

**Javítsa ki** Ha a Azure Resource Manager sablont a munkamenet-gazda virtuális gépek üzembe helyezéséhez futtatja, az első két karakternek egyedinek kell lennie az előfizetési erőforráscsoport neveként.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: DeploymentFailed – InvalidResourceReference

Nyers hiba – példa:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Okozhat** Ez a hiba azért van, mert a Azure Resource Manager sablonnal létrehozott hálózati adapter neve megegyezik a VNET található másik hálózati adapter nevével.

**Javítsa ki** Használjon másik gazdagép-előtagot.

### <a name="error-deploymentfailed--error-downloading"></a>Hiba: DeploymentFailed – hiba a letöltéskor

Nyers hiba – példa:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Okozhat** Ezt a hibát egy statikus útvonal, tűzfalszabály vagy NSG okozza, amely blokkolja a Azure Resource Manager sablonhoz kötött zip-fájl letöltését.

**Javítsa ki** Távolítsa el a statikus útvonal, a tűzfalszabály vagy a NSG blokkolását. Szükség esetén megnyithatja a Azure Resource Manager sablon JSON-fájlját egy szövegszerkesztőben, megtekintheti a zip-fájlra mutató hivatkozást, és letöltheti az erőforrást egy engedélyezett helyre.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználónak nincs engedélye a kezelési szolgáltatás lekérdezésére

Nyers hiba – példa:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Okozhat** A megadott Windows virtuális asztali bérlői rendszergazda nem rendelkezik érvényes szerepkör-hozzárendeléssel.

**Javítsa ki** A Windows rendszerű virtuális asztali bérlőt létrehozó felhasználónak be kell jelentkeznie a Windows rendszerű virtuális asztali PowerShellbe, és hozzá kell rendelnie egy szerepkör-hozzárendelést a megkísérelt felhasználóhoz. Ha a GitHub Azure Resource Manager-sablon paramétereit futtatja, kövesse az alábbi utasításokat a PowerShell-parancsok használatával:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Hiba: A felhasználónak az Azure multi-Factor Authentication (MFA) szolgáltatásra van szüksége

![A központi telepítés képernyőképe a többtényezős hitelesítés (MFA) hiánya miatt nem sikerült](media/MFARequiredError.png)

Nyers hiba – példa:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Okozhat** A megadott Windows virtuális asztali bérlői rendszergazdának az Azure multi-Factor Authentication (MFA) használatára van szüksége a bejelentkezéshez.

**Javítsa ki** Hozzon létre egy egyszerű szolgáltatásnevet, és rendeljen hozzá egy szerepkört a Windows rendszerű virtuális asztali [bérlőhöz az oktatóanyag lépéseinek követésével: Egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)PowerShell-lel. Miután meggyőződött arról, hogy be tud jelentkezni a Windows rendszerű virtuális asztalra az egyszerű szolgáltatással, futtassa újra az Azure Marketplace-ajánlatot vagy a GitHub-Azure Resource Manager sablont attól függően, hogy melyik módszert használja. A metódus helyes paramétereinek megadásához kövesse az alábbi utasításokat.

Ha az Azure Marketplace-ajánlatot futtatja, adja meg a következő paraméterek értékeit a Windows rendszerű virtuális asztal megfelelő hitelesítéséhez:

- Windows rendszerű virtuális asztali bérlői RDS-tulajdonos: Egyszerű szolgáltatásnév
- Alkalmazás azonosítója: A létrehozott új egyszerű szolgáltatás alkalmazás-azonosítója
- Jelszó/Jelszó megerősítése: Az egyszerű szolgáltatáshoz létrehozott jelszó titkos kulcsa
- Azure AD-bérlő azonosítója: A létrehozott szolgáltatásnév Azure AD-bérlői azonosítója

Ha a GitHub Azure Resource Manager sablont futtatja, adja meg a következő paraméterek értékeit a Windows rendszerű virtuális asztal megfelelő hitelesítéséhez:

- Bérlői rendszergazdai egyszerű felhasználónév (UPN) vagy alkalmazás azonosítója: A létrehozott új egyszerű szolgáltatás alkalmazás-azonosítója
- Bérlői rendszergazdai jelszó: Az egyszerű szolgáltatáshoz létrehozott jelszó titkos kulcsa
- IsServicePrincipal: **true**
- AadTenantId: A létrehozott szolgáltatásnév Azure AD-bérlői azonosítója

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához lásd: [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- Az előzetes verziójú szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows asztali előnézet környezetét](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)ismertető témakört.
- A következő témakörben talál útmutatást [a hibakereséshez: oktatóanyag: Resource Manager-sablonok központi telepítésének](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)hibája.
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
