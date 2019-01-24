---
title: Az Azure Log Analytics Nozzle üzembe helyezése a Cloud Foundry-figyelés |} A Microsoft Docs
description: A Cloud Foundry loggregator Nozzle üzembe helyezése az Azure Log Analytics lépésenkénti útmutatóját. Használja a Nozzle a Cloud Foundry rendszer állapotának és teljesítményének metrikák figyelése.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 198d6e596faf47528c508a9323ab22de563dfc62
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819033"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Az Azure Log Analytics Nozzle üzembe helyezése a Cloud Foundry figyelése

[Az Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) egy szolgáltatás az Azure-ban. Ez segít összegyűjteni és elemezni az adatokat, amelyeket akkor jön létre, a felhőből a helyszíni környezetekben.

A Log Analytics Nozzle (Nozzle) egy Cloud Foundry (CF) összetevő, amely továbbítja a metrikák a [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) "firehose" a Log Analytics szolgáltatásba. A Nozzle a collect, megtekintheti, és elemezze a CF-hez rendszer állapotának és teljesítményének metrikákat, a több központi telepítést.

Ebből a dokumentumból megismerheti, hogyan a Nozzle a CF-hez környezet üzembe helyezése, és majd elérni az adatokat a Log Analytics-konzolról.

## <a name="prerequisites"></a>Előfeltételek

A következő lépéseket kell a Nozzle üzembe helyezésére vonatkozó követelményeket.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. A CF-hez vagy a Pivotal Cloud Foundry Azure-beli környezet üzembe helyezése

A Nozzle egy nyílt forráskódú CF központi vagy a Pivotal Cloud Foundry (PCF) központi telepítés is használhatja.

* [Az Azure-ban a Cloud Foundry üzembe helyezése](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Az Azure-ban a Pivotal Cloud Foundry üzembe helyezése](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Üzembe helyezéséhez a Nozzle a CF-hez parancssori eszközök telepítése

A Nozzle a CF-hez környezet alkalmazásként működik. Az alkalmazás központi telepítése a CF CLI van szüksége.

A Nozzle kell rendelkeznie a hozzáférési engedélyt a loggregator "firehose" és a felhő vezérlő is. Hozhat létre és konfigurálja a felhasználó a felhasználói fiók és hitelesítés (UAA) szolgáltatásra van szükségük.

* [A Cloud Foundry parancssori felület telepítése](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA parancssori ügyfél telepítése](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Mielőtt beállítaná a UAA parancssori ügyfelét, győződjön meg arról, hogy telepítve van-e a Rubygems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Az Azure Log Analytics-munkaterület létrehozása

A Log Analytics-munkaterületet is létrehozhat, manuálisan vagy egy sablon használatával. A sablon üzembe helyezni egy előre konfigurált KPI-nézetek és riasztások a Log Analytics-konzol telepítése. 

#### <a name="to-create-the-workspace-manually"></a>A munkaterület manuális létrehozásához:

1. Az Azure Portalon a szolgáltatások listájában keresse az Azure piactéren, és válassza ki a Log Analytics.
2. Válassza ki **létrehozás**, majd válassza ki az egyik lehetőséget a következő elemeknél:

   * **Log Analytics-munkaterület**: Írja be a munkaterület nevét.
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, kiválaszthatja a ugyanaz, mint a CF-telepítés.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy használja ugyanazt a CF-hez üzemelő példány.
   * **Hely**: Adja meg a helyet.
   * **A tarifacsomag**: Válassza ki **OK** befejezéséhez.

További információkért lásd: [Ismerkedés a Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>A figyelési sablon segítségével a Log Analytics-munkaterület létrehozása az Azure Marketplace-beli:

1. Nyissa meg az Azure Portalon.
2. Kattintson a "+" jelre, vagy az "Erőforrás létrehozása" a bal felső sarokban.
3. Írja be a "Cloud Foundry" kifejezést a keresési ablakba, és válassza a "Cloud Foundry figyelési megoldás".
4. A Cloud Foundry figyelési megoldást sablon első lapon be van töltve, a sablon panel elindításához a "Létrehozás" gombra.
5. Adja meg a szükséges paramétereket:
    * **Előfizetés**: Válassza ki a Log Analytics-munkaterületet, általában ugyanaz a Cloud Foundry üzembe helyezés az Azure-előfizetést.
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Log Analytics-munkaterületet.
    * **Erőforráscsoport helye**: Válassza ki az erőforráscsoport helyét.
    * **OMS_Workspace_Name**: Adjon meg egy nevet, ha a munkaterület nem létezik, a sablon létrehoz egy új.
    * **OMS_Workspace_Region**: Válassza ki a munkaterület helyét.
    * **OMS_Workspace_Pricing_Tier**: Válassza ki a Log Analytics-munkaterület Termékváltozat. Tekintse meg a [díjszabási útmutatóját](https://azure.microsoft.com/pricing/details/log-analytics/) referenciaként.
    * **Jogi feltételek**: Kattintson a jogi feltételeket, majd kattintson a "Create", fogadja el a jogi kifejezés.
- Után minden paraméter meg van adva, kattintson a "Létrehozás" a sablon üzembe helyezéséhez. Az üzembe helyezés befejezése után az állapot jelennek meg az értesítési lapon.


## <a name="deploy-the-nozzle"></a>A Nozzle üzembe helyezése

Többféleképpen is a Nozzle üzembe helyezése néhány: PCF csempe formájában, vagy a CF-alkalmazás.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>A PCF az Ops Manager csempeként az Nozzle üzembe helyezése

Kövesse a lépéseket a [telepítése és konfigurálása az Azure Log Analytics Nozzle a PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Ez az egyszerűsített módszer, automatikusan konfigurálja és küldje le a nozzle a PCF az Ops manager csempét. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Telepítheti manuálisan a Nozzle a CF-alkalmazás

Ha nem használ a PCF az Ops Manager, a Nozzle üzembe helyezése alkalmazásként. A következő szakaszok ismertetik a folyamatot.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Jelentkezzen be a CF-hez telepítéséhez rendszergazdai CF parancssori felületén keresztül

Futtassa az alábbi parancsot:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" is your CF domain name. Kérheti le azt a "SYSTEM_DOMAIN" Keresés a CF-üzembe helyezési jegyzékfájl. 

"CF_User" a CF-hez rendszergazdájának nevét. Keressen a "scim" szakaszban, a név és a "cf_admin_password" a CF-üzembe helyezési jegyzékfájl kérheti le a nevet és jelszót.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>A CF-felhasználó létrehozása és a szükséges jogosultságok engedélyezése

Futtassa az alábbi parancsot:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" is your CF domain name. Kérheti le azt a "SYSTEM_DOMAIN" Keresés a CF-üzembe helyezési jegyzékfájl.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Töltse le a Log Analytics Nozzle legújabb kiadását

Futtassa az alábbi parancsot:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Most már beállíthatja a környezeti változók a manifest.yml fájl az aktuális könyvtárban található. Az alábbiakban látható a Nozzle manifest aplikace. Értékeket cserélje le a Log Analytics munkaterület információkat.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Az alkalmazás a fejlesztői számítógépről küldése

Győződjön meg arról, hogy az oms-log-analytics – "firehose"-nozzle mappa alatt áll. Futtassa az alábbi parancsot:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>A Nozzle a telepítés ellenőrzése

### <a name="from-apps-manager-for-pcf"></a>Az alkalmazások Manager (a PCF)

1. Jelentkezzen be az Ops Manager, és ellenőrizze, hogy a telepítés irányítópulton jelennek meg a csempét.
2. Jelentkezzen be a kezelő alkalmazások, győződjön meg arról, hogy a használati jelentés megjelenik a létrehozott Nozzle a terület, és győződjön meg arról, hogy az állapot normál.

### <a name="from-your-development-computer"></a>A fejlesztői számítógépről

A CF-hez parancssori felület ablakában írja be:
```
cf apps
```
Győződjön meg arról, hogy az OMS Nozzle alkalmazás fut-e.

## <a name="view-the-data-in-the-azure-portal"></a>Az adatok megtekintése az Azure Portalon

Ha telepítette a figyelési megoldás a Marketplace-beli sablonon keresztül, az Azure portal megnyitása, és keresse meg a megoldás. A sablonban megadott erőforráscsoportban találja a megoldást. Kattintson a megoldást, keresse meg a "Log Analytics konzolt", az előre konfigurált nézeteket szerepel a listában, felső Cloud Foundry rendszer KPI-k, az alkalmazásadatok, a riasztások és a virtuális gép mérőszámok. 

Ha manuálisan hozott létre a Log Analytics-munkaterülethez, kövesse az alábbi lépéseket, a nézeteket és riasztásokat hozhat létre:

### <a name="1-import-the-oms-view"></a>1. Importálja az OMS-nézet

Az OMS-portálon keresse meg a **adatforrásnézet-tervezőből** > **importálás** > **Tallózás**, és válassza ki a omsview fájlok. Válassza ki például *felhőalapú Foundry.omsview*, és mentse a nézetet. Most egy csempe jelenik meg a **áttekintése** lapot. Válassza ki azt a teszi metrikákat.

Ezek a nézetek testreszabását, vagy hozzon létre új nézetek keresztül **adatforrásnézet-tervezőből**.

A *"Cloud Foundry.omsview"* a Cloud Foundry OMS sablon megtekintése előzetes verziója. Ez a teljesen konfigurált alapértelmezett sablont. Ha javaslata vagy a sablonnal visszajelzést, küldje el a a [szakasz ki](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Riasztási szabályok létrehozása

Is [a riasztások létrehozása](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), és szükség szerint a lekérdezések és a küszöbértékek testre szabhatja. A következő ajánlott riasztások:

| Keresési lekérdezés                                                                  | Riasztás létrehozása ez alapján | Leírás                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Eredmények < 1 száma   | **BBS. Domain.cf-alkalmazások** azt jelzi, hogy a cf-alkalmazás tartomány naprakész. Ez azt jelenti, hogy bbs felhőalapú adatkezelő CF-alkalmazás kéréseit a rendszer szinkronizálja. LRPsDesired (AIs Diego kívánt) végrehajtásához. Nem érkeztek adatok azt jelenti, hogy a cf-alkalmazás tartománya nem naprakész a megadott időtartományban. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Eredmények > 0 száma   | Cellák Diego a 0 azt jelenti, hogy kifogástalan, és 1 azt jelenti, hogy nem megfelelő állapotú. Állítsa be a riasztás, ha több nem kifogástalan Diego cellák észlelt a megadott időtartományban. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Eredmények > 0 száma | 1 jelenti a rendszer kifogástalan állapotú, és a 0 azt jelenti, hogy a rendszer állapota nem kifogástalan. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Eredmények > 0 száma   | Consul rendszeres időközönként bocsát ki a megfelelő állapotot. a 0 azt jelenti, a rendszer kifogástalan állapotú, és 1 azt jelenti, hogy az útvonal vezérlő észleli, hogy Consul le van-e. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Eredmények > 0 száma | A különbözeti száma miatt nyomása Doppler által szándékosan eldobott üzenetek. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Eredmények > 0 száma   | Loggregator bocsát ki **LGR** való problémáira utalnak a naplózási folyamatot. Egy példa ilyen probléma esetén, hogy a kimenet üzenet értéke túl magas. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Eredmények > 0 száma   | A Nozzle loggregator egy lassú fogyasztói riasztást kap, amikor elküldi a **slowConsumerAlert** ValueMetric a Log Analytics szolgáltatásba. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Eredmények > 0 száma   | Ha a különbözeti elveszett események száma eléri a küszöbértéket, az azt jelenti, a Nozzle futó problémák lehetnek. |

## <a name="scale"></a>Méretezés

A Nozzle és a loggregator skálázhatja.

### <a name="scale-the-nozzle"></a>A Nozzle méretezése

A Nozzle legalább két példányban kell kezdődnie. A "firehose" a Nozzle minden példánya között osztja el a számítási feladatok.
Ahhoz, hogy a Nozzle képes tartani a "firehose" adatok forgalmát, állítsa be a **slowConsumerAlert** riasztás ("A riasztási szabályok létrehozása", az előző szakaszban felsorolt). Akkor figyelmezteti, miután a [lassú Nozzle útmutatást](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) meghatározni a skálázás szükség van-e.
A Nozzle a vertikális alkalmazások Manager vagy a CF-hez CLI segítségével kívánja a példány számokat vagy a memória vagy lemezterület erőforrások bővíthetők a Nozzle a.

### <a name="scale-the-loggregator"></a>A loggregator méretezése

Loggregator küld egy **LGR** log üzenet problémáira utalnak a naplózási folyamatot. Figyelheti a meghatározásához, hogy a loggregator vertikálisan fel kell-e riasztást.
A loggregator vertikális felskálázásához az Doppler puffer méretét, vagy adjon hozzá további Doppler kiszolgálópéldányok a CF-jegyzékfájlban. További információkért lásd: [a loggregator skálázás útmutatóját](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Frissítés

A Nozzle frissítése egy újabb verzióval, töltse le az új Nozzle kiadásban, kövesse a fenti "A Nozzle üzembe helyezése" című szakaszának lépéseit, és újból az alkalmazást leküldéses.

### <a name="remove-the-nozzle-from-ops-manager"></a>Távolítsa el a Nozzle az Ops Manager

1. Jelentkezzen be az Ops Manager.
2. Keresse meg a **a Microsoft Azure Log Analytics Nozzle a PCF** csempére.
3. A szemétgyűjtési ikonra, és erősítse meg a törlést.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Távolítsa el a Nozzle a fejlesztői számítógépről

A CF-hez parancssori felület ablakában írja be:
```
cf delete <App Name> -r
```

Ha eltávolítja a Nozzle, az adatok az OMS-portálon nem törlődik automatikusan. A Log Analytics megőrzési beállítás alapján lejár.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Log Analytics Nozzle a nyílt forráskóddal. A kérdések és visszajelzés küldése a [GitHub szakasz](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Nyisson meg egy Azure-támogatási kérést, válassza a "Cloud Foundry szolgáltatást futtató virtuális gép" a szolgáltatás kategória szerint. 

## <a name="next-step"></a>Következő lépés

PCF2.0, a virtuális gép teljesítmény-mérőszámok kerüljenek az Azure log analytics nozzle rendszer metrikák továbbítót, és a Log Analytics-munkaterület integrálva. Már nincs szüksége a Log Analytics-ügynököket a virtuális gép teljesítmény-mérőszámon. Syslog kapcsolatos információk összegyűjtéséhez azonban a Log Analytics-ügynökök továbbra is használhatja. A Log Analytics-ügynök telepítve van egy Bosh végpontállapot CF virtuális gépekhez. 

További információkért lásd: [üzembe helyezése a Log Analytics-ügynök a Cloud Foundry telepítés](https://github.com/Azure/oms-agent-for-linux-boshrelease).
