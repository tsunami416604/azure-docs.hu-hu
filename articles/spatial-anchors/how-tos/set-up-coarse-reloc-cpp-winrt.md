---
title: Durva újralokalizáció C++/WinRT-ben
description: Részletes magyarázat a horgonyok létrehozásáról és megkereséséről a C+++/WinRT durva újraáthelyezésével.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6d2bb4c563bd7bae186ce9832ff0472f17a8afd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76548288"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-cwinrt"></a>Horgonyok létrehozása és megkeresése durva újraáthelyezéssel C++/WinRT nyelven

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Az Azure Spatial Anchors társíthatja az eszközön, pozicionálásérzékelő adatok at a létrehozott horgonyok. Ezek az adatok arra is használhatók, hogy gyorsan megállapíthassák, vannak-e horgonyok az eszköz közelében. További információ: [Durva áthelyezés](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató kitöltéséhez győződjön meg arról, hogy:

- Alapvető ismeretek a C++ és a <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows futásidejű API-kon.</a>
- Olvassa el az [Azure Spatial Anchors áttekintését.](../overview.md)
- Az 5 [perces rövid összefoglalók egyike befejeződött.](../index.yml)
- Olvassa el a [Létrehozás és a horgonyok útmutatójának megkeresését.](../create-locate-anchors-overview.md)

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]