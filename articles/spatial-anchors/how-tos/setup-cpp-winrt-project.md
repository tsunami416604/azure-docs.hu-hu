---
title: Azure térbeli horgonyok telepítése C++/WinRT HoloLens-alkalmazáshoz
description: C++/WinRT HoloLens-projekt konfigurálása az Azure térbeli horgonyok használatára
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 710f023a64f66e486bb2db9c08807af3431d0016
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812321"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Azure térbeli horgonyok konfigurálása C++/WinRT HoloLens projektben

## <a name="requirements"></a>Követelmények

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/) telepítve van a **univerzális Windows-platform fejlesztési** számítási feladattal és a **Windows 10 SDK-val (10.0.18362.0 vagy újabb)** .

## <a name="configuring-a-project"></a>Projekt konfigurálása

A HoloLens és C++/WinRT Azure térbeli horgonyai a [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet csomag használatával terjeszthetők.

Kövesse az [itt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio) található utasításokat a Visual Studio NuGet Package Manager használatával a [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet csomag telepítéséhez a projektben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: horgonyok létrehozása és megkeresése a C++/WinRT](./create-locate-anchors-cpp-winrt.md)
