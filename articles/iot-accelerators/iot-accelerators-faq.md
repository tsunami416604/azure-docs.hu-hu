---
title: IoT megoldás-gyorsítók – GYIK – Azure | Microsoft Docs
description: Ez a cikk a IoT-megoldási gyorssegédekkel kapcsolatos gyakori kérdésekre ad választ. Ide tartoznak a GitHub-adattárakra mutató hivatkozások.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b448099dab0e79dca38310396896be46cf2e0ff6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826254"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Gyakori kérdések a IoT-megoldási gyorssegédekről

Lásd még: a [csatlakoztatott gyárra vonatkozó gyakori kérdések](iot-accelerators-faq-cf.md) és a [távoli FIGYELÉSRE vonatkozó gyakori kérdések](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Hol találom a megoldásgyorsító forráskódját?

A forráskód a következő GitHub-adattárakban tárolódik:

* [A távoli monitorozási megoldásgyorsító (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [A távoli monitorozási megoldásgyorsító (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Prediktív karbantartás megoldásgyorsító](https://github.com/Azure/azure-iot-predictive-maintenance)
* [A csatlakoztatottgyár-alapú megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Mely SDK-kat használhatom megoldásgyorsítókhoz való eszközkliensek fejlesztésére?

A [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub-adattárakban a különböző nyelvekre (C, .net, Java, Node. js, Python) mutató hivatkozásokat talál a IoT.

Ha a fejlesztői készlet eszközt használja, a [IoT fejlesztői készlet SDK](https://github.com/Microsoft/devkit-sdk) GitHub-tárházban találhat erőforrásokat és mintákat.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Elérhető-e az új Service-architektúra a három megoldási gyorssegédhez?

Jelenleg csak a távoli figyelési megoldás használja a Services architektúrát, mivel az a legszélesebb forgatókönyvet fedi le.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyökkel jár az új, nyílt forráskódú Service-alapú architektúrák az új frissítésben?

Az elmúlt két évben a Felhőbeli architektúra nagy mértékben fejlődött. A szolgáltatások nagy léptékben és rugalmasan, a fejlesztési sebesség feláldozása nélkül váltak elérhetővé. Ez az építészeti minta számos Microsoft-szolgáltatásban használatos, nagy megbízhatósági és méretezhetőségi eredményekkel. A Microsoft ezeket a lépéseket a megoldás-gyorsítók között helyezi üzembe, így az ügyfelek hasznát vehetik.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném módosítani a címtár-hozzárendelést az előfizetésem és egy adott Azure AD-bérlő között. Hogyan elvégezte ezt a feladatot?

Lásd: [meglévő előfizetés hozzáadása az Azure ad-címtárhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Meg szeretném változtatni a szolgáltatás-rendszergazdát vagy a társ-rendszergazdát, ha szervezeti fiókkal jelentkeznek be

[Ha szervezeti fiókkal jelentkezik be, tekintse meg a szolgáltatás-rendszergazda és a társ-rendszergazda módosításával foglalkozó](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)cikket.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg ez a hiba? "A fiók nem rendelkezik megfelelő engedélyekkel a megoldás létrehozásához. Érdeklődjön a fiók rendszergazdájánál, vagy próbáljon meg egy másik fiókot. "

Tekintse meg az alábbi ábrát, amely útmutatást nyújt:

![Engedélyek folyamatábrája](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Ha továbbra is megjelenik a hiba, miután a rendszer ellenőrzi, hogy Ön az Azure AD-bérlő globális rendszergazdája, és az előfizetés egy közös rendszergazdája, akkor a fiók rendszergazdája távolítsa el a felhasználót, és rendelje újra a szükséges engedélyeket ebben a sorrendben. Először adja hozzá a felhasználót globális rendszergazdaként, majd adja hozzá a felhasználót az Azure-előfizetés közös rendszergazdájához. Ha a probléma továbbra is fennáll, forduljon a [súgó & támogatási szolgálatához](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért jelenik meg ez a hibaüzenet, ha Azure-előfizetésem van? "Az előre konfigurált megoldások létrehozásához Azure-előfizetésre van szükség. Mindössze néhány perc alatt létrehozhat egy ingyenes próbaverziós fiókot. "

Ha biztos benne, hogy rendelkezik Azure-előfizetéssel, érvényesítse az előfizetés bérlői leképezését, és ellenőrizze, hogy a megfelelő bérlő van-e kiválasztva a legördülő menüben. Ha ellenőrizte a bérlő helyességét, kövesse az előző ábrát, és ellenőrizze az előfizetés és az Azure AD-bérlő hozzárendelését.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol találhatok információt a távoli figyelési megoldás előző verziójáról?

A távoli figyelési megoldáshoz tartozó gyorssegéd előző verzióját a IoT Suite távoli figyelési előre konfigurált megoldásnak hívták. Az archivált dokumentációt itt találja: [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Elérhető-e az új megoldás-gyorsító ugyanabban a földrajzi régióban, mint a meglévő megoldás?

Igen, az új távoli figyelés ugyanabban a földrajzi régióban érhető el.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Mi a különbség az erőforráscsoport törlése között a Azure Portalban, majd a azureiotsolutions.com a megoldás-gyorsító elemre kattintva?

* Ha törli a megoldás-gyorsító a [azureiotsolutions.com](https://www.azureiotsolutions.com/)-ben, a megoldás-gyorsító létrehozásakor telepített összes erőforrást törli. Ha további erőforrásokat adott hozzá az erőforráscsoporthoz, ezek az erőforrások is törlődnek.
* Ha törli az erőforráscsoportot a [Azure Portalban](https://portal.azure.com), csak az adott erőforráscsoport erőforrásait törli. Törölnie kell a megoldás-gyorssegédhez társított Azure Active Directory alkalmazást is.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Továbbra is használhatom meglévő befektetéseit az Azure IoT-megoldás-gyorsítók között?

Igen. A ma már meglévő megoldások továbbra is működnek az Azure-előfizetésben, és a forráskód elérhető marad a GitHubban.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hány IoT Hub-példányt tudok kiépíteni egy előfizetésben?

Alapértelmezés szerint [előfizetéssel 10 IoT hub-t](../azure-subscription-service-limits.md#iot-hub-limits)lehet kiépíteni. A korlát növeléséhez létrehozhat egy [Azure-támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . Ennek eredményeképpen, mivel minden megoldás-gyorsító új IoT Hub hoz létre, egy adott előfizetésben legfeljebb 10 megoldási gyorssegédet tud kiépíteni.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hány Azure Cosmos DB-példányt tudok kiépíteni egy előfizetésben?

50. Létrehozhat egy Azure- [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a korlát növeléséhez, de alapértelmezés szerint csak 50 Cosmos db példányokat lehet kiépíteni előfizetésre.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Azure-előfizetésben csak két belső tranzakciós 1. szintű Bing Maps-t hozhat létre nagyvállalati csomagokhoz. A távoli figyelési megoldás alapértelmezés szerint a belső Transactions 1 csomaggal lett kiépítve. Ennek eredményeképpen legfeljebb két távoli figyelési megoldást állíthat be egy előfizetésben, módosítás nélkül.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok-e megoldás-gyorsító megoldást, ha Microsoft Azureem a DreamSpark?

> [!NOTE]
> A DreamSpark Microsoft Azure már a diákoknak szóló Microsoft Imagine néven is ismert.

Jelenleg nem hozható létre megoldás-gyorsító [Microsoft Azure a DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) -fiókhoz. Néhány perc alatt létrehozhat egy [ingyenes próbaverziós fiókot az Azure](https://azure.microsoft.com/free/) -hoz, amely lehetővé teszi megoldás-gyorsító létrehozását.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan törölni egy Azure AD-bérlőt?

Az [Azure ad-bérlő törlésével](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)kapcsolatban lásd: Eric golpe 's blog post (útmutató).

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távoli figyelési megoldás gyorsító képességeinek megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Csatlakoztatott gyári megoldás-gyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT biztonság az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
