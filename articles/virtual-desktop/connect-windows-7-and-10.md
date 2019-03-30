---
title: Csatlakozás Windows virtuális asztali Preview, Windows 7 és Windows 10 – Azure
description: Hogyan lehet csatlakozni a Windows 7 vagy Windows 10-es Windows virtuális asztal előzetes szolgáltatást.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/27/2019
ms.author: helohr
ms.openlocfilehash: 8b5a649d767dae9ae9c0fb077f550451a7f83601
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630380"
---
# <a name="connect-with-windows-7-and-windows-10"></a>Kapcsolódás Windows 7-en és Windows 10-en

> A következőre érvényes Windows 7 és Windows 10-es.

A letölthető ügyfél érhető el, amely hozzáférést biztosít a Windows virtuális asztal előzetes erőforrásokhoz Windows 7 és Windows 10 rendszerű eszközökön.

> [!IMPORTANT]
> Ne használjon **RemoteApp- és asztali kapcsolat (RADC)** vagy **távoli asztali kapcsolat (MSTSC)** Windows virtuális asztal erőforrások eléréséhez, mivel a Windows virtuális asztal vagy ügyfél nem támogatja.

## <a name="install-the-client"></a>Az ügyfél telepítése

[Töltse le](https://go.microsoft.com/fwlink/?linkid=2068602) és az ügyfél telepítése a helyi számítógéphez. Ehhez rendszergazdai jogosultságokkal.

## <a name="subscribe-to-a-feed"></a>Előfizetés-hírcsatornára

Listájának rendelkezésre álló erőforrások Önnek a helyi számítógépről feliratkozik a a rendszergazda által megadott hírcsatornán

Előfizetés a riasztásoktól adatcsatornára:

1. Indítsa el az ügyfél az összes alkalmazások listájában keresse meg a **a távoli asztal**.
1. Válassza ki **előfizetés** a főoldalon csatlakozni a szolgáltatáshoz, és az erőforrások lekéréséhez.
1. **Jelentkezzen be a** a felhasználói fiókjával, ha a rendszer kéri.

A sikeres hitelesítés után most már megjelenik az Ön számára elérhető erőforrások listáját.

Két módszer egyikével erőforrások el is indíthatja.

- Az ügyfél fő lapján kattintson duplán az erőforrást, indítsa el az eszközt.
- Indítsa el egy erőforrást, ahogy azt szokásosan tenné más alkalmazások a Start menüből.
  - Az alkalmazások a keresősávba is kereshet.

## <a name="update-the-client"></a>Az ügyfél frissítése

Ha az ügyfél egy új verziója érhető el, akkor az ügyfél és a Windows Műveletközpontban értesítést. Válassza ki a frissítési folyamat elindításához az értesítést.
