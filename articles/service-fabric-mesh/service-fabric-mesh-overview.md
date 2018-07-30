---
title: Az Azure Service Fabric Mesh áttekintése | Microsoft Docs
description: Az Azure Service Fabric Mesh bemutatása. A Service Fabric Mesh használatával anélkül helyezheti üzembe és skálázhatja az alkalmazást, hogy aggódnia kellene az alkalmazás infrastrukturális igényei miatt.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 06/27/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 65a9b1afcc0e1e6d4fcbb60a38ab0764e6fe2f18
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226444"
---
# <a name="what-is-service-fabric-mesh"></a>Mi a Service Fabric Mesh?

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh használatával üzembe helyezett alkalmazások futtatásakor és skálázásakor nem kell aggódnia a mögöttes infrastruktúra miatt.  A Service Fabric Mesh több ezer gép alkotta fürtökből áll.  A fürtök minden művelete rejtve van a fejlesztő elől. Egyszerűen töltse fel a kódot és határozza meg a szükséges erőforrásokat, a rendelkezésre állási követelményeket és az erőforrások korlátait.  A Service Fabric Mesh automatikusan lefoglalja az alkalmazástelepítés által igényelt infrastruktúrát, továbbá az infrastruktúrával kapcsolatos hibákat is kezeli, így biztosítva az alkalmazásai magas rendelkezésre állását. Csak az alkalmazás állapotával és válaszképességével kell törődnie, az infrastruktúrával nem.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ez a cikk áttekintést nyújt a Service Fabric Mesh fő előnyeiről.

## <a name="great-developer-experience"></a>Nagyszerű fejlesztői élmény

A Service Fabric Mesh a tárolókon futtatható bármely programozási nyelvet vagy keretrendszert támogatja. A Visual Studio 2017 és a Visual Studio Code eszköztámogatása hatékony szerkesztést és hibakeresést tesz lehetővé a .NET- és a .NET Core-alkalmazások esetében. 

A Service Fabric Mesh használatával a következőket teheti:

- Meglévő alkalmazások „átemelése” tárolókba, hogy modernizálhassa és nagy mennyiségben futtathassa az aktuális alkalmazásokat. 
- Új mikroszolgáltatás-alkalmazások létrehozása és üzembe helyezése nagy mennyiségben az Azure-ban.  Integráció más Azure-szolgáltatásokkal vagy tárolókban futó meglévő alkalmazásokkal. Minden mikroszolgáltatás egy biztonságos, a hálózaton elkülönített alkalmazás része, amelyben a processzormagokhoz, a memóriához, a lemezterülethez és egyebekhez kapcsolódó erőforrás-szabályozási szabályzatok vannak meghatározva.
- Meglévő alkalmazásokkal való integráció és bővítés az alkalmazások módosítása nélkül. A meglévő alkalmazás új alkalmazáshoz való csatlakoztatása saját virtuális hálózat használatával.  
- A meglévő Cloud Services-alkalmazások modernizálása a Service Fabric Mesh-be való áttelepítéssel.  

## <a name="simple-operational-lifecycle"></a>Egyszerű működési életciklus

Könnyen kezelheti a futó alkalmazásokat, beleértve az alkalmazások frissítését, verziókezelését, monitorozását és hibakeresését termelési környezetekben. Ezek az alkalmazások egyetlen mikroszolgáltatásból vagy a saját hálózatukon belül elkülönített több mikroszolgáltatásból is állhatnak. Az alkalmazások hatékonyan futnak gyors üzembe helyezés, elhelyezés és feladatátvételi idők mellett.

A Service Fabric Mesh használatával a következőket teheti:

- Az alkalmazásokat az infrastruktúra explicit kiépítése és kezelése nélkül helyezheti üzembe és kezelheti.  A Service Fabric Mesh kiépíti, frissíti, javítja és fenntartja a mögöttes infrastruktúrát Ön helyett.
- Folyamatos integrációt állíthat be az integrált eszközökkel az alkalmazások egyszerű csomagolása és üzembe helyezése érdekében.
- Kihasználhatja az Azure Resource Manager-erőforrások összes funkciójának előnyeit (például a naplózást és a [szerepköralapú hozzáférés-vezérlést (RBAC)](/azure/role-based-access-control/overview)), mert az Azure-ban az SF Mesh-szolgáltatásban üzembe helyezett összes erőforrás (például az alkalmazások, a szolgáltatások, a titkos kulcsok stb.) Azure Resource Manager-erőforrás. 
- Az [Azure Portal](https://portal.azure.com), Resource Manager-sablonok vagy Azure CLI-/PowerShell-kódtárak használatával helyezheti üzembe és kezelheti az erőforrásokat.
- Működésfigyelést és riasztást állíthat be az [Application Insights](/azure/application-insights/) (vagy a kívánt eszköz) használatával a működési és diagnosztikai nyomkövetések rögzítéséhez a platformról. 
- Az alkalmazásmodellből küldött alkalmazásdiagnosztikai információkat érhet el az [Application Insights](/azure/application-insights/) vagy a kívánt eszköz használatával.
- Az alkalmazásdefinícióban a szolgáltatások automatikus skálázási szabályainak meghatározásával optimalizálhatja az erőforrások használatát.  (hamarosan elérhető)
- Hálózatelkülönítést és biztonsági határokat hozhat létre az alkalmazásokhoz, ami a Hyper-V-tárolókkal kombinálva egy igen hatékony funkciót eredményez. Elkülönítheti a szolgáltatásokra irányuló és az onnan érkező hálózati forgalmat szolgáltatásonként több IP-címmel és alkalmazásonként elkülönített virtuális hálózatokkal.  (hamarosan elérhető) 


## <a name="mission-critical-platform-capabilities"></a>Az üzletmenet szempontjából kritikus fontosságú platformképességek

A Service Fabric Mesh a fürtök egy gyűjteményét hozza létre, amelyek áthidalják az [Azure rendelkezésre állási zónák](/azure/availability-zones/az-overview) és/vagy a geopolitikai régiók határait. Az alkalmazások különböző rendeltetésekkel vannak leírva (például skálázás, hardverkövetelmények, tartóssággal kapcsolatos követelmények és biztonsági szabályzatok).  Az alkalmazás üzembe helyezésekor a Service Fabric Mesh megkeresi a futtatásához optimális helyet.

A Service Fabric Mesh használatával a következőket teheti:

- Használja ki a magas rendelkezésre állás, a le-/felskálázás, a felderíthetőség, a vezénylés, az üzenet-útválasztás, a megbízható üzenetkezelés, az állásidő nélküli frissítések, a biztonság/a titkos kódok kezelése, a vészhelyreállítás, az állapotkezelés, a konfigurációkezelés és az elosztott tranzakciók előnyeit.
- Az alkalmazások létrehozásakor több alkalmazásmodell közül választhat.
- A REST-végpontokon keresztül közzétett platformképességeket használhat a Swagger révén létrehozott nyelvspecifikus kötések használatával.
- [Rendelkezésre állási zónák](/azure/availability-zones/az-overview) és több régió között helyezhet üzembe alkalmazásokat a földrajzi megbízhatóság érdekében.
- Az Azure által nyújtott összes biztonsági és megfelelőségi szolgáltatást használhatja.

## <a name="next-steps"></a>További lépések

Csupán néhány lépést kell elvégezni a mintaprojekt Visual Studióval való üzembe helyezéséhez. További információért lásd: [ASP.NET Core-webhely létrehozása](service-fabric-mesh-quickstart-dotnet-core.md). 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
