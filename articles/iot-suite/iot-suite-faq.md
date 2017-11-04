---
title: "Az Azure IoT Suite – gyakori kérdések |} Microsoft Docs"
description: "Gyakran ismételt kérdések az IoT Suite-ról"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2017
ms.author: dobett
ms.openlocfilehash: 63b059cb91956231fd3bafbe9770a04a0b7e347a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Gyakran ismételt kérdések az IoT Suite-ról

További információ, a csatlakoztatott gyári-specifikus [gyakran ismételt kérdések](iot-suite-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Hol található az előkonfigurált megoldásokat a kódot?

A forráskód a következő GitHub-adattárak tárolja:

* [A távoli felügyeleti előkonfigurált megoldás (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [A távoli felügyeleti előkonfigurált megoldás (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Előre konfigurált prediktív karbantartási megoldás](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Előre konfigurált csatlakoztatott gyári megoldás](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Nem mennyibe kiépíteni az új távoli figyelési megoldást igényelnek?

Az új előkonfigurált megoldás két telepítési lehetőségeket nyújtja:

* A *alapvető* lehetőség, a fejlesztők alacsonyabb fejlesztési költségek vagy az ügyfelek keresése bemutató vagy a koncepció igazolása keres.
* A *szabványos* lehetőséget szeretne telepíteni egy éles használatra kész infrastruktúra vállalatok számára készült.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hogyan tudom arról, hogy I a költséges alacsonyan tartása szeretnék a saját megoldás fejlesztése során?

Csupán két eltérő központi telepítések, az új távoli figyelési megoldást igényelnek engedélyezheti vagy tilthatja le a szimulált eszköz, az igény szerinti beállítás van. A szimuláció letiltása csökkenti az adatokat, a megoldás, és így a teljes költség szempontjából okozhatnak.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Érhető el az új mikroszolgáltatások architektúra összes három előkonfigurált megoldásokat?

Jelenleg csak a távoli figyelési megoldást használja a mikroszolgáltatások architektúra a legszélesebb forgatókönyv bemutatja.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyökkel biztosítja az új nyílt forrása mikroszolgáltatások-alapú architektúra az új frissítés?

Az utolsó két évek architektúra nagy mértékben alakulását. Micro szolgáltatások eléréséhez méretezés és rugalmasság, fejlesztési sebesség feláldozása nélkül kiváló mintaként rendelkezik kiderült. Ez az architektúra minta használatú több Microsoft-szolgáltatásokban a nagy megbízhatósági és méretezhetőségi eredményeit. A Microsoft ezeket a gyakorlatban tanulási, hogy a felhasználók hasznát vehetik helyeznek.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>Az új előkonfigurált megoldás, a meglévő megoldást azonos földrajzi régióban elérhető van?

Igen, az új távoli figyelés nem áll rendelkezésre a azonos földrajzi régióban.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Mi a különbség a a basic és standard telepítési lehetőségek között? Hogyan dönt a két telepítési lehetőségek között?

Telepítési lehetőségek válaszol-e a különböző igényeinek. Az alapszintű központi telepítést célja, hogy első lépések és PoC és kisméretű próbakörnyezetek fejlesztéséhez. Biztosít egy egyszerűbb architektúra alacsonyabb költségekkel és a minimálisan szükséges erőforrásokat. Szabványos telepítési célja, hogy létrehozása és testreszabása üzemi használatra kész megoldást, és vegye figyelembe, hogy a szükséges elemeket a központi telepítés biztosít. A megbízhatóság és a skála alkalmazás mikroszolgáltatások Docker tárolóként beépített, és az orchestrator (alapértelmezés szerint Kubernetes) használatával telepíthetők. A koordináló rendszer feladata telepítés, a méretezés és a az alkalmazás felügyelete szempontjából. Válasszon egy beállítást, a jelenlegi igények alapján. Használhat egy, a másik vagy mindkettőt, attól függően, hogy a projekt szakasza.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Használhatom továbbra is kihasználhatják a meglévő Azure IoT Suite?

Igen. Az Azure-előfizetése ma létezik megoldások használható folyamatosan, és a forráskód továbbra is elérhető a Githubon.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Mi a különbség egy erőforráscsoport Azure Portalról való törlése és azureiotsuite.com webhelyen egy előre konfigurált megoldásban a törlés gombra való kattintás között?

* Ha törli az előkonfigurált megoldás [azureiotsuite.com](https://www.azureiotsuite.com/), az előkonfigurált megoldás létrehozása után kiépített összes erőforrást törli. Ha további erőforrások az erőforráscsoportot, ezeket az erőforrásokat is törlődnek.
* Ha törli az erőforráscsoportja a [Azure-portálon](https://portal.azure.com), csak törli az erőforrásokat, az erőforráscsoport. Szükség az Azure Active Directory-alkalmazás az előkonfigurált megoldás társított törlése.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Az IoT-központ hány példányban hozhat létre az előfizetést?

Alapértelmezés szerint oszthat [10 IoT-központok előfizetésenként](../azure-subscription-service-limits.md#iot-hub-limits). Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését. Ennek eredményeképpen minden előkonfigurált megoldás látja el egy új IoT Hub, mert csak megadhat egy adott előfizetésben legfeljebb 10 előkonfigurált megoldásokat.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Azure Cosmos DB hány példányban lehet kiépíteni egy előfizetésben?

Ötven. Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését, de alapértelmezés szerint csak oszthat előfizetésenként legfeljebb 50 Cosmos DB példányt.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Az Azure-előfizetéssel csak két belső tranzakciók szintjét 1 a Bing Maps vállalati terveket hozhat létre. A távoli figyelési megoldást igényelnek a belső tranzakció szintjét 1 csomaggal alapértelmezett lett kiépítve. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok előre konfigurált megoldást, ha Microsoft Azure for DreamSpark-fiókom van?

Jelenleg nem hozható létre egy előre konfigurált megoldást egy [dreamspark révén a Microsoft Azure](https://www.dreamspark.com/Product/Product.aspx?productid=99) fiók. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Létrehozhatók előkonfigurált megoldás, ha a Cloud Solution Provider (CSP) előfizetés van?

A Cloud Solution Provider (CSP) előfizetést jelenleg nem hozható létre előre konfigurált megoldást. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hogyan törölhetek egy AAD-bérlőt?

Eric Golpe blogbejegyzésből [bemutató az Azure AD-bérlő törlésével](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Következő lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése](iot-suite-predictive-overview.md)
* [Előre konfigurált csatlakoztatott gyári megoldási áttekintés](iot-suite-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](securing-iot-ground-up.md)