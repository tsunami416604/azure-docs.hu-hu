---
title: 'Azure AD Connect: Sql-kapcsolattal kapcsolatos problémák elhárítása | Microsoft dokumentumok'
description: A cikk az Azure AD Connect teljein előforduló SQL-kapcsolati problémák elhárítását ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cde94dce13eeb7536f72fb0dcd937265960c7314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70842696"
---
# <a name="troubleshoot-sql-connectivity-issues-with-azure-ad-connect"></a>Az Azure AD Connect SQL-kapcsolati problémáinak elhárítása
Ez a cikk bemutatja, hogyan háríthatja el az Azure AD Connect és az SQL Server közötti kapcsolódási problémákat. 

A következő képernyőképen egy tipikus hiba látható, ha az SQL Server nem található.

![SQL-hiba](./media/tshoot-connect-tshoot-sql-connectivity/sql1.png)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Powershell ablak megnyitása és az ADSyncTools Powershell modul importálása

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1" 
```

>[!NOTE]
>A telepítőmodul t [a PowerShell 5.0 (WMF 5.0)](https://www.microsoft.com/download/details.aspx?id=50395) vagy újabb verzióra kell frissíteni;  
Vagy telepítse [a PackageManagement PowerShell-modulok előzetes verzióját – 2016 márciusa a PowerShell 3.0/4.0-s verziójához](/powershell/module/PackageManagement) 

- **Az összes parancs megjelenítése:**`Get-Command -Module AdSyncTools` 
- **A powershell függvény végrehajtása:** `Connect-ADSyncDatabase` a következő paraméterekkel
    - Szerver. Az SQL Server neve.
    - Példány. (Nem kötelező) Az SQL Server-példány neve és szükség esetén portszáma, amelyet használni szeretne. Ne adja meg ezt a paramétert az alapértelmezett példány használatához.
    - Felhasználónév. (Nem kötelező) A felhasználói fiók, amelyhez csatlakozni lehet. Ha üresen hagyja, a rendszer az aktuálisan bejelentkezett felhasználót fogja használni. Ha egy távoli SQL Server-kiszolgálóhoz csatlakozik, ez legyen az Azure ADConnect SQL Connectivity hez létrehozott egyéni szolgáltatásfiók. Az Azure AD Connect az Azure AD Connect szinkronizálási szolgáltatásfiókját használja a távoli SQL-kiszolgálón való hitelesítéshez.
    - Jelszó. (Nem kötelező) A megadott felhasználónév jelszava.

Ez a powershell-függvény megkísérli a megadott SQL-kiszolgálóhoz és példányhoz való kötést a vagy az aktuális felhasználó hitelesítő adatait használó hitelesítő adatok használatával. Ha az SQL Server nem található, a parancsfájl megpróbál csatlakozni az SQL Browser szolgáltatáshoz az engedélyezett protokollok és portok meghatározásához.

Példa csak kiszolgálónév használatára:
```
PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> import-module .\AdSyncTools.psm1

PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> Connect-AdSyncDatabase -Server SQL1
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1 using a TCP binding for the default instance.
   Data Source=tcp:SQL1\;Integrated Security=True.ConnectionString
   Successfully connected.


StatisticsEnabled                : False
AccessToken                      : 
ConnectionString                 : Data Source=tcp:SQL1\;Integrated Security=True
ConnectionTimeout                : 15
Database                         : master
DataSource                       : tcp:SQL1\
PacketSize                       : 8000
ClientConnectionId               : 23e06ef2-0a38-4f5f-9291-da931de40375
ServerVersion                    : 13.00.4474
State                            : Open
WorkstationId                    : SQL1
Credential                       : 
FireInfoMessageEventOnUserErrors : False
Site                             : 
Container                        : 




PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> 
```

Példa nem létező példány- és portszám használatára:

```
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow 
remote connections. (provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
Get-ADSyncSQLBrowserInstances : Unable to read the SQL Server Browser configuration. An existing connection was forcibly closed by the remote host. 
Ensure port 1434 (UDP) is open on SQL1 and the SQL Server Browser service is running. 
At C:\Program Files\Microsoft Azure Active Directory Connect\tools\AdSyncTools.psm1:1717 char:18
+     $instances = Get-ADSyncSQLBrowserInstances $Server
+                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Get-ADSyncSQLBrowserInstances
 


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an inbound firewall rule on SQL1 to allow connection. Enable the SQL Se
rver Browser service temporarily on the SQL server and run this cmdLet again to further troubleshoot the issue. Alternatively use the SQL Server Configur
ation Manager on SQL1 to verify the instance name and TCP/IP port assignment manually. 

You must specify both the instance name and the port to connect when the SQL Server Browser service is not running. An inbound firewall rule on SQL1 is required for the associated port.
    Example: 'MySQLInstance,1234' where 1234 has a matching firewall rule.

PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> 
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1,99 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1,99;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: TCP Provider, error: 0 - The remote computer refused the network connection.)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
SQL browser response contained 2 instances.
Verifying protocol bindings and port connectivity.
MSSQLSERVER     : Enabled - port 1433 is assigned and reachable through the firewall
INSTANCE1       : Blocked - the inbound firewall rule for port 58379 is missing or disabled


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an
inbound firewall rule on SQL1 to allow connection. Review the TcpStatus field
for each instance and take corrective action.


InstanceName : MSSQLSERVER
tcp          : 1433
TcpStatus    : Enabled - port 1433 is assigned and reachable through the firewall

InstanceName : INSTANCE1
tcp          : 58379
TcpStatus    : Blocked - the inbound firewall rule for port 58379 is missing or disabled




PS C:\Program Files\Microsoft Azure Active Directory Connect\tools>  
```

## <a name="next-steps"></a>Következő lépések
- [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
-  [Azure AD-csatlakozási lehetőségek az Azure AD Connecttel](tshoot-connect-connectivity.md)
