---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305356"
---
Nyissa meg a projektre a Unity, az `Unity` mappát.

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Az a **Platform** szakaszban jelölje be **Android**. Módosítása a **hozhat létre a rendszer** való **Gradle** , és válassza ki **exportálása projekt**.

Válassza ki **kapcsoló Platform** módosítása a platform **Android**. Unity kérhetik androidu összetevők telepítéséhez, ha hiányzik.

![Unity-beállítások létrehozása ablak](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zárja be a **Build Settings** ablak.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Töltse le és importálja a ARCore SDK for Unity

Töltse le a `unitypackage` fájlt a [ARCore SDK for Unity-kiadások](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Vissza a Unity project válassza **eszközök** > **csomag importálása** > **egyéni csomag** majd válassza ki a `unitypackage` fájl korábban letöltött. Az a **Unity-csomag importálása** párbeszédpanelen győződjön meg arról, hogy minden fájl ki van jelölve, és adja meg **importálás**.
