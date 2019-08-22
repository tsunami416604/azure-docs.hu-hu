---
title: Az Azure IoT Central felhasználói felületének testreszabása | Microsoft Docs
description: Az Azure IoT Central-alkalmazás témájának és súgójának testreszabása
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6d120ecc322318e6daf72c506131bcd85c3be4e5
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880851"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Az Azure IoT Central felhasználói felület testreszabása (előzetes verziójú funkciók)

Ez a cikk leírja, hogyan szabhatja testre az alkalmazás felhasználói felületét egyéni témák alkalmazásával, és módosíthatja a Súgó hivatkozásait úgy, hogy az egyéni súgó erőforrásaira mutasson. 

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Az alábbi képernyőképen egy oldal látható a standard téma használatával:

![Standard IoT Central téma](./media/howto-customize-ui-pnp/standard-ui.png)

Az alábbi képernyőképen egy olyan oldal látható, amely egy egyéni képernyőképet használ a Kiemelt FELHASZNÁLÓIFELÜLET-elemek kiemelésével:

![Egyéni IoT Central téma](./media/howto-customize-ui-pnp/themed-ui.png)

## <a name="create-theme"></a>Téma létrehozása

Egyéni téma létrehozásához navigáljon az **alkalmazás testreszabása** lapra az **Adminisztráció** szakaszban:

![IoT Central témák](./media/howto-customize-ui-pnp/themes.png)

Ezen az oldalon testreszabhatja az alkalmazás következő aspektusait:

### <a name="application-logo"></a>Alkalmazás emblémája

Egy 1 MB-nál nem nagyobb PNG-rendszerkép átlátszó háttérrel. Ez az embléma a IoT Central alkalmazás címsorának bal oldalán jelenik meg.

Ha az embléma képe tartalmazza az alkalmazás nevét, akkor elrejtheti az alkalmazásnév szövegét. További információ: [az alkalmazás kezelése](./howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Böngésző ikonja (favicon)

Egy PNG-rendszerkép, amely nem nagyobb, mint 32 x 32 képpont, átlátszó háttérrel. A webböngésző használhatja ezt a rendszerképet a címsor, előzmények, könyvjelzők és böngésző lapokon.

### <a name="browser-colors"></a>Böngésző színei

Megváltoztathatja az oldal fejlécének színét, valamint az ékezetes gombokhoz és egyéb csúcsfényekhez használt színeket. Használjon egy hat karakteres hexadecimális színértéket a `##ff6347`formátumban. További információ a **hexadecimális érték** színének jelöléséről: [HTML-színek](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Az **alkalmazás testreszabása** oldalon bármikor visszatérhet az alapértelmezett beállításokra.

### <a name="changes-for-operators"></a>Operátorok módosításai

Ha egy rendszergazda egyéni témát hoz létre, akkor a kezelők és az alkalmazás más felhasználói már nem választhatnak témát a **beállításokban**.

## <a name="replace-help-links"></a>A Súgó hivatkozásainak cseréje

Ha egyéni súgót szeretne megadni a kezelők és más felhasználók számára, módosíthatja az alkalmazás **Súgó** menüjében található hivatkozásokat.

A Súgó hivatkozásainak módosításához navigáljon a **Súgó testreszabása** lapra az **Adminisztráció** szakaszban:

![IoT Central Súgó hivatkozásainak testreszabása](./media/howto-customize-ui-pnp/help-links.png)

Új bejegyzéseket is hozzáadhat a Súgó menühöz, és eltávolíthatja az alapértelmezett bejegyzéseket:

![Testreszabott IoT Central Súgó](./media/howto-customize-ui-pnp/custom-help.png)

> [!NOTE]
> A **Súgó testreszabása** lapon bármikor visszatérhet az alapértelmezett Súgó hivatkozásokra.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan szabhatja testre a felhasználói felületet a IoT Central alkalmazásban, néhány javasolt lépés:

- [Az alkalmazás felügyelete](./howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [Az alkalmazás irányítópultjának konfigurálása](./howto-configure-homepage.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)