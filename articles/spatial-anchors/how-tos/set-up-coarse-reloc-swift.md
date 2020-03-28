---
title: Durva újratelepülés Swift
description: Részletes magyarázat arról, hogyan lehet horgonyokat létrehozni és megtalálni a Swift durva újralokalizációjával.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7b568c43f8420b5a2f994b4cba145bf4d70b9010
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76546163"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-swift"></a>Hogyan hozzunk létre és keresse horgonyok segítségével durva relocalization a Swift

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Célkitűzés-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Az Azure Spatial Anchors társíthatja az eszközön, pozicionálásérzékelő adatok at a létrehozott horgonyok. Ezek az adatok arra is használhatók, hogy gyorsan megállapíthassák, vannak-e horgonyok az eszköz közelében. További információ: [Durva áthelyezés](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Előfeltételek

Az útmutató kitöltéséhez győződjön meg arról, hogy:

- Alapvető ismeretek swift.
- Olvassa el az [Azure Spatial Anchors áttekintését.](../overview.md)
- Az 5 [perces rövid összefoglalók egyike befejeződött.](../index.yml)
- Olvassa el a [Létrehozás és a horgonyok útmutatójának megkeresését.](../create-locate-anchors-overview.md)

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Allow GPS
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true

// Allow WiFi scanning
sensors.wifiEnabled = true

// Populate the set of known BLE beacons' UUIDs
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

// Allow the set of known BLE beacons
sensors.bluetoothEnabled = true
sensors.knownBeaconProximityUuids = uuids
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```swift
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider

// Configure the near-device criteria
let nearDeviceCriteria = ASANearDeviceCriteria()!
nearDeviceCriteria.distanceInMeters = 5.0
nearDeviceCriteria.maxResultCount = 25

// Set the session's locate criteria
let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]