---
title: Windows virtuális asztal bérlő és a gazdagép-készlet létrehozása – Azure
description: Hibaelhárításához és megoldásához bérlő és a gazdagépcsoport készlet bocsát ki egy virtuális asztali Windows-bérlős környezet beállítása során.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 7ec4e0ffd87c0ef73a551416d8a8cc672f095483
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786737"
---
# <a name="tenant-and-host-pool-creation"></a>Bérlői és gazdagépcsoport létrehozása

Ez a cikk ismerteti a problémák a Windows virtuális asztal bérlő és a kapcsolódó munkamenet készlet gazda-infrastruktúrát a kezdeti beállítás során.

## <a name="provide-feedback"></a>Visszajelzés küldése

A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>A Windows 10 Enterprise több munkamenet kép beolvasása

Használhatja a Windows 10 Enterprise több munkamenet-rendszerképet, lépjen az Azure piactéren, válassza ki a **Ismerkedés** > **Microsoft Windows 10-es** > és [a Windows 10 Enterprise Virtuális asztalok előzetes verzió, a verzió 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Válassza ki a Windows 10 Enterprise virtuális asztalok előzetes verziójú 1809 a képernyőkép.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows virtuális asztal bérlő létrehozása

Ez a szakasz ismertet a potenciális problémákat, amikor a virtuális asztali Windows-bérlő létrehozása.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezése

![Képernyőkép a PowerShell-ablakot, amelyben a felhasználó nem jogosult le a felügyeleti szolgáltatást.](media/UserNotAuthorizedNewTenant.png)

Példa a nyers hiba:

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

**OK:** A felhasználó, aki be van jelentkezve a TenantCreator szerepkört az Azure Active Directory még nem lett hozzárendelve.

**Javítás:** Kövesse a [TenantCreator alkalmazás-szerepkör hozzárendelése egy felhasználóhoz, az Azure Active Directory-bérlőben](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Az utasítások után a TenantCreator szerepkörhöz rendelt felhasználó.

![Képernyőkép a TenantCreator hozzárendelt szerepkör.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows virtuális asztal munkamenetben gazdagép VM-EK létrehozása

Munkamenetgazda virtuális gépeket hozhat létre több módszert is, de a távoli asztali szolgáltatások/Windows virtuális asztal csapatok csak támogatja a virtuális gép kiépítése az Azure Resource Manager-sablonnal kapcsolatos problémákat. Az Azure Resource Manager-sablon megtalálható [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) és [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Windows virtuális asztal – kiépítése egy gazdagép készlet Azure Marketplace-ajánlat kapcsolatos problémákat

A Windows virtuális asztal – egy készlet alkalmazásgazda-sablon üzembe helyezése érhető el az Azure Marketplace-ről.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hiba: A GitHub-hivatkozás használata esetén az üzenet "hozzon létre egy ingyenes fiókot" jelenik meg

![Képernyőkép: hozzon létre egy ingyenes fiókot.](media/be615904ace9832754f0669de28abd94.png)

**1. ok:** Nincs aktív előfizetések az Azure-bA bejelentkezni használt fiók, vagy a használt fiók nem rendelkezik megfelelő engedélyekkel az előfizetések megtekintésére.

**1 javítás:** Jelentkezzen be egy olyan fiókkal, amely rendelkezik közreműködői hozzáférés szükséges az előfizetéshez (minimum), ahol munkamenetgazda virtuális gépek vannak fog üzembe helyezni.

**2. ok:** A használatban lévő előfizetés a Microsoft Felhőszolgáltató (CSP) bérlő egy része.

**2 Javítás:** Nyissa meg a GitHub-helyre a **létrehozásával és kiépítésével új Windows virtuális asztali állomás készlet** , és kövesse az alábbi utasításokat:

1. Kattintson a jobb gombbal a **üzembe helyezés az Azure** válassza **másolási hivatkozás címe**.
2. Nyissa meg **Jegyzettömb** , és illessze be a hivatkozást.
3. A # karaktert, mielőtt beszúrása a CSP teljes ügyfél bérlő neve.
4. Nyissa meg egy böngészőt, és az Azure Portalon lévő új hivatkozásra fogja betölteni a sablont.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Az Azure Resource Manager-sablon és a PowerShell Desired State Configuration (DSC) hibák

Sikertelen üzembe helyezés Azure Resource Manager-sablonok és PowerShell DSC hibaelhárítása ezeket az utasításokat követve.

1. Tekintse át a hibákat a központi telepítés használatával [megtekintése az Azure Resource Manager üzembe helyezési műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Ha nincsenek hibák az üzemelő példányban, tekintse át a hibákat a tevékenység napló használatával [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. A hiba azonosítása, miután használja, a hibaüzenet, és az erőforrások [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) a probléma megoldása érdekében.
4. Újra üzembe a sablont újra és a korábbi központi telepítés során létrehozott bármely erőforrás törlése.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hiba: A telepítés sikertelen volt...<hostname>JoinDomain

![A telepítés nem sikerült képernyőképe.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Példa a nyers hiba:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. ok:** A virtuális gépek csatlakoztatása a tartományhoz megadott hitelesítő adatok helytelenek.

**1 javítás:** Az "Helytelen hitelesítő adatok" hibát látja, a virtuális gépek a tartományhoz nem csatlakozó [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).

**2. ok:** Tartomány neve nem oldható fel.

**2 Javítás:** A "tartománynév nem oldja meg" hiba jelenik meg, a virtuális gépek a tartományhoz nem csatlakozó [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Hiba: Az üzembe helyezési failed...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**OK:** Az előfizetés használata olyan, amely nem érhető el a régióban, ahol az ügyfél megpróbálja telepíteni a szükséges funkciókat. Például az MSDN, ingyenes vagy oktatási előfizetések ezt a hibát jeleníti meg.

**Javítás:** Módosítsa az előfizetés-típus vagy a régiót, a szükséges szolgáltatások eléréséhez.

### <a name="error-vmextensionprovisioningerror"></a>Hiba: VMExtensionProvisioningError

![Képernyőkép, a központi telepítés nem sikerült befejező üzembe helyezési állapota sikertelen.](media/7aaf15615309c18a984673be73ac969a.png)

**1. ok:** Átmeneti hiba történt a virtuális asztali Windows-környezetet.

**2. ok:** Átmeneti hiba történt a kapcsolat.

**Javítás:** Erősítse meg, jelentkezzen be a PowerShell használatával a Windows virtuális asztali környezetben állapota kifogástalan. A virtuális Gépet manuálisan a regisztráció befejezéséhez [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hiba: A megadott rendszergazdai felhasználónév nem megengedett.

![Képernyőkép az üzembe helyezés nem sikerült a, amely egy megadott rendszergazda számára nem engedélyezett.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Példa a nyers hiba:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**OK:** A megadott jelszó tiltott karakterláncrész (felügyeleti, rendszergazda, legfelső szintű) tartalmaz.

**Javítás:** Frissítse a felhasználónevet, vagy használja a különböző felhasználók.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hiba: Virtuális gép hibát jelzett bővítmény feldolgozása során

![Képernyőkép: az erőforrás-művelet befejező üzembe helyezési állapota a központi telepítés sikertelen.](media/49c4a1836a55d91cd65125cf227f411f.png)

Példa a nyers hiba:

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

**OK:** PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférés a virtuális gépen.

**Javítás:** Erősítse meg a felhasználónév és jelszó rendszergazdai hozzáféréssel rendelkeznek a virtuális gépen, és futtassa újra az Azure Resource Manager-sablon.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hiba: Sikertelen – PowerShell DSC-konfiguráció "FirstSessionHost" hibával fejeződött be

![Képernyőkép a központi telepítés sikertelen, és a PowerShell DSC-konfiguráció "FirstSessionHost" hibával fejeződött be.](media/64870370bcbe1286906f34cf0a8646ab.png)

Példa a nyers hiba:

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

**OK:** PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférés a virtuális gépen.

**Javítás:** Ellenőrizze a felhasználónevet és jelszót adott rendszergazdai hozzáféréssel rendelkeznek a virtuális gépen, majd futtassa újra az Azure Resource Manager-sablon.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: Sikertelen – InvalidResourceReference

Példa a nyers hiba:

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

**OK:** Az erőforráscsoport nevének egy részét az egyes erőforrások a sablon által létrehozott szolgál. A neve egyezik a meglévő erőforrások, mert a sablon előfordulhat, hogy meglévő erőforrást akkor válasszon egy másik csoportból.

**Javítás:** Munkamenetgazda virtuális gépek üzembe helyezéséhez az Azure Resource Manager-sablon futtatásakor győződjön meg az előfizetés erőforráscsoport-név egyedi az első két karakter.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: Sikertelen – InvalidResourceReference

Példa a nyers hiba:

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

**OK:** Ez a hiba oka az, hogy az Azure Resource Manager-sablonnal létrehozott hálózati adapter már szerepel a neve megegyezik egy másik hálózati Adaptert a virtuális hálózat.

**Javítás:** Egy másik gazdagépre előtagot használja.

### <a name="error-deploymentfailed--error-downloading"></a>Hiba: Sikertelen – hiba letöltése

Példa a nyers hiba:

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

**OK:** Ez a hiba miatt egy statikus útvonalat, tűzfalszabály vagy az NSG blokkolja a zip-fájl kapcsolódik az Azure Resource Manager-sablon letöltése nem lehetséges.

**Javítás:** Távolítsa el a statikus útvonal, tűzfalszabály és az NSG blokkolja. Szükség esetén nyissa meg az Azure Resource Manager sablon json-fájlt egy szövegszerkesztőben, igénybe vehet a hivatkozás a zip-fájlba, és töltse le az erőforrás engedélyezett helyre.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezése

Példa a nyers hiba:

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

**OK:** A megadott Windows virtuális asztal Bérlői rendszergazda nem rendelkezik érvényes szerepkör-hozzárendelés.

**Javítás:** Jelentkezzen be a Windows virtuális asztal PowerShell, és rendelje hozzá a megkísérelt felhasználói szerepkör-hozzárendelés kell a felhasználó, aki létrehozta a virtuális asztali Windows-bérlő. Ha a GitHub Azure Resource Manager-sablon paramétereit futtat, kövesse az alábbi utasításokat, PowerShell-parancsokkal:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Hiba: Felhasználónak van szüksége az Azure multi-factor Authentication (MFA)

![Képernyőkép az üzembe helyezés erőforráshiány miatt sikertelen volt a multi-factor Authentication (MFA)](media/MFARequiredError.png)

Példa a nyers hiba:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**OK:** A megadott virtuális asztali Windows-bérlő rendszergazdája megköveteli, az Azure multi-factor Authentication (MFA) való bejelentkezéshez.

**Javítás:** Hozzon létre egy egyszerű szolgáltatást, és hozzá kell rendelnie egy szerepkört a Windows virtuális asztal bérlő leírt lépések végrehajtásával [oktatóanyag: Szolgáltatásnevek és szerepkör-hozzárendelések létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Miután ellenőrizte, hogy be tud virtuális asztali Windows szolgáltatásnévhez, futtassa újra az Azure Marketplace-ajánlat vagy a GitHub Azure Resource Manager-sablon, attól függően, melyik módszert használ. Kövesse az alábbi utasításokat, hogy a megfelelő paramétereket adja meg a metódus.

Ha az Azure piactéren, amely rendszert használ, adja meg a Windows virtuális asztalok megfelelően hitelesíteni a következő paraméterek értékeit:

- Windows virtuális asztal bérlői RDS tulajdonosa: Egyszerű szolgáltatás
- Alkalmazásazonosító: Az alkalmazás azonosítója az új egyszerű szolgáltatás létrehozása
- A jelszó jelszó/megerősítése: A jelszó titkos kulcsot, az egyszerű szolgáltatás számára létrehozott
- Az Azure AD-bérlő azonosítója: A létrehozott egyszerű szolgáltatás Azure AD-bérlő azonosítója

Ha a GitHub Azure Resource Manager-sablon rendszert használ, adja meg a Windows virtuális asztalok megfelelően hitelesíteni a következő paraméterek értékeit:

- Bérlői rendszergazda egyszerű felhasználónév (UPN) vagy az alkalmazás azonosítója: Az alkalmazás azonosítója az új egyszerű szolgáltatás létrehozása
- Bérlői rendszergazda jelszava: A jelszó titkos kulcsot, az egyszerű szolgáltatás számára létrehozott
- IsServicePrincipal: **true**
- AadTenantId: A létrehozott egyszerű szolgáltatás Azure AD-bérlő azonosítója

## <a name="next-steps"></a>További lépések

- Hibaelhárítási Windows virtuális asztal és a kiterjesztés nyomon követi az áttekintést lásd: [hibaelhárítási áttekintése, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- Egy virtuális gépet (VM) konfigurálása a Windows virtuális asztal során problémák hibaelhárítása: [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- Windows virtuális asztali kapcsolatok problémáinak hibaelhárítása: [távoli asztali kapcsolatok](troubleshoot-client-connection.md).
- A virtuális asztal Windows PowerShell-lel kapcsolatos problémák elhárításához lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Nyissa meg a hibaelhárítás az oktatóanyagot, tekintse meg [oktatóanyag: Resource Manager-sablon üzemelő példányok hibaelhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).