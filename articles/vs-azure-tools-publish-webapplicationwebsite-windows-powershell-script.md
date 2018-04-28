---
title: (A Windows PowerShell-parancsfájl) közzététele-WebApplicationWebSite |} Microsoft Docs
description: Ismerje meg, hogy egy webes projekt közzététele egy Azure-webhelyen. Ezt a parancsfájlt a szükséges erőforrásokat az Azure-előfizetése hoz létre, ha azok még nem léteznek.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: b540734f17ed11e4c438e1248ed9612fb892e89a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>(A Windows PowerShell-parancsfájl) közzététele-WebApplicationWebSite
## <a name="syntax"></a>Szintaxis
A webes projekt közzététele egy Azure-webhelyen. A parancsfájl a szükséges erőforrásokat az Azure-előfizetése hoz létre, ha azok még nem léteznek.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfiguráció
A JSON-konfigurációs fájlt, amely leírja a központi telepítés részleteinek elérési útja.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasnevek |nincs |
| Kötelező? |true |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="subscriptionname"></a>Előfizetés neve
Az Azure-előfizetés, amelyet a webhely neve.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasnevek |nincs |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="webdeploypackage"></a>WebDeployPackage
A webhelyen közzétenni a webes telepítési csomag elérési útja. Ezt a csomagot a Visual Studio webhely közzététele varázsló használatával hozhat létre. További információkért lásd: [Ismerkedés az Azure felhőalapú szolgáltatásairól és ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasnevek |nincs |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
A felhasználónevet és jelszót az SQL-adatbázis, az Azure-ban.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasnevek |nincs |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |nincs |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Amennyiben az értéke igaz, a nyomtató érkező üzenetek a parancsfájl a kimeneti adatfolyamba.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasnevek |nincs |
| Kötelező? |false |
| Beosztás |nevű |
| Alapértelmezett érték |false |
| Fogadja el a feldolgozási sor beviteli? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="remarks"></a>Megjegyzések
A parancsfájl használata létrehozásához teljes leírását fejlesztési és tesztkörnyezetek, lásd: [Windows PowerShell parancsfájlok használata a közzététel a fejlesztési és tesztkörnyezetek](vs-azure-tools-publishing-using-powershell-scripts.md).

A JSON-konfigurációs fájl meghatározza, hogy mit telepítendő részletes adatait. Ez magában foglalja a projekt, például a nevét, és a felhasználónév, a webhely létrehozásakor megadott adatokat. Is az adatbázis rendelkezésre, ha van ilyen. A következő kód bemutatja egy példa JSON-konfigurációs fájlt:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Szerkesztheti a JSON-konfigurációs fájl módosítása, hogy telepítve van. A webhely szakaszban szükség, de az adatbázis szakaszban nem kötelező megadni.

## <a name="next-steps"></a>További lépések
További információkért lásd: [Publish-WebApplicationVM (a Windows PowerShell-parancsfájl)](vs-azure-tools-publish-webapplicationvm.md)

