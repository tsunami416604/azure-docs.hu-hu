---
title: Azure Analysis Services feladatok automatizálása a szolgáltatásnevekről |} Microsoft Docs
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2b49b85d39f55052e112fd9f4f0e28bdc6c91637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="automation-with-service-principals"></a>Automatizálása a szolgáltatás rendszerbiztonsági tagok

Szolgáltatásnevekről az Azure Active Directory-alkalmazás erőforrás hoz létre a bérlő felügyelet nélküli erőforrás és a szolgáltatási szint műveletek elvégzéséhez belül. Egy egyedi típusú fontosságúak *felhasználói identitás* alkalmazás azonosító és jelszó vagy tanúsítvány. Egy egyszerű szolgáltatás csak azokat a szerepköröket és engedélyeket, amelynek van hozzárendelve által meghatározott feladatok végrehajtásához szükséges engedélyekkel rendelkezik. 

Az Analysis Services szolgáltatás rendszerbiztonsági tagok szolgálnak az Azure Automation PowerShell felügyelet nélküli módban, egyedi ügyfél-alkalmazások és webalkalmazások gyakori feladatok automatizálásához. Például létesítési kiszolgálók modellek, az Adatfrissítés, méretezési kialakítása fel/le, és szüneteltet vagy összes automatizálható szolgáltatásnevekről használatával. Engedélyek szolgáltatásnevekről keresztül szerepköri tagság hasonlóan rendszeres Azure AD UPN-fiókok vannak hozzárendelve.

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
 
Szolgáltatás rendszerbiztonsági tagoknak is létrehozható, az Azure portálon vagy a PowerShell használatával. További tudnivalókért lásd:

[Hozzon létre egyszerű szolgáltatásnév - Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Az Azure Automationben hitelesítő adat és a tanúsítvány eszközök tárolásához

Tanúsítványok és a szolgáltatás egyszerű hitelesítő adatait tárolhatja biztonságosan Azure Automation a runbook-műveleteivel. További tudnivalókért lásd:

[Azure Automation szolgáltatásbeli hitelesítőadat eszközök](../automation/automation-credentials.md)   
[Azure Automation szolgáltatásbeli tanúsítvány eszközök](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Kiszolgáló-rendszergazdai szerepkör szolgáltatásnevekről hozzáadása

Egy egyszerű szolgáltatásnév az Analysis Services kiszolgáló műveletek használata előtt hozzá kell adnia azt a kiszolgáló-Rendszergazdák szerepkör. További tudnivalókért lásd: [egyszerű szolgáltatás hozzáadása a kiszolgáló-rendszergazdai szerepkör](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>A kapcsolati karakterláncok szolgáltatásnevekről

Szolgáltatás egyszerű appID és a jelszó vagy a tanúsítvány használható kapcsolódási karakterláncokat sokkal ugyanaz, mint egy egyszerű Felhasználónévvel.

### <a name="powershell"></a>PowerShell

Az erőforrás műveletek az egyszerű szolgáltatás használata esetén a [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) modul, használjon `Login-AzureRmAccount` parancsmag. Kiszolgáló műveletek esetében egy egyszerű szolgáltatás használata esetén a [SQLServer](https://www.powershellgallery.com/packages/SqlServer) modul, használjon `Add-AzureAnalysisServicesAccount` parancsmag. 

A következő példában appID és a jelszót használt a modell adatbázis-frissítési művelet végrehajtása:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO és ADOMD 

Ügyfélalkalmazások és a web apps használatával történő csatlakozáskor [AMO és ADOMD ügyfélkódtáraival](analysis-services-data-providers.md) verziója 15.0.2 és magasabb telepíthető NuGet-csomagok támogatja szolgáltatásnevekről kapcsolati karakterláncok a következő szintaxis használatával: `app:AppID` és jelszó vagy `cert:thumbprint`. 

A következő példában `appID` és egy `password` modell adatbázis-frissítési művelet végrehajtásához használják:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>További lépések
[Jelentkezzen be az Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Egy egyszerű szolgáltatás hozzáadása a kiszolgáló-rendszergazdai szerepkör](analysis-services-addservprinc-admins.md)   