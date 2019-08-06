---
title: Mi az OPC Twin – Azure | Microsoft Docs
description: Az OPC Twin áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c0d824e23a98aa14081fbd21bd6a9fbec5d583e0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815940"
---
# <a name="what-is-opc-twin"></a>Mi az OPC Twin?

Az OPC Twin olyan szolgáltatásokból áll, amelyek Azure IoT Edge és IoT Hub használnak a felhő és a gyári hálózat összekapcsolásához. Az OPC Twin a REST API-kon keresztül biztosítja az ipari eszközök felderítését, regisztrálását és távvezérlését. Az OPC Twin nem igényel egy OPC Unified Architecture (OPC UA) SDK-t, a programozási nyelv agnosztikus, és a kiszolgáló nélküli munkafolyamatban is szerepelhet. Ez a cikk több OPC kettős használati esetet ismertet.

## <a name="discovery-and-control"></a>Felderítés és vezérlés
A felderítéshez és a regisztráláshoz egyszerűen használhatja az OPC Twin-et is.

### <a name="simple-discovery-and-registration"></a>Egyszerű felderítés és regisztráció
Az OPC Twin lehetővé teszi, hogy a gyári operátorok beszkennelik a gyári hálózatot, így az OPC UA-kiszolgálókat fel lehet deríteni és regisztrálni kell. Alternatív megoldásként a gyári operátorok manuálisan is regisztrálhatják az OPC UA-eszközöket egy ismert felderítési URL-címmel. Ha például az összes OPC UA-eszközhöz szeretne csatlakozni az OPC Twin modullal rendelkező IoT Edge átjáró után, a gyári operátor távolról elindíthatja a hálózat vizsgálatát, és vizuálisan megtekintheti az összes OPC UA-kiszolgálót. 

### <a name="simple-control"></a>Egyszerű vezérlés
Az OPC Twin lehetővé teszi, hogy a gyári operátorok reagálni tudjanak az eseményekre, és a felhőből automatikusan, vagy menet közben újra konfigurálhatják a gyári padló gépeket. Az OPC Twin olyan REST API-kat biztosít, amelyekkel szolgáltatásokat hívhat meg az OPC UA-kiszolgálón, böngészheti a címtartomány, valamint az írási/olvasási változók és a metódusok végrehajtása. A kazán például hőmérsékleti KPI-t használ az üzemi vonal szabályozására. A hőmérséklet-érzékelő közzéteszi az adatváltozást az OPC-közzétevő használatával. A gyári operátor fogadja a riasztást, hogy a hőmérséklet elérte a küszöbértéket. Az üzemi sor automatikusan lehűti az OPC Twin-en keresztül. A gyári operátor értesítést kap a lehűtésről.

## <a name="authentication"></a>Authentication
Az OPC Twin egyszerű hitelesítésre és egyszerű fejlesztői élményre is használható.

### <a name="simple-authentication"></a>Egyszerű hitelesítés 
Az OPC Twin Azure Active Directory (HRE) alapú hitelesítést és naplózást használ teljes körűen. Az OPC Twin például lehetővé teszi, hogy az alkalmazás az OPC Twin-re legyen építve, hogy meghatározza, milyen műveletet hajtottak végre a gépen. A gép oldalán az OPC UA-naplózást hajtja végre. A felhős oldalon egy nem módosítható ügyfél-naplózási napló és HRE hitelesítés tárolása a REST API.

### <a name="simple-developer-experience"></a>Egyszerű fejlesztői élmény 
Az OPC Twin a REST API-kon keresztül bármilyen programozási nyelven írt alkalmazásokkal használható. Mivel a fejlesztők egy OPC UA-ügyfelet integrálnak egy megoldásba, nem szükséges az OPC UA SDK ismerete. Az OPC Twin zökkenőmentesen integrálható állapot nélküli, kiszolgáló nélküli architektúrába. Például egy teljes stack webfejlesztő, aki egy riasztási és esemény-irányítópultot fejleszt, megírhatja a logikát, hogy válaszoljon a JavaScript vagy az írógéppel típusú eseményekre, a C C#ismerete nélkül, vagy a teljes OPC ua stack implementációt. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC Twin és annak felhasználási lehetőségeit, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Mi az OPC-tároló?](overview-opc-vault.md)
