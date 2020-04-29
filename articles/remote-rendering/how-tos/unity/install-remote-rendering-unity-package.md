---
title: A Unity Remote Rendering-csomagjának telepítése
description: Elmagyarázza, hogyan telepítheti a távoli renderelési ügyféloldali DLL-eket az egységhez
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681180"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>A Unity Remote Rendering-csomagjának telepítése

Az Azure távoli renderelés egy Unity-csomagot használ az integráció egységbe való beágyazásához.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>A távoli renderelési csomagok kezelése az egységben

Az Unity-csomagok olyan tárolók, amelyek az Unity [csomagkezelő](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)használatával kezelhetők.
Ez a csomag tartalmazza a teljes C# API-t, valamint az Azure Remote rendering és az Unity használatával szükséges összes beépülő modult.
A csomagok esetében az egység elnevezési sémája a következő: **com. microsoft. Azure. Remote-rendering**.

A csomag nem része az [ARR Samples adattárnak](https://github.com/Azure/azure-remote-rendering), és nem érhető el az egység belső csomagjának beállításjegyzékében. Ha hozzá szeretne adni egy projekthez, manuálisan kell szerkesztenie a projekt `manifest.md` fájlját a következők hozzáadásához:
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
A hozzáadása után a Unity csomagkezelő segítségével ellenőrizheti, hogy a legújabb verziót használja-e.
Részletesebb útmutatást az [oktatóanyagban talál: Unity-projekt létrehozása a semmiből](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unity renderelési folyamatok

A távoli renderelés az **univerzális renderelési folyamattal** és a **szabványos renderelési folyamattal**is működik. A teljesítménnyel kapcsolatos okokból az univerzális renderelési folyamat ajánlott.

Az **univerzális renderelési folyamat**használatához a csomagot az Unity-ben kell telepíteni. Ezt az egység **csomagkezelő** felhasználói felületén (a csomag neve **univerzális RP**, 7.2.1 vagy újabb verzió) vagy a `Packages/manifest.json` fájlon keresztül teheti meg, az [Unity Project Setup oktatóanyagban](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)leírtak szerint.

## <a name="next-steps"></a>További lépések

* [Unity game Objects és Components](objects-components.md)
* [Oktatóanyag: Unity-projekt létrehozása a semmiből](../../tutorials/unity/project-setup.md)
