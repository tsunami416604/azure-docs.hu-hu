---
title: Durva újrahonosítás az egységben
description: Részletesen ismertetjük, hogyan lehet a C#-ban durva újrahonosítást használó horgonyokat létrehozni és megkeresni.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 42a237caf03d4d3fb6dd9aa70668cbb84ff40072
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008624"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-c"></a>A durva újrahonosítást használó horgonyok létrehozása és megkeresése C-ben #

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Az Azure térbeli horgonyok a létrehozott horgonyokkal társíthatók az eszközön, a helymeghatározási érzékelőkkel. Ezzel az adattal gyorsan megállapítható, hogy vannak-e az eszköz közelében horgonyok. További információ: [durva újrahonosítás](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik a következővel:

- A C# alapszintű ismerete.
- Olvassa el az [Azure térbeli horgonyok áttekintése című témakört](../overview.md).
- Az [5 perces rövid](../index.yml)útmutatók egyikét fejezte be.
- Olvassa el a [create (létrehozás), majd a horgonyok megkeresése című témakört](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```csharp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
sensorProvider.Sensors.GeoLocationEnabled = true;

// Allow WiFi scanning
sensorProvider.Sensors.WifiEnabled = true;

// Allow a set of known BLE beacons
sensorProvider.Sensors.BluetoothEnabled = true;
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```csharp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters = 5;
nearDeviceCriteria.MaxResultCount = 25;

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]