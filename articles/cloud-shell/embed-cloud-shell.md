---
title: Az Azure Cloud Shell beágyazása |} A Microsoft Docs
description: Ismerkedjen meg az Azure Cloud Shell beágyazása.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995977"
---
# <a name="embed-azure-cloud-shell"></a>Az Azure Cloud Shell beágyazása

A Cloud Shell beágyazása lehetővé teszi a fejlesztők és a tartalom írók közvetlenül egy dedikált URL-címet, nyissa meg a Cloud Shell [shell.azure.com](https://shell.azure.com). Ekkor azonnal a Cloud Shell-hitelesítés, eszközök, minden előnyét, és naprakész az Azure parancssori felület vagy az Azure PowerShell-eszközök a felhasználók számára.

Normál méretű gomb

[![Rendszeres indítási](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell indítása")](https://shell.azure.com)

Nagy méretű gomb

[![Nagy indítási](https://shell.azure.com/images/launchcloudshell@2x.png "Azure Cloud Shell indítása")](https://shell.azure.com)

## <a name="how-to"></a>Használati útmutató

A Cloud Shell indítása gombra integrálása markdown-fájlok másolása a következő:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Egy előugró ablak nem éri el a Cloud Shell beágyazása HTML:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Élmény testreszabása

Állítsa be egy adott rendszerhéj-felület az URL-cím való bővítésével.

|Felhasználói élményt   |URL-cím   |
|---|---|
|Legutóbb használt rendszerhéj   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>További lépések
[A rövid útmutatóban a Cloud Shell bash](quickstart.md)<br>
[A PowerShell Cloud Shell a rövid útmutatóban](quickstart-powershell.md)
