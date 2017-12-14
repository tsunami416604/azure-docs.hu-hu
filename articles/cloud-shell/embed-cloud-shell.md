---
title: "Azure-felhőbe rendszerhéj beágyazása |} Microsoft Docs"
description: "Ismerkedjen meg az Azure felhőalapú rendszerhéj beágyazása."
services: cloud-shell
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 7c06a51e7f9f402b2ec10e440ca98125a7f2a7cf
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="embed-azure-cloud-shell"></a>Azure-felhőbe rendszerhéj beágyazása

Felhő rendszerhéj beágyazás lehetővé teszi a fejlesztők és a tartalom írók közvetlenül egy dedikált URL-címet, a felhő rendszerhéj megnyitása [shell.azure.com](https://shell.azure.com). Ez feloldja a teljes power felhő rendszerhéj hitelesítési, tooling és naprakész Azure CLI-t vagy az Azure PowerShell-eszközök.

## <a name="how-to"></a>Használati útmutató

Felhő rendszerhéj indítási gomb integrálja markdown-fájlokat másolja a következő:

```markdown
[![Launch Cloud Shell](https:shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

A HTML-beágyazás egy előugró ablak felhő rendszerhéj nem éri el:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="next-steps"></a>Következő lépések
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md)<br>
[PowerShell használatával a felhő rendszerhéj gyors üzembe helyezés](quickstart-powershell.md)