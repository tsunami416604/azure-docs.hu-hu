---
title: Az Azure Service Fabric Mesh áttekintése | Microsoft Docs
description: Az Azure Service Fabric Mesh bemutatása. A Service Fabric Mesh használatával anélkül helyezheti üzembe és skálázhatja az alkalmazást, hogy aggódnia kellene az alkalmazás infrastrukturális igényei miatt.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d315ca0702b1d76e0f990d4d33a3807a1dc57935
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428190"
---
# <a name="what-is-service-fabric-mesh"></a>Mi a Service Fabric Mesh?

Ez a videó rövid áttekintést nyújt a Service Fabric Mesh szolgáltatásról.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh használatával üzembe helyezett alkalmazások futtatásakor és skálázásakor nem kell aggódnia a mögöttes infrastruktúra miatt.  A Service Fabric Mesh több ezer gép alkotta fürtökből áll.  A fürtök minden művelete rejtve van a fejlesztő elől. Töltse fel a kódot, és adja meg a szükséges erőforrásokat, rendelkezésre állással és erőforrás-korlátozások.  A Service Fabric Mesh automatikusan lefoglalja az infrastruktúrát, és kezeli az infrastruktúrával kapcsolatos hibákat is, így biztosítva az alkalmazásai magas rendelkezésre állását. Csak az alkalmazás állapotával és válaszképességével kell törődnie, az infrastruktúrával nem.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ez a cikk áttekintést nyújt a Service Fabric Mesh fő előnyeiről.

## <a name="great-developer-experience"></a>Nagyszerű fejlesztői élmény

A Service Fabric Mesh a tárolókon futtatható bármely programozási nyelvet vagy keretrendszert támogatja. A Visual Studio 2019 és a Visual Studio Code-eszköztámogatás biztosítja a hatékony Szerkesztés és hibakeresés élmény .NET és .NET Core-alkalmazásokhoz. 

A Service Fabric Mesh használatával a következőket teheti:

- Meglévő alkalmazások „átemelése” tárolókba, hogy modernizálhassa és nagy mennyiségben futtathassa az aktuális alkalmazásokat.
- Új mikroszolgáltatás-alkalmazások létrehozása és üzembe helyezése nagy mennyiségben az Azure-ban.  Integráció más Azure-szolgáltatásokkal vagy tárolókban futó meglévő alkalmazásokkal. Mindegyik mikroszolgáltatás egy biztonságos, elkülönített hálózati alkalmazás részét képezi. A mikroszolgáltatás erőforrás cégirányítási szabályzatok CPU magok, memória, lemezterület és további definiálva van.
- Meglévő alkalmazásokkal való integráció és bővítés az alkalmazások módosítása nélkül. A meglévő alkalmazás új alkalmazáshoz való csatlakoztatása saját virtuális hálózat használatával.  
- A meglévő Cloud Services-alkalmazások modernizálása a Service Fabric Mesh-be való áttelepítéssel.  

## <a name="simple-operational-lifecycle"></a>Egyszerű működési életciklus

Könnyen kezelheti az alkalmazások figyelése és a Hibakeresés az éles környezetben futó alkalmazások. Ez a felügyeleti alkalmazások frissítéséhez és a verziókezelés tartalmaz. Ezek az alkalmazások egyetlen mikroszolgáltatásból vagy a saját hálózatukon belül elkülönített több mikroszolgáltatásból is állhatnak. Az alkalmazások hatékonyan futnak gyors üzembe helyezés, elhelyezés és feladatátvételi idők mellett.

A Service Fabric Mesh használatával a következőket teheti:

- Az alkalmazásokat az infrastruktúra explicit kiépítése és kezelése nélkül helyezheti üzembe és kezelheti.  A Service Fabric Mesh kiépíti, frissíti, javítja és fenntartja a mögöttes infrastruktúrát Ön helyett.
- Folyamatos integrációt állíthat be az integrált eszközökkel az alkalmazások egyszerű csomagolása és üzembe helyezése érdekében.
- Az Azure Resource Manager-erőforrások összes funkcióját kihasználva. Ezek a funkciók közé auditnapló és [szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview)). A Service Fabric-háló szolgáltatás az Azure-ban üzembe helyezett összes erőforrást az Azure Resource Manager-erőforrások. Ilyen erőforrások többek között az alkalmazások, szolgáltatások, titkos kódok és így tovább.
- Az [Azure Portal](https://portal.azure.com), Resource Manager-sablonok vagy Azure CLI-/PowerShell-kódtárak használatával helyezheti üzembe és kezelheti az erőforrásokat.
- Működésfigyelést és riasztást állíthat be az [Application Insights](/azure/application-insights/) (vagy a kívánt eszköz) használatával a működési és diagnosztikai nyomkövetések rögzítéséhez a platformról.
- Az alkalmazásmodellből küldött alkalmazásdiagnosztikai információkat érhet el az [Application Insights](/azure/application-insights/) vagy a kívánt eszköz használatával.
- Erőforrás-használat optimalizálása a felügyeltalkalmazás-definíció a szolgáltatások automatikus skálázási szabályok megadásával.

## <a name="mission-critical-platform-capabilities"></a>Az üzletmenet szempontjából kritikus fontosságú platformképességek

A Service Fabric Mesh a fürtök egy gyűjteményét hozza létre, amelyek áthidalják az [Azure rendelkezésre állási zónák](/azure/availability-zones/az-overview) és/vagy a geopolitikai régiók határait. Service Fabric-háló ismerteti az alkalmazások szándék fog vonatkozni, például a méretezési csoport, hardverkövetelmények, tartósság követelmények és biztonsági szabályzatok vannak beállítva.  Az alkalmazás üzembe helyezésekor a Service Fabric Mesh megkeresi a futtatásához optimális helyet.

A Service Fabric Mesh használatával a következőket teheti:

- Használja ki a magas rendelkezésre állás, a le-/felskálázás, a felderíthetőség, a vezénylés, az üzenet-útválasztás, a megbízható üzenetkezelés, az állásidő nélküli frissítések, a biztonság/a titkos kódok kezelése, a vészhelyreállítás, az állapotkezelés, a konfigurációkezelés és az elosztott tranzakciók előnyeit.
- Az alkalmazások létrehozásakor több alkalmazásmodell közül választhat.
- A REST-végpontokon keresztül közzétett platformképességeket használhat a Swagger révén létrehozott nyelvspecifikus kötések használatával.
- [Rendelkezésre állási zónák](/azure/availability-zones/az-overview) és több régió között helyezhet üzembe alkalmazásokat a földrajzi megbízhatóság érdekében.
- Az Azure által nyújtott összes biztonsági és megfelelőségi szolgáltatást használhatja.

## <a name="next-steps"></a>További lépések

Csupán néhány lépést kell elvégezni a mintaprojekt Visual Studióval való üzembe helyezéséhez. További információért lásd: [ASP.NET Core-webhely létrehozása](service-fabric-mesh-quickstart-dotnet-core.md). 

Válaszok a [gyakori kérdésekre](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
