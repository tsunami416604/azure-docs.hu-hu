---
title: Azure-felhőbe rendszerhéj beágyazása |} Microsoft Docs
description: Ismerkedjen meg az Azure felhőalapú rendszerhéj beágyazása.
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
ms.locfileid: "27864884"
---
# <a name="embed-azure-cloud-shell"></a>Azure-felhőbe rendszerhéj beágyazása

Felhő rendszerhéj beágyazás lehetővé teszi a fejlesztők és a tartalom írók közvetlenül egy dedikált URL-címet, a felhő rendszerhéj megnyitása [shell.azure.com](https://shell.azure.com). Ekkor azonnal felhő rendszerhéj hitelesítési tooling eszköz, a teljes power, és naprakész az Azure CLI-t vagy az Azure PowerShell eszközöket a felhasználók számára.

Normál méretű gomb

[![](https://shell.azure.com/images/launchcloudshell.png "Indítsa el az Azure-felhőbe rendszerhéj")](https://shell.azure.com)

Nagy méretű gomb

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Indítsa el az Azure-felhőbe rendszerhéj")](https://shell.azure.com)

## <a name="how-to"></a>Használati útmutató

Felhő rendszerhéj indítási gomb integrálja markdown-fájlokat másolja a következő:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

A HTML-beágyazás egy előugró ablak felhő rendszerhéj nem éri el:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Testreszabásához

Egy adott rendszerhéj élmény bővítheti az URL-cím megadásához.
|Felhasználói élmény   |URL-cím   |
|---|---|
|Legutóbb használt rendszerhéj   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>További lépések
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md)<br>
[PowerShell használatával a felhő rendszerhéj gyors üzembe helyezés](quickstart-powershell.md)
