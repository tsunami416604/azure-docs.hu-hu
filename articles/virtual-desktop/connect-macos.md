---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz macOS rendszerről – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a macOS-ügyféllel.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: fa61d69ae7aa650b6011696fd45d05ba20d2ac8b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338708"
---
# <a name="connect-with-the-macos-client"></a>Kapcsolódás a macOS-ügyfélhez

> A következőkre vonatkozik: macOS 10,12 vagy újabb

>[!NOTE]
> Jelenleg előzetes verzióban érhető el a Windows rendszerű virtuális asztali erőforrások elérésének lehetősége a macOS-ügyfélről.

A macOS-eszközökről elérheti a Windows rendszerű virtuális asztali erőforrásokat a letölthető ügyféllel. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az ügyfelet.

## <a name="install-the-client"></a>Az ügyfél telepítése

Első lépésként [töltse le](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) és telepítse a-ügyfelet a MacOS-eszközre.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a hírcsatornára a rendszergazda a macOS-eszközön elérhető felügyelt erőforrások listájának beszerzéséhez.

Előfizetés a hírcsatornára:

1. A főoldalon válassza a **hírcsatorna hozzáadása** lehetőséget a szolgáltatáshoz való kapcsolódáshoz és az erőforrások lekéréséhez.
2. Adja meg a hírcsatorna URL-címét. Ez lehet URL-cím vagy e-mail-cím:
   - Ha URL-címet használ, használja a rendszergazdát. Általában az URL-cím <https://rdweb.wvd.microsoft.com>.
   - E-mail-cím használatához adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda konfigurálta a kiszolgálót.
3. Válassza az **előfizetés**lehetőséget.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

Miután bejelentkezett, megjelenik az elérhető erőforrások listája.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>További lépések

A macOS-ügyféllel kapcsolatos további tudnivalókért tekintse meg a macOS-ügyfél dokumentációjának első [lépéseit](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac) .
