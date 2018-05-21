---
title: Az Azure IoT Suite – gyakori kérdések |} Microsoft Docs
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
ms.openlocfilehash: 4c8ecdae8d84028986b4b3fba94486ce8eb7f1bc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Gyakran ismételt kérdések az IoT Suite-ról

További információ, a csatlakoztatott gyári specifikus [gyakran ismételt kérdések](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Hol található az előkonfigurált megoldásokat a kódot?

A forráskód a következő GitHub-adattárak tárolja:
* [Távoli figyelési előkonfigurált megoldás][lnk-remote-monitoring-github]
* [Előre konfigurált prediktív karbantartási megoldás][lnk-predictive-maintenance-github]
* [Előre konfigurált csatlakoztatott gyári megoldás](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Hogyan frissíthetők a legújabb verzióra a távoli figyelési előkonfigurált megoldás, amely az IoT Hub eszköz felügyeleti funkciókat használ?

* Ha telepít egy előre konfigurált megoldást a https://www.azureiotsuite.com/ hely, mindig a legújabb verzióját a megoldás egy új példányát telepíti.
* Ha telepít egy parancssor segítségével előre beállított megoldás, frissítheti egy meglévő központi telepítési új kóddal. Lásd: [felhőalapú üzemelő példány] [ lnk-cloud-deployment] a github [tárház][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Hogyan vehetek fel egy új eszköz metódus támogatja a távoli felügyeleti előre konfigurált megoldáshoz?

Című témakör [támogatása egy új módszer hozzáadása a szimulátor] [ lnk-add-method] a a [előkonfigurált megoldás testreszabása] [ lnk-customize] cikk.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>A szimulált eszköz figyelmen kívül hagyja a kívánt tulajdonságok módosítását, miért?
A távoli felügyeleti előkonfigurált megoldás, a szimulált eszköz kódban csak a **Desired.Config.TemperatureMeanValue** és **Desired.Config.TelemetryInterval** a jelentett tulajdonságainak frissítésére való tulajdonságok szükséges. Az összes többi kívánt tulajdonság változáskérések figyelmen kívül lesznek hagyva.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Az eszköz nem szerepel a listán szereplő eszközök a megoldás irányítópultjának miért?

A megoldás irányítópultjának eszközök listája egy lekérdezést az eszközök listájának használja. Jelenleg a lekérdezés nem adhat vissza több mint 10 KB-os eszközök. Próbálja meg a keresési feltételeknek, a lekérdezés szigorúbb elvégzése.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Mi a különbség egy erőforráscsoport Azure Portalról való törlése és azureiotsuite.com webhelyen egy előre konfigurált megoldásban a törlés gombra való kattintás között?

* Ha törli az előkonfigurált megoldás [azureiotsuite.com][lnk-azureiotsuite], az előkonfigurált megoldás létrehozása után kiépített összes erőforrást törli. Ha további erőforrások az erőforráscsoportot, ezeket az erőforrásokat is törlődnek. 
* Ha törli az erőforráscsoportja a [Azure-portálon][lnk-azure-portal], csak törli az erőforrásokat, az erőforráscsoport. Az Azure Active Directory-alkalmazás az előkonfigurált megoldás társított törölnie kell a [Azure-portálon][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Az IoT-központ hány példányban hozhat létre az előfizetést?

Alapértelmezés szerint oszthat [10 IoT-központok előfizetésenként][link-azuresublimits]. Létrehozhat egy [az Azure támogatási jegy] [ link-azuresupportticket] szolgálattól a korlát növelését. Ennek eredményeképpen minden előkonfigurált megoldás látja el egy új IoT Hub, mert csak megadhat egy adott előfizetésben legfeljebb 10 előkonfigurált megoldásokat. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Azure Cosmos DB hány példányban lehet kiépíteni egy előfizetésben?

Ötven. Létrehozhat egy [az Azure támogatási jegy] [ link-azuresupportticket] szolgálattól a korlát növelését, de alapértelmezés szerint csak oszthat előfizetésenként legfeljebb 50 Cosmos DB példányt. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Az Azure-előfizetéssel csak két belső tranzakciók szintjét 1 a Bing Maps vállalati terveket hozhat létre. A távoli figyelési megoldást igényelnek a belső tranzakció szintjét 1 csomaggal alapértelmezett lett kiépítve. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Statikus térképpel rendelkező távoli figyelő megoldásom van, hogyan adhatok hozzá interaktív Bing-térképet?

1. A Bing térképek API beszerzése a vállalati QueryKey [Azure-portálon][lnk-azure-portal]: 
   
   1. Keresse meg az erőforráscsoportot, ahol a Bing térképek API-t a vállalati van a [Azure-portálon][lnk-azure-portal].
   2. Kattintson a **összes beállítás**, majd **kulcskezelés**. 
   3. Két kulcsokat: **főkulcsos** és **QueryKey**. Másolja a következő **QueryKey**.
      
      > [!NOTE]
      > Nem rendelkezik Vállalati Bing Térképek API-fiókkal? Hozzon létre egyet-egyet a [Azure-portálon] [ lnk-azure-portal] az kattintva + új, a Bing térképek API-t a vállalati, és az útmutatást követve hozzon létre, hogy.
      > 
      > 
2. Húzza le a legfrissebb kódot a [Azure IoT-távoli-ellenőrző][lnk-remote-monitoring-github].
3. Futtassa a helyi vagy felhőalapú üzemelő példány parancssori telepítési útmutatása a tárházban /docs/ mappában. 
4. Helyi vagy felhőbeli telepítés futtatása után keresse meg a gyökérmappában az üzembe helyezés alatt létrehozott *.user.config fájlt. Nyissa meg ezt a fájlt egy szövegszerkesztőben. 
5. Az érték, amelyből másolta felvenni a következő sorban módosítsa a **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok előre konfigurált megoldást, ha Microsoft Azure for DreamSpark-fiókom van?

> [!NOTE]
> Dreamspark révén a Microsoft Azure mostantól a diákok a Microsoft Imagine néven.

Jelenleg nem hozható létre egy előre konfigurált megoldást egy [dreamspark révén a Microsoft Azure](https://azure.microsoft.com/pricing/member-offers/imagine/) fiók. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-](https://azure.microsoft.com/free/) mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Létrehozhatók előkonfigurált megoldás, ha a Cloud Solution Provider (CSP) előfizetés van?

A Cloud Solution Provider (CSP) előfizetést jelenleg nem hozható létre előre konfigurált megoldást. Azonban létrehozhat egy [ingyenes próba-fiókot az Azure-] [ lnk-30daytrial] mindössze néhány percet, amely lehetővé teszi az előkonfigurált megoldás létrehozása.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan törli a Azure AD-bérlő?

Eric Golpe blogbejegyzésből [bemutató az Azure AD-bérlő törlésével][lnk-delete-aad-tennant].

### <a name="next-steps"></a>További lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive-overview]
* [Előre konfigurált csatlakoztatott gyári megoldási áttekintés](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

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
