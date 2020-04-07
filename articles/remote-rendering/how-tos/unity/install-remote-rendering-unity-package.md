---
title: A Unity távoli renderelési csomagjának telepítése
description: A távoli renderelési ügyfél DL-ek unity telepítésének témaköre
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681180"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>A Unity távoli renderelési csomagjának telepítése

Az Azure remote rendering egy Unity csomagot használ a Unity-be való integráció beágyazásához.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>A távoli renderelési csomagok kezelése a Unity-ben

Unity csomagok konténerek, hogy lehet kezelni keresztül Unity [Package Manager](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html).
Ez a csomag tartalmazza a teljes C# API-t, valamint az Azure Remote Rendering with Unity használatához szükséges összes beépülő modul bináris fájljait.
A Unity csomagok elnevezési sémát követve a csomag neve **com.microsoft.azure.remote-rendering**.

A csomag nem része az [ARR mintatárnak,](https://github.com/Azure/azure-remote-rendering)és nem érhető el a Unity belső csomagnyilvántartásából. Ha hozzá szeretné adni egy projekthez, manuálisan `manifest.md` kell módosítania a projekt fájlját a következők hozzáadásához:
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Miután ezt hozzáadta, használhatja a Unity Csomagkezelőt, hogy megbizonyosodjon arról, hogy a legújabb verzióval rendelkezik.
Átfogóbb utasításokat ad nak a [bemutató: Beállítása Unity projekt a semmiből](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Egység renderelő folyamatok

A távoli renderelés az **Univerzális renderelési folyamattal** és a **Standard renderelési folyamattal**is működik. A teljesítmény miatt az Univerzális renderelési folyamat ajánlott.

Az **Univerzális renderelési folyamat**használatához a csomagját unity-be kell telepíteni. Ez történhet a Unity **Csomagkezelő** felhasználói felületén (a csomag neve **Universal RP**, 7.2.1-es vagy újabb verzió), vagy a `Packages/manifest.json` fájlon keresztül, aUnity projekt [beállítási oktatóanyagában](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)leírtak szerint.

## <a name="next-steps"></a>További lépések

* [Unity játék tárgyak és alkatrészek](objects-components.md)
* [Oktatóanyag: Unity-projekt beállítása a semmiből](../../tutorials/unity/project-setup.md)
