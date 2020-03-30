---
title: Azure térbeli horgonyok – áttekintés
description: Ismerje meg, hogy az Azure Spatial Anchors hogyan segíti a platformok közötti vegyes valóság élmények fejlesztését.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77657307"
---
# <a name="azure-spatial-anchors-overview"></a>Azure térbeli horgonyok – áttekintés

Üdvözöljük az Azure Spatial Anchors. Az Azure Spatial Anchors lehetővé teszi a fejlesztők számára az alapvető képességeket, hogy térbelileg tudatos vegyes valóságú alkalmazásokat építsenek. Ezek az alkalmazások támogathatják a Microsoft HoloLenst, az ARKit-ot támogató iOS-alapú eszközöket és az ARCore-t támogató Android-alapú eszközöket. Az Azure Spatial Anchorsben a fejlesztők vegyesvalóság-platformokat használhatnak a terek érzékeléséhez, a fontos helyek pontos kijelöléséhez, valamint a támogatott eszközökről származó fontos helyek felidézéséhez.
Ezeket a pontos érdekes helyeket térbeli horgonyoknak nevezzük.

![Platformfüggetlen](./media/cross-platform.png)

## <a name="examples"></a>Példák

Néhány példa a Térbeli horgonyok által engedélyezett használati esetekre:

- [Többfelhasználós élmény](tutorials/tutorial-share-anchors-across-devices.md). A Térbeli horgonyok megkönnyítik az ugyanazon a helyen lévő emberek számára, hogy részt vegyenek a többfelhasználós vegyes valóságú alkalmazásokban. Például, két ember lehet kezdeni egy játékot a vegyes valóság sakk azáltal, hogy egy virtuális sakktábla egy asztalra. Ezután, ha az eszközüket az asztalhoz irányítják, együtt tekinthetik meg a virtuális sakktáblát, és kapcsolatba léphetnek velük.

- [Útkereső](concepts/anchor-relationships-way-finding.md). A fejlesztők a térbeli horgonyokat is összekapcsolhatják, és kapcsolatokat hozhatnak létre közöttük. Egy alkalmazás például tartalmazhat egy olyan élményt, amelykét vagy több érdekes pontot tartalmaz, amelyekkel a felhasználónak interakcióba kell lépnie egy feladat elvégzéséhez. Ezeket az érdekes helyeket összekapcsolt módon lehet létrehozni. Később, amikor a felhasználó befejezi a többlépéses feladatot, az alkalmazás kérheti horgonyok, amelyek a közelben az aktuális, hogy irányítsa a felhasználót a következő lépés a feladat.

- [Továbbra is fennáll a virtuális tartalom a valós világban](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Az alkalmazások lehetővé tehetik, hogy a felhasználó virtuális naptárat helyezzen el a konferenciaterem falán, amelyet az emberek telefonos alkalmazás vagy HoloLens-eszköz segítségével láthatnak. Ipari környezetben a felhasználók környezeti információkat kaphatnak egy gépről, ha egy támogatott eszköz kamerájával felé mutatnak.

Az Azure Spatial Anchors egy felügyelt szolgáltatásból és a támogatott eszközplatformok ügyféloldali SDK-iból áll. A következő szakaszok az Azure Spatial Anchors használatával alkalmazások készítésének megkezdéséről nyújtanak tájékoztatást.

## <a name="next-steps"></a>További lépések

Hozza létre első alkalmazását térbeli horgonyokkal.

> [!div class="nextstepaction"]
> [Egység (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Egység (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Egység (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
