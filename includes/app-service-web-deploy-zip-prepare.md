---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945150"
---
## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlba, először bontsa ki a fájlokat. Ha például letöltött egy ZIP-fájlt a GitHubról, akkor nem telepítheti a fájlt. A GitHub további beágyazott könyvtárakat is felvehet, amelyek nem működnek App Service. 
>

A helyi terminál ablakban navigáljon az alkalmazás-projekt gyökérkönyvtárához. 

Ennek a könyvtárnak tartalmaznia kell a webalkalmazáshoz tartozó bejegyzést, például az _index. html_, az _index. php_és az _app. js_fájlt. Olyan csomagkezelő fájlokat is tartalmazhat, mint például a _Project. JSON_, a _Composer. JSON_, a _Package. JSON_, a _Bower. JSON_és a _követelmények. txt_.

Hacsak nem szeretné, hogy App Service az üzembe helyezési automatizálás futtatására, futtassa az összes felépítési feladatot (például `npm`, `bower`, `gulp`, `composer`és `pip`), és győződjön meg arról, hogy rendelkezik az alkalmazás futtatásához szükséges összes fájllal. Erre a lépésre akkor van szükség, ha [közvetlenül szeretné futtatni a csomagot](../articles/app-service/deploy-run-package.md).

Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

