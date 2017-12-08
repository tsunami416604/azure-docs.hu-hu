---
title: "Az Azure parancssori felület használata Windows rendszeren |} Microsoft Docs"
description: "A Windows az Azure parancssori felület használatával"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
ms.openlocfilehash: 0ac4aa10db43fb84119ab97cf78b2d5592cfd277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cli-on-windows"></a>A Windows az Azure parancssori felület használatával

Az Azure parancssori felület (CLI) biztosít egy parancssor, és a parancsfájl-kezelési környezet létrehozására és Azure-erőforrások kezelésére. Az Azure parancssori felület macOS, a Linux és a Windows operációs rendszereken érhető el. Ezek az operációs rendszerek között a parancssori felület parancsai azonosak, azonban az operációs rendszer adott parancsfájlkezelő szintaxis eltérőek lehetnek.

Ez a dokumentum részletesen, hogy az Azure parancssori felület telepítve-e, és hogy az egyes Windows és a részletek szintaktikai szempontok futtatnak. A részletes Azure CLI dokumentációját lásd [Azure CLI dokumentáció]( https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="windows-subsystem-for-linux"></a>Linux rendszerhez készült Windows alrendszer

A Windows alrendszer Linux (WSL) Ubuntu Linux környezetet biztosít a Windows 10 évforduló és a későbbi kiadások. Ha engedélyezve van, WSL natív Bash élményt, létrehozásának és az Azure CLI-parancsfájlok futtatásakor, amellyel nyújt. WSL natív Bash élményt biztosít, mert az Azure parancssori felület parancsfájlok megoszthatók macOS, a Linux és a Windows között módosítás nélkül.

Ha szeretne használni az Azure parancssori felület WSL, az alábbi lépések elvégzésével.

|Tevékenység | Útmutatás |
|---|---|
| WSL engedélyezése | [Telepítse a WSL dokumentáció](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Telepítse az Azure CLI-t |[A parancssori felület telepítése WSL/Ubuntu 14.04](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Az Azure parancssori felület a Windows natív módon futtatható. Ebben a konfigurációban az Azure CLI csomag telepítve van a Windows operációs rendszer, és PowerShell parancsok futtatását. Ebben a konfigurációban az Azure parancssori felület parancsaiban és parancsfájljaiban futtatható bármely, a Windows támogatott verzióját azonban platform adott parancsfájlkezelő szintaxis kötelező. Ebből kifolyólag parancsfájlok nem feltétlenül lehet megosztani macOS, a Linux és a Windows között módosítás nélkül.

Szeretne használni az Azure CLI Windows, telepítse a csomagot ezeket az utasításokat a [a parancssori felület telepítése Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Docker kép

Docker Windows használatakor a Docker-lemezkép indíthatók el, amely tartalmazza az Azure parancssori felület. Ez a rendszerkép kijelentkezés Linux alapul, és egy natív Bash élmény tartalmaz.  Docker a Windows és az Azure CLI lemezképének macOS, a Linux és a Windows között megosztani kívánt parancsfájlok használata. 

A Docker a Windows Azure CLI használata, ellenőrizze, hogy fut-e a Windows Docker, és futtassa a következő parancsot.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Ezt követően a Bash munkamenet indul el, amely előre feltölti az Azure CLI-eszközökkel.

## <a name="next-steps"></a>Következő lépések

[Az Azure virtuális gépek CLI minta](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Az Azure Web Apps CLI-minták](../../app-service/app-service-cli-samples.md)

[Az Azure SQL CLI minták](../../sql-database/sql-database-cli-samples.md)
