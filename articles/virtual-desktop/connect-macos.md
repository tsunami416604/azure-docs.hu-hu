---
title: Csatlakozás a Windows virtuális asztalhoz macOS-ból – Azure
description: Csatlakozás a Windows virtuális asztalhoz a macOS-ügyfélhasználatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128234"
---
# <a name="connect-with-the-macos-client"></a>Kapcsolódás a macOS-ügyfélhez

> A következőre vonatkozik: macOS 10.12 vagy újabb

A Windows Virtual Desktop erőforrásait a macOS-eszközökről a letölthető kliensünkkel érheti el. Ez az útmutató megmutatja, hogyan kell beállítani az ügyfelet.

## <a name="install-the-client"></a>Az ügyfél telepítése

Első lépésekhez [töltse le](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) és telepítse az ügyfelet a macOS-eszközre.

## <a name="subscribe-to-a-feed"></a>Feliratkozás hírcsatornára

Iratkozzon fel arra a hírcsatornára, amelyet a rendszergazda adott, hogy megkapja a macOS-eszközön elérhető felügyelt erőforrások listáját.

Feliratkozás hírcsatornára:

1. Válassza a **Hírcsatorna hozzáadása** lehetőséget a főoldalon a szolgáltatáshoz való csatlakozáshoz és az erőforrások beolvasásához.
2. Adja meg a hírcsatorna URL-címét. Ez lehet URL vagy e-mail cím:
   - Ha URL-t használsz, használd azt, amit a rendszergazda adott neked. Általában az <https://rdweb.wvd.microsoft.com>URL.
   - Az e-mail használatához adja meg e-mail címét. Ez arra utasítja az ügyfelet, hogy keressen egy URL-t az e-mail címéhez társított url-cím után, ha a rendszergazda így konfigurálta a kiszolgálót.
3. Válassza **az Előfizetés**lehetőséget.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

Miután bejelentkezett, meg kell jelennie az elérhető erőforrások listájának.

Miután feliratkoztál egy hírcsatornára, a hírcsatorna tartalma rendszeresen automatikusan frissül. Az erőforrások a rendszergazda által végzett módosítások alapján adhatók hozzá, módosíthatók vagy távolíthatók el.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a macOS-ügyfélről, olvassa el [a MacOS-ügyfél dokumentációjának első lépéseit.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
