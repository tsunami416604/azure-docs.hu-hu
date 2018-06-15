---
title: A Node.js verzió megadása
description: 'Útmutató: Azure-webhelyek és Felhőszolgáltatások által használt Node.js verziójának megadása'
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864300"
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>A Node.js verzió megadása az Azure alkalmazásban
Amikor egy Node.js-alkalmazást üzemeltet, érdemes lehet győződjön meg arról, hogy az alkalmazás a Node.js meghatározott verzióját használja. Ehhez az Azure-platformon futó alkalmazások számos módja van.

## <a name="default-versions"></a>Alapértelmezett verziók
Az Azure által biztosított Node.js verziók folyamatosan frissülnek. Hacsak másként nincs megadva, a megadott alapértelmezett verzió a `WEBSITE_NODE_DEFAULT_VERSION` környezeti változó fogja használni. Bírálja felül az alapértelmezett érték, kövesse a cikk a következő szakaszokban

> [!NOTE]
> Ha az alkalmazást egy Azure-Felhőszolgáltatásban (webes vagy feldolgozói szerepkör) üzemeltet, és először az alkalmazást telepített, Azure megpróbálja telepítette a fejlesztési környezet egyik t egyező Node.js ugyanazon verzióját használja He alapértelmezett verzió érhető el az Azure-on.
>
>

## <a name="versioning-with-packagejson"></a>A package.json Versioning
Megadhatja, hogy a Node.js adja hozzá az alábbiakat a által használt verzióját a **package.json** fájlt:

    "engines":{"node":version}

Ha *verzió* használandó adott verziószáma. Például megadhatja verziójához, összetettebb feltételek:

    "engines":{"node": "0.6.22 || 0.8.x"}

Mivel 0.6.22 nem érhető el az üzemeltetési környezetben verziók egyike, a rendelkezésre álló sorozat lesz 0,8 legmagasabb verzióval használja helyette - 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Az alkalmazásbeállítások Versioning webhelyek
Ha egy webhelyen lévő alkalmazás üzemeltet, beállíthatja a környezeti változó **WEBSITE_NODE_DEFAULT_VERSION** a kívánt verziót.

## <a name="versioning-cloud-services-with-powershell"></a>A PowerShell-lel Versioning Cloud Services csomag
Ha az alkalmazás felhőszolgáltatásban működtet, és telepíti az alkalmazást az Azure PowerShell, a beállítás felülbírálható az alapértelmezett Node.js verziót használatával a **Set-AzureServiceProjectRole** PowerShell-parancsmagot. Példa:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Vegye figyelembe, hogy a fenti nyilatkozatot paraméterei kis-és nagybetűket.  Node.js megfelelő verziója van beállítva ellenőrzésével ellenőrizheti a **motorok** tulajdonság a szerepkör **package.json**.

Használhatja a **Get-AzureServiceProjectRoleRuntime** Node.js verzió érhető el a felhőalapú szolgáltatásként üzemeltetett alkalmazások listájának beolvasása.  Mindig ellenőrizze a Node.js függ, a projekt verziója ezen a listán.

## <a name="using-a-custom-version-with-azure-websites"></a>Az Azure Websitesra egyéni verziójával
Amíg az Azure biztosít a Node.js több alapértelmezett verzióját, érdemes lehet német nyelvű verziót, amely alapértelmezés szerint nincs megadva. Ha az alkalmazás üzemel, egy Azure-webhelyre, ez elvégezhető használatával a **iisnode.yml fájlt** fájlt. Az alábbi lépéseket ismerteti a folyamatot, amely az Azure-webhely Node.Js egyéni verzióját használja:

1. Hozzon létre egy új könyvtárat, és hozzon létre egy **server.js** fájl a könyvtárban található. A **server.js** fájl a következőket kell tartalmaznia:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Ez megjeleníti a Node.js verziót használják, ha a felhasználó a webhelyen.
2. Új webhely létrehozása, és jegyezze fel a hely nevét. Például a következők használatát a [Azure parancssori eszközök] nevű új Azure-webhely létrehozása **segítségével**, majd engedélyezi a webhely egy Git-tárházat.

        azure site create mywebsite --git
3. Hozzon létre egy új könyvtárat nevű **bin** a könyvtárat tartalmazó gyermekeként a **server.js** fájlt.
4. Az adott verziójának letöltéséhez **node.exe** (a Windows-verzió), amelyet meg kíván használni az alkalmazást. Például a következők használatát **curl** 0.8.1 verziójának letöltéséhez:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Mentse a **node.exe** fájlt a **bin** korábban létrehozott mappába.
5. Hozzon létre egy **iisnode.yml fájlt** fájl ugyanabban a könyvtárban a **server.js** fájlt, és adja meg a következő tartalmat a **iisnode.yml fájlt** fájlt:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Az elérési út akkor, ha a **node.exe** fájlt a projektben található Miután közzétette az alkalmazást az Azure-webhely.
6. Az alkalmazás közzétételére. Például új webhely korábbi--git paraméterrel létrehozott, mert a következő parancsok lesz az alkalmazás-fájlok hozzáadása a helyi Git-tárház, és majd azok leküldése a webhely-tárház:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Miután közzétette az alkalmazást, a webhely megnyitása a böngészőben. Megjelenik egy üzenet "Hello Azure futó csomópont verziójáról: v0.8.1".

## <a name="next-steps"></a>Következő lépések
Most, hogy adja meg az alkalmazás által használt Node.js verziószámát, ismerje meg, hogy megismerkedett hogyan [a modulok], [és üzembe egy Node.js Web Site](app-service/app-service-web-get-started-nodejs.md), és [az Azure használatával A Mac és Linux parancssori eszközök].

További információk: [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/).

[az Azure használatával A Mac és Linux parancssori eszközök]:cli-install-nodejs.md
[Azure parancssori eszközök]:cli-install-nodejs.md
[a modulok]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
