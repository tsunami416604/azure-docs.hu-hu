---
title: Közzététel WebApplicationVM |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy webalkalmazást egy virtuális gépet. Ez a szkript létrehoz az Azure-előfizetésben a szükséges erőforrásokkal, ha azok nem léteznek.
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: c2dc6057eeb4eba1306309785e13192674bc43c6
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054910"
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Közzététel-WebApplicationVM (Windows PowerShell-parancsfájl)
Üzembe helyez egy webalkalmazást egy virtuális géphez. A parancsfájl a szükséges erőforrásokat az Azure-előfizetésben hoz létre, ha azok nem léteznek.

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
Az üzembe helyezés részleteit leíró JSON-konfigurációs fájl elérési útja.

| Aliasok | nincs |
| --- | --- |
| Kötelező? |true |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="subscriptionname"></a>Előfizetés neve
Az Azure-előfizetést, amelyben szeretné létrehozni a virtuális gép neve.

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |Az első előfizetést használja az előfizetés-fájl |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
A webes telepítési Web csomag közzététele a virtuális gép elérési útja. Ezt a csomagot a Visual Studio Publish Web varázsló használatával hozhat létre. Lásd: [Útmutató: webes telepítési csomag létrehozása a Visual Studióban](https://msdn.microsoft.com/library/dd465323.aspx).

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
Ha az értéke igaz, nem egy megbízható legfelső szintű hitelesítésszolgáltató által aláírt tanúsítványokat használatának engedélyezése.

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |false |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="vmpassword"></a>VMPassword
A virtuális gép fiók hitelesítő adatait. Példa: - VMPassword @{név = "admin"; Jelszó = "password"}

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Az SQL database az Azure-beli hitelesítő adatait. Példa: - DatabaseServerPassword @{név = "admin"; Jelszó = "password"}

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Amennyiben az értéke igaz, nyomtatás üzenetek a parancsfájlból a kimeneti adatfolyamba.

| Aliasok | nincs |
| --- | --- |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |false |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="remarks"></a>Megjegyzések
Bemutatja, hogyan hozhat létre a parancsfájl használatával teljes ismertetése: fejlesztési és tesztelési környezetek [Windows PowerShell parancsfájlok használata a fejlesztési és tesztelési környezetek a közzététel](vs-azure-tools-publishing-using-powershell-scripts.md).

A JSON konfigurációs fájl meghatározza a részleteit, mi az a telepítésre. A projekt, például a nevét, a affinitáscsoport, a VHD-rendszerképet és a méretet a virtuális gép létrehozásakor megadott információkat tartalmazza. Tartalmazza a végpontok a virtuális gépen, az adatbázisok kiépítéséhez, ha van ilyen, és webes üzembe helyezéshez megadott paraméterek. A következő kód bemutatja egy példa JSON-konfigurációs fájl:

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

Szerkesztheti a JSON konfigurációs fájl módosítása, hogy mi van kiépítve. Szükség egy virtuális gép és a egy felhőalapú szolgáltatás, de az adatbázis a szakasz nem kötelező.

