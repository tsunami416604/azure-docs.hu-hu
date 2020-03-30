---
title: Windows virtuális asztal bérlői gazdakészletének létrehozása – Azure
description: A bérlői és gazdakészlettel kapcsolatos problémák elhárítása és megoldása a Windows virtuális asztal bérlői környezetének beállítása során.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371596"
---
# <a name="tenant-and-host-pool-creation"></a>Bérlői és gazdagépcsoport létrehozása

Ez a cikk a Windows virtuális asztal bérlője és a kapcsolódó munkamenetgazda-készlet infrastruktúra kezdeti beállítása során felmerülő problémákat ismerteti.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows Virtual Desktop Tech Közösség,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) hogy megvitassák a Windows Virtual Desktop szolgáltatás a termék csapat és az aktív közösség tagjai.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>A Windows 10 Enterprise többmunkamenetes lemezképének beolvasása

A Windows 10 Enterprise többmunkamenetes lemezképének használatához nyissa meg az Azure Piactérről a**Microsoft Windows 10** > és [a Windows 10 Enterprise for Virtual Desktops 1809-es verziójának](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) **első lépések** > lehetőséget.

![Képernyőkép a Windows 10 Enterprise virtuális asztalokhoz, 1809-es verzió kiválasztásáról.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>A Windows virtuális asztal bérlőjének létrehozása

Ez a szakasz a Windows virtuális asztal bérlőjének létrehozásakor felmerülő lehetséges problémákat ismerteti.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználó nem jogosult lekérdezni a felügyeleti szolgáltatást

![Képernyőkép a PowerShell ablakról, amelyben a felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezésére.](media/UserNotAuthorizedNewTenant.png)

Példa nyers hibára:

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

**Ok:** A bejelentkezett felhasználó nincs hozzárendelve a TenantCreator szerepkör az Azure Active Directoryban.

**Javítás:** Kövesse a [TenantCreator alkalmazásszerepkör hozzárendelése az Azure Active Directory-bérlő egyik felhasználójához](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)című utasításokat. Az utasítások követése után egy felhasználó lesz hozzárendelve a TenantCreator szerepkörhöz.

![Képernyőkép a TenantCreator szerepkör hozzárendeléséről.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows Virtual Desktop munkamenetgazda virtuális gépek létrehozása

A munkamenetgazda virtuális gépek többféleképpen is létrehozhatók, de a Windows virtuális asztal csapata csak az [Azure Marketplace-ajánlattal](https://azuremarketplace.microsoft.com/) kapcsolatos virtuális gépek kiépítési problémáit támogatja. További információ: [Kérdések a Windows virtuális asztal használatával – Host Pool Azure Marketplace-ajánlat kiépítése.](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémák a Windows virtuális asztal használatával – Állomáskészlet kiépítése Azure Marketplace-ajánlat

A Windows virtuális asztal – Üzembe létesítési szolgáltatás sablon érhető el az Azure Marketplace-en.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hiba: A GitHubon lévő hivatkozás használatakor megjelenik a "Szabad fiók létrehozása" üzenet

![Képernyőkép: ingyenes fiók létrehozása.](media/be615904ace9832754f0669de28abd94.png)

**1. ok:** Nincsenek aktív előfizetések az Azure-ba való bejelentkezéshez használt fiókban, vagy a használt fiók nem rendelkezik az előfizetések megtekintéséhez szükséges engedélyekkel.

**1. javítás:** Jelentkezzen be egy olyan fiókkal, amely (legalább) közreműködői hozzáféréssel rendelkezik ahhoz az előfizetéshez, ahol a munkamenetgazda virtuális gépei üzembe kerülnek.

**2. ok:** A használt előfizetés egy Microsoft Cloud Service Provider (CSP) bérlő része.

**2. javítás:** Nyissa meg a GitHub helyét az **új Windows virtuális asztali gazdakészlet létrehozásához és kiépítéséhez,** és kövesse az alábbi utasításokat:

1. Kattintson a jobb gombbal az **Azure-ba való telepítéselemre,** és válassza **a Hivatkozáscím másolása parancsot.**
2. Nyissa **meg a Jegyzettömböt,** és illessze be a hivatkozást.
3. A # karakter előtt szúrja be a kriptaközponti ügyfél bérlőjének nevét.
4. Nyissa meg az új hivatkozást egy böngészőben, és az Azure Portal betölti a sablont.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Hiba: "A sablon központi telepítése érvénytelen" hibaüzenet jelenik meg

![Képernyőkép a "sablon telepítés ... érvénytelen" hibaüzenet](media/troubleshooting-marketplace-validation-error-generic.png)

Adott művelet elvégzése előtt ellenőriznie kell a tevékenységnaplót a sikertelen központi telepítés ellenőrzésének részletes hibájának megtekintéséhez.

A hiba megtekintése a tevékenységnaplóban:

1. Lépjen ki az Azure Marketplace aktuális telepítési ajánlata.
2. A felső keresősávban keresse meg a Tevékenységnapló t, és válassza a **Tevékenységnapló lehetőséget.**
3. Keressen egy **sikertelen** állapotú központi **Failed** telepítés ellenőrzése nevű tevékenységet, és válassza ki a tevékenységet.
   ![Képernyőkép az egyéni **Deployment** ellenőrzés **sikertelen** állapotú tevékenységekről](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Válassza a JSON lehetőséget, majd görgessen le a képernyő aljára, amíg meg nem jelenik a "statusMessage" mező.
   ![Képernyőkép a sikertelen tevékenységről, a JSON-szöveg statusMessage tulajdonsága körül egy piros négyzettel.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Ha a műveletsablon túllépi a kvótakorlátot, az alábbi műveletek egyikével javíthatja ki:

 - Futtassa az Azure Piacteret az első alkalommal használt paraméterekkel, de ezúttal kevesebb virtuális gépet és virtuális gépmeket használjon.
 - Nyissa meg a **böngésző statusMessage** mezőjében látható hivatkozást, és küldjön egy kérelmet az Azure-előfizetés kvótájának növelésére a megadott virtuálisgép-termékváltozathoz.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Az Azure Resource Manager-sablon és a PowerShell kívánt állapotkonfigurációs (DSC) hibái

Kövesse ezeket az utasításokat az Azure Resource Manager-sablonok és a PowerShell DSC sikertelen központi telepítéseinek elhárításához.

1. Tekintse át a telepítési hibákat a Telepítési műveletek megtekintése az [Azure Resource Manager rel](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Ha nincshiba a központi telepítésben, tekintse át a hibákat a tevékenységnaplóban a Tevékenységnaplók megtekintése segítségével [az erőforrásokon végzett műveletek naplózásához.](../azure-resource-manager/resource-group-audit.md)
3. A hiba azonosítását követően használja a hibaüzenetet, és az erőforrások [hibaelhárítási általános Azure-telepítési hibák](../azure-resource-manager/resource-manager-common-deployment-errors.md) az Azure Resource Manager a probléma megoldásához.
4. Törölje az előző központi telepítés során létrehozott erőforrásokat, és próbálja meg újra telepíteni a sablont.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hiba: A telepítés nem\<sikerült.... állomásnév>/joindomain

![A központi telepítés sikertelen képernyőkép.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Példa nyers hibára:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. ok:** A virtuális gépek tartományhoz való csatlakozásához megadott hitelesítő adatok helytelenek.

**1. javítás:** Tekintse meg a "Helytelen hitelesítő adatok" hibaüzenetet a virtuális gépek nem csatlakozik a tartományhoz a [munkamenetgazda virtuális gép konfigurációjában.](troubleshoot-vm-configuration.md)

**2. ok:** A tartománynév nem oldható fel.

**2. javítás:** Lásd: [Hiba: A tartománynév nem oldható fel](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) a [munkamenetgazda virtuális gépének konfigurációjában.](troubleshoot-vm-configuration.md)

**3. ok:** A virtuális hálózati (VNET) DNS-konfigurációja **Alapértelmezett**értékre van állítva.

A probléma megoldásához tegye a következőket:

1. Nyissa meg az Azure Portalt, és nyissa meg a **Virtuális hálózatok** lapot.
2. Keresse meg a virtuális hálózatot, majd válassza a **DNS-kiszolgálók at.**
3. A DNS-kiszolgálók menüjének a képernyő jobb oldalán kell megjelennie. Kattintson az Egyéni menü **egyéni parancsára.**
4. Győződjön meg arról, hogy az Egyéni csoportban felsorolt DNS-kiszolgálók megegyeznek a tartományvezérlővel vagy az Active Directory-tartománnyal. Ha nem látja a DNS-kiszolgálót, hozzáadhatja, ha beírja annak értékét a **DNS-kiszolgáló hozzáadása** mezőbe.

### <a name="error-your-deployment-failedunauthorized"></a>Hiba: A telepítés nem sikerült...\Nem engedélyezett

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Ok:** A használt előfizetés olyan típus, amely nem tudja elérni a szükséges funkciókat abban a régióban, ahol az ügyfél telepíti. Az MSDN-, az Ingyenes vagy az Oktatási előfizetések például megjeleníthetik ezt a hibát.

**Javítás:** Módosítsa az előfizetés típusát vagy régióját olyanra, amely hozzáférhet a szükséges funkciókhoz.

### <a name="error-vmextensionprovisioningerror"></a>Hiba: VMExtensionProvisioningError

![Képernyőkép a központi telepítés sikertelen, ha a terminál létesítési állapota sikertelen.](media/7aaf15615309c18a984673be73ac969a.png)

**1. ok:** Átmeneti hiba a Windows virtuális asztal környezetben.

**2. ok:** Átmeneti hiba a kapcsolattal.

**Javítás:** Győződjön meg arról, hogy a Windows virtuális asztali környezet kifogástalan a PowerShell használatával történő bejelentkezéssel. Végezze el manuálisan a virtuális gép regisztrációját [a Gazdagépkészlet létrehozása a PowerShell használatával](create-host-pools-powershell.md)című részben.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hiba: A megadott rendszergazdai felhasználónév nem engedélyezett.

![A központi telepítés ről képernyőkép sikertelen, amelyben a megadott rendszergazda nem engedélyezett.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Példa nyers hibára:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Ok:** A megadott jelszó tiltott részkarakterláncokat tartalmaz (rendszergazda, rendszergazda, gyökér).

**Javítás:** Frissítse a felhasználónevet, vagy használjon különböző felhasználókat.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hiba: A virtuális gép hibát jelentett a bővítmény feldolgozásakor

![Képernyőkép a terminállétesítési állapottal a központi telepítés sikertelen állapotával befejezett erőforrás-műveletről.](media/49c4a1836a55d91cd65125cf227f411f.png)

Példa nyers hibára:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Ok:** A PowerShell DSC-bővítmény nem tudott rendszergazdai hozzáférést szerezni a virtuális gépen.

**Javítás:** Erősítse meg, hogy a felhasználónév és a jelszó rendszergazdai hozzáféréssel rendelkezik a virtuális gépen, és futtassa újra az Azure Resource Manager sablont.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hiba: A telepítés sikertelen – a PowerShell DSC konfigurációja "FirstSessionHost" hiba(ok)

![Képernyőkép a telepítési hiba a PowerShell DSC konfiguráció "FirstSessionHost" befejeződött hiba (ok).](media/64870370bcbe1286906f34cf0a8646ab.png)

Példa nyers hibára:

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

**Ok:** A PowerShell DSC-bővítmény nem tudott rendszergazdai hozzáférést szerezni a virtuális gépen.

**Javítás:** Erősítse meg a felhasználónevet és a jelszót, és rendszergazdai hozzáféréssel rendelkezik a virtuális gépen, és futtassa újra az Azure Resource Manager sablont.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: A telepítés sikertelen – invalidResourceReference

Példa nyers hibára:

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

**Ok:** Az erőforráscsoport nevének egy része a sablon által létrehozott bizonyos erőforrásokhoz használatos. A meglévő erőforrásoknak megfelelő név miatt a sablon kiválaszthat egy meglévő erőforrást egy másik csoportból.

**Javítás:** Az Azure Resource Manager-sablon futtatásakor a munkamenet gazdagépei üzembe helyezésekor tegye egyedivé az első két karaktert az előfizetési erőforráscsoport nevéhez.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: A telepítés sikertelen – invalidResourceReference

Példa nyers hibára:

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

**Ok:** Ez a hiba azért van, mert az Azure Resource Manager sablonnal létrehozott hálózati adapter neve megegyezik a virtuális hálózatban már szereplő másik hálózati adapter nevével.

**Javítás:** Használjon másik állomáselőtagot.

### <a name="error-deploymentfailed--error-downloading"></a>Hiba: A telepítés sikertelen – hiba történt a letöltéssorán

Példa nyers hibára:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Ok:** Ez a hiba egy statikus útvonal, tűzfalszabály vagy NSG miatt blokkolja az Azure Resource Manager-sablonhoz kötött zip-fájl letöltését.

**Javítás:** Távolítsa el a blokkolási statikus útvonalat, a tűzfalszabályt vagy az NSG-t. Megnyithatja az Azure Resource Manager json sablonjának jsonfájlját egy szövegszerkesztőben, vigye a zip-fájlra mutató hivatkozást, és töltse le az erőforrást egy engedélyezett helyre.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: A felhasználó nem jogosult lekérdezni a felügyeleti szolgáltatást

Példa nyers hibára:

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

**Ok:** A megadott Windows virtuális asztal bérlői rendszergazdája nem rendelkezik érvényes szerepkör-hozzárendeléssel.

**Javítás:** A Windows virtuális asztal bérlőjét létrehozó felhasználónak be kell jelentkeznie a Windows virtuális asztali PowerShell szolgáltatásba, és szerepkör-hozzárendelést kell rendelnie a megkísérelt felhasználóhoz. Ha a GitHub Azure Resource Manager-sablon paramétereit futtatja, kövesse az alábbi utasításokat a PowerShell-parancsok használatával:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Hiba: A felhasználónak Azure többtényezős hitelesítésre (MFA) van szüksége

![A többtényezős hitelesítés (MFA) hiánya miatt nem sikerült a központi telepítésről készült képernyőkép](media/MFARequiredError.png)

Példa nyers hibára:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Ok:** A megadott Windows virtuális asztal bérlői rendszergazdája az Azure többtényezős hitelesítéséhez (MFA) van szükség a bejelentkezéshez.

**Javítás:** Hozzon létre egy egyszerű szolgáltatásszolgáltatást, és rendeljen hozzá egy szerepkört a Windows virtuális asztal bérlőjéhez az [Oktatóanyag: Egyszerű szolgáltatás és szerepkör-hozzárendelések létrehozása](create-service-principal-role-powershell.md)a PowerShell használatával című lépésekkel. Miután meggyőződött arról, hogy be tud-e jelentkezni a Windows virtuális asztalra az egyszerű szolgáltatással, futtassa újra az Azure Marketplace-ajánlat vagy a GitHub Azure Resource Manager sablont, attól függően, hogy melyik módszert használja. A módszer helyes paramétereinek megadásához kövesse az alábbi utasításokat.

Ha az Azure Marketplace-ajánlatot futtatja, adja meg a következő paraméterek értékét a Windows virtuális asztal megfelelő hitelesítéséhez:

- Windows virtuális asztal bérlői RDS-ének tulajdonosa: egyszerű szolgáltatás
- Alkalmazásazonosító: A létrehozott új egyszerű szolgáltatás alkalmazásazonosítója
- Jelszó/Jelszó megerősítése: A szolgáltatásnévhez létrehozott jelszótitok
- Azure AD-bérlőazonosító: A létrehozott egyszerű szolgáltatás Azure AD-bérlőazonosítója

Ha a GitHub Azure Resource Manager sablont futtatja, adja meg a következő paraméterek et a Windows virtual desktop on való megfelelő hitelesítéshez:

- Bérlői rendszergazda egyszerű felhasználóneve (UPN) vagy alkalmazásazonosító: A létrehozott új egyszerű szolgáltatás alkalmazásazonosítója
- Bérlői rendszergazdai jelszó: A szolgáltatásnévhez létrehozott jelszótitok
- IsServicePrincipal: **igaz**
- AadTenantId: A létrehozott egyszerű szolgáltatás névsor Ának Azure AD-bérlőazonosítója

## <a name="next-steps"></a>További lépések

- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A Windows Virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításáról a Windows Virtuális asztal szolgáltatás kapcsolatai című [témakörben található.](troubleshoot-service-connection.md)
- A Távoli asztali ügyfelekkel kapcsolatos problémák elhárításáról [a Távoli asztali ügyfél – Problémaeltetés – problémamegoldás](troubleshoot-client.md)
- A PowerShell Windows virtuális asztallal való használatakor felmerülő problémák elhárításáról a [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)című témakörben található.
- A szolgáltatásról a [Windows virtuális asztali környezet című témakörben](environment-setup.md)olvashat bővebben.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- A naplózási műveletekről a [Naplózási műveletek az Erőforrás-kezelővel (Audit operations with Resource Manager) témakörben](../azure-resource-manager/management/view-activity-logs.md)olvashat.
- A központi telepítés során fellépő hibák meghatározására irányuló műveletekről a [Telepítési műveletek megtekintése című témakörben](../azure-resource-manager/templates/deployment-history.md)olvashat.
