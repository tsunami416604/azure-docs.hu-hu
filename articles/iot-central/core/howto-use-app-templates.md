---
title: Alkalmazás-sablonok használata az Azure IoT Centralban | Microsoft Docs
description: Kezelőként, hogyan használhatók az eszközök az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982460"
---
# <a name="use-application-templates"></a>Alkalmazássablonok használata

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk bemutatja, hogyan hozhat létre és használhat alkalmazás-sablonokat a megoldás-kezelője.

Azure IoT Central-alkalmazás létrehozásakor választhat a beépített sablonok közül. Saját alkalmazásokat is létrehozhat meglévő IoT Central alkalmazásokból. Az új alkalmazások létrehozásakor saját alkalmazás-sablonjait is használhat.

Alkalmazás-sablon létrehozásakor a következő elemeket tartalmazza a meglévő alkalmazásból:

- Az alkalmazás alapértelmezett irányítópultja, beleértve az irányítópult elrendezését és az összes definiált csempét.
- Eszközök sablonjai, beleértve a méréseket, a beállításokat, a tulajdonságokat, a parancsokat és az irányítópultot.
- Szabályok. A rendszer minden szabály-definíciót tartalmaz. Az e-mail-műveletek kivételével azonban nem tartalmazza a műveleteket.
- Eszközök készletei, beleértve a feltételeket és az irányítópultokat.

> [!WARNING]
> Ha az irányítópult olyan csempéket tartalmaz, amelyek adott eszközökre vonatkozó információkat jelenítenek meg, akkor ezek a csempék **a kért erőforrást nem találhatók** az új alkalmazásban. Ezeket a csempéket újra kell konfigurálnia az új alkalmazás eszközeivel kapcsolatos információk megjelenítéséhez.

Alkalmazás sablonjának létrehozásakor az nem tartalmazza a következő elemeket:

- Eszközök
- Felhasználók
- Feladatdefiníciók
- Folyamatos adatexportálási definíciók

Ezeket az elemeket manuálisan adja hozzá az alkalmazás sablonból létrehozott összes alkalmazáshoz.

## <a name="create-an-application-template"></a>Alkalmazás sablonjának létrehozása

Alkalmazás sablonjának létrehozása meglévő IoT Central alkalmazásból:

1. Nyissa meg az alkalmazás **Adminisztráció** szakaszát.
1. Válassza az **alkalmazás sablon exportálás**lehetőséget.
1. Az **alkalmazás sablonjának exportálása** lapon adja meg a sablon nevét és leírását.
1. Válassza az **Exportálás** gombot az alkalmazás sablonjának létrehozásához. Most már másolhatja a **megosztható hivatkozást** , amely lehetővé teszi, hogy valaki új alkalmazást hozzon létre a sablonból:

![Alkalmazás sablonjának létrehozása](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Alkalmazás sablonjának használata

Ha új IoT Central alkalmazást szeretne létrehozni egy alkalmazás-sablonnal, egy korábban létrehozott **megosztható hivatkozásra**van szüksége. Illessze be a **megosztható hivatkozást** a böngésző címsorába. Megjelenik az **alkalmazás létrehozása** lap a kiválasztott egyéni alkalmazás sablonnal:

![Alkalmazás létrehozása sablonból](media/howto-use-app-templates/create-app.png)

Válassza ki a díjszabási tervet, és töltse ki a többi mezőt az űrlapon. Ezután a **Létrehozás** gombra kattintva hozzon létre egy új IoT Central alkalmazást az alkalmazás sablonból.

## <a name="manage-application-templates"></a>Alkalmazás-sablonok kezelése

Az **alkalmazás sablonjának exportálása** lapon törölheti vagy frissítheti az alkalmazás sablonját.

Ha töröl egy alkalmazást, már nem használhatja a korábban létrehozott megosztható hivatkozást új alkalmazások létrehozásához.

Az alkalmazás sablonjának frissítéséhez módosítsa a sablon nevét vagy leírását az **alkalmazás sablon exportálása** lapon. Ezután kattintson ismét az **Exportálás** gombra. Ez a művelet létrehoz egy új **megosztható hivatkozást** , és érvényteleníti az előző **megosztható hivatkozás** URL-címét.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az alkalmazás sablonjainak használatát, a javasolt következő lépés a [IoT Central kezelésének megismerése a Azure Portal](howto-manage-iot-central-from-portal.md)
