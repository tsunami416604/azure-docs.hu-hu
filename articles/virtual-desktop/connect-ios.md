---
title: Csatlakozás a Windows virtuális asztalhoz iOS-ből – Azure
description: Hogyan csatlakozhat a Windows virtuális asztalhoz az iOS-ügyfélhasználatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128251"
---
# <a name="connect-with-the-ios-client"></a>Kapcsolódás az iOS-ügyfélhez

> Az iOS 13.0-s vagy újabb verzióra vonatkozik. Kompatibilis az iPhone, iPad és iPod touch készülékekkel.

A Windows Virtual Desktop erőforrásait iOS-eszközéről érheti el letölthető kliensünkkel. Ez az útmutató megmutatja, hogyan állíthatja be az iOS-ügyfelet.

## <a name="install-the-ios-client"></a>Az iOS-ügyfél telepítése

Első lépésekhez [töltse le](https://aka.ms/rdios) és telepítse az ügyfelet iOS-készülékére.

## <a name="subscribe-to-a-feed"></a>Feliratkozás hírcsatornára

Iratkozzon fel a rendszergazda által biztosított hírcsatornára, és megkapja az iOS-készülékén elérhető felügyelt erőforrások listáját.

Feliratkozás hírcsatornára:

1. A Kapcsolatközpontban koppintson a ikonra, **+** majd a Munkaterület **hozzáadása**elemre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címmezőjébe.** A hírcsatorna URL-címe lehet URL vagy e-mail cím.
   - Ha URL-t használsz, használd azt, amit a rendszergazda adott neked. Általában az <https://rdweb.wvd.microsoft.com>URL.
   - Az e-mail használatához adja meg e-mail címét. Ez arra utasítja az ügyfelet, hogy keressen egy URL-t az e-mail címéhez társított url-cím után, ha a rendszergazda így konfigurálta a kiszolgálót.
3. Koppintson a **Tovább gombra.**
4. Adja meg a hitelesítő adatait, amikor a rendszer kéri.
   - A **Felhasználónév (Felhasználónév)** mezőbe adja meg az erőforrásokhoz való hozzáféréshez szükséges jogosultsággal rendelkező felhasználónevet.
   - A **Jelszó**mezőbe adja meg a felhasználónévhez társított jelszót.
   - A rendszer további tényezők megadását is kérheti, ha a rendszergazda így konfigurálta a hitelesítést.
5. Koppintson a **Mentés** gombra.

Ezt követően a kapcsolatközpontnak meg kell jelenítenie a távoli erőforrásokat.

Miután feliratkozott egy hírcsatornára, a hírcsatorna tartalma rendszeresen automatikusan frissül. Az erőforrások a rendszergazda által végzett módosítások alapján adhatók hozzá, módosíthatók vagy távolíthatók el.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az iOS-ügyfél használatáról, olvassa el [az Első lépések az iOS-ügyfél](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) dokumentációját.
