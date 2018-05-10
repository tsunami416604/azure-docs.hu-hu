---
title: Az Azure IoT-megoldás gyorsítók – gyakori kérdések |} Microsoft Docs
description: Az IoT-megoldás gyorsítók gyakori kérdések
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
ms.openlocfilehash: fcaf0c2d4a8be1358e4510524c8b15e063c647bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Az IoT-megoldás gyorsítók gyakori kérdések

Lásd még a [csatlakoztatott gyári vonatkozó gyakran ismételt kérdések](iot-suite-faq-cf.md) és a [távoli ellenőrzésére vonatkozó gyakran ismételt kérdések](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Hol található a megoldás gyorsítók a kódot?

A forráskód a következő GitHub-adattárak tárolja:

* [Távoli figyelés megoldásgyorsító (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Távoli figyelés megoldásgyorsító (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [A prediktív karbantartási megoldásgyorsító](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Csatlakoztatott gyári megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Milyen SDK-k használhatók lévő ügyfelek számára a megoldás gyorsítók fejlesztése?

A különböző nyelvi (C, .NET, Java, Node.js, Python) IoT eszközre SDK-k hivatkozásait megtalálhatja az a [Microsoft Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks) GitHub-adattárak.

Ha a DevKit eszközt használ, erőforrások és a minták található a [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-tárházban.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Érhető el az új mikroszolgáltatások architektúra a három megoldás-gyorsítókat?

Jelenleg csak a távoli figyelési megoldást használja a mikroszolgáltatások architektúra a legszélesebb forgatókönyv bemutatja.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyökkel biztosítja az új nyílt forrása mikroszolgáltatások-alapú architektúra az új frissítés?

Az utolsó két évek architektúra nagy mértékben alakulását. Mikroszolgáltatások rendelkezik kiderült kiváló mintaként méretezés és rugalmasság, eléréséhez fejlesztési sebesség feláldozása nélkül. Ez az architektúra minta használatú több Microsoft-szolgáltatásokban a nagy megbízhatósági és méretezhetőségi eredményeit. A Microsoft ezeket a gyakorlatban tanulási, hogy a felhasználók hasznát vehetik helyeznek.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Érhető el az új megoldásgyorsító ugyanabban a földrajzi régióban a meglévő megoldáshoz?

Igen, az új távoli figyelés nem áll rendelkezésre a azonos földrajzi régióban.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Mi az a különbség törlése egy erőforráscsoportot az Azure portálon, és kattintson egy megoldásgyorsító azureiotsuite.com a delete?

* Ha törli a megoldásgyorsító a [azureiotsuite.com](https://www.azureiotsuite.com/), amikor létrehozta a megoldásgyorsító kiépített összes erőforrást törli. Ha további erőforrások az erőforráscsoportot, ezeket az erőforrásokat is törlődnek.
* Ha törli az erőforráscsoportja a [Azure-portálon](https://portal.azure.com), csak törli az erőforrásokat, az erőforráscsoport. Is kell törölnie a megoldásgyorsító tartozó Azure Active Directory-alkalmazást.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Használhatom továbbra is kihasználhatják a meglévő Azure IoT-megoldás gyorsítók?

Igen. Az Azure-előfizetése ma létezik megoldások használható folyamatosan, és a forráskód továbbra is elérhető a Githubon.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Az IoT-központ hány példányban hozhat létre az előfizetést?

Alapértelmezés szerint oszthat [10 IoT-központok előfizetésenként](../azure-subscription-service-limits.md#iot-hub-limits). Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését. Ennek eredményeképpen minden megoldás gyorsító szánt új IoT Hub, mert csak létesíthet egy adott előfizetésben legfeljebb 10 megoldás gyorsítókra.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Azure Cosmos DB hány példányban lehet kiépíteni egy előfizetésben?

Ötven. Létrehozhat egy [az Azure támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését, de alapértelmezés szerint csak oszthat előfizetésenként legfeljebb 50 Cosmos DB példányt.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Az Azure-előfizetéssel csak két belső tranzakciók szintjét 1 a Bing Maps vállalati terveket hozhat létre. A távoli figyelési megoldást igényelnek a belső tranzakció szintjét 1 csomaggal alapértelmezett lett kiépítve. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatók a megoldásgyorsító, ha a Microsoft Azure a DreamSpark?

> [!NOTE]
> Dreamspark révén a Microsoft Azure mostantól a diákok a Microsoft Imagine néven.

Jelenleg nem hozható létre egy megoldásgyorsító rendelkező egy [dreamspark révén a Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) fiók. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) néhány percig, amely lehetővé teszi, hogy hozzon létre egy megoldásgyorsító.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>Létrehozhatók a megoldásgyorsító, ha a Cloud Solution Provider (CSP) előfizetés van?

A Cloud Solution Provider (CSP) előfizetést jelenleg nem hozható létre egy megoldásgyorsító. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) néhány percig, amely lehetővé teszi, hogy hozzon létre egy megoldásgyorsító.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hogyan törölhetek egy AAD-bérlőt?

Eric Golpe blogbejegyzésből [bemutató az Azure AD-bérlő törlésével](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>További lépések

Egyes más szolgáltatásait és képességeit mutatja az IoT-megoldás gyorsítók is ismerheti:

* [A távoli felügyeleti megoldásgyorsító lehetőségeinek felfedezése](iot-suite-remote-monitoring-explore.md)
* [A prediktív karbantartási megoldás gyorsító – áttekintés](iot-suite-predictive-overview.md)
* [Csatlakoztatott gyári megoldás gyorsító – áttekintés](iot-suite-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](securing-iot-ground-up.md)