---
title: A Csatlakoztatott gyármegoldás testreszabása - Azure | Microsoft dokumentumok
description: A Csatlakoztatott gyári megoldásgyorsító viselkedésének testreszabása.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080486"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>A Csatlakoztatott gyári megoldás adatok megjelenítésének testreszabása az OPC UA-kiszolgálókról

A Connected Factory megoldás összesíti és megjeleníti a megoldáshoz csatlakoztatott OPC UA-kiszolgálók adatait. A megoldásban tallózhat és parancsokat küldhet az OPC UA-kiszolgálóinak. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.

A megoldás összesített adatai közé tartozik például a teljes berendezéshatékonyság (OEE) és a fő teljesítménymutatók (KPI-k), amelyeket gyári, vonal- és állomásszintekirányítópultján tekinthet meg. Az alábbi képernyőképen az **összeszerelő** állomás OEE- és KPI-értékei láthatók az **1.** **Munich**

![Példa oee- és KPI-értékekre a megoldásban][img-oee-kpi]

A megoldás lehetővé teszi az OPC UA-kiszolgálók, az úgynevezett állomások részletes adatainak *megtekintését.* A következő képernyőképen egy adott állomásról gyártott elemek száma látható:

![A gyártott cikkek számának mintaszáma][img-manufactured-items]

Ha az egyik grafikonra kattint, a Time Series Insights (TSI) használatával tovább fedezheti fel az adatokat:

![Adatok feltárása a Time Series Insights használatával][img-tsi]

Ez a cikk a következőket ismerteti:

- Hogyan érhető el az adatok a megoldás különböző nézetei számára.
- Hogyan szabhatja testre a megoldás adatok megjelenítésének módját.

## <a name="data-sources"></a>Adatforrások

A Connected Factory megoldás a megoldáshoz csatlakoztatott OPC UA-kiszolgálók adatait jeleníti meg. Az alapértelmezett telepítés több, gyári szimulációt futtató OPC UA-kiszolgálót tartalmaz. Hozzáadhatja saját OPC UA-kiszolgálóit, amelyek [átjárón keresztül csatlakoznak][lnk-connect-cf] a megoldáshoz.

Az irányítópulton tallózhat a csatlakoztatott OPC UA-kiszolgáló által a megoldásnak küldhető adatelemek között:

1. Válassza a **Böngésző** lehetőséget az **OPC UA-kiszolgáló kiválasztása nézetre** való navigáláshoz:

    ![Navigálás az OPC UA-kiszolgáló kiválasztása nézetre][img-select-server]

1. Jelöljön ki egy kiszolgálót, és kattintson a **Csatlakozás gombra.** Kattintson a **Folytatás gombra,** amikor megjelenik a biztonsági figyelmeztetés.

    > [!NOTE]
    > Ez a figyelmeztetés csak egyszer jelenik meg minden kiszolgálón, és megbízhatósági kapcsolatot hoz létre a megoldás irányítópultja és a kiszolgáló között.

1. Most már tallózhat az adatelemek között, amelyeket a kiszolgáló elküldhet a megoldásnak. A megoldásra küldött elemek be vannak jelölve:

    ![Közzétett elemek][img-published]

1. Ha Ön *rendszergazda* a megoldásban, közzétehet egy adatelemet, hogy elérhetővé tegye a Csatlakoztatott gyári megoldásban. Rendszergazdaként módosíthatja az adatelemek és a hívási módszerek értékét az OPC UA-kiszolgálón is.

## <a name="map-the-data"></a>Az adatok leképezése

A Connected Factory megoldás leképezi és összesíti a közzétett adatelemeket az OPC UA-kiszolgálóról a megoldás különböző nézeteire. A Connected Factory megoldás üzembe helyezi az Azure-fiók, amikor a megoldás kiépítése. A Visual Studio Connected Factory megoldásban található JSON-fájl tárolja ezt a leképezési információt. Ezt a JSON konfigurációs fájlt a Connected Factory Visual Studio megoldásban tekintheti meg és módosíthatja. A módosítás után újratelepítheti a megoldást.

A konfigurációs fájl segítségével:

- A meglévő szimulált gyárak, gyártósorok és állomások szerkesztése.
- A megoldáshoz csatlakozó valós OPC UA-kiszolgálók adatainak leképezése.

Az adatok nak az adott követelményeknek való megfelelés érdekében történő leképezéséről és összesítéséről [a Csatlakoztatott gyári megoldásgyorsító konfigurálása ](iot-accelerators-connected-factory-configure.md)című témakörben talál további információt.

## <a name="deploy-the-changes"></a>A módosítások telepítése

Ha befejezte a **ContosoTopologyDescription.json** fájl módosításait, újra telepítenie kell a Connected Factory megoldást az Azure-fiókjába.

Az **azure-iot-connected-factory** repository tartalmaz egy **build.ps1** PowerShell-parancsfájlt, amelya megoldás újraépítéséhez és üzembe helyezéséhez használható.

## <a name="next-steps"></a>Következő lépések

A Connected Factory megoldásgyorsítóról az alábbi cikkekben olvashat bővebben:

* [Engedélyek a azureiotsolutions.com webhelyen][lnk-permissions]
* [Csatlakoztatott gyári gyakori kérdések](iot-accelerators-faq-cf.md)
* [Gyik][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md