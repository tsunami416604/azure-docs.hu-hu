---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz iOS-Azure-ból
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az iOS-ügyfél használatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605837"
---
# <a name="connect-with-the-ios-client"></a>Kapcsolódás az iOS-ügyfélhez

> A következőkre vonatkozik: iOS 8,0 vagy újabb. IPhone-, iPad-és iPod Touch-kompatibilis.

>[!NOTE]
> Az iOS-ügyfél jelenleg még előzetes verzióban érhető el.

A Windows rendszerű virtuális asztali erőforrásokat a letölthető ügyféllel is elérheti iOS-eszközéről. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az iOS-ügyfelet.

## <a name="install-the-ios-beta-client"></a>Az iOS Beta-ügyfél telepítése
Az iOS Beta-ügyfél telepítése:

1. Telepítse az [Apple testflighthoz](https://apps.apple.com/us/app/testflight/id899247664) alkalmazást az iOS-eszközön.
2. Az iOS-eszközön nyisson meg egy böngészőt, és navigáljon a [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. A **2. lépés: csatlakozás a bétaverzióhoz**szakaszban válassza a **tesztelés indítása**lehetőséget.
4. Ha átirányítja a Testflighthoz alkalmazáshoz, válassza az **elfogadás**lehetőséget, majd válassza a **telepítés**lehetőséget.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a rendszergazda által biztosított hírcsatornára az iOS-eszközön elérhető felügyelt erőforrások listájának lekéréséhez.

Előfizetés a hírcsatornára:

1. A Csatlakozáskezelőben koppintson a **+** elemre, majd koppintson a **munkaterület hozzáadása**lehetőségre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címe** mezőbe. A hírcsatorna URL-címe lehet URL vagy e-mail-cím.
   - Ha URL-címet használ, használja a rendszergazdát. Az URL-cím általában <https://rdweb.wvd.microsoft.com>.
   - E-mail-cím használatához adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda konfigurálta a kiszolgálót.
3. Koppintson a **tovább**gombra.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
   - A **Felhasználónév mezőben**adja meg a felhasználónevet, amely jogosult az erőforrások elérésére.
   - A **Password (jelszó**) mezőben adja meg a felhasználónévhez tartozó jelszót.
   - Az is előfordulhat, hogy további tényezőket kell megadnia, ha a rendszergazda úgy konfigurálta a hitelesítést.
5. Koppintson a **Mentés**gombra.

Ezután a Csatlakozáskezelő megjeleníti a távoli erőforrásokat.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az iOS Beta-ügyfél használatáról, tekintse meg az iOS-ügyfél dokumentációjának használatának [első lépéseit](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) .
