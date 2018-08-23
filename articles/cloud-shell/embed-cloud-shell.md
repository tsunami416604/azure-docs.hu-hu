---
title: Az Azure Cloud Shell beágyazása |} A Microsoft Docs
description: Ismerkedjen meg az Azure Cloud Shell beágyazása.
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
ms.openlocfilehash: 0bd5382e5ea37f7c3c52d119e9d39fe7e0bfdc7c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060593"
---
# <a name="embed-azure-cloud-shell"></a>Az Azure Cloud Shell beágyazása

A Cloud Shell beágyazása lehetővé teszi a fejlesztők és a tartalom írók közvetlenül egy dedikált URL-címet, nyissa meg a Cloud Shell [shell.azure.com](https://shell.azure.com). Ekkor azonnal a Cloud Shell-hitelesítés, eszközök, minden előnyét, és naprakész az Azure parancssori felület vagy az Azure PowerShell-eszközök a felhasználók számára.

Normál méretű gomb

[![](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Nagy méretű gomb

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

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
|Bash                       |[Shell.Azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[Shell.Azure.com/PowerShell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>További lépések
[A rövid útmutatóban a Cloud Shell bash](quickstart.md)<br>
[A PowerShell Cloud Shell a rövid útmutatóban](quickstart-powershell.md)
