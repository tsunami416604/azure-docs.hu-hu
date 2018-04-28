---
title: Közzététel WebApplicationVM |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti egy webalkalmazást egy virtuális géphez. Ezt a parancsfájlt a szükséges erőforrásokat az Azure-előfizetése hoz létre, ha azok még nem léteznek.
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 3fdd11387096d95359fb5f578ca64720f2182c45
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>(A Windows PowerShell-parancsfájl) közzététele-WebApplicationVM
A webalkalmazások egy virtuális gépet telepít. A parancsfájl a szükséges erőforrásokat az Azure-előfizetése hoz létre, ha azok még nem léteznek.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Konfiguráció
A JSON-konfigurációs fájlt, amely leírja a központi telepítés részleteinek elérési útja.

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |true |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="subscriptionname"></a>Előfizetés neve
Kívánja a virtuális gép létrehozása Azure-előfizetés neve.

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |Az előfizetés fájlban az első előfizetést használ |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
A webes telepítési csomag közzétételére a virtuális gép elérési útja. Ezt a csomagot a Visual Studio webhely közzététele varázsló használatával hozhat létre. Lásd: [Útmutató: webes telepítési csomag létrehozása a Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
Amennyiben az értéke igaz, nem egy megbízható legfelső szintű hitelesítésszolgáltató által aláírt tanúsítványok használatának engedélyezése.

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |false |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="vmpassword"></a>VMPassword
A virtuális gép fiók hitelesítő adatait. Példa: - VMPassword @{név = "rendszergazda"; Jelszó = a "password"}

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
A hitelesítő adatokat az Azure SQL-adatbázis. Példa: - DatabaseServerPassword @{név = "rendszergazda"; Jelszó = a "password"}

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Amennyiben az értéke igaz, a nyomtató érkező üzenetek a parancsfájl a kimeneti adatfolyamba.

| Aliasnevek | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |false |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="remarks"></a>Megjegyzések
A parancsfájl használata létrehozásához teljes leírását fejlesztési és tesztkörnyezetek, lásd: [Windows PowerShell parancsfájlok használata a közzététel a fejlesztési és tesztkörnyezetek](vs-azure-tools-publishing-using-powershell-scripts.md).

A JSON-konfigurációs fájl meghatározza, hogy mit telepítendő részletes adatait. A projekt, például a nevét, a affinitáscsoport, a Virtuálismerevlemez-kép és a virtuális gép mérete létrehozásakor megadott információkat tartalmazza. Azt is magában foglalja a végpontokat a virtuális gépen, az adatbázisok kiépítéséhez, ha van ilyen, webes és üzembe helyezéshez megadott paraméterek. A következő kód bemutatja egy példa JSON-konfigurációs fájlt:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Szerkesztheti a JSON-konfigurációs fájl módosítása milyen ki van építve. Szükség egy virtuális gép és egy felhőalapú szolgáltatás, de az adatbázis szakaszban nem kötelező megadni.

