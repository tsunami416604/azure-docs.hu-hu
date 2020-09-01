---
title: Kapcsolódás a Windows rendszerű virtuális asztali Windows 10 vagy 7-es verzióhoz – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a Windows asztali ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e4f41cb23ec4a9463b983049f5f37d09d1b8c71
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226709"
---
# <a name="connect-with-the-windows-desktop-client"></a>Kapcsolódás a Windows asztali ügyféllel

> A következőkre vonatkozik: Windows 7, Windows 10 és Windows 10 IoT Enterprise

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).

A Windows rendszerű virtuális asztali erőforrásokat Windows 7, Windows 10 és Windows 10 IoT Enterprise rendszerű eszközökön érheti el a Windows asztali ügyfélprogram használatával. Az ügyfél nem támogatja a 8. vagy a Windows 8,1 rendszert.

>[!NOTE]
>A Windows-ügyfél automatikusan alapértéke a Windows Virtual Desktop (klasszikus). Ha azonban az ügyfél észleli, hogy a felhasználó Azure Resource Manager erőforrásokkal is rendelkezik, automatikusan hozzáadja az erőforrásokat, vagy értesíti a felhasználót, hogy elérhetők.

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp-és asztali kapcsolatok (RADC) ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

> [!IMPORTANT]
> A Windows virtuális asztal jelenleg nem támogatja a Távoli asztal ügyfelet a Windows áruházból.

## <a name="install-the-windows-desktop-client"></a>A Windows asztali ügyfél telepítése

Válassza ki azt az ügyfelet, amely megfelel a Windows-verziójának:

- [Windows 64 bites](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Telepítheti az aktuális felhasználóhoz tartozó ügyfelet, amely nem követeli meg a rendszergazdai jogosultságokat, vagy a rendszergazda telepítheti és konfigurálhatja az ügyfelet, hogy az eszköz összes felhasználója hozzáférhessen.

A telepítést követően a-ügyfél a Start menüből indítható el **Távoli asztal**keresésével.

## <a name="subscribe-to-a-workspace"></a>Előfizetés munkaterületre

A munkaterületekre kétféleképpen lehet előfizetni. Az ügyfél megpróbálhatja felderíteni a munkahelyi vagy iskolai fiókja által elérhető erőforrásokat, vagy közvetlenül megadhatja az URL-címet, ahol az erőforrások olyan esetekre vonatkoznak, amikor az ügyfél nem találja őket. Miután előfizetett egy munkaterületre, az alábbi módszerek egyikével indíthatja el az erőforrásokat:

- Nyissa meg a kapcsolódási központot, majd kattintson duplán egy erőforrásra az elindításához.
- Nyissa meg a Start menüt, és keressen egy mappát a munkaterület nevével, vagy adja meg az erőforrás nevét a keresősáv alatt.

### <a name="subscribe-with-a-user-account"></a>Előfizetés felhasználói fiókkal

1. Az ügyfél főoldalán válassza az **előfizetés**lehetőséget.
2. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.
3. Az erőforrások megjelennek a kapcsolatok központjában, és a munkaterület szerint vannak csoportosítva.

### <a name="subscribe-with-a-url"></a>Előfizetés URL-címmel

1. Az ügyfél főoldalán válassza az **előfizetés URL-lel**lehetőséget.
2. Adja meg a munkaterület URL-címét vagy e-mail-címét:
   - Ha a **munkaterület URL-címét**használja, használja a rendszergazda által megadott címet. Ha a Windows virtuális asztalról fér hozzá az erőforrásokhoz, a következő URL-címek egyikét használhatja:
     - Windowsos virtuális asztal (klasszikus): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows rendszerű virtuális asztal: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
     - Windows rendszerű virtuális asztal (US Gov): `https://rdweb.wvd.azure.us/api/arm/feeddiscovery`
   - Ha ehelyett az **e-mail** mezőt használja, adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda beállította az [e-mail felderítését](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Kattintson a **Tovább** gombra.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.
5. Az erőforrásoknak a kapcsolatok központjában kell megjelenniük, munkaterület szerint csoportosítva.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Windows asztali ügyfél használatáról, tekintse meg [a Windows asztali ügyfélprogram használatának első lépéseivel foglalkozó](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)témakört.
