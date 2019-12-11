---
title: Kapcsolódás a Windows rendszerű virtuális asztali Windows 10 vagy 7-es verzióhoz – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a Windows asztali ügyfélprogram használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 745b33efe46c82e3b9358c9c5a2ed13292242db1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997343"
---
# <a name="connect-with-the-windows-desktop-client"></a>Kapcsolódás a Windows asztali ügyféllel

> A következőkre vonatkozik: Windows 7, Windows 10 és Windows 10 IoT Enterprise

A Windows rendszerű virtuális asztali erőforrásokat Windows 7, Windows 10 és Windows 10 IoT Enterprise rendszerű eszközökön érheti el a Windows asztali ügyfélprogram használatával.

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp-és asztali kapcsolatok (RADC) ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

## <a name="install-the-windows-desktop-client"></a>A Windows asztali ügyfél telepítése

Válassza ki azt az ügyfelet, amely megfelel a Windows-verziójának:

- [Windows 64 bites](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Telepítheti az aktuális felhasználóhoz tartozó ügyfelet, amely nem követeli meg a rendszergazdai jogosultságokat, vagy a rendszergazda telepítheti és konfigurálhatja az ügyfelet, hogy az eszköz összes felhasználója hozzáférhessen.

A telepítést követően a-ügyfél a Start menüből indítható el **Távoli asztal**keresésével.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Az Ön által elérhetővé tett felügyelt erőforrások listájának lekérése a rendszergazda által biztosított hírcsatornára való feliratkozással. A Feliratkozás az erőforrásokat a helyi számítógépen is elérhetővé teszi.

Előfizetés a hírcsatornára:

1. Nyissa meg a Windows asztali ügyfelet.
2. Válassza a Főoldal **előfizetés** elemét a szolgáltatáshoz való kapcsolódáshoz és az erőforrások lekéréséhez.
3. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

A sikeres bejelentkezést követően meg kell jelennie az elérni kívánt erőforrások listájának.

Az erőforrásokat két módszer egyikével indíthatja el.

- Az ügyfél főoldalán kattintson duplán egy erőforrásra az elindításához.
- Indítson el egy erőforrást, ahogy a Start menü más alkalmazásaiban is.
  - Az alkalmazásokat a keresősáv segítségével is megkeresheti.

A hírcsatornára való előfizetést követően a rendszer rendszeresen frissíti a hírcsatorna tartalmát. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Windows asztali ügyfél használatáról, tekintse meg [a Windows asztali ügyfélprogram használatának első lépéseivel foglalkozó](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop)témakört.
