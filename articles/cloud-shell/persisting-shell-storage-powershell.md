---
title: "Azure Cloud rendszerhéj (előzetes verzió) fájlok maradnak |} Microsoft Docs"
description: "A forgatókönyv a hogyan Azure Cloud rendszerhéj fájlokat továbbra is fennáll."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 933af3860bfe087a0b4db7eff53d4b978a1475da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-works"></a>Felhő rendszerhéj működése
Felhő rendszerhéj továbbra is fennáll a fájlok a következő metóduson keresztül: 
* A megadott fájlmegosztással csatlakoztatása `clouddrive` a a `$Home` könyvtárhoz, a fájlmegosztás közvetlen interakció.

## <a name="list-cloud-drive-file-shares"></a>Lista felhőalapú Meghajtójukra fájlmegosztások
A `Get-CloudDrive` parancs a felhő rendszerhéj a felhő meghajtó által jelenleg csatlakoztatott fájl megosztás adatainak beolvasása. <br>
![Get-CloudDrive fut](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Felhőalapú Meghajtójukra leválasztása
Egy fájlmegosztást, hogy bármikor a felhő rendszerhéj csatlakoztatott is leválasztása. Ha a fájlmegosztás el lett távolítva, kérni fogja hozhat létre, és a következő munkamenet egy új fájlmegosztást csatlakoztatni.

A `Dismount-CloudDrive` parancs fájlmegosztás leválasztja az aktuális tárfiókból. Az aktuális munkamenet leválasztása a felhőalapú Meghajtójukra leáll. A felhasználó kéri hozhat létre, és az Új fájlmegosztás csatlakoztatása a következő munkamenet során.
![Dismount-CloudDrive fut](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Következő lépések
[Rövid útmutató a PowerShellhez](quickstart-powershell.md) <br>
[További tudnivalók az Azure File storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>