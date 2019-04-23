---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012122"
---
Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Az a **Platform** szakaszban jelölje be **Android**. Módosítása a **hozhat létre a rendszer** való **Gradle** , és válassza ki **exportálása projekt**.

Válassza ki **kapcsoló Platform** módosítása a platform **Android**. Unity kérhetik androidu összetevők telepítéséhez, ha hiányzik.

![Unity-beállítások létrehozása ablak](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zárja be a **Build Settings** ablak.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Töltse le és importálja a ARCore SDK for Unity

Töltse le a `unitypackage` fájlt a [ARCore SDK 1.7-es Unity-kiadások](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Vissza a Unity project válassza **eszközök** > **csomag importálása** > **egyéni csomag** majd válassza ki a `unitypackage` fájl korábban letöltött. Az a **Unity-csomag importálása** párbeszédpanelen győződjön meg arról, hogy minden fájl ki van jelölve, és adja meg **importálás**.
