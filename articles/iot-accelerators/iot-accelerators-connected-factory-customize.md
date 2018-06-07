---
title: A csatlakoztatott gyári megoldás - Azure testreszabása |} Microsoft Docs
description: Hogyan szabhatja testre a csatlakoztatott gyári megoldásgyorsító viselkedésének leírása.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: e91f36c9d6f0cb3195e6903d55cd676f1e9ccf5a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626550"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Testre szabhatja, hogy a csatlakoztatott gyári megoldás OPC EE-kiszolgálóinak adatait jeleníti meg

A csatlakoztatott gyári megoldás összesíti, és a megoldás kapcsolódó OPC EE-kiszolgálók adatait jeleníti meg. Keresse meg, és parancsainak elküldését a OPC EE-kiszolgálók a megoldásban. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.

Összesített adatai a megoldás például a teljes berendezések hatékonyságát (OEE) és fő teljesítménymutatók (KPI-k), megtekinthető az irányítópulton a gyári, a sor és az állomás szintjén. Az alábbi képernyőfelvételen látható OEE és KPI értékeit a **szerelvény** állomás, a **termelési sor 1**, a a **München** gyári:

![A megoldás OEE és a KPI értékének – példa][img-oee-kpi]

A megoldás lehetővé teszi a OPC EE-kiszolgálókról, úgynevezett adott adatelemek részletes információk megtekintése *állomások*. Az alábbi képernyőfelvételen látható függvényében egy adott állomásról előállított elemek száma:

![Felvétel az előállított elemek száma.][img-manufactured-items]

A diagramokon választásakor ismerheti meg az adatokat további időt adatsorozat Insights (ÁME):

![Adatokba idő adatsorozat Insights segítségével][img-tsi]

Ez a cikk ismerteti:

- Hogyan adatokat szeretné elérhetővé tenni a különböző nézetek a megoldásban.
- Hogyan szabhatja testre a módját a megoldás adatait jeleníti meg.

## <a name="data-sources"></a>Adatforrások

A csatlakoztatott gyári megoldás a megoldás kapcsolódó OPC EE-kiszolgálók adatait jeleníti meg. Az alapértelmezett telepítés magában foglalja a gyári szimuláció futtató több OPC EE. A saját OPC EE-kiszolgálót is hozzáadhat, amelyek [átjárón keresztül csatlakozni] [ lnk-connect-cf] a megoldáshoz.

A csatlakoztatott OPC EE-kiszolgáló küldhet a megoldás az irányítópulton adatelemek tallózással:

1. Válasszon **böngésző** lehetőségre, és navigáljon a **jelöljön ki egy OPC EE** megtekintése:

    ![Navigáljon a válassza egy OPC EE-kiszolgáló megtekintése][img-select-server]

1. Válasszon kiszolgálót, majd kattintson a **Connect**. Kattintson a **folytatása** Ha a biztonsági figyelmeztetés jelenik meg.

    > [!NOTE]
    > Ez a figyelmeztetés csak egyszer jelenik meg minden olyan kiszolgálón, és a megoldás irányítópult és a kiszolgáló közötti megbízhatósági kapcsolatot létesít.

1. A kiszolgáló elküldheti a megoldáshoz adatelemek tallózhatnak. A megoldáshoz küldött elemek be van jelölve:

    ![Közzétett elemek][img-published]

1. Ha Ön egy *rendszergazda* a megoldást választhatja ki elérhetővé a csatlakoztatott gyári megoldásban adatelemet közzététele. A rendszergazdák is adatelemek értékének módosítása és hívható meg metódus a OPC EE-kiszolgálón.

## <a name="map-the-data"></a>Az adatok leképezése

A csatlakoztatott gyári megoldás le, és a közzétett adatelemeket összesíti a OPC EE-kiszolgálóról a különböző nézetek a megoldásban. Ha a megoldás a csatlakoztatott gyári megoldás központilag telepíti az Azure-fiókjával. A JSON-fájl, a Visual Studio csatlakoztatott gyári megoldásban ez leképezési információkat tárolja. Megtekintheti és módosíthatja a JSON-konfigurációs fájlt a csatlakoztatott gyári Visual Studio-megoldásban. A módosítást követően központilag telepítheti a megoldás.

A konfigurációs fájlt is használhatja:

- A meglévő szimulált előállítók, éles sorok és állomások szerkesztése.
- Valós OPC EE-kiszolgálókhoz, amelyek a megoldással az adatok leképezése.

Leképezési, és az adatokat az adott igényeknek összesítése kapcsolatos további információkért lásd: [konfigurálása a csatlakoztatott gyári megoldásgyorsító ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>A módosítások telepítése

Miután végzett a módosítások elvégzése az **ContosoTopologyDescription.json** fájlt, újra kell telepítenie a csatlakoztatott gyári megoldás az Azure-fiókjába.

A **azure iot-csatlakoztatott-gyári** tárház tartalmaz egy **build.ps1** PowerShell parancsprogram, amellyel újraépítéséhez és a megoldás üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések

További tudnivalók a kapcsolódó gyári megoldásgyorsító elolvasni a következő cikkeket:

* [A csatlakoztatottgyár-alapú megoldásgyorsító bemutatója][lnk-rm-walkthrough]
* [Telepítsen egy átjárót a beépített csatlakoztatva][lnk-connect-cf]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]
* [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md)
* [GYAKORI KÉRDÉSEK][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md