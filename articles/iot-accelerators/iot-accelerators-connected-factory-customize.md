---
title: Testre szabhatja a csatlakoztatott gyár megoldás – Azure |} A Microsoft Docs
description: Hogyan szabhatja testre az Okosgyár-megoldásgyorsító viselkedésének leírása.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010072"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Testre szabhatja, hogy a csatlakoztatott gyár megoldás az OPC UA-kiszolgálók adatait jeleníti meg

A csatlakoztatott gyár megoldás összesíti, és a megoldáshoz csatlakoztatott OPC UA-kiszolgálók adatait jeleníti meg. Keresse meg, és parancsokat küldjön az OPC UA-kiszolgálók a megoldásban. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.

A megoldás az összesített adatok közé tartoznak az általános berendezések hatékonyságát (OEE) és a fő teljesítménymutatók (KPI-k), amely a factory, a sor és az állomás szinten az irányítópult megtekintéséhez. Az alábbi képernyőfelvételen az OEE és KPI értékeit a **szerelvény** az állomás **gyártósor 1**, a a **München** gyári:

![OEE és KPI mutatókra, a megoldás – példa][img-oee-kpi]

A megoldás lehetővé teszi, hogy az adott adatelemek nevű OPC UA-kiszolgálók a részletes információk megtekintéséhez *állomások*. Az alábbi képernyőfelvételen a grafikon, egy adott állomásról előállított elemek száma:

![Feldolgozott elemek száma a grafikon][img-manufactured-items]

Ha rákattint az egyik a gráfok, feltárhatja az adatokat, továbbá a Time Series Insights (TSI) használatával:

![Fedezze fel az adatokat a Time Series Insights használatával][img-tsi]

Ez a cikk ismerteti:

- Hogyan az adatok legyen elérhető a különböző nézetek a megoldásban.
- Hogyan szabhatja testre a megoldás módja adatait jeleníti meg.

## <a name="data-sources"></a>Adatforrások

A csatlakoztatott gyár megoldás a megoldáshoz csatlakoztatott OPC UA-kiszolgálók adatait jeleníti meg. Az alapértelmezett telepítés magában foglalja a több OPC UA-kiszolgálók egy gyári szimuláció futtatása. Saját OPC UA-kiszolgálókat is hozzáadhat, amelyek [átjárón keresztül csatlakozzon] [ lnk-connect-cf] a megoldáshoz.

Egy csatlakoztatott OPC UA-kiszolgálóval küldhet a az irányítópulton lévő megoldás által megnyithatja:

1. Válasszon **böngésző** navigálhat a **OPC UA-kiszolgáló kiválasztása** megtekintése:

    ![Keresse meg a Select egy OPC UA-kiszolgáló megtekintése][img-select-server]

1. Válasszon kiszolgálót, majd kattintson a **Connect**. Kattintson a **folytatása** amikor a biztonsági figyelmeztetés jelenik meg.

    > [!NOTE]
    > Ez a figyelmeztetés csak egyszer jelenik meg minden olyan kiszolgáló esetén, és a megoldás irányítópultján, és a kiszolgáló közötti megbízhatósági kapcsolatot hoz létre.

1. Tallózhat az adatelemek, amely a kiszolgáló elküldheti a megoldáshoz. A megoldás elküldött elemek be van jelölve:

    ![Közzétett elemeket][img-published]

1. Ha Ön egy *rendszergazda* a megoldást, és választhatja ki közzététele egy adatelemet, hogy a csatlakoztatott gyár megoldás érhető el. A rendszergazdák is módosítsa az értéket adatelemek és metódusokat hívja meg az OPC UA-kiszolgálóval.

## <a name="map-the-data"></a>Az adatok leképezése

A csatlakoztatott gyár megoldás vannak leképezve, és a közzétett adatelemeket a forrásrendszerektől összegyűjtött és összegzett OPC UA-kiszolgáló a megoldásban a különböző nézetek. A megoldás üzembe helyezésekor a csatlakoztatott gyár megoldás üzembe helyezi az Azure-fiókjával. Egy JSON-fájlt a Visual Studio csatlakoztatott gyár megoldás tárolja a megfeleltetési adatokat. Megtekintheti, és módosítsa a JSON konfigurációs fájlt a csatlakoztatott gyári Visual Studio-megoldás. Ugyanakkor a megoldást, a módosítás végrehajtása után.

A konfigurációs fájlt használhatja:

- A meglévő szimulált gyárak, gyártósorok és állomások szerkesztése.
- Térkép a megoldáshoz csatlakozó valós OPC UA-kiszolgálók adatait.

Leképezési és összesíti az adatokat az adott igényeknek kapcsolatos további információkért lásd: [konfigurálása az Okosgyár-megoldásgyorsító ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>A módosítások üzembe helyezése

Miután végzett, a módosításokat a **ContosoTopologyDescription.json** fájlt, újra kell telepítenie a csatlakoztatott gyár megoldás az Azure-fiókjába.

A **azure-iot-connected-factory** tárház tartalmaz egy **build.ps1** PowerShell-parancsprogram, amellyel újraépítéséhez és a megoldás üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések

További információ az Okosgyár-megoldásgyorsító a következő cikkek elolvasásával:

* [A csatlakoztatottgyár-alapú megoldásgyorsító bemutatója][lnk-rm-walkthrough]
* [Átjáró telepítése csatlakoztatott gyárhoz][lnk-connect-cf]
* [Engedélyek az azureiotsolutions.com webhelyen][lnk-permissions]
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