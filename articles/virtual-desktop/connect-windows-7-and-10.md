---
title: Csatlakozás windowsos virtuális asztalhoz Windows 10 vagy 7 – Azure
description: Csatlakozás a Windows virtuális asztalhoz a Windows Asztali ügyfélprogram használatával?
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154338"
---
# <a name="connect-with-the-windows-desktop-client"></a>Kapcsolódás a Windows asztali ügyféllel

> A következőkre vonatkozik: Windows 7, Windows 10 és Windows 10 IoT Enterprise

A Windows 7, a Windows 10 és a Windows 10 IoT Enterprise rendszerrel rendelkező eszközökön a Windows Desktop ügyfélprogram mal érheti el a Windows virtuális asztal erőforrásait.

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp és az Asztali kapcsolatok (RADC) vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

> [!IMPORTANT]
> A Windows virtuális asztal jelenleg nem támogatja a Távoli asztali ügyfél programot a Windows Áruházból. Az ügyfél támogatása egy későbbi kiadásban lesz hozzáadva.

## <a name="install-the-windows-desktop-client"></a>A Windows Asztali ügyfél telepítése

Válassza ki azt az ügyfelet, amely megfelel az Ön Windows-verziójának:

- [64 bites Windows](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Telepítheti az ügyfelet az aktuális felhasználóhoz, amely nem igényel rendszergazdai jogokat, vagy a rendszergazda telepítheti és konfigurálhatja az ügyfelet, hogy az eszköz összes felhasználója hozzáférhessen.

A telepítés után az ügyfél a Start menüből indítható el a **Távoli asztal**keresésével.

## <a name="subscribe-to-a-feed"></a>Feliratkozás hírcsatornára

A rendszergazda által biztosított hírcsatornára való feliratkozással megkaphatja a rendelkezésre álló felügyelt erőforrások listáját. Az előfizetés elérhetővé teszi az erőforrásokat a helyi számítógépen.

Feliratkozás hírcsatornára:

1. Nyissa meg a Windows Asztali ügyfelet.
2. Válassza az **Előfizetés** lehetőséget a főoldalon a szolgáltatáshoz való csatlakozáshoz és az erőforrások lekéréséhez.
3. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

Miután sikeresen bejelentkezett, meg kell jelennie az elérhető erőforrások listájának.

Az erőforrásokat két módszer segítségével indíthatja el.

- Az ügyfél főlapján kattintson duplán egy erőforrásra a elindításához.
- Indítson el egy erőforrást a szokásos módon a Start menüből.
  - Az alkalmazásokat a keresősávban is megkeresheti.

Miután feliratkozott egy hírcsatornára, a hírcsatorna tartalma rendszeresen automatikusan frissül. Az erőforrások a rendszergazda által végzett módosítások alapján adhatók hozzá, módosíthatók vagy távolíthatók el.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Windows Asztali ügyfél használatáról, olvassa el az Első lépések a Windows Asztali ügyféllel című [kiadványcímű e-című e-kiadvány című e-című, a Windows Asztali ügyfélalkalmazást.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)
