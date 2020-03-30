---
title: Az Azure IoT központi felhasználói felületének testreszabása | Microsoft dokumentumok
description: Az Azure IoT központi alkalmazásának téma- és súgóhivatkozásai testreszabása
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157999"
---
# <a name="customize-the-azure-iot-central-ui"></a>Az Azure IoT központi felhasználói felületének testreszabása

Ez a cikk azt ismerteti, hogy rendszergazdaként hogyan szabhatja testre az alkalmazás felhasználói felületét egyéni témák alkalmazásával, és módosíthatja a súgóhivatkozásokat, hogy a saját egyéni súgóforrásaira mutasson. 



A következő képernyőképen egy lap látható a szokásos témát használva:

![Szabványos IoT-központi téma](./media/howto-customize-ui/standard-ui.png)

A következő képernyőképen egy egyéni képernyőképet használó lap látható, amelyen a kiemelt testreszabott felhasználói felületelemei vannak kiemelve:

![Egyéni IoT-központi téma](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Téma létrehozása

Egyéni téma létrehozásához keresse meg az **Alkalmazás testreszabása** lapot a **Felügyelet** szakaszban:

![IoT Central témák](./media/howto-customize-ui/themes.png)

Ezen az oldalon testreszabhatja az alkalmazás következő aspektusait:

### <a name="application-logo"></a>Alkalmazás emblémája

1 MB-nál nem nagyobb PNG-kép átlátszó háttérrel. Ez az embléma balra jelenik meg az IoT Central alkalmazás címsorán.

Ha az emblémakép tartalmazza az alkalmazás nevét, elrejtheti az alkalmazás nevének szövegét. További információt az Alkalmazás kezelése című témakörben [talál.](howto-administer.md#change-application-name-and-url)

### <a name="browser-icon-favicon"></a>Böngésző ikonja (favicon)

32 x 32 képpontnál nem nagyobb PNG-kép átlátszó háttérrel. A webböngésző használhatja ezt a képet a címsorban, az előzményekben, a könyvjelzőkben és a böngésző lapon.

### <a name="browser-colors"></a>Böngésző színei

Módosíthatja az oldalfejléc színét, valamint a gombok és egyéb kiemelések színét. Hatkarakteres hexaxi színértéket `##ff6347`használjon a formátumban. A **HEX-érték** színjelöléséről a [HTML-színek](https://www.w3schools.com/html/html_colors.asp)című témakörben talál további információt.

> [!NOTE]
> Bármikor visszatérhet az alapértelmezett beállításokhoz az **Alkalmazás testreszabása** lapon.

### <a name="changes-for-operators"></a>Változások a gazdasági szereplők

Ha a rendszergazda egyéni témát hoz létre, akkor az operátorok és az alkalmazás más felhasználói már nem választhatnak témát a **Beállítások**menüben.

## <a name="replace-help-links"></a>Súgóhivatkozások cseréje

Ha egyéni súgóinformációkat szeretne biztosítani az operátorok és más felhasználók számára, módosíthatja az alkalmazás **súgómenüjében** található hivatkozásokat.

A súgóhivatkozások módosításához keresse meg a **Felügyelet** szakasz **Súgó testreszabása** lapját:

![Az IoT-központ súgóhivatkozásainak testreszabása](./media/howto-customize-ui/help-links.png)

Új bejegyzéseket is hozzáadhat a súgómenühöz, és eltávolíthatja az alapértelmezett bejegyzéseket:

![Testre szabott IoT Central súgó](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Bármikor visszatérhet az alapértelmezett súgóhivatkozásokhoz a **Súgó testreszabása** lapon.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan szabhatja testre a felhasználói felületet az IoT Central alkalmazásban, az alábbi lépéseket a következő lépések közül olvashat:

- [Alkalmazás felügyelése](./howto-administer.md)
- [Csempék hozzáadása az irányítópulthoz](howto-add-tiles-to-your-dashboard.md)