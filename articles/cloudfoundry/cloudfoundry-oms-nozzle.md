---
title: Azure Log Analytics fúvóka üzembe helyezése a cloud foundry figyeléséhez
description: Részletes útmutatást nyújt a Cloud Foundry loggregátor fúvóka üzembe helyezéséhez az Azure Log Analytics számára. A fúvóka segítségével figyelheti a Cloud Foundry rendszer állapotát és teljesítménymutatóit.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277357"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Az Azure Log Analytics fúvóka üzembe helyezése a cloud foundry rendszer figyeléséhez

[Az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) egy szolgáltatás az Azure-ban. Segít a felhőbeli és helyszíni környezetekből létrehozott adatok összegyűjtésében és elemzésében.

A Log Analytics fúvóka (a fúvóka) egy Cloud Foundry (CF) összetevő, amely továbbítja a metrikákat a [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose az Azure Monitor naplók. A fúvókával több telepítésben is gyűjtheti, megtekintheti és elemezheti a CF-rendszer állapotát és teljesítménymutatóit.

Ebben a dokumentumban megtudhatja, hogyan telepítheti a fúvókát a CF-környezetben, és majd hozzáférhet az adatokhoz az Azure Monitor naplók konzolról.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő lépések a fúvóka üzembe helyezésének előfeltételei.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. CF vagy pivotal cloud foundry környezet üzembe helyezése az Azure-ban

A fúvókát nyílt forráskódú CF-telepítéssel vagy pivotal cloud foundry (PCF) üzembe helyezéssel is használhatja.

* [A Cloud Foundry üzembe helyezése az Azure-ban](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Pivotal Cloud Foundry üzembe helyezése az Azure-ban](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Telepítse a CF parancssori eszközeit a fúvóka

A fúvóka alkalmazásként fut CF környezetben. Az alkalmazás üzembe helyezéséhez CF CLI szükséges.

A fúvóka is szüksége van hozzáférési engedélyt a loggregátor firehose és a Cloud Controller. A felhasználó létrehozásához és konfigurálásához szüksége van a Felhasználói fiók és hitelesítés (UAA) szolgáltatásra.

* [A Cloud Foundry CLI telepítése](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [A Cloud Foundry UAA parancssori kliens telepítése](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Az UAA parancssori ügyfél beállítása előtt győződjön meg arról, hogy a RubyGems telepítve van.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Log Analytics-munkaterület létrehozása az Azure-ban

A Log Analytics munkaterületet manuálisan vagy sablon használatával is létrehozhatja. A sablon telepíti az előre konfigurált KPI-nézetek és riasztások beállítását az Azure Monitor naplók konzolhoz. 

#### <a name="to-create-the-workspace-manually"></a>A munkaterület manuális létrehozása:

1. Az Azure Portalon keresse meg a szolgáltatások listáját az Azure Marketplace-en, és válassza a Log Analytics-munkaterületek.
2. Válassza **a Létrehozás**lehetőséget, majd adja meg a következő elemek hez szükséges beállításokat:

   * **Log Analytics-munkaterület:** Írja be a munkaterület nevét.
   * **Előfizetés:** Ha több előfizetéssel rendelkezik, válassza ki azt, amely megegyezik a CF-telepítéssel.
   * **Erőforráscsoport:** Létrehozhat egy új erőforráscsoportot, vagy használhatja ugyanazt a CF-telepítéssel.
   * **Hely**: Adja meg a helyet.
   * **Tarifacsomag:** Válassza az **OK** gombot a befejezéshez.

További információ: [Az Azure Monitor naplóinak első lépései című témakörben talál.](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>A Log Analytics-munkaterület létrehozása az Azure piacáról származó figyelési sablonon keresztül:

1. Nyissa meg az Azure Portalt.
1. Kattintson a "+" vagy a "Erőforrás létrehozása" elemre a bal felső sarokban.
1. Írja be a "Cloud Foundry" kifejezést a keresési ablakba, és válassza a "Cloud Foundry Monitoring Solution" (Felhőöntödei felügyeleti megoldás) lehetőséget.
1. A Cloud Foundry monitoring megoldás sablon első oldal betöltődik, kattintson a "Create" a sablon panel elindításához.
1. Adja meg a szükséges paramétereket:
    * **Előfizetés**: Válasszon egy Azure-előfizetést a Log Analytics-munkaterülethez, általában ugyanúgy a Cloud Foundry üzembe helyezésével.
    * **Erőforráscsoport:** Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Log Analytics-munkaterülethez.
    * **Erőforráscsoport helye**: Válassza ki az erőforráscsoport helyét.
    * **OMS_Workspace_Name**: Ha a munkaterület nem létezik, adjon meg egy munkaterületet, a sablon létrehoz egy újat.
    * **OMS_Workspace_Region**: A munkaterület helyének kiválasztása.
    * **OMS_Workspace_Pricing_Tier**: Válassza ki a Log Analytics munkaterület termékváltozat. Tekintse meg az [árképzési útmutatót.](https://azure.microsoft.com/pricing/details/log-analytics/)
    * **Jogi kifejezések**: Kattintson a Jogi kifejezések, majd a "Létrehozás" gombra a jogi kifejezés elfogadásához.
1. Az összes paraméter megadása után kattintson a "Létrehozás" gombra a sablon üzembe helyezéséhez. Amikor a központi telepítés befejeződött, az állapot megjelenik az értesítési lapon.


## <a name="deploy-the-nozzle"></a>A fúvóka telepítése

A fúvóka telepítésének több módja is van: PCF csempeként vagy CF alkalmazásként.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>A fúvóka üzembe helyezése PCF Ops Manager csempeként

Kövesse az [Azure Log Analytics fúvóka PCF-hez telepítésének és konfigurálásának](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)lépéseit. Ez az egyszerűsített megközelítés, a PCF Ops manager csempe automatikusan konfigurálja és nyomja a fúvókát. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>A fúvóka manuális üzembe helyezése CF-alkalmazásként

Ha nem használja a PCF Ops Managert, telepítse a fúvókát alkalmazásként. A következő szakaszok ezt a folyamatot ismertetik.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Jelentkezzen be a CF-telepítésbe rendszergazdaként a CF CLI-n keresztül

Futtassa az alábbi parancsot:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" a CF domain nevet. A CF központi telepítési jegyzékfájljában található "SYSTEM_DOMAIN" kereséssel lekérheti azt. 

"CF_User" a CF admin neve. A név és a jelszó lekéréséhez keresse meg a "scim" szakaszt, és keresse meg a nevet és a "cf_admin_password" a CF központi telepítési jegyzékfájljában.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Cf-felhasználó létrehozása és a szükséges jogosultságok megadása

Futtassa az alábbi parancsot:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" a CF domain nevet. A CF központi telepítési jegyzékfájljában található "SYSTEM_DOMAIN" kereséssel lekérheti azt.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Töltse le a Log Analytics Fúvóka legújabb kiadását

Futtassa az alábbi parancsot:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Most antól beállíthatja a környezeti változókat a manifest.yml fájlban az aktuális könyvtárban. A következőkben látható az alkalmazás jegyzékfájlja a fúvóka. Cserélje le az értékeket a Log Analytics konkrét munkaterületi adataira.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Az alkalmazás leküldése a fejlesztői számítógépről

Győződjön meg arról, hogy az oms-log-analytics-firehose-fúvóka mappa alatt van. Futtassa az alábbi parancsot:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>A fúvóka telepítésének ellenőrzése

### <a name="from-apps-manager-for-pcf"></a>Az Alkalmazáskezelőből (PCF-hez)

1. Jelentkezzen be az Ops Manager be, és győződjön meg arról, hogy a csempe megjelenik a telepítési irányítópulton.
2. Jelentkezzen be az Alkalmazások kezelőjébe, győződjön meg arról, hogy a fúvóka számára létrehozott terület szerepel a használati jelentésben, és ellenőrizze, hogy az állapot normális-e.

### <a name="from-your-development-computer"></a>A fejlesztői számítógépről

A CF CLI ablakban írja be a következőt:
```
cf apps
```
Ellenőrizze, hogy fut-e az OMS fúvóka alkalmazás.

## <a name="view-the-data-in-the-azure-portal"></a>Az adatok megtekintése az Azure Portalon

Ha a figyelési megoldást a piachely sablonon keresztül telepítette, keresse meg az Azure Portalt, és keresse meg a megoldást. A megoldást a sablonban megadott erőforráscsoportban találja. Kattintson a megoldásra, keresse meg a "log analytics konzol", az előre konfigurált nézetek vannak felsorolva, a felső Cloud Foundry rendszer KPI-k, alkalmazásadatok, riasztások és virtuális gép állapotmetrikodik. 

Ha manuálisan hozta létre a Log Analytics-munkaterületet, kövesse az alábbi lépéseket a nézetek és riasztások létrehozásához:

### <a name="1-import-the-oms-view"></a>1. Az OMS nézet importálása

Az OMS portálon keresse meg a Tervező > **importálási** > **tallózásának nézetében,** és válassza ki az omsview fájlok egyikét. **View Designer** Válassza például a *Cloud Foundry.omsview*lehetőséget, és mentse a nézetet. Most egy csempe jelenik meg az **Áttekintés** oldalon. Jelölje ki, hogy megjelenítéséhez látható metrikák.

Ezeket a nézeteket testreszabhatja, vagy új nézeteket hozhat létre a **Tervező megtekintése**oldalon keresztül.

A *"Cloud Foundry.omsview"* a Cloud Foundry OMS nézetsablon előzetes verziója. Ez egy teljesen konfigurált, alapértelmezett sablon. Ha javaslata vagy visszajelzése van a sablonnal kapcsolatban, küldje el őket a [probléma szakaszba.](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)

### <a name="2-create-alert-rules"></a>2. Riasztási szabályok létrehozása

[Létrehozhatja a riasztásokat,](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)és szükség szerint testreszabhatja a lekérdezéseket és a küszöbértékeket. A következő ajánlott riasztások ajánlottak:

| Keresési lekérdezés                                                                  | Riasztás létrehozása a | Leírás                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Eredmények száma 1 <   | **bbs. Domain.cf-alkalmazások** jelzi, ha a cf-apps Domain up-to-date. Ez azt jelenti, hogy a Cf App kérelmek cloud controller szinkronizálva bbs. LRPsDesired (Diego által kívánt AI) a végrehajtáshoz. Nincs kapott adat azt jelenti, cf-apps Domain nem up-to-date a megadott időablakban. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Eredmények száma > 0   | A Diego-sejtek, 0 azt jelenti, egészséges, és 1 azt jelenti, egészségtelen. Állítsa be a riasztást, ha a megadott időablakban több nem megfelelő állapotú Diego-cellát észlel. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Eredmények száma > 0 | 1 azt jelenti, hogy a rendszer kifogástalan, a 0 pedig azt jelenti, hogy a rendszer nem kifogástalan. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=>Value_d 0 | Eredmények száma > 0   | A konzul rendszeresen kibocsátja egészségi állapotát. A 0 azt jelenti, hogy a rendszer kifogástalan állapotú, az 1 pedig azt jelenti, hogy az útvonalkibocsátó észleli, hogy a konzul nem működik. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" vagy Name_s="doppler.shedEnvelopes") Delta_d>0 | Eredmények száma > 0 | A Doppler által az ellennyomás miatt szándékosan eldobott üzenetek delta száma. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Eredmények száma > 0   | A loggregátor **LGR-t** bocsát ki a naplózási folyamattal kapcsolatos problémák jelzésére. Egy ilyen probléma például az, ha a naplóüzenet kimenete túl magas. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Eredmények száma > 0   | Amikor a fúvóka lassú fogyasztói riasztást kap a loggregátortól, elküldi a **slowConsumerAlert** ValueMetric-t az Azure Monitor naplóinak. |
| Type=CF_CounterEvent_CL Job_s=fúvóka Name_s=eventsLost Delta_d>0              | Eredmények száma > 0   | Ha az elveszett események delta száma eléri a küszöbértéket, az azt jelenti, hogy a fúvóka futási problémája lehet. |

## <a name="scale"></a>Méretezés

A fúvókát és a loggregátort skálán módosíthatja.

### <a name="scale-the-nozzle"></a>A fúvóka méretezése

A fúvóka legalább két példányával kell kezdenie. A tűzoltótömlő elosztja a munkaterhelést a fúvóka összes példányában.
Annak érdekében, hogy a fúvóka lépést tartson a firehose adatforgalommal, állítsa be a **slowConsumerAlert** riasztást (az előző szakaszban a "Riasztási szabályok létrehozása" című szakaszban). A riasztás után kövesse a [lassú fúvóka útmutatását,](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) és állapítsa meg, hogy szükség van-e méretezésre.
A fúvóka felskálázásához használja az Apps Manager t vagy a CF CLI-t a méretszámok vagy a fúvóka memória- vagy lemezerőforrásainak növeléséhez.

### <a name="scale-the-loggregator"></a>A loggregátor méretezése

A loggregátor **LGR** naplóüzenetet küld a naplózási folyamattal kapcsolatos problémák jelzésére. Figyelheti a riasztást annak meghatározásához, hogy a loggregátort fel kell-e skálázni.
A loggregátor felskálázásához növelje a Doppler pufferméretét, vagy adjon hozzá további Doppler-kiszolgálópéldányokat a CF-jegyzékben. További információt [a loggregátor méretezésére vonatkozó útmutatóban](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)talál.

## <a name="update"></a>Frissítés

A fúvóka újabb verzióval történő frissítéséhez töltse le az új fúvókakiadást, kövesse az előző, "A fúvóka telepítése" című részben leírt lépéseket, majd nyomja le újra az alkalmazást.

### <a name="remove-the-nozzle-from-ops-manager"></a>A fúvóka eltávolítása a Műveleti kezelőből

1. Jelentkezzen be a műveleti igazgatóhoz.
2. Keresse meg a **Microsoft Azure Log Analytics fúvóka PCF** csempe.
3. Jelölje ki a szemét ikont, és erősítse meg a törlést.

### <a name="remove-the-nozzle-from-your-development-computer"></a>A fúvóka eltávolítása a fejlesztői számítógépről

A CF CLI ablakban írja be a következőt:
```
cf delete <App Name> -r
```

Ha eltávolítja a fúvókát, az OMS portálon lévő adatok nem törlődnek automatikusan. Az Azure Monitor naplók megőrzési beállítása alapján lejár.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Log Analytics fúvóka nyílt forráskódú. Küldje el kérdéseit és visszajelzéseit a [GitHub szakaszra.](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues) Azure-támogatási kérelem megnyitásához válassza a "Cloud Foundry-t futtató virtuális gép" szolgáltatáskategóriaként. 

## <a name="next-step"></a>Következő lépés

A PCF2.0-ból a virtuális gépek teljesítménymutatói a Rendszermetrikák továbbítója által az Azure Log Analytics fúvókába kerülnek, és integrálva vannak a Log Analytics-munkaterületbe. Már nincs szüksége a Log Analytics-ügynök a virtuális gép teljesítménymetrikák. Azonban továbbra is használhatja a Log Analytics-ügynök syslog adatok gyűjtésére. A Log Analytics-ügynök Bosh-bővítményként van telepítve a CF-virtuális gépekhez. 

További információt a [Log Analytics-ügynök telepítése a Cloud Foundry üzembe helyezéséhez című témakörben talál.](https://github.com/Azure/oms-agent-for-linux-boshrelease)
