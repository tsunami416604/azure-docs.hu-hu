---
title: Durva áthelyezés a C célkitűzésben
description: Részletes magyarázat a horgonyok létrehozásáról és megtalálásáról durva áthelyezéssel a C célkitűzésben.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 251f0d8609921bd1d0222d9e30c537ecbb2a04bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76548271"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-objective-c"></a>Hogyan hozzunk létre és keresse horgonyok segítségével durva relocalization a C célkitűzés

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

- A C célkitűzés alapismerete
- Olvassa el az [Azure Spatial Anchors áttekintését.](../overview.md)
- Az 5 [perces rövid összefoglalók egyike befejeződött.](../index.yml)
- Olvassa el a [Létrehozás és a horgonyok útmutatójának megkeresését.](../create-locate-anchors-overview.md)

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Allow GPS
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;

// Allow WiFi scanning
sensors.wifiEnabled = true;

// Populate the set of known BLE beacons' UUIDs
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

// Allow a set of known BLE beacons
sensors.bluetoothEnabled = true;
sensors.knownBeaconProximityUuids = uuids;
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```objc
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;

// Configure the near-device criteria
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];
nearDeviceCriteria.distanceInMeters = 5.0f;
nearDeviceCriteria.maxResultCount = 25;

// Set the session's locate criteria
ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]