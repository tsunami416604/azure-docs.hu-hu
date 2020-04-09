---
title: Csatlakozás a Windows virtuális asztalhoz macOS-ból – Azure
description: Csatlakozás a Windows virtuális asztalhoz a macOS-ügyfélhasználatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6ac3ee8d44d68f5c7929c2f93f1a1182c7f6083b
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891254"
---
# <a name="connect-with-the-macos-client"></a>Kapcsolódás a macOS-ügyfélhez

> A következőre vonatkozik: macOS 10.12 vagy újabb

A Windows Virtual Desktop erőforrásait a macOS-eszközökről a letölthető kliensünkkel érheti el. Ez az útmutató megmutatja, hogyan kell beállítani az ügyfelet.

## <a name="install-the-client"></a>Az ügyfél telepítése

Első lépésekhez [töltse le](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) és telepítse az ügyfelet a macOS-eszközre.

## <a name="subscribe-to-a-feed"></a>Feliratkozás hírcsatornára

Iratkozzon fel arra a hírcsatornára, amelyet a rendszergazda adott, hogy megkapja a macOS-eszközön elérhető felügyelt erőforrások listáját.

Feliratkozás hírcsatornára:

1. Válassza a **munkaterület hozzáadása** lehetőséget a főlapon a szolgáltatáshoz való csatlakozáshoz és az erőforrások beolvasásához.
2. Adja meg a hírcsatorna URL-címét. Ez lehet URL vagy e-mail cím:
   - Ha URL-t használsz, használd azt, amit a rendszergazda adott neked. Általában az <https://rdweb.wvd.microsoft.com>URL.
   - Az e-mail használatához adja meg e-mail címét. Ez arra utasítja az ügyfelet, hogy keressen egy URL-t az e-mail címéhez társított url-cím után, ha a rendszergazda így konfigurálta a kiszolgálót.
3. Válassza a **Hozzáadás** lehetőséget.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

Miután bejelentkezett, meg kell jelennie az elérhető erőforrások listájának.

Miután feliratkoztál egy hírcsatornára, a hírcsatorna tartalma rendszeresen automatikusan frissül. Az erőforrások a rendszergazda által végzett módosítások alapján adhatók hozzá, módosíthatók vagy távolíthatók el.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a macOS-ügyfélről, olvassa el [a MacOS-ügyfél dokumentációjának első lépéseit.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
