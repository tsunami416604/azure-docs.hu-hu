---
title: Azure IoT Central-alkalmazás exportálása | Microsoft Docs
description: Megoldás-kezelőként exportálni szeretném az alkalmazás sablonját, hogy újra lehessen használni.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81e24712de186e2d5ec03e33ecabe485cc0d9f31
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894759"
---
# <a name="export-your-application-preview-features"></a>Alkalmazás exportálása (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk azt ismerteti, hogyan lehet a megoldás-kezelőként exportálni egy IoT Central alkalmazást, hogy újra lehessen használni.

Erre két lehetősége van:

- Létrehozhat egy másolatot az alkalmazásról, ha csak egy másolatot kell készítenie az alkalmazásról.
- Ha több példányt szeretne létrehozni, létrehozhat egy alkalmazás-sablont az alkalmazásból.

## <a name="copy-your-application"></a>Az alkalmazás másolása

Létrehozhat egy másolatot bármely alkalmazásról, levonva az eszköz példányait, az eszköz adatelőzményeit és a felhasználói adatmennyiségeket. A másolás egy utólagos elszámolású alkalmazás, amelyet fizetnie kell. Egy alkalmazás másolásával nem hozhat létre próbaverziós alkalmazást.

Válassza a **Másolás**lehetőséget. A párbeszédpanelen adja meg az új utólagos elszámolású alkalmazás részleteit. Ezután a **Másolás** gombra kattintva erősítse meg, hogy folytatni kívánja. Ha többet szeretne megtudni az űrlap mezőiről, tekintse meg az [alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutatót.

![Alkalmazásbeállítások lap](media/howto-use-app-templates/appcopy2.png)

Az alkalmazás másolási műveletének sikerességét követően az új alkalmazásra kattintva megnyithatja a hivatkozást.

![Alkalmazásbeállítások lap](media/howto-use-app-templates/appcopy3a.png)

Az alkalmazások másolása a szabályok és az e-mail művelet definícióját is másolja. Bizonyos műveletek, például a flow és a Logic Apps a szabály-AZONOSÍTÓn keresztül vannak társítva meghatározott szabályokhoz. Ha egy szabályt egy másik alkalmazásba másolnak, az a saját szabály AZONOSÍTÓját kapja meg. Ebben az esetben a felhasználóknak új műveletet kell létrehozniuk, majd hozzá kell rendelniük az új szabályt. Általánosságban is érdemes ellenőrizni a szabályokat és a műveleteket, hogy biztosan naprakészek legyenek az új alkalmazásban.

> [!WARNING]
> Ha az irányítópult olyan csempéket tartalmaz, amelyek adott eszközökre vonatkozó információkat jelenítenek meg, akkor ezek a csempék **a kért erőforrást nem találhatók** az új alkalmazásban. Ezeket a csempéket újra kell konfigurálnia az új alkalmazás eszközeivel kapcsolatos információk megjelenítéséhez.

## <a name="create-an-application-template"></a>Alkalmazás sablonjának létrehozása

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

Alkalmazás sablonjának létrehozása meglévő IoT Central alkalmazásból:

1. Nyissa meg az alkalmazás **Adminisztráció** szakaszát.
1. Válassza az **alkalmazás sablon exportálás**lehetőséget.
1. Az **alkalmazás sablonjának exportálása** lapon adja meg a sablon nevét és leírását.
1. Válassza az **Exportálás** gombot az alkalmazás sablonjának létrehozásához. Most már másolhatja a **megosztható hivatkozást** , amely lehetővé teszi, hogy valaki új alkalmazást hozzon létre a sablonból:

![Alkalmazás sablonjának létrehozása](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Alkalmazás sablonjának használata

Ha új IoT Central alkalmazást szeretne létrehozni egy alkalmazás-sablonnal, egy korábban létrehozott **megosztható hivatkozásra**van szüksége. Illessze be a **megosztható hivatkozást** a böngésző címsorába. Megjelenik az **alkalmazás létrehozása** lap a kiválasztott egyéni alkalmazás sablonnal:

![Alkalmazás létrehozása sablonból](media/howto-use-app-templates/create-app.png)

Válassza ki a fizetési tervet, és töltse ki a többi mezőt az űrlapon. Ezután a **Létrehozás** gombra kattintva hozzon létre egy új IoT Central alkalmazást az alkalmazás sablonból.

### <a name="manage-application-templates"></a>Alkalmazás-sablonok kezelése

Az **alkalmazás sablonjának exportálása** lapon törölheti vagy frissítheti az alkalmazás sablonját.

Ha töröl egy alkalmazást, már nem használhatja a korábban létrehozott megosztható hivatkozást új alkalmazások létrehozásához.

Az alkalmazás sablonjának frissítéséhez módosítsa a sablon nevét vagy leírását az **alkalmazás sablon exportálása** lapon. Ezután kattintson ismét az **Exportálás** gombra. Ez a művelet létrehoz egy új **megosztható hivatkozást** , és érvényteleníti az előző **megosztható hivatkozás** URL-címét.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az alkalmazás sablonjainak használatát, a javasolt következő lépés a [IoT Central kezelésének megismerése a Azure Portal](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
