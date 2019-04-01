---
title: Az Azure IoT OPC UA-Eszközfelügyelet |} A Microsoft Docs
description: Az OPC-Twin áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3bbc20aa61faafb1405fa756b334bae05ec4129b
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759257"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-device-management"></a>Mi az Azure IoT nyílt Platform kommunikáció (OPC) Eszközfelügyelet?
Az Azure IoT OPC UA Eszközfelügyelet, más néven a OPC Ikereszköz áll, amelyek az Azure IoT Edge és IoT Hub használata a felhőben és a gyári hálózathoz csatlakozni. Az OPC-Twin felderítési, regisztrációs és távvezérlési ipari eszközök REST API-kon keresztül biztosít. Az OPC-Twin nem szükséges egy OPC egyesített architektúra (OPC UA) SDK-t és a programozási nyelvtől és egy kiszolgáló nélküli munkafolyamatot tartalmazhat. Ez a cikk ismerteti az OPC-Twin több felhasználási eset.

## <a name="discovery-and-control"></a>Észlelés és vezérlés
Az OPC-Twin felderítési és a nyilvántartási egyszerű is használhat.

### <a name="simple-discovery-and-registration"></a>Egyszerű felderítési és regisztráció
Az OPC-Twin lehetővé teszi a feldolgozó kezelők a gyári hálózati megvizsgálja, hogy az OPC UA-kiszolgálók felderíthető és regisztrálva. Alternatív megoldásként a gyári operátorok manuálisan is regisztrálhatja az OPC UA-eszközök egy ismert felderítési URL-cím használatával. Például az összes OPC UA-eszköz csatlakozik az IoT Edge-átjáró OPC Ikereszköz-modullal a gyárban telepítése után, a gyári operátor távolról aktiválása keresését, a hálózati vizuálisan láthat és az OPC UA-kiszolgálók. 

### <a name="simple-control"></a>Egyszerű vezérlési
Az OPC-Twin lehetővé teszi a feldolgozó kezelők reagálhat rájuk, és automatikusan vagy manuálisan konfigurálja újra a felhőből emelet gyári gépeik menet közben. Az OPC-Twin el az OPC UA-kiszolgálóval a szolgáltatásokat, keresse meg a címtér olvasási/írási változók és a metódusok végrehajtása, a REST API-kat biztosít. Például egy gép hőmérséklet KPI-t használ a gyártósor vezérlésére. Hőmérséklet-érzékelő a módosítás közzéteszi az adatokat az OPC-közzétevő használatával. A gyári operátor megkapja a riasztást, hogy a hőmérséklet elérte a küszöbértéket. A gyártósor cools automatikusan az OPC-Twin keresztül. A gyári operátor értesítést kap a ritka elérésű le.

## <a name="authentication"></a>Authentication
Egyszerű hitelesítés és a egy egyszerű fejlesztői felületet biztosít az OPC-Twin is használhat.

### <a name="simple-authentication"></a>Egyszerű hitelesítés 
OPC Ikereszköz használja az Azure Active Directory (AAD) alapú hitelesítést és a teljes körű teljes naplózási. Például az OPC-Twin köszönhetően az alkalmazás kell meghatározni, hogy mi az operátornak hajtott végre egy gépen OPC Ikereszköz épült. A gép oldalon célszerű a naplózás az OPC UA segítségével. A felhő oldalon van való tárolásának egy napló nem módosítható ügyfél és az AAD-hitelesítés a REST API használatával.

### <a name="simple-developer-experience"></a>Egyszerű fejlesztői élmény 
Az OPC-Twin REST API-kon keresztül bármilyen programozási nyelven írt alkalmazások is használható. A fejlesztők beépíthetik OPC UA-ügyféllel egy megoldást, mert az OPC UA SDK ismerete már nem szükséges. Az OPC-Twin zökkenőmentesen integrálható az állapot nélküli, kiszolgáló nélküli architektúrák. Például egy teljes verem webfejlesztő, aki egy alkalmazást fejleszt, egy riasztás és az irányítópult a logika válaszadására a JavaScript- vagy TypeScript, C, ismerete nélkül OPC Ikereszköz használatával írhat a C#, vagy a teljes OPC UA-verem megvalósítás. 

## <a name="next-steps"></a>További lépések

Most, hogy az OPC-Twin és használata ismerkedtünk, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Mi az OPC-tároló](overview-opc-twin-architecture.md)