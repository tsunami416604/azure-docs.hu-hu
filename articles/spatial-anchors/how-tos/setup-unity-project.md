---
title: Azure térbeli horgonyok telepítése Unity számára
description: Unity-projekt konfigurálása az Azure térbeli horgonyok használatára
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536355"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Azure térbeli horgonyok konfigurálása Unity-projektben

Ez az útmutató bemutatja, hogyan kezdheti meg az Azure térbeli horgonyok SDK-t az Unity-projektben.

## <a name="requirements"></a>Követelmények

Az Azure térbeli horgonyok jelenleg a 2019,4 (LTS) Unity-t támogatják a következő konfigurációkkal.

* Az 2019,4-os egység az AR Foundation 3,1-mel támogatott az Azure térbeli 2.4.0 +-ben.

## <a name="configuring-a-project"></a>Projekt konfigurálása

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Az Unity Package Manager-csomagok hozzáadása a projekthez](#tab/UPMPackage)

Az Unity Azure térbeli Horgonyait jelenleg az Unity Package Manager (UPM) csomagok használatával osztják el. Ezek a csomagok a [NPM-beállításjegyzékben](https://bintray.com/microsoft/AzureMixedReality-NPM)találhatók. Ha többet szeretne megtudni a hatókörön belüli csomag-beállításjegyzékek Unity-projektben való használatáról, tekintse meg a hivatalos [Unity dokumentációját](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Adja hozzá a beállításjegyzéket az Unity-projekthez

1. A Fájlkezelőben navigáljon a Unity Project `Packages` mappájához. Nyissa meg a Project manifest-fájlt `manifest.json` egy szövegszerkesztőben.
2. A fájl tetején, a szakasztal megegyező szinten `dependencies` adja hozzá a következő bejegyzést, hogy tartalmazza az Azure térbeli horgonyok beállításjegyzékét a projekthez. A `scopedRegistries` bejegyzés közli az egységgel, hogy hol keresi az Azure térbeli horgonyok SDK-csomagjait.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Az SDK-csomag (ok) hozzáadása az Unity-projekthez

| Platform | Csomag neve                                    |
|----------|-------------------------------------------------|
| Android  | com. microsoft. Azure. térbeli-horgonyok – SDK. Android |
| iOS      | com. microsoft. Azure. Spatial-Anchors-SDK. iOS     |
| HoloLens | com. microsoft. Azure. térbeli-Anchors-SDK. Windows |

1. Minden olyan platformhoz (Android/iOS/HoloLens), amelyet támogatni szeretne a projektben, adjon hozzá egy bejegyzést a csomag nevével és a csomag verziószámával a `dependencies` projekt jegyzékfájljának szakaszához. Erre alább látható példa.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Mentse és zárjuk be a `manifest.json` fájlt. Ha az egységbe tér vissza, az egység automatikusan felismeri a projekt jegyzékfájljának változását, és beolvassa a megadott csomagokat. A Project nézetben kibonthatja a `Packages` mappát, hogy ellenőrizze, hogy a megfelelő csomagok importálása megtörtént-e.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Csak Android esetén: a mainTemplate. gradle fájl konfigurálása

1. Lépjen a **Edit**  >  **Project Settings**  >  **Player**szerkesztése menüpontra.
2. A **Player-beállítások** **ellenőr paneljén** válassza az **Android** ikont.
3. A **build (létrehozás** ) szakaszban jelölje be az **Egyéni Gradle-sablon** jelölőnégyzetet az egyéni Gradle-sablon létrehozásához a következő helyen: `Assets\Plugins\Android\mainTemplate.gradle` .
4. Nyissa meg a `mainTemplate.gradle` fájlt egy szövegszerkesztőben. 
5. A `dependencies` szakaszban illessze be a következő függőségeket:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Ha elkészült, a `dependencies` szakasznak a következőhöz hasonlóan kell kinéznie:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Az adategység importálása](#tab/UnityAssetPackage)

> [!WARNING]
> Az Azure térbeli horgonyok SDK Unity Asset csomagjának eloszlása elavulttá válik az SDK 2.5.0-es verziójának használata után.

1. Töltse le a `AzureSpatialAnchors.unitypackage` [GitHub-kiadásokból](https://github.com/Azure/azure-spatial-anchors-samples/releases)célként használni kívánt verzióhoz tartozó fájlt. 
2. Kövesse az [itt](https://docs.unity3d.com/Manual/AssetPackagesImport.html) található utasításokat az Unity Asset csomag projektbe történő importálásához.    

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: horgonyok létrehozása és megkeresése az egységben](./create-locate-anchors-unity.md)
