---
title: Az Azure IoT Suite – gyakori kérdések |} Microsoft Docs
description: Gyakran ismételt kérdések az IoT Suite-ról
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Gyakran ismételt kérdések az IoT Suite-ról

Lásd még a [gyári vonatkozó gyakran ismételt kérdések csatlakoztatott](iot-suite-faq-cf.md) és a [távoli ellenőrzésére vonatkozó gyakran ismételt kérdések](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Hol található az előkonfigurált megoldásokat a kódot?

A forráskód a következő GitHub-adattárak tárolja:

* [A távoli felügyeleti előkonfigurált megoldás (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [A távoli felügyeleti előkonfigurált megoldás (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Előre konfigurált prediktív karbantartási megoldás](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Előre konfigurált csatlakoztatott gyári megoldás](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>Milyen SDK-k is használni eszközügyfeleitől az előkonfigurált megoldások kialakításához?

A különböző nyelvi (C, .NET, Java, Node.js, Python) IoT eszközre SDK-k hivatkozásait megtalálhatja az a [Microsoft Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks) GitHub-adattárak.

Ha a DevKit eszközt használ, erőforrások és a minták található a [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-tárházban.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Érhető el az új mikroszolgáltatások architektúra összes három előkonfigurált megoldásokat?

Jelenleg csak a távoli figyelési megoldást használja a mikroszolgáltatások architektúra a legszélesebb forgatókönyv bemutatja.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyökkel biztosítja az új nyílt forrása mikroszolgáltatások-alapú architektúra az új frissítés?

Az utolsó két évek architektúra nagy mértékben alakulását. Mikroszolgáltatások rendelkezik kiderült kiváló mintaként méretezés és rugalmasság, eléréséhez fejlesztési sebesség feláldozása nélkül. Ez az architektúra minta használatú több Microsoft-szolgáltatásokban a nagy megbízhatósági és méretezhetőségi eredményeit. A Microsoft ezeket a gyakorlatban tanulási, hogy a felhasználók hasznát vehetik helyeznek.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>Az új előkonfigurált megoldás, a meglévő megoldást azonos földrajzi régióban elérhető van?

Igen, az új távoli figyelés nem áll rendelkezésre a azonos földrajzi régióban.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Mi a különbség egy erőforráscsoport Azure Portalról való törlése és azureiotsuite.com webhelyen egy előre konfigurált megoldásban a törlés gombra való kattintás között?

* Ha törli az előkonfigurált megoldás [azureiotsuite.com](https://www.azureiotsuite.com/), az előkonfigurált megoldás létrehozása után kiépített összes erőforrást törli. Ha további erőforrások az erőforráscsoportot, ezeket az erőforrásokat is törlődnek.
* Ha törli az erőforráscsoportja a [Azure-portálon](https://portal.azure.com), csak törli az erőforrásokat, az erőforráscsoport. Szükség az Azure Active Directory-alkalmazás az előkonfigurált megoldás társított törlése.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Használhatom továbbra is kihasználhatják a meglévő Azure IoT Suite?

Igen. Az Azure-előfizetése ma létezik megoldások használható folyamatosan, és a forráskód továbbra is elérhető a Githubon.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Az IoT-központ hány példányban hozhat létre az előfizetést?

Alapértelmezés szerint oszthat [10 IoT-központok előfizetésenként](../azure-subscription-service-limits.md#iot-hub-limits). Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését. Ennek eredményeképpen minden előkonfigurált megoldás látja el egy új IoT Hub, mert csak megadhat egy adott előfizetésben legfeljebb 10 előkonfigurált megoldásokat.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Azure Cosmos DB hány példányban lehet kiépíteni egy előfizetésben?

Ötven. Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését, de alapértelmezés szerint csak oszthat előfizetésenként legfeljebb 50 Cosmos DB példányt.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Az Azure-előfizetéssel csak két belső tranzakciók szintjét 1 a Bing Maps vállalati terveket hozhat létre. A távoli figyelési megoldást igényelnek a belső tranzakció szintjét 1 csomaggal alapértelmezett lett kiépítve. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok előre konfigurált megoldást, ha Microsoft Azure for DreamSpark-fiókom van?

> [!NOTE]
> Dreamspark révén a Microsoft Azure mostantól a diákok a Microsoft Imagine néven.

Jelenleg nem hozható létre egy előre konfigurált megoldást egy [dreamspark révén a Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) fiók. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Létrehozhatók előkonfigurált megoldás, ha a Cloud Solution Provider (CSP) előfizetés van?

A Cloud Solution Provider (CSP) előfizetést jelenleg nem hozható létre előre konfigurált megoldást. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hogyan törölhetek egy AAD-bérlőt?

Eric Golpe blogbejegyzésből [bemutató az Azure AD-bérlő törlésével](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>További lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [A távoli felügyeleti előkonfigurált megoldás funkcióinak felfedezése](iot-suite-remote-monitoring-explore.md)
* [Előre konfigurált prediktív karbantartási megoldás áttekintése](iot-suite-predictive-overview.md)
* [Előre konfigurált csatlakoztatott gyári megoldási áttekintés](iot-suite-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](securing-iot-ground-up.md)