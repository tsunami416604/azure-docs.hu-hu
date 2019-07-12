---
title: Csatlakozás a Windows 10 vagy Windows 7 – az Azure Windows virtuális asztali előzetes verzió
description: Hogyan lehet csatlakozni, hogy a Windows Virtual Desktop előzetes verziójához a Windows 10-es vagy Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: 66b423ea27cc0f9112349964fcfd0d75f08c12bd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620120"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Csatlakozás Windows 10 vagy Windows Server 7 rendszerről

> A következőkre érvényes: Windows 7 és Windows 10-es.

A letölthető ügyfél érhető el, amely hozzáférést biztosít a Windows virtuális asztal előzetes erőforrásokhoz Windows 7 és Windows 10 rendszerű eszközökön.

> [!IMPORTANT]
> Ne használjon **RemoteApp- és asztali kapcsolat (RADC)** vagy **távoli asztali kapcsolat (MSTSC)** Windows virtuális asztal erőforrások eléréséhez, mivel a Windows virtuális asztal vagy ügyfél nem támogatja.

## <a name="install-the-client"></a>Az ügyfél telepítése

[Töltse le](https://go.microsoft.com/fwlink/?linkid=2068602) és az ügyfél telepítése a helyi számítógéphez. A telepítés rendszergazdai jogosultság szükséges.

## <a name="subscribe-to-a-feed"></a>Előfizetés-hírcsatornára

Elérhető a felügyelt erőforrások listájának beolvasása, hogy feliratkozik a a rendszergazda által megadott hírcsatornán Előfizetés teszi elérhetővé az erőforrásokat a helyi számítógépen.

Előfizetés a riasztásoktól adatcsatornára:

1. Indítsa el az ügyfél az összes alkalmazások listájában keresse meg a **a távoli asztal**.
1. Válassza ki **előfizetés** a főoldalon csatlakozni a szolgáltatáshoz, és az erőforrások lekéréséhez.
1. **Jelentkezzen be a** a felhasználói fiókjával, ha a rendszer kéri.

A sikeres hitelesítés után most már megjelenik az Ön számára elérhető erőforrások listáját.

Két módszer egyikével erőforrások el is indíthatja.

- Az ügyfél fő lapján kattintson duplán az erőforrást, indítsa el az eszközt.
- Indítsa el egy erőforrást, ahogy azt szokásosan tenné más alkalmazások a Start menüből.
  - Az alkalmazások a keresősávba is kereshet.

Miután előfizetett egy csatornához, a tartalom a hírcsatorna automatikusan frissülnek rendszeresen. Erőforrások előfordulhat, hogy hozzáadott, módosítani vagy eltávolítani a rendszergazda által végzett módosítások alapján.

## <a name="view-the-details-of-a-feed"></a>Hírcsatorna részleteinek megtekintése

Előfizetés után megtekintheti a hírcsatorna további információt a részletek panelen elérésével.

1. Az ügyfél fő lapján, válassza a három pontra ( **...** ) a hírcsatorna nevétől jobbra.
1. A legördülő menüből válassza ki a **részletek**.
1. Az ügyfél jobb oldalán megjelenik a részleteket tartalmazó panel.

A részleteket tartalmazó panel tartalmazza a hírcsatorna hasznos információkat:

- Az URL-cím és az előfizetéséhez használt felhasználónév
- Alkalmazások és asztali számítógépek száma
- A dátum/idő az utolsó frissítés
- Az utolsó frissítés állapota

Ha szükséges, elkezdheti válassza a manuális frissítési **frissítés most**.

## <a name="unsubscribe-from-a-feed"></a>Hírcsatorna leiratkozni

Ebben a szakaszban fog arról szól, hogyan hírcsatorna leiratkozni. Fizessen elő újra egy másik fiókkal vagy az erőforrások eltávolítása a rendszerből leiratkozhat.

1. Az ügyfél fő lapján, válassza a három pontra ( **...** ) a hírcsatorna nevétől jobbra.
1. A legördülő menüből válassza ki a **Unsubscribe**.
1. Tekintse át, és válassza ki **Folytatás** a párbeszédpanelről.

## <a name="update-the-client"></a>Az ügyfél frissítése

Ha az ügyfél egy új verziója érhető el, akkor az ügyfél és a Windows Műveletközpontban értesítést. Válassza ki a frissítési folyamat elindításához az értesítést.
