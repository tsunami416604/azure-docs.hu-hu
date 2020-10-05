---
title: Natív C++ oktatóanyag üzembe helyezése a HoloLens
description: Gyors útmutató, amely bemutatja, hogyan futtatható a natív C++ oktatóanyag a HoloLens-on
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: 71760e9b54ff3a520f0784ecda4484bb3ea047e3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88892660"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Gyors útmutató: natív C++ minta üzembe helyezése a HoloLens-ben

Ez a rövid útmutató ismerteti, hogyan telepítheti és futtathatja a natív C++ oktatóanyag-alkalmazást egy HoloLens 2 rendszeren.

Ebből a rövid útmutatóból megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
>* Készítse elő az oktatóanyag-alkalmazást a HoloLens.
>* Módosítsa az ARR hitelesítő adatokat a forráskódban.
>* A minta üzembe helyezése és futtatása az eszközön.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy hozzáférjen az Azure távoli renderelési szolgáltatáshoz, először [létre kell hoznia egy fiókot](../../../how-tos/create-an-account.md).

A következő szoftvereket kell telepíteni:

* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio-eszközök vegyes valósághoz](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* GIT [(letöltés)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Az ARR-minták tárházának klónozása

Első lépésként a git-tárházat klónozottuk, amely a nyilvános Azure-beli távoli renderelési mintákat képezi. Nyisson meg egy parancssort (írja be `cmd` a Windows Start menüjét), és váltson arra a könyvtárba, ahol az ARR-minta projektet tárolni szeretné.

Futtassa az alábbi parancsot:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Az utolsó parancs létrehoz egy alkönyvtárat az ARR könyvtárban, amely az Azure távoli renderelés különböző mintáit tartalmazza.

A C++ HoloLens oktatóanyag a *NativeCpp/HoloLens*alkönyvtárban található.

## <a name="build-the-project"></a>A projekt felépítése

Nyissa meg a *NativeCpp/HoloLens* alkönyvtárában található *HolographicApp. SLN* fájlt a Visual Studio 2019-ben.

Váltson a Build konfigurációra a *hibakereséshez* (vagy *kiadáshoz*) és a *ARM64*. Győződjön meg arról is, hogy a hibakereső üzemmód az *eszközre* van beállítva, a *távoli gép*helyett:

![Visual Studio-konfiguráció](media/vs-config-native-cpp-tutorial.png)

Mivel a fiók hitelesítő adatai az oktatóanyag hardcoded találhatók, módosítsa azokat érvényes hitelesítő adatokra. Ehhez nyissa meg a fájlt a `HolographicAppMain.cpp` Visual Studióban, és változtassa meg a felületet a következő osztály konstruktorán belül `HolographicAppMain` :

```cpp
// 2. Create front end
{
    // Users need to fill out the following with their account data and model
    RR::AzureFrontendAccountInfo init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
}
```

Pontosabban módosítsa a következő értékeket:
* `init.AccountId` és `init.AccountKey` a fiókja adatait is használhatja. Lásd: a [fiókadatok beolvasása](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* A karakterlánc régiójának része `init.AccountDomain` más régiókban, mint `westus2` például `"westeurope.mixedreality.azure.com"`
* Emellett a `m_sessionOverride` meglévő munkamenet-azonosítóra is módosítható. A munkamenetek ezen a mintán kívül is létrehozhatók, például [a PowerShell-parancsfájl](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) használatával vagy a [munkamenet-REST API](../../../how-tos/session-rest-api.md#create-a-session) közvetlen használatával.
Ha a mintát többször kell futtatni, akkor a mintán kívüli munkamenet létrehozása ajánlott. Ha a rendszer nem ad át munkamenetet, a minta minden indításkor létrehoz egy új munkamenetet, amely több percet is igénybe vehet.

Az alkalmazás most már lefordítható.

## <a name="launch-the-application"></a>Az alkalmazás indítása

1. Csatlakoztassa a HoloLens USB-kábellel a számítógéphez.
1. Kapcsolja be a HoloLens, és várjon, amíg megjelenik a Start menü.
1. Indítsa el a hibakeresőt a Visual Studióban (F5). A rendszer automatikusan telepíti az alkalmazást az eszközre.

A minta alkalmazásnak el kell indulnia, és egy szöveges panelnek kell megjelennie, amely tájékoztatja Önt az aktuális alkalmazás állapotáról. Az állapot indítási időpontban egy új munkamenet indítása vagy egy meglévő munkamenethez való csatlakozás. A modell betöltésének befejezése után a beépített motor modell közvetlenül a feje pozíciójában jelenik meg. A motor modellje a megfelelő működést is lehetővé teszi a helyileg megjelenített forgó adatkockával.

 Ha később el szeretné indítani a mintát, azt a HoloLens Start menüjéből is megtalálhatja, de előfordulhat, hogy egy lejárt munkamenet-AZONOSÍTÓval van lefordítva.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató egy olyan oktatóanyag eredményein alapul, amely elmagyarázza, hogyan integrálható a távoli rendereléssel kapcsolatos összes darab egy készlet *holografikus alkalmazásba*. A szükséges lépések elsajátításához kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: távoli renderelés integrálása HoloLens holografikus alkalmazásba](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)
