---
title: "Felhő Foundry figyelés Azure napló Analytics dugulásellenőrzési telepítése |} Microsoft Docs"
description: "Részletes útmutatás a felhő Foundry loggregator dugulásellenőrzési telepítése az Azure Naplóelemzés. A fúvóka segítségével nyomon követheti a felhő Foundry rendszer állapotának és teljesítményének metrikákat."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 0d13d39d2921c51c537534a5b000564a9df91880
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Felhő Foundry system monitoring Azure napló Analytics dugulásellenőrzési telepítése

[Az Azure Naplóelemzés](https://azure.microsoft.com/services/log-analytics/) szolgáltatás a Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Segít összegyűjti és elemzi az adatokat, jön létre a felhőalapú és helyszíni környezetben.

A napló Analytics dugulásellenőrzési (dugulásellenőrzési) egy felhő Foundry (CF) összetevő, amely továbbítja a metrikák a [felhő Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) "firehose" szolgáltatáshoz. A fúvóka rendelkező gyűjtése, megtekintése és elemzése a CF rendszer állapotának és teljesítményének metrikákat, több központi telepítések egységességét.

Ebből a dokumentumból megismerheti, hogyan központi telepítése a fúvóka CF környezetét, és majd hozzá az adatokhoz a napló Analytics OMS-konzolról.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi lépéseket kell a fúvóka telepítésének előfeltételeit.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Központi telepítése a CF vagy a döntő felhő Foundry környezet az Azure-ban

A fúvóka egy nyílt forráskódú CF telepítés vagy a döntő felhő Foundry (PCF) központi telepítés is használhatja.

* [Azure Cloud Foundry telepítése](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure döntő felhő Foundry telepítése](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. A fúvóka telepítéséhez a CF parancssori eszközök telepítése

A fúvóka CF környezetben alkalmazásként fut. Az alkalmazás telepítéséhez CF CLI van szüksége.

A fúvóka kell engedéllyel a loggregator "firehose" és a felhő vezérlő. Létrehozni és konfigurálni a felhasználó, a felhasználói fiók és hitelesítés (UAA) szolgáltatás szükséges.

* [Felhő Foundry parancssori felület telepítése](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Felhő Foundry UAA parancssori ügyfél telepítése](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Mielőtt beállítaná a UAA parancssori ügyfél, győződjön meg arról, hogy telepítve van-e a Rubygems.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Az OMS-munkaterület létrehozása az Azure-ban

Az OMS-munkaterület manuálisan vagy egy sablon használatával hozhat létre. A előre konfigurált OMS-nézetek és a riasztások betöltése a fúvóka központi telepítés befejezése után.

A munkaterület manuális létrehozása:

1. Az Azure portálon a szolgáltatások listájában keresse az Azure piactéren, és válassza a Naplóelemzési.
2. Válassza ki **létrehozása**, majd válassza ki a következő elemeket kell:

   * **OMS-munkaterület**: írja be a munkaterület nevét.
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki azt, amelyik megegyezik a CF központi telepítés.
   * **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot, vagy használja ugyanazt a CF telepítés.
   * **Hely**: Adja meg a helyet.
   * **A tarifacsomag**: válasszon **OK** befejezéséhez.

További információkért lásd: [Ismerkedés a Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

Másik lehetőségként az OMS-munkaterület az OMS-sablon segítségével is létrehozhat. Ezzel a módszerrel a sablon betölti a OMS előre konfigurált nézeteket és riasztásokat automatikusan. További információkért lásd: a [felhő Foundry Azure OMS Naplóelemzés megoldás](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>A fúvóka telepítése

Többféleképpen is telepítheti a fúvóka néhány: PCF csempe vagy CF alkalmazásként.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Központi telepítése a fúvóka PCF Ops Manager csempe

Ha PCF telepítése után az Ops Manager használatával, kövesse a lépéseket [telepíteni és beállítani a fúvóka PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). A fúvóka csempe az Ops Manager telepítve van.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>A fúvóka CF alkalmazás telepítése

Ha nem használ PCF Ops Manager, központi telepítése a fúvóka alkalmazásként. A következő szakaszok ismertetik a folyamatot.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Jelentkezzen be az Ön CF telepítésére vonatkozó rendszergazdai CF parancssori felület használatával

Futtassa az alábbi parancsot:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" pedig a CF tartomány neve. Kérheti le azt a "SYSTEM_DOMAIN" keresve a CF telepítési jegyzékfájl. 

"CF_User" a CF felügyeleti neve. Olvashatók be a nevet és jelszót keresést a "scim" szakaszban, a név és a "cf_admin_password" a CF telepítési jegyzékfájl keres.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Hozzon létre egy CF felhasználót és megkaphatják a szükséges jogosultságokkal

Futtassa az alábbi parancsot:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" pedig a CF tartomány neve. Kérheti le azt a "SYSTEM_DOMAIN" keresve a CF telepítési jegyzékfájl.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Töltse le a legújabb napló Analytics dugulásellenőrzési kiadás

Futtassa az alábbi parancsot:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Beállíthatja a környezeti változók a manifest.yml fájl az aktuális könyvtárban található. Az alábbiakban látható a fúvóka alkalmazás jegyzékfájljának. Értékek cserélje le a kért OMS-munkaterület adatokat.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to OMS Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to OMS Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to OMS Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Az alkalmazást a fejlesztési számítógépen leküldéses

Győződjön meg arról, hogy az oms-napló-analytics-"firehose"-fúvóka mappa alatt áll. Futtassa az alábbi parancsot:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>A fúvóka a telepítés ellenőrzése

### <a name="from-apps-manager-for-pcf"></a>Az alkalmazások Manager (a PCF)

1. Jelentkezzen be az Ops Manager, és győződjön meg arról, hogy a csempe jelenik meg a telepítési irányítópult.
2. Alkalmazások Manager bejelentkezni, ellenőrizze, hogy a hely a fúvóka létrehozott szerepel a használati jelentés, és győződjön meg arról, hogy az állapot normál.

### <a name="from-your-development-computer"></a>A fejlesztési számítógépen

A CF parancssori ablakban írja be:
```
cf apps
```
Ellenőrizze, fut-e az OMS dugulásellenőrzési alkalmazást.

## <a name="view-the-data-in-the-oms-portal"></a>Az adatok megtekintése az OMS-portálon

### <a name="1-import-the-oms-view"></a>1. Az OMS-nézet importálása

Az OMS-portálon keresse meg a **adatforrásnézet-tervezőből** > **importálási** > **Tallózás**, és válassza ki a omsview fájlokat. Válassza például *felhő Foundry.omsview*, és menti a nézetet. Most egy csempe jelenik meg az OMS **áttekintése** lap. Válassza ki azt, hogy feladatkonfigurációkat metrikákat.

Ezek a nézetek testreszabása, vagy hozzon létre új nézetek keresztül **adatforrásnézet-tervezőből**.

A *"Felhő Foundry.omsview"* a felhő Foundry OMS sablon megtekintése előzetes verziója. Ez az a teljesen konfigurált alapértelmezett sablon. Ha javaslata vagy a sablon visszajelzést, küldje el a a [szakasz ki](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Riasztási szabályok létrehozása

Is [hozzon létre a riasztások](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), és szabja testre a lekérdezések és a küszöbértékeket, igény szerint. A következő figyelmeztetések támogatottak:

| Keresési lekérdezés                                                                  | Riasztás alapján | Leírás                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Típus = CF_ValueMetric_CL Origin_s bbs Name_s = "Domain.cf-alkalmazások" =                   | Eredmények < 1 száma   | **BBS. Domain.cf-alkalmazások** azt jelzi, hogy a cf-alkalmazások tartomány naprakész. Ez azt jelenti, hogy a felhő vezérlőről CF App kérelmek bbs lettek szinkronizálva. LRPsDesired (Diego szükséges AIs) végrehajtásra. Nem érkezett adat azt jelenti, hogy cf-alkalmazások tartomány nincs naprakész állapotban a megadott időszak. |
| Típus = CF_ValueMetric_CL Origin_s rep Name_s = UnhealthyCell Value_d = > 1            | Eredmények > 0 száma   | Diego cellák, a 0 azt jelenti, hogy kifogástalan, és 1 azt jelenti, hogy a nem megfelelő. Állítsa be a riasztás, ha több nem kifogástalan Diego cella észlelt a megadott időszak. |
| Típus CF_ValueMetric_CL Origin_s = "bosh-hm-továbbító" Name_s="system.healthy =" Value_d = 0 | Eredmények > 0 száma | 1 azt jelenti, hogy a rendszer nem működik megfelelően, és a 0 azt jelenti, hogy a rendszer nem működik megfelelően. |
| Típus = CF_ValueMetric_CL Origin_s route_emitter Name_s = ConsulDownMode Value_d = > 0 | Eredmények > 0 száma   | Consul az állapotadatok rendszeresen bocsát ki. 0 azt jelenti, hogy a rendszer nem működik megfelelően, és az 1 azt jelenti, hogy az útvonal-vezérlő azt észleli, hogy Consul le. |
| Típus = CF_CounterEvent_CL Origin_s DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" vagy Name_s="doppler.shedEnvelopes") Delta_d = > 0 | Eredmények > 0 száma | A különbözeti üzenetek szándékosan által eldobott száma Doppler miatt vissza. |
| Típus = CF_LogMessage_CL SourceType_s LGR MessageType_s = hiba =                      | Eredmények > 0 száma   | Loggregator bocsát ki **LGR** utalnak a naplózási folyamat számára. Ilyen probléma például akkor, ha a napló üzenet kimeneti értéke túl magas. |
| Típus = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Eredmények > 0 száma   | Amikor a fúvóka egy lassú fogyasztói riasztást kap loggregator, elküldi a **slowConsumerAlert** ValueMetric az OMS Szolgáltatáshoz. |
| Típus = CF_CounterEvent_CL Job_s fúvóka Name_s = elveszett események Delta_d = > 0              | Eredmények > 0 száma   | Ha a különbözeti elveszett események száma eléri a, az azt jelenti, a fúvóka futtató problémák lehetnek. |

## <a name="scale"></a>Méretezés

A sugárcsővel és a loggregator méretezheti.

### <a name="scale-the-nozzle"></a>A fúvóka méretezése

A fúvóka legalább két példányban kell kezdődnie. A "firehose" elosztja a fúvóka összes példányát a munkaterhelés.
Győződjön meg arról, hogy a fúvóka továbbra is a "firehose" adatok forgalmát, állítsa be a **slowConsumerAlert** riasztás (szerepel az előző szakaszban, "A riasztási szabályok létrehozása"). Miután így riasztást, kövesse a [lassú dugulásellenőrzési útmutatást](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) annak meghatározásához, hogy szükség van-e a méretezés.
Növelheti a fúvóka, használja alkalmazások Manager vagy a CF CLI a fúvóka a példány számokat vagy a memória -erőforrások növelése érdekében.

### <a name="scale-the-loggregator"></a>A loggregator méretezése

Loggregator küld egy **LGR** naplófájlüzenetre utalnak a naplózási folyamat számára. A riasztás annak eldöntéséhez, hogy szükséges-e a loggregator a, akár méretezhető figyelheti.
A loggregator méretezéséhez az Doppler puffer méretét, vagy adja hozzá a további Doppler kiszolgálópéldányok a CF jegyzékben. További információkért lásd: [az útmutatás a loggregator méretezését](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Frissítés

Egy újabb verziójával a fúvóka frissítéséhez töltse le az új dugulásellenőrzési kiadását, hajtsa végre az előző "Központi telepítése a fúvóka" szakaszban leírt lépéseket és leküldéses újból az alkalmazást.

### <a name="remove-the-nozzle-from-ops-manager"></a>Távolítsa el a fúvóka az Ops Manager

1. Jelentkezzen be az Ops Manager.
2. Keresse meg a **Microsoft Azure napló Analytics fúvóka PCF** csempére.
3. Válassza ki a szemétgyűjtési ikonra, és a törlés jóváhagyásához.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Távolítsa el a fúvóka a fejlesztési számítógépen

A CF parancssori ablakban írja be:
```
cf delete <App Name> -r
```

Ha eltávolítja a fúvóka, az adatokat az OMS-portálon nem törlődnek automatikusan. Az OMS szolgáltatáshoz adatmegőrzési beállítás alapján jár le.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Azure napló Analytics dugulásellenőrzési termékekre van megnyitva. A kérdések és a visszajelzés küldése a [GitHub szakasz](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Az Azure támogatási kérelmet megnyitásához válassza a "Felhő Foundry futó virtuális gép" a szolgáltatás kategória szerint. 

## <a name="next-step"></a>Következő lépés

Mellett a felhő Foundry metrika a fúvóka tárgyalja az OMS-ügynököt segítségével betekintést nyerhet Virtuálisgép-szintű működési adatokat (például Syslog, teljesítmény, riasztások, a szoftverleltár). Az OMS-ügynök van telepítve egy Bosh bővítmény a CF virtuális gépekhez.

További információkért lásd: [telepítése OMS-ügynök a felhő Foundry telepítéséhez](https://github.com/Azure/oms-agent-for-linux-boshrelease).
