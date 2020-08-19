---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605967"
---
<!--Create a media services asset CLI-->

A következő Azure CLI-parancs egy új Media Services objektumot hoz létre. Cserélje le az értékeket `assetName` `amsAccountName` és `resourceGroup` azokat az értékeket, amelyekkel jelenleg dolgozik.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
