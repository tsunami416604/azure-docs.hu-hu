---
title: (Windows PowerShell-parancsprogram) közzététele – WebApplicationWebSite |} A Microsoft Docs
description: Ismerje meg, hogy egy webes projekt közzététele az Azure-webhelyekre. Ez a szkript létrehoz az Azure-előfizetésben a szükséges erőforrásokkal, ha azok nem léteznek.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: ea8e36aabb75839a9c301f45a82241e3a859d42a
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055330"
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Közzététel-WebApplicationWebSite (Windows PowerShell-parancsfájl)
## <a name="syntax"></a>Szintaxis
Tesz közzé olyan webes projekt egy Azure-webhelyen. A parancsfájl a szükséges erőforrásokat az Azure-előfizetésben hoz létre, ha azok nem léteznek.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfiguráció
Az üzembe helyezés részleteit leíró JSON-konfigurációs fájl elérési útja.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasok |nincs |
| Kötelező? |true |
| Pozíció |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="subscriptionname"></a>Előfizetés neve
Az Azure-előfizetést, amelyet szeretne létrehozni a webhely neve.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasok |nincs |
| Kötelező? |false |
| Pozíció |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="webdeploypackage"></a>WebDeployPackage
A webhely közzétételéhez a webes telepítési Web csomag elérési útja. Ezt a csomagot a Visual Studio Publish Web varázsló használatával hozhat létre. További információkért lásd: [Ismerkedés az Azure Cloud Services és ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasok |nincs |
| Kötelező? |false |
| Pozíció |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
A felhasználónevet és jelszót az SQL Database az Azure-ban.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasok |nincs |
| Kötelező? |false |
| Pozíció |nevű |
| Alapértelmezett érték |nincs |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Amennyiben az értéke igaz, nyomtatás üzenetek a parancsfájlból a kimeneti adatfolyamba.

| Paraméter | Alapértelmezett érték |
| --- | --- |
| Aliasok |nincs |
| Kötelező? |false |
| Pozíció |nevű |
| Alapértelmezett érték |false |
| Adatcsatorna bemenetének elfogadása? |false |
| Helyettesítő karakterek elfogadása? |false |

## <a name="remarks"></a>Megjegyzések
Bemutatja, hogyan hozhat létre a parancsfájl használatával teljes ismertetése: fejlesztési és tesztelési környezetek [Windows PowerShell parancsfájlok használata a fejlesztési és tesztelési környezetek a közzététel](vs-azure-tools-publishing-using-powershell-scripts.md).

A JSON konfigurációs fájl meghatározza a részleteit, mi az a telepítésre. A projekt, például a nevet és egy felhasználónevet a webhely létrehozásakor megadott adatokat tartalmazza. Is az adatbázis létrehozását, ha van ilyen. A következő kód bemutatja egy példa JSON-konfigurációs fájl:

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

Szerkesztheti a JSON konfigurációs fájl módosítása, hogy mi van telepítve. Egy webhely szakasz megadása kötelező, de az adatbázis a szakasz nem kötelező.

## <a name="next-steps"></a>További lépések
További információkért lásd: [Publish-WebApplicationVM (Windows PowerShell-parancsfájl)](vs-azure-tools-publish-webapplicationvm.md)

