---
title: Az Azure Service Fabric hálójának áttekintése
description: Az Azure Service Fabric Mesh bemutatása. A Service Fabric Mesh használatával anélkül helyezheti üzembe és skálázhatja az alkalmazást, hogy aggódnia kellene az alkalmazás infrastrukturális igényei miatt.
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: d6522d417556104a1ece703c725f3fbeab49d683
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75458986"
---
# <a name="what-is-service-fabric-mesh"></a>Mi a Service Fabric Mesh?

Ez a videó rövid áttekintést nyújt a Service Fabric Mesh szolgáltatásról.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh használatával üzembe helyezett alkalmazások futtatásakor és skálázásakor nem kell aggódnia a mögöttes infrastruktúra miatt.  A Service Fabric Mesh több ezer gép alkotta fürtökből áll.  A fürtök minden művelete rejtve van a fejlesztő elől. Töltse fel a kódot, és adja meg a szükséges erőforrásokat, a rendelkezésre állási követelményeket és az erőforráskorlátokat.  A Service Fabric Mesh automatikusan lefoglalja az infrastruktúrát, és kezeli az infrastruktúrával kapcsolatos hibákat is, így biztosítva az alkalmazásai magas rendelkezésre állását. Csak az alkalmazás állapotával és válaszképességével kell törődnie, az infrastruktúrával nem.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ez a cikk áttekintést nyújt a Service Fabric Mesh fő előnyeiről.

## <a name="great-developer-experience"></a>Nagyszerű fejlesztői élmény

A Service Fabric Mesh a tárolókon futtatható bármely programozási nyelvet vagy keretrendszert támogatja. A Visual Studio 2019 és a Visual Studio Code eszközeszközök támogatása hatékony szerkesztési és hibakeresési élményt nyújt a .NET és a .NET Core alkalmazásokszámára. 

A Service Fabric Mesh használatával a következőket teheti:

- Meglévő alkalmazások „átemelése” tárolókba, hogy modernizálhassa és nagy mennyiségben futtathassa az aktuális alkalmazásokat.
- Új mikroszolgáltatás-alkalmazások létrehozása és üzembe helyezése nagy mennyiségben az Azure-ban.  Integráció más Azure-szolgáltatásokkal vagy tárolókban futó meglévő alkalmazásokkal. Minden mikroszolgáltatás egy biztonságos, hálózatra elkülönített alkalmazás része. A mikroszolgáltatás rendelkezik a CPU-magok, a memória, a lemezterület és egyebek erőforrás-irányítási szabályzatokkal.
- Meglévő alkalmazásokkal való integráció és bővítés az alkalmazások módosítása nélkül. A meglévő alkalmazás új alkalmazáshoz való csatlakoztatása saját virtuális hálózat használatával.  
- A meglévő Cloud Services-alkalmazások modernizálása a Service Fabric Mesh-be való áttelepítéssel.  

## <a name="simple-operational-lifecycle"></a>Egyszerű működési életciklus

Egyszerűen kezelheti a futó alkalmazásokat, az alkalmazások figyelését és a hibakeresést éles környezetekben. Ez a kezelés tartalmazza az alkalmazásfrissítéseket és a verziószámozást. Ezek az alkalmazások egyetlen mikroszolgáltatásból vagy a saját hálózatukon belül elkülönített több mikroszolgáltatásból is állhatnak. Az alkalmazások hatékonyan futnak gyors üzembe helyezés, elhelyezés és feladatátvételi idők mellett.

A Service Fabric Mesh használatával a következőket teheti:

- Az alkalmazásokat az infrastruktúra explicit kiépítése és kezelése nélkül helyezheti üzembe és kezelheti.  A Service Fabric Mesh kiépíti, frissíti, javítja és fenntartja a mögöttes infrastruktúrát Ön helyett.
- Folyamatos integrációt állíthat be az integrált eszközökkel az alkalmazások egyszerű csomagolása és üzembe helyezése érdekében.
- Használja ki az Azure Resource Manager erőforrásainak összes funkcióját. Ilyen funkciók például a naplózási nyomvonal és [a szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview)). Az Azure-beli Service Fabric Mesh szolgáltatásban üzembe helyezett összes erőforrás az Azure Resource Manager erőforrásai. Ezek az erőforrások alkalmazásokat, szolgáltatásokat, titkokat és így tovább tartalmaznak.
- Az [Azure Portal](https://portal.azure.com), Resource Manager-sablonok vagy Azure CLI-/PowerShell-kódtárak használatával helyezheti üzembe és kezelheti az erőforrásokat.
- Működésfigyelést és riasztást állíthat be az [Application Insights](/azure/application-insights/) (vagy a kívánt eszköz) használatával a működési és diagnosztikai nyomkövetések rögzítéséhez a platformról.
- Az alkalmazásmodellből küldött alkalmazásdiagnosztikai információkat érhet el az [Application Insights](/azure/application-insights/) vagy a kívánt eszköz használatával.
- Az erőforrás-használat optimalizálása az alkalmazásdefinícióban szereplő szolgáltatások automatikus méretezési szabályainak megadásával.

## <a name="mission-critical-platform-capabilities"></a>Az üzletmenet szempontjából kritikus fontosságú platformképességek

A Service Fabric Mesh a fürtök egy gyűjteményét hozza létre, amelyek áthidalják az [Azure rendelkezésre állási zónák](/azure/availability-zones/az-overview) és/vagy a geopolitikai régiók határait. A Service Fabric Mesh olyan leképezésekkel rendelkező alkalmazásokat ír le, mint a méretezés, a hardverkövetelmények, a tartóssági követelmények és a biztonsági házirendek.  Az alkalmazás üzembe helyezésekor a Service Fabric Mesh megkeresi a futtatásához optimális helyet.

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
