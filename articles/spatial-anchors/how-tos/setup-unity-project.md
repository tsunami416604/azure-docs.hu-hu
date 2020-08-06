---
title: Azure térbeli horgonyok telepítése Unity számára
description: Unity-projekt konfigurálása az Azure térbeli horgonyok használatára
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812312"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Azure térbeli horgonyok konfigurálása Unity-projektben

## <a name="requirements"></a>Követelmények

Az Azure térbeli horgonyok jelenleg a 2019,4 (LTS) Unity-t támogatják a következő konfigurációkkal.

* Az 2019,4-os egység az AR Foundation 3,1-mel támogatott az Azure térbeli 2.4.0 +-ben.

## <a name="configuring-a-project"></a>Projekt konfigurálása

Az egységhez készült Azure térbeli horgonyok jelenleg egy Unity Asset csomaggal ( `.unitypackage` ) oszlanak el, amely a GitHub- [kiadásokban](https://github.com/Azure/azure-spatial-anchors-samples/releases)található.

### <a name="import-the-asset-package"></a>Az adategység importálása

1. Töltse le a `AzureSpatialAnchors.unitypackage` [GitHub-kiadásokból](https://github.com/Azure/azure-spatial-anchors-samples/releases)célként használni kívánt verzióhoz tartozó fájlt.
2. Kövesse az [itt](https://docs.unity3d.com/Manual/AssetPackagesImport.html) található utasításokat az Unity Asset csomag projektbe történő importálásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: horgonyok létrehozása és megkeresése az egységben](./create-locate-anchors-unity.md)
