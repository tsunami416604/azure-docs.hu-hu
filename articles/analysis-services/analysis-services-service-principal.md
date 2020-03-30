---
title: Az Azure Analysis Services-feladatok automatizálása egyszerű szolgáltatásokkal | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre egyszerű szolgáltatást az Azure Analysis Services felügyeleti feladatok automatizálásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266142"
---
# <a name="automation-with-service-principals"></a>Automatizálás szolgáltatásnevekkel

A szolgáltatásnevek olyan Azure Active Directory-alkalmazáserőforrások, amelyeket felügyelet nélküli erőforrás- vagy szolgáltatásszintű műveletek végrehajtásához hozhat létre a bérlőn belül. Ők egy egyedi típusú *felhasználói identitás* egy alkalmazás azonosítóés jelszó vagy tanúsítvány. Az egyszerű szolgáltatás csak azokkal az engedélyekkel rendelkezik, amelyek a szerepkörök és engedélyek által meghatározott feladatok végrehajtásához szükségesek. 

Az Analysis Services szolgáltatásnévi tagok az Azure Automation, a PowerShell felügyelet nélküli üzemmódja, az egyéni ügyfélalkalmazások és a webalkalmazások segítségével automatikusan automatizálják a gyakori feladatokat. Például a kiszolgálók kiépítése, modellek telepítése, adatfrissítés, felfelé /le, és szünet/folytatás mind automatizálhatók egyszerű szolgáltatás használatával. Az engedélyek szerepkör-tagságon keresztül vannak hozzárendelve a szolgáltatástagokhoz, hasonlóan a hagyományos Azure AD UPN-fiókokhoz.

Az Analysis Services a felügyelt identitások által a szolgáltatásnévi szolgáltatásokat használó műveleteket is támogatja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) és [az Azure-hitelesítést támogató Azure-szolgáltatásokhoz](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)című témakörben olvashat.  

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
 
Egyszerű szolgáltatástagok hozhatók létre az Azure Portalon vagy a PowerShell használatával. További tudnivalókért lásd:

[Egyszerű szolgáltatás létrehozása - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Hitelesítő adatok és tanúsítványelemek tárolása az Azure Automationben

Az egyszerű szolgáltatás hitelesítő adatai és tanúsítványai biztonságosan tárolhatók az Azure Automationben a runbook-műveletekhez. További tudnivalókért lásd:

[Hitelesítő adatok az Azure Automationben](../automation/automation-credentials.md)   
[Az Azure Automation tanúsítvány adategységei](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Szolgáltatásnévmegések hozzáadása a kiszolgálófelügyeleti szerepkörhöz

Mielőtt az Analysis Services kiszolgálókezelési műveleteihez egyszerű szolgáltatást használhatna, hozzá kell adnia azt a kiszolgálórendszergazdák szerepkörhöz. További információ: [Egyszerű szolgáltatás hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)című témakörben olvashat.

## <a name="service-principals-in-connection-strings"></a>Szolgáltatásnévtagok kapcsolati karakterláncokban

Az egyszerű alkalmazásazonosító és jelszó vagy tanúsítvány a kapcsolati karakterláncokban nagyjából ugyanaz, mint egy egyszerű felhasználónév.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Az Az.AnalysisServices modul használata

Amikor egy egyszerű szolgáltatás az [Az.AnalysisServices](/powershell/module/az.analysisservices) modul erőforrás-kezelési műveletek, használja `Connect-AzAccount` a parancsmag. 

A következő példában az appID és a jelszó segítségével vezérlősík-műveleteket hajthat végre az írásvédett replikák szinkronizálása és a fel-fel/kibővített skálázás érdekében:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Az SQLServer modul használata

A következő példában az alkalmazásazonosító és a jelszó a modelladatbázis-frissítési művelet végrehajtására szolgál:

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

Ügyfélalkalmazásokkal és webalkalmazásokkal való csatlakozáskor az [AMO és az ADOMD ügyfélkönyvtárak](analysis-services-data-providers.md) 15.0.2-es és magasabb szintű telepíthető `app:AppID` csomagjai `cert:thumbprint`a NuGet támogatási szolgáltatásának rendszerénél a következő szintaxissal: és jelszó vagy . 

A következő `appID` példában, `password` és a modelladatbázis-frissítési művelet végrehajtására szolgál:

```csharp
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
[Bejelentkezés az Azure PowerShell használatával](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Egyszerű szolgáltatás hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)   
