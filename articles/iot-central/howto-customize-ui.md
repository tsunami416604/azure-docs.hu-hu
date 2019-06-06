---
title: Az Azure IoT-központ felhasználói felület testreszabása |} A Microsoft Docs
description: A téma- és Súgó hivatkozásokat az Azure IoT központi alkalmazás testreszabása
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d385f1e8c883453b4153ca4c9119d3be0a608bb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495561"
---
# <a name="customize-the-azure-iot-central-ui"></a>Az Azure IoT-központ felhasználói felület testreszabása 

*Ez a cikk rendszergazdák vonatkozik.*

IoT Central lehetővé teszi az alkalmazás a felhasználói felület testreszabásával alkalmazása egyéni témákat, és a saját egyéni erőforrások átirányítása súgóhivatkozások módosítása. Az alábbi képernyőfelvételen egy oldal, a standard szintű téma használatával:

![Standard szintű IoT-központ téma](./media/howto-customize-ui/standard-ui.png)

Az alábbi képernyőképen egy lapot egy egyéni képernyőképe a kiemelt testre szabott felhasználói felületi elemeket jelenít meg:

![Egyéni IoT Central téma](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Téma létrehozása

Egyéni téma létrehozásához keresse meg a **testre szabhatja az alkalmazás** lapját a **felügyeleti** szakaszban:

![IoT-központ témák](./media/howto-customize-ui/themes.png)

Ezen az oldalon testre szabhatja az alkalmazás a következő szempontokat:

### <a name="application-logo"></a>Alkalmazás emblémája

PNG-kép, áttetsző háttérrel, 1 MB-nál nagyobb. Az embléma IoT központi alkalmazás címsorában balra jeleníti meg.

Ha a emblémakép tartalmazza az alkalmazás nevét, az alkalmazás neve szöveg elrejtheti. További információkért lásd: [Spravovat nastavení aplikace](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Böngésző ikonra (favicon)

PNG-kép, nagyobb, mint 32 x 32 képpont áttetsző háttérrel. Egy webböngészőben a képet a címet a böngésző címsorába, az előzmények, a könyvjelzők és a böngészőlapon használhatja.

### <a name="browser-colors"></a>Böngésző színek

Az oldal fejlécében színét és gombok és további kiemelt accenting használt szín módosítása Hat karakter hexadecimális szín értéket használja a következő formátumban `##ff6347`. További információ **HEXADECIMÁLIS érték** jelöléssel szín, lásd: [HTML színek](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Akkor is mindig visszatérhet az alapértelmezett beállításokat a a **testre szabhatja az alkalmazás** lapot.

### <a name="changes-for-operators"></a>A kezelők módosítása

A rendszergazda létrehoz egy egyéni téma, akkor a kezelők vagy más felhasználók az alkalmazás már nem választhat a témát a **beállítások**.

## <a name="replace-help-links"></a>Cserélje le a súgó hivatkozásai

Egyéni információt nyújt a a kezelők vagy más felhasználók számára, módosíthatja a kapcsolatokat az alkalmazás **súgó** menü.

A súgó hivatkozásai módosításához lépjen a **testre szabhatja a Súgó** lap a **felügyeleti** szakaszban:

![IoT-központ súgóhivatkozások testreszabása](./media/howto-customize-ui/help-links.png)

Adjon hozzá új bejegyzéseket a Súgó menü is, és távolítsa el az alapértelmezett bejegyzéseket:

![Testreszabott IoT-központ Súgó](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Akkor is mindig visszatérhet az alapértelmezett súgóhivatkozások az a **testre szabhatja a Súgó** lapot.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan szabhatja testre a felhasználói felület az IoT-központ alkalmazásban, az alábbiakban néhány javasolt következő lépések:

- [Az alkalmazás felügyelete](./howto-administer.md)
- [Az irányítópult konfigurálása](./howto-configure-homepage.md)