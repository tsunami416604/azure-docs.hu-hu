---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz macOS rendszerről – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a macOS-ügyféllel.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 80d75357baeb3f8afd910e22abca8b11af2d80b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285168"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz a macOS-ügyféllel

> A következőkre vonatkozik: macOS 10,12 vagy újabb

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-macos-2019.md).

A macOS-eszközökről elérheti a Windows rendszerű virtuális asztali erőforrásokat a letölthető ügyféllel. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az ügyfelet.

## <a name="install-the-client"></a>Az ügyfél telepítése

Első lépésként [töltse le](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   és telepítse a-ügyfelet a MacOS-eszközre.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a hírcsatornára a rendszergazda a macOS-eszközön elérhető felügyelt erőforrások listájának beszerzéséhez.

Előfizetés a hírcsatornára:

1. A főoldalon válassza a **munkaterület hozzáadása** lehetőséget a szolgáltatáshoz való kapcsolódáshoz és az erőforrások lekéréséhez.
2. Adja meg a hírcsatorna URL-címét. Ez lehet URL-cím vagy e-mail-cím:
   - Ha URL-címet használ, használja a rendszergazdát. Általában az URL-cím <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - E-mail-cím használatához adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda konfigurálta a kiszolgálót.
3. Válassza a **Hozzáadás** elemet.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

Miután bejelentkezett, megjelenik az elérhető erőforrások listája.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>További lépések

A macOS-ügyféllel kapcsolatos további tudnivalókért tekintse meg a macOS-ügyfél dokumentációjának első [lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
