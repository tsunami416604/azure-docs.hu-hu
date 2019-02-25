---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752212"
---
Nyissa meg a Unity, és nyissa meg a projektet, a `Unity` mappát.

Nyissa meg **Build Settings** kiválasztásával **fájl** -> **Build Settings**.

Az a **Platform** szakaszban jelölje be **Android**. Majd módosíthatja a **hozhat létre a rendszer** való **Gradle** , és ellenőrizze a **exportálása projekt** lehetőséget.

Válassza ki **kapcsoló Platform** módosítása a platform **Android**. Unity megkérheti, hogy androidu összetevők telepítéséhez, ha hiányzik.

![Unity-létrehozási beállítások](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zárja be a **Build Settings** ablak.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Töltse le és importálja a ARCore SDK for Unity

Töltse le a `unitypackage` fájlt a [ARCore SDK for Unity-kiadások](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Vissza a Unity project válassza **eszközök** -> **csomag importálása** -> **egyéni csomag...**  majd válassza ki a `unitypackage` korábban letöltött fájl. Az a **Unity-csomag importálása** párbeszédpanelen győződjön meg arról, hogy az összes fájl ki van jelölve, és adja meg **importálás**.
