---
title: Azure IoT Central alkalmazás exportálása | Microsoft dokumentumok
description: Megoldáskezelőként szeretnék exportálni egy alkalmazássablont, hogy újra felhasználhassam.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157617"
---
# <a name="export-your-application"></a>Az alkalmazás exportálása



Ez a cikk ismerteti, hogyan, a megoldás kezelője, az IoT Central-alkalmazás exportálásához, hogy képes legyen újra feldolgozni.

Erre két lehetősége van:

- Létrehozhat egy másolatot az alkalmazásról, ha csak egy másolatot kell létrehoznia az alkalmazásról.
- Ha több példányt kíván létrehozni, az alkalmazásból is létrehozhat alkalmazássablont.

## <a name="copy-your-application"></a>Az alkalmazás másolása

Bármely alkalmazásról létrehozhat másolatot, levonva belőle az eszközpéldányokat, az eszközadat-előzményeket és a felhasználói adatokat. A másolat egy szabványos díjszabási csomagot használ, amelyért fizetni fog. Nem hozhat létre olyan alkalmazást, amely az ingyenes díjszabási csomagot használja egy alkalmazás másolásával.

Válassza a **Másolás** lehetőséget. A párbeszédpanelen adja meg az új alkalmazás adatait. Ezután a **Másolás gombra** a folytatás megerősítéséhez kattintson a másolás gombra. Ha többet szeretne tudni az űrlap mezőiről, olvassa el az [Alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutató című témakört.

![Alkalmazásbeállítások lap](media/howto-use-app-templates/appcopy2.png)

Miután az alkalmazás másolási művelete sikeres volt, a hivatkozás használatával navigálhat az új alkalmazáshoz.

![Alkalmazásbeállítások lap](media/howto-use-app-templates/appcopy3a.png)

Az alkalmazás másolása a szabályok és az e-mail művelet definícióját is másolja. Egyes műveletek, például a Flow és a Logic Apps, a szabályazonosítón keresztül meghatározott szabályokhoz vannak kötve. Ha egy szabályt egy másik alkalmazásba másol, saját szabályazonosítót kap. Ebben az esetben a felhasználóknak létre kell hozniuk egy új műveletet, majd hozzá kell rendelniük az új szabályt. Általánosságban elmondható, hogy érdemes ellenőrizni a szabályokat és műveleteket, hogy megbizonyosodjon arról, hogy azok naprakészek az új alkalmazásban.

> [!WARNING]
> Ha egy irányítópult olyan csempéket tartalmaz, amelyek adott eszközökre vonatkozó információkat jelenítenek meg, akkor ezek a csempék **a Kért erőforrás nem találhatók** az új alkalmazásban. Újra kell konfigurálnia ezeket a csempéket, hogy az új alkalmazásban lévő eszközökre vonatkozó információkat jelenítsen meg.

## <a name="create-an-application-template"></a>Alkalmazássablon létrehozása

Amikor létrehoz egy Azure IoT Central-alkalmazást, beépített mintasablonok közül választhat. Saját alkalmazássablonokat is létrehozhat a meglévő IoT Central-alkalmazásokból. Ezután használhatja a saját alkalmazássablonokat, amikor új alkalmazásokat hoz létre.

Alkalmazássablon létrehozásakor a következő elemeket tartalmazza a meglévő alkalmazásból:

- Az alapértelmezett alkalmazás-irányítópult, beleértve az irányítópult elrendezését és az összes definiált csempét.
- Eszközsablonok, beleértve a méréseket, a beállításokat, a tulajdonságokat, a parancsokat és az irányítópultot.
- Szabályok. Minden szabálydefiníció szerepel. Az e-mailes műveletek kivételével azonban a műveletek nem szerepelnek.
- Eszközkészletek, beleértve azok feltételeit és irányítópultjait.

> [!WARNING]
> Ha egy irányítópult olyan csempéket tartalmaz, amelyek adott eszközökre vonatkozó információkat jelenítenek meg, akkor ezek a csempék **a Kért erőforrás nem találhatók** az új alkalmazásban. Újra kell konfigurálnia ezeket a csempéket, hogy az új alkalmazásban lévő eszközökre vonatkozó információkat jelenítsen meg.

Alkalmazássablon létrehozásakor az nem tartalmazza a következő elemeket:

- Eszközök
- Felhasználók
- Feladatdefiníciók
- Folyamatos adatexportálási definíciók

Adja hozzá ezeket az elemeket manuálisan az alkalmazássablonból létrehozott alkalmazásokhoz.

Alkalmazássablon létrehozása meglévő IoT Central-alkalmazásból:

1. Nyissa meg az **alkalmazás Adminisztráció** szakaszát.
1. Válassza **az Alkalmazássablon exportálása**lehetőséget.
1. Az **Alkalmazássablon exportálása** lapon adja meg a sablon nevét és leírását.
1. Az alkalmazássablon létrehozásához kattintson az **Exportálás** gombra. Most már másolhatja a **megosztható hivatkozást,** amely lehetővé teszi, hogy valaki új alkalmazást hozzon létre a sablonból:

![Alkalmazássablon létrehozása](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Alkalmazássablon használata

Ha alkalmazássablont szeretne használni egy új IoT Central alkalmazás létrehozásához, egy korábban létrehozott **megosztható hivatkozásra**van szükség. Illessze be a **megosztható hivatkozást** a böngésző címsorába. Az **Alkalmazás létrehozása** lap az egyéni alkalmazássablon kijelölésével jelenik meg:

![Alkalmazás létrehozása sablonból](media/howto-use-app-templates/create-app.png)

Válassza ki az árképzési tervet, és töltse ki az űrlap többi mezőjét. Ezután válassza **a Létrehozás** lehetőséget egy új IoT Central alkalmazás létrehozásához az alkalmazássablonból.

### <a name="manage-application-templates"></a>Alkalmazássablonok kezelése

Az **Alkalmazássablon exportálása** lapon törölheti vagy frissítheti az alkalmazássablont.

Ha töröl egy alkalmazássablont, a továbbiakban nem használhatja a korábban létrehozott megosztható hivatkozást új alkalmazások létrehozásához.

Az alkalmazássablon frissítéséhez módosítsa a sablon nevét vagy leírását az **Alkalmazássablon exportálása** lapon. Ezután ismét válassza az **Exportálás** gombot. Ez a művelet új **megosztható hivatkozást** hoz létre, és érvényteleníti a korábbi **megosztható hivatkozás** URL-címét.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az alkalmazássablonok használatát, a javasolt következő lépés az [IoT Central kezelése az Azure Portalról](howto-manage-iot-central-from-portal.md)
