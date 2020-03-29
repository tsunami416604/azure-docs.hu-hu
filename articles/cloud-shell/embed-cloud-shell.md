---
title: Az Azure Cloud Shell beágyazása | Microsoft dokumentumok
description: Ismerje meg az Azure Cloud Shell beágyazását.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60614310"
---
# <a name="embed-azure-cloud-shell"></a>Az Azure Cloud Shell beágyazása

A Cloud Shell beágyazása lehetővé teszi a fejlesztők és a tartalomírók számára, hogy közvetlenül megnyissák a Cloud Shellt egy dedikált URL-címről, [shell.azure.com.](https://shell.azure.com) Ez azonnal a Cloud Shell hitelesítési, eszköz- és naprakész Azure CLI/Azure PowerShell-eszközeinek teljes erejét hozza a felhasználók számára.

Normál méretű gomb

[![Rendszeres indítás](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Nagy méretű gomb

[![Nagy indítás](https://shell.azure.com/images/launchcloudshell@2x.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

## <a name="how-to"></a>Használati útmutató

Integrálja a Cloud Shell indítógombját a markdown fájlokba a következők másolásával:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

A POP-Up Cloud Shell beágyazására irányuló HTML az alábbi:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>A felhasználói élmény testreszabása

Állítson be egy adott rendszerhéj-élményt az URL-cím bővítésével.

|Élmény   |URL-cím   |
|---|---|
|Legutóbb használt héj   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>További lépések
[Bash a Cloud Shell rövid útmutató](quickstart.md)<br>
[PowerShell a Cloud Shell rövid útmutatójában](quickstart-powershell.md)
