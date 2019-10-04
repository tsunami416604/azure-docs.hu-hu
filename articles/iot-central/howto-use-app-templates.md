---
title: Alkalmazássablonok használata az Azure IoT Central |} A Microsoft Docs
description: Kezelőként használata az eszköz beállítása az Azure IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499219"
---
# <a name="use-application-templates"></a>Alkalmazássablonok használata

Ez a cikk azt ismerteti, hogyan megoldás vezető, létrehozása és használata az alkalmazás.

Amikor létrehoz egy Azure IoT Central alkalmazáshoz, hogy beépített minta sablonok közül választhat. A meglévő IoT-központ alkalmazás saját alkalmazás sablonokat is létrehozhat. Ezután használhatja a saját alkalmazássablonok, amikor új alkalmazások létrehozása.

Amikor létrehoz egy alkalmazást sablon, a meglévő alkalmazás a következő elemeket tartalmazza:

- Az alapértelmezett irányítópult, beleértve az irányítópult elrendezése és meghatározta az összes csempe.
- Eszköz sablonok, beleértve a mérések, beállítások, tulajdonságainak, parancsokat és irányítópult.
- A szabályok. Minden szabály definíciók szerepelnek. Művelet, kivéve az e-mail-műveletek, azonban nem tartalmaz.
- Eszköz beállítása, beleértve azok a feltételek és irányítópultokat.

> [!WARNING]
> Ha egy irányítópultot tartalmaz csempék, amelyek az adott eszközök információit jeleníti meg, akkor ezek a csempék megjelenítése **nem található a kért erőforrás** az új alkalmazás. Ezek a csempék eszközök információit jeleníti meg az új alkalmazás újra kell konfigurálnia.

Amikor létrehoz egy alkalmazást sablon, a következő elemeket nem tartalmazza:

- Eszközök
- Felhasználók
- Feladatdefiníciók
- Folyamatos adatexportálás definíciók

Manuálisan adja hozzá ezeket az elemeket a egy alkalmazást sablon alapján létrehozott alkalmazásokat.

## <a name="create-an-application-template"></a>Hozzon létre egy alkalmazást sablon

Egy alkalmazás-sablon létrehozása egy meglévő IoT Central-alkalmazást:

1. Nyissa meg a **felügyeleti** szakasz az alkalmazásban.
1. Válassza ki **alkalmazást sablon exportálása**.
1. Az a **alkalmazást sablon exportálása** lap, adja meg egy nevet és leírást a sablon.
1. Válassza ki a **exportálása** gombra az sablon létrehozásához. Ekkor átmásolhatja a **megosztható hivatkozás** , amely lehetővé teszi, hogy valaki egy új alkalmazás létrehozása a sablonból:

![Hozzon létre egy alkalmazást sablon](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Alkalmazás-sablonnal

Egy alkalmazás-sablon használatával hozzon létre egy új IoT Central-alkalmazást, szüksége egy korábban létrehozott **megosztható hivatkozás**. Illessze be a **megosztható hivatkozás** a böngésző címsorába. A **hozzon létre egy alkalmazást** weboldal megjelenik a kiválasztott egyéni alkalmazást sablon:

![Alkalmazás létrehozása sablon alapján](media/howto-use-app-templates/create-app.png)

Válassza ki a támogatási csomagot, és a többi mező az űrlap kitöltésekor. Válassza ki **létrehozás** hozhat létre egy új IoT Central-alkalmazást az alkalmazás sablonból.

## <a name="manage-application-templates"></a>Az alkalmazás kezelése

Az a **alkalmazást sablon exportálása** lapon törölheti, vagy az alkalmazás-sablon frissítéséhez.

Ha törli egy alkalmazás-sablon, új alkalmazások létrehozása már nem használhatja a korábban létrehozott megosztható hivatkozás.

Az alkalmazás-sablon frissítéséhez módosítsa a sablon neve vagy leírása a **alkalmazást sablon exportálása** lapot. Válassza ki a **exportálása** újra gombra. Ez a művelet létrehoz egy új **megosztható hivatkozás** és érvényteleníti minden előző **megosztható hivatkozás** URL-CÍMÉT.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan használható az alkalmazás, a javasolt következő lépésre megtudhatja, hogyan [kezelése IoT Central az Azure Portalról](howto-manage-iot-central-from-portal.md)
