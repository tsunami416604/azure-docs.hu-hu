---
title: Kapcsolódás Windowsos virtuális asztalhoz Androidról – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az Android-ügyfél használatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: 41b0c1ced9e66bd58d73683865b2c40afc16c5d3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605849"
---
# <a name="connect-with-the-android-client"></a>Kapcsolódás az Android-ügyféllel

> A következőkre vonatkozik: Android 4,1 és újabb verziók, Chromebook a ChromeOS 53 és újabb verziókkal.

>[!NOTE]
> A Windows rendszerű virtuális asztali erőforrások az Android-ügyfélről való elérésének lehetősége jelenleg előzetes verzióban érhető el.

A Windows rendszerű virtuális asztali erőforrásokat elérhetővé teheti az Android-eszközről a letölthető ügyféllel. Az Android-ügyfelet a Google Play Áruház támogató Chromebook-eszközökön is használhatja. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az Android-ügyfelet.

## <a name="install-the-android-client"></a>Az Android-ügyfél telepítése

Első lépésként [töltse le](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android) és telepítse az ügyfelet az Android-eszközön.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a rendszergazda által biztosított hírcsatornára, hogy lekérje az Android-eszközén elérhető felügyelt erőforrások listáját.

Előfizetés a hírcsatornára:

1. A Csatlakozáskezelőben koppintson a **+** elemre, majd koppintson a **távoli erőforrás-hírcsatorna**elemre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címe** mezőbe. A hírcsatorna URL-címe lehet URL vagy e-mail-cím.
   - Ha URL-címet használ, használja a rendszergazdát, és általában <https://rdweb.wvd.microsoft.com>.
   - E-mail-cím használatához adja meg az e-mail-címét. Az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda úgy konfigurálta a kiszolgálót.
3. Koppintson a **tovább**gombra.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
   - A **Felhasználónév mezőben**adja meg a felhasználónevet, amely jogosult az erőforrások elérésére.
   - A **Password (jelszó**) mezőben adja meg a felhasználónévhez tartozó jelszót.
   - Az is előfordulhat, hogy további tényezőket kell megadnia, ha a rendszergazda úgy konfigurálta a hitelesítést.

A feliratkozást követően a kapcsolatok középpontjában meg kell jelennie a távoli erőforrásoknak.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Android-ügyfél használatáról, tekintse meg [az Android-ügyfél használatának első lépéseit](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android)ismertető témakört.
