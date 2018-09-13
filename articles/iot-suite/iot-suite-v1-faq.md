---
title: Az Azure IoT Suite – gyakori kérdések |} A Microsoft Docs
description: Gyakran ismételt kérdések az IoT Suite-ról
services: ''
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
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 7e7c4affee64a945900c02b6375ba4df5d085183
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35757804"
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Gyakran ismételt kérdések az IoT Suite-ról

Lásd még a csatlakoztatott gyár konkrét [– gyakori kérdések](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Hol található a forráskódban előre konfigurált megoldások?

A forráskód következő GitHub-adattárában tárolja:
* [Előre konfigurált távoli figyelési megoldás][lnk-remote-monitoring-github]
* [Prediktív karbantartási előre konfigurált megoldás][lnk-predictive-maintenance-github]
* [Előre konfigurált csatlakoztatott gyár](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Hogyan frissíthetem a legújabb verzióra, a távoli figyelési előre konfigurált megoldásban az IoT Hub eszközfelügyeleti szolgáltatásaival használó?

* Az előre konfigurált megoldás telepítésekor a https://www.azureiotsuite.com/ hely, minden esetben üzembe helyez egy új példányát a megoldás a legújabb verzióra.
* A parancssor használatával egy előre konfigurált megoldás üzembe helyezésekor, ha egy meglévő üzemelő példány új kóddal frissítheti. Lásd: [Felhőbeli telepítés] [ lnk-cloud-deployment] a github [tárház][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Hogyan adhatom hozzá egy új eszköz módszer támogatja a távoli figyelési előre konfigurált megoldáshoz?

Című témakör [hozzáadása egy új módszer támogatása a szimulátor] [ lnk-add-method] a a [előkonfigurált megoldások testreszabása] [ lnk-customize] cikk.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>A szimulált eszköz figyelmen kívül hagyja a kívánt tulajdonság módosításokat, hogy miért?
A távoli figyelési előre konfigurált megoldásban a szimulált eszköz kód csak használja a **Desired.Config.TemperatureMeanValue** és **Desired.Config.TelemetryInterval** kívánt tulajdonságot a jelentett tulajdonságok frissítésére. Az összes többi kívánt tulajdonságmódosítási kérelmet a rendszer figyelmen kívül hagyja.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Az eszköz nem jelenik meg a megoldás irányítópultján eszközök miért?

Eszközök a megoldás irányítópultján egy lekérdezést használ az eszközök listájának visszaadása. Jelenleg egy lekérdezés legfeljebb 10 ezer eszközök nem tud visszaadni. Próbálja ki, így a keresési feltételeknek, a lekérdezés szigorúbb.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Mi a különbség egy erőforráscsoport Azure Portalról való törlése és azureiotsuite.com webhelyen egy előre konfigurált megoldásban a törlés gombra való kattintás között?

* Ha törli az előre konfigurált megoldást [azureiotsuite.com][lnk-azureiotsuite], az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást törli. Ha további erőforrásokat adott az erőforráscsoporthoz, ezeket az erőforrásokat is törli. 
* Ha törli az erőforráscsoportot a [az Azure portal][lnk-azure-portal], csak az adott erőforráscsoportban lévő erőforrásokat törli. Törli a társított az előre konfigurált megoldás az Azure Active Directory-alkalmazás is van szükség a [az Azure portal][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hány az IoT Hub-példányt adhatok meg egy előfizetést?

Alapértelmezés szerint helyezheti üzembe a [előfizetésenként 10 IoT-központok][link-azuresublimits]. Létrehozhat egy [Azure támogatási jegyet] [ link-azuresupportticket] szolgálattól a korlát növelését. Ennek eredményeképpen minden előre konfigurált megoldás látja el egy új IoT hubot, mivel csak telepíthet egy adott előfizetésben legfeljebb 10 előre konfigurált megoldásokat. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hány Azure Cosmos DB-példányt adhatok meg egy előfizetést?

Ötven. Létrehozhat egy [Azure támogatási jegyet] [ link-azuresupportticket] szolgálattól a korlát növelését, de alapértelmezés szerint csak kiépíthetők előfizetésenként 50 Cosmos DB-példány. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Azure-előfizetésben csak két belső tranzakció szintjét 1 a Bing Maps Enterprise-csomagok is létrehozhat. A távoli figyelési megoldásban a belső tranzakció Level 1 csomag alapértelmezés szerint ki van építve. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Statikus térképpel rendelkező távoli figyelő megoldásom van, hogyan adhatok hozzá interaktív Bing-térképet?

1. A Bing térképek API beszerzése a vállalati querykey értékét a [az Azure portal][lnk-azure-portal]: 
   
   1. Keresse meg az erőforráscsoport, amelyben szerepel a vállalati Bing térképek API a [az Azure portal][lnk-azure-portal].
   2. Kattintson a **minden beállítás**, majd **kulcskezelés**. 
   3. Láthatja, hogy két kulccsal: **MasterKey** és **QueryKey**. Másolja az értéket a **QueryKey**.
      
      > [!NOTE]
      > Nem rendelkezik Vállalati Bing Térképek API-fiókkal? Hozzon létre egyet a [az Azure portal] [ lnk-azure-portal] szerint kattintva + új, a Bing Maps API for Enterprise keresése és útmutatást követve hozzon létre.
      > 
      > 
2. Kérje le a legújabb kódot az a [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Futtasson helyi vagy felhőbeli telepítést a tárház /docs/ mappájában található parancssori telepítési útmutatást. 
4. Helyi vagy felhőbeli telepítés futtatása után keresse meg a gyökérmappában az üzembe helyezés alatt létrehozott *.user.config fájlt. Nyissa meg ezt a fájlt egy szövegszerkesztőben. 
5. Módosítsa a következő sort, hogy tartalmazza, amelyet másolt a **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok előre konfigurált megoldást, ha Microsoft Azure for DreamSpark-fiókom van?

> [!NOTE]
> A Microsoft Azure for DreamSpark most néven ismert Microsoft Imagine diákoknak.

Jelenleg nem hozható létre egy előre konfigurált megoldás egy [Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) fiókot. Létrehozhat azonban egy [ingyenes próbafiókot Azure](https://azure.microsoft.com/free/) mindössze néhány perc alatt, amely lehetővé teszi, hogy hozzon létre egy előre konfigurált megoldás.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Létrehozhatok előre konfigurált megoldást, ha a Cloud Solution Provider (CSP) előfizetésem van?

Cloud Solution Provider (CSP) előfizetéssel jelenleg nem hozható létre előre konfigurált megoldást. Létrehozhat azonban egy [ingyenes próbafiókot Azure] [ lnk-30daytrial] mindössze néhány perc alatt, amely lehetővé teszi, hogy hozzon létre egy előre konfigurált megoldás.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan törölhetek Azure AD-bérlő?

Lásd Eric Golpe blogbejegyzését [bemutatója az Azure AD-bérlő törlése][lnk-delete-aad-tennant].

### <a name="next-steps"></a>További lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Prediktív karbantartási előre konfigurált megoldás áttekintése][lnk-predictive-overview]
* [Előre konfigurált csatlakoztatott gyár megoldás – áttekintés](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
