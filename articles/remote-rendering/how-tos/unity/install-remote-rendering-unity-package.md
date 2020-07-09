---
title: A Unity Remote Rendering-csomagjának telepítése
description: Elmagyarázza, hogyan telepítheti a távoli renderelési ügyféloldali DLL-eket az egységhez
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564311"
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
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

A hozzáadása után a Unity csomagkezelő segítségével ellenőrizheti, hogy a legújabb verziót használja-e.
Részletesebb útmutatást az [oktatóanyagban talál: távoli modellek megtekintése](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Unity renderelési folyamatok

A távoli renderelés a és a együttesével is működik **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** . A teljesítménnyel kapcsolatos okokból az univerzális renderelési folyamat ajánlott.

A használatához a **:::no-loc text="Universal render pipeline":::** csomagját az Unity-ben kell telepíteni. Ezt az egység **csomagkezelő** felhasználói felületén (a csomag neve **univerzális RP**, a 7.3.1-es vagy újabb verzióban) vagy a fájlon keresztül teheti meg, az `Packages/manifest.json` [Unity Project Setup oktatóanyagban](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)leírtak szerint.

## <a name="next-steps"></a>További lépések

* [Unity game Objects és Components](objects-components.md)
* [Oktatóanyag: távoli modellek megtekintése](../../tutorials/unity/view-remote-models/view-remote-models.md)
