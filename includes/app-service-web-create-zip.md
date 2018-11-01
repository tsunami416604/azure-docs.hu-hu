---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133371"
---
## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

Győződjön meg arról, hogy még a mintaprojekt gyökérkönyvtárában van. Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Később ezt a ZIP-fájlt fogja feltölteni az Azure-ba, majd üzembe helyezni az App Service-ben.