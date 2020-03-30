---
title: OPC Twin architektúra - Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az OPC Twin architektúráról. A kiszolgáló felderítéséről, aktiválásáról, böngészéséről és figyeléséről ismertet.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819855"
---
# <a name="opc-twin-architecture"></a>OPC Twin architektúra

Az alábbi ábrák az OPC Twin architektúrát mutatják be.

## <a name="discover-and-activate"></a>Felfedezés és aktiválás

1. Az üzemeltető engedélyezi a hálózati beolvasást a modulon, vagy egyszeri felderítést tesz lehetővé felderítési URL használatával. A felderített végpontok és az alkalmazás adatait telemetriai adatokon keresztül küldi el a bevezetési ügynök feldolgozásra.  Az OPC UA eszköz bevezetési ügynöke feldolgozza az OPC UA-kiszolgáló felderítési eseményeit, amelyeket az OPC Twin IoT Edge modul küldött felderítési vagy betöltési módban. A felderítési események alkalmazásregisztrációt és frissítéseket eredményeznek az OPC UA-eszköz beállításjegyzékében.

   ![Hogyan működik az OPC Twin?](media/overview-opc-twin-architecture/opc-twin1.png)

1. Az operátor megvizsgálja a felderített végpont tanúsítványát, és aktiválja a regisztrált végpont ikerhozzáférést. 

   ![Hogyan működik az OPC Twin?](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Böngészés és figyelés

1. Aktiválásután az operátor használhatja a Twin service REST API-t a kiszolgálóinformációs modell tallózásához vagy ellenőrzéséhez, az objektumváltozók olvasásához/írásához és a hívási módszerekhez.  A felhasználó egyszerűsített OPC UA API-t használ, amelyet teljes mértékben http-ben és JSON-ban fejeznek ki.

   ![Hogyan működik az OPC Twin?](media/overview-opc-twin-architecture/opc-twin3.png)

1. A twin service REST felület is használható figyelt elemek és előfizetések létrehozásához az OPC Publisher. Az OPC Publisher lehetővé teszi, hogy telemetriát kell küldeni az OPC UA kiszolgálórendszerek ioT Hub. Az OPC Publisherről a [Mi az OPC Publisher](overview-opc-publisher.md)című témakörben talál további információt.

   ![Hogyan működik az OPC Twin?](media/overview-opc-twin-architecture/opc-twin4.png)
