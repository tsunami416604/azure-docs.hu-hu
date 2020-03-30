---
title: Csatlakozás a Windows virtuális asztalhoz Androidról – Azure
description: Hogyan lehet csatlakozni a Windows Virtual Desktop az Android kliens.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295378"
---
# <a name="connect-with-the-android-client"></a>Kapcsolódás az Android-ügyféllel

> Az Android 4.1-es és újabb verzióira, ChromeOS 53-as és újabb rendszerű Chromebookokra vonatkozik.

>[!NOTE]
> A Windows virtuális asztal erőforrásainak az Android-ügyfélről való elérésének lehetősége jelenleg előzetes verzióban érhető el.

A Windows Virtual Desktop erőforrásait androidos eszközéről érheti el letölthető kliensünkkel. Az Android-klienst olyan Chromebook-eszközökön is használhatja, amelyek támogatják a Google Play Áruházat. Ez az útmutató megmutatja, hogyan kell beállítani az Android klienst.

## <a name="install-the-android-client"></a>Az Android-ügyfél telepítése

A kezdéshez [töltse le](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) és telepítse az ügyfelet Android-eszközére.

## <a name="subscribe-to-a-feed"></a>Feliratkozás hírcsatornára

Iratkozzon fel a rendszergazda által biztosított hírcsatornára, és megkapja az Android-eszközön elérhető felügyelt erőforrások listáját.

Feliratkozás hírcsatornára:

1. A Kapcsolatközpontban koppintson a ikonra, **+** majd a Távoli **erőforrás-hírcsatorna**elemre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címmezőjébe.** A hírcsatorna URL-címe lehet URL vagy e-mail cím.
   - Ha egy URL-t, használja az egyik <https://rdweb.wvd.microsoft.com>a rendszergazda adott neked, általában .
   - Az e-mail használatához adja meg e-mail címét. Az ügyfél megkeresi az e-mail címéhez társított URL-címet, ha a rendszergazda így konfigurálta a kiszolgálót.
3. Koppintson **a Tovább gombra.**
4. Adja meg a hitelesítő adatait, amikor a rendszer kéri.
   - A **Felhasználónév (Felhasználónév)** mezőbe adja meg az erőforrásokhoz való hozzáféréshez szükséges jogosultsággal rendelkező felhasználónevet.
   - A **Jelszó**mezőbe adja meg a felhasználónévhez társított jelszót.
   - A rendszer további tényezők megadását is kérheti, ha a rendszergazda így konfigurálta a hitelesítést.

Az előfizetés után a Kapcsolatközpontnak meg kell jelenítenie a távoli erőforrásokat.

Miután feliratkozott egy hírcsatornára, a hírcsatorna tartalma rendszeresen automatikusan frissül. Az erőforrások a rendszergazda által végzett módosítások alapján adhatók hozzá, módosíthatók vagy távolíthatók el.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Android-ügyfél használatáról, olvassa [el az Első lépések az Android-ügyféllel című verziót.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)
