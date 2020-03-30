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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945150"
---
## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha a fájlokat ZIP-fájlban töltötte le, először bontsa ki a fájlokat. Ha például letöltött egy ZIP-fájlt a GitHubról, nem telepítheti a fájlt úgy, ahogy van. A GitHub további beágyazott könyvtárakat ad hozzá, amelyek nem működnek az App Service szolgáltatással. 
>

A helyi terminálablakban keresse meg az alkalmazásprojekt gyökérkönyvtárát. 

Ennek a könyvtárnak tartalmaznia kell a webes alkalmazás belépési fájlját, például _az index.html_, _az index.php_és _az app.js._ Azt is tartalmazhat csomagkezelési fájlokat, mint _a project.json_, _composer.json_, _package.json_, _bower.json_, és _requirements.txt_.

Hacsak nem szeretné, hogy az App Service futtassa a `npm` `bower`központi `gulp` `composer`telepítés automatizálását, futtassa az összes buildfeladatot (például , , , és `pip`), és győződjön meg arról, hogy rendelkezik az alkalmazás futtatásához szükséges összes fájlval. Ez a lépés akkor szükséges, ha közvetlenül szeretné [futtatni a csomagot.](../articles/app-service/deploy-run-package.md)

Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

