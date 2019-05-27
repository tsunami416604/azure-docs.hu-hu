---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914287"
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