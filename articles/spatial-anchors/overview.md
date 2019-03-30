---
title: Az Azure térbeli kapcsolatok alapjainak áttekintése |} A Microsoft Docs
description: Ismerje meg a térbeli horgonyok Azure segítségével miként fejleszthet platformfüggetlen vegyes valóság élményt.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2451922f0eb49a5ccee036db72eb046760287dca
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58628865"
---
# <a name="azure-spatial-anchors-overview"></a>Az Azure térbeli kapcsolatok alapjainak áttekintése

Üdvözli az Azure térbeli horgonyok. Azure térbeli horgonyok segítségével a fejlesztők alapvető funkcióit kijelölése tisztában a vegyes valóságon alapuló alkalmazások létrehozását. Ezeket az alkalmazásokat is támogatja a Microsoft HoloLens ARKit támogató iOS-alapú eszközök és ARCore támogató Android-alapú eszközök. Az Azure térbeli horgonyok segítségével a fejlesztők használata vegyes valósághoz platformok mennyire fogja világosnak látni a tárolóhelyek, a lényeges pontos kijelölni, és ezen pontok, a támogatott eszközökről lényeges visszaírásához.
Ezek a lényeges pontos térbeli horgonyok nevezzük.

![Platformokon átívelő](./media/cross-platform.png)

## <a name="examples"></a>Példák

Egyes használati példák térbeli horgonyok által engedélyezett többek között:

- [Több felhasználói élményeket](tutorials/tutorial-share-anchors-across-devices.md). Térbeli horgonyok megkönnyíti a felhasználók ugyanazon a helyen a többfelhasználós vegyes valóság alkalmazások való részvételre. Például két személy indításához vegyes valóság sakk virtuális sakktábla elhelyezése egy táblát. Ezt követően mutat, az eszközt, a tábla, megtekintheti és interakcióba együtt a virtuális sakktábla.

- [Módon felmérő](concepts/anchor-relationships-way-finding.md). A fejlesztők is csatlakoztathat térbeli horgonyok együtt a kettő közötti kapcsolatok létrehozása. Például egy alkalmazás tartalmazhat felületet nyújt, amelynek két vagy több pont a lényeges, hogy egy felhasználó egy adott feladat végrehajtásához együtt kell működnie. Ezek a lényeges pontok csatlakoztatott módon hozható létre. Később amikor a felhasználó a többlépéses feladat befejeződik, az alkalmazás kérhet, amelyek a jelenlegivel irányítani felé a következő lépés a felhasználó a feladatnak a közeli kapcsolatok alapjainak.

- [A való életből vett megőrzése virtuális tartalom](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Egy alkalmazás hagyhatja, hogy egy felhasználó egy virtuális naptár helyezze egy konferencia szoba üzenőfalon, a felhasználók egy telefonos alkalmazást vagy a HoloLens eszköz használatával is láthatják. Egy ipari beállítás, a felhasználó kap a gépre vonatkozó környezeti információkat húzni a támogatott eszközön a kamera.

Az Azure térbeli horgonyok egy felügyelt szolgáltatás és ügyféloldali SDK-támogatott eszközplatformok tevődik össze. A következő szakaszok Azure térbeli horgonyok használó alkalmazások használatának első lépéseivel kapcsolatos információkat.

## <a name="next-steps"></a>További lépések

Az első alkalmazás létrehozása a térbeli horgonyok.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
