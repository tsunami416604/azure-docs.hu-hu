---
title: Azure Analysis Services feladatok automatizálása szolgáltatásnevekkel |} A Microsoft Docs
description: Ismerje meg, az egyszerű szolgáltatások létrehozása az Azure Analysis Services feladatok automatizálásához.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b10be061e015686c68684723fd2d73c1431c7266
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699406"
---
# <a name="automation-with-service-principals"></a>Automatizálás szolgáltatásnevekkel

A szolgáltatásnevek olyan Azure Active Directory-alkalmazáserőforrások, amelyeket felügyelet nélküli erőforrás- vagy szolgáltatásszintű műveletek végrehajtásához hozhat létre a bérlőn belül. Még egy egyedi típusú *felhasználói identitás* és a egy alkalmazás-azonosító és jelszó vagy tanúsítvány. Egyszerű szolgáltatás csak azokat a szerepköröket és engedélyeket, amelynek van hozzárendelve által meghatározott feladatok végrehajtásához szükséges engedélyekkel rendelkezik. 

Az Analysis Servicesben az egyszerű szolgáltatások gyakori feladatok automatizálásához használható szolgálnak az Azure Automation, PowerShell, a felügyelet nélküli módban, egyedi ügyfél-alkalmazások és webalkalmazások. Ha például kiépítési kiszolgálók, a modellek, az adatok frissítése, a méretezési csoport üzembe helyezése felfelé és lefelé, és szüneteltethet és folytathat minden automatizálható a szolgáltatásnevek használatával. Engedélyeit szerepköri tagság a normál Azure AD UPN-jét fiókok hasonlóan keresztül szolgáltatásnevek vannak hozzárendelve.

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
 
Az egyszerű szolgáltatások az Azure Portalon vagy a PowerShell használatával hozható létre. További tudnivalókért lásd:

[Szolgáltatásnév létrehozása – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Hitelesítő adatok és a tanúsítvány Store az Azure Automationben

Egyszerű szolgáltatás hitelesítő adatai és a tanúsítványok tárolhatók biztonságosan az Azure Automationben a runbook-művelet. További tudnivalókért lásd:

[Hitelesítő eszközök az Azure Automationben](../automation/automation-credentials.md)   
[Az Azure Automation tanúsítvány adategységei](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz

Egy egyszerű szolgáltatást az Analysis Services kiszolgálói felügyeleti műveletek használata előtt hozzá kell adnia azt a kiszolgáló-rendszergazdák szerepkörhöz. További tudnivalókért lásd: [szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Kapcsolati karakterláncok az egyszerű szolgáltatások

Szolgáltatás egyszerű appID és jelszó vagy tanúsítvány használható kapcsolati karakterláncokat ugyanúgy zajlik, mint egy egyszerű felhasználónév.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egyszerű szolgáltatás használata esetén az erőforrás-felügyeleti műveletek a [Az.AnalysisServices](/powershell/module/az.analysisservices) használja, a modul `Connect-AzAccount` parancsmagot. Kiszolgálói műveletek az egyszerű szolgáltatás használatakor a [SQLServer](https://www.powershellgallery.com/packages/SqlServer) használja, a modul `Add-AzAnalysisServicesAccount` parancsmagot. 

A következő példában appID és a egy jelszót használt modell adatbázis-frissítési művelet végrehajtásához:

```powershell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO és ADOMD 

Az ügyfélalkalmazások számára, és a web apps kapcsolódáskor [AMO és ADOMD-ügyfélkódtárak](analysis-services-data-providers.md) 15.0.2 és a magasabb telepíthető NuGet-csomagot támogatja az egyszerű szolgáltatások a következő szintaxis használatával a kapcsolati karakterláncokban lévő verzió: `app:AppID` és a jelszó vagy `cert:thumbprint`. 

A következő példában `appID` és a egy `password` model adatbázis-frissítési művelet elvégzéséhez használt:

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
[Szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)   
