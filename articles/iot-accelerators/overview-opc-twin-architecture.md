---
title: Az OPC-Twin-architektúra – Azure |} A Microsoft Docs
description: Az OPC-Twin-architektúra
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 002e2e06201688638df98e16c45282187f593a3a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444307"
---
# <a name="opc-twin-architecture"></a>Az OPC-Twin-architektúra

Az alábbi ábrák bemutatják az OPC-Twin-architektúra.

## <a name="discover-and-activate"></a>Fedezze fel és aktiválása

1. A kezelő lehetővé teszi, hogy a modul a hálózatvizsgálati, vagy egy felderítési URL-címet használó felderítés egyszeri felderítés lehetővé teszi. A felderített végpontok és az alkalmazással kapcsolatos adatok érkeznek keresztül telemetriai adatokat a bevezetési ügynök feldolgozásra.  Az OPC UA-eszköz bevezetési ügynök OPC UA-kiszolgáló felderítési eseményeit, felderítési vagy vizsgálat módban, amely OPC Ikereszköz IoT Edge-modul által küldött dolgozza fel. A felderítési események eredmény az alkalmazás regisztrálása és az OPC UA-jegyzékben lévő frissítéseket.

   ![Az OPC-Twin működése](media/overview-opc-twin-architecture/opc-twin1.png)

1. Az operátor megvizsgálja a tanúsítványt a felderített végpont, és aktiválja a regisztrált végpont iker, a hozzáféréshez. 

   ![Az OPC-Twin működése](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Tallózással keresse meg és monitorozása

1. Aktiválta, miután az üzemeltető használatával az Ikereszköz-szolgáltatás REST API keresse meg vagy megvizsgálhatja a kiszolgálómodellt adatokat, olvasási/írási objektum változók és metódusok meghívása.  A felhasználó egy egyszerűsített OPC UA API HTTP vagy JSON teljes kifejezett használ.

   ![Az OPC-Twin működése](media/overview-opc-twin-architecture/opc-twin3.png)

1. Az ikereszköz szolgáltatás REST-felület is használható az OPC-közzétevő figyelt elemek és -előfizetések létrehozására. Az OPC-közzétevő lehetővé teszi, hogy a telemetria OPC UA-kiszolgálórendszerekkel küldését az IoT hubnak. Az OPC-közzétevő kapcsolatos további információkért lásd: [Mi az az OPC-közzétevő](overview-opc-publisher.md).

   ![Az OPC-Twin működése](media/overview-opc-twin-architecture/opc-twin4.png)
