---
title: Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozással Azure Monitor
description: Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozással Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: b0cdff2ce71fb63194933bdfed26da16fdebcca7
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361921"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozással Azure Monitor

> [!IMPORTANT]
> Jelenleg a funkció használatához hozzáférést kell **kérnie** . A [feliratkozási űrlap](https://aka.ms/AzMonPrivateLinkSignup)használatával kérheti a hozzáférést.


Az [Azure Private link](../../private-link/private-link-overview.md) lehetővé teszi, hogy a privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat a virtuális hálózattal. Számos szolgáltatás esetében csak egy végpontot állít be erőforrásként. A Azure Monitor azonban a különböző összekapcsolt szolgáltatások egyik konstellációja, amely együttműködik a számítási feladatok monitorozásával. Ennek eredményeképpen létrehozott egy Azure Monitor Private link (AMPLS) nevű erőforrást, amely lehetővé teszi a megfigyelési hálózat határainak meghatározását és a virtuális hálózathoz való csatlakozást. Ez a cikk azt ismerteti, hogy mikor kell használni és hogyan kell beállítani egy Azure Monitor privát hivatkozás hatókörét.

## <a name="advantages"></a>Előnyök

A privát hivatkozással a következőket teheti:

- A nyilvános hálózati hozzáférés megnyitása nélkül csatlakozhat a Azure Monitorhoz
- Győződjön meg arról, hogy a figyelési adatai csak a jogosult magánhálózatok használatával érhetők el
- A magánhálózati adatok kiszűrése megakadályozása a privát végponton keresztül kapcsolódó Azure Monitor erőforrások definiálásával
- A privát helyszíni hálózat biztonságos csatlakoztatása Azure Monitor ExpressRoute és privát kapcsolat használatával
- Az Microsoft Azure gerinc hálózatán belüli összes forgalom megőrzése

További információ: [a privát hivatkozás legfontosabb előnyei](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Működés

Azure Monitor a privát hivatkozás hatóköre olyan csoportosítási erőforrás, amely egy vagy több privát végpont (és így a virtuális hálózatok) egy vagy több Azure Monitor erőforráshoz való csatlakoztatására szolgál. Az erőforrások közé tartoznak Log Analytics munkaterületek és Application Insights összetevők.

![Erőforrás-topológia ábrája](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLSs is tartozhat, de egyetlen VNet nem csatlakoztatható egynél több AMPLS. 

## <a name="planning-based-on-your-network"></a>Tervezés a hálózat alapján

A AMPLS-erőforrások beállítása előtt vegye figyelembe a hálózat elkülönítésének követelményeit. Értékelje ki a virtuális hálózatok hozzáférését a nyilvános internethez, valamint az egyes Azure Monitor erőforrások (azaz Application Insights összetevők és Log Analytics munkaterületek) hozzáférési korlátozásait.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Annak kiértékelése, hogy mely virtuális hálózatok csatlakozzanak egy privát kapcsolathoz

Kezdje azzal, hogy kiértékeli, hogy a virtuális hálózatok (virtuális hálózatok-EK) közül melyiknek van korlátozott hozzáférése az internethez. Előfordulhat, hogy az ingyenes internettel rendelkező virtuális hálózatok nem igényelnek privát hivatkozást a Azure Monitor erőforrásainak eléréséhez. A virtuális hálózatok-hez csatlakozó figyelési erőforrások korlátozhatják a bejövő forgalmat, és privát kapcsolati kapcsolatot igényelnek (a napló betöltéséhez vagy lekérdezéséhez). Ilyen esetekben még a nyilvános internethez hozzáféréssel rendelkező VNet is csatlakoznia kell ezen erőforrásokhoz egy privát kapcsolaton keresztül, illetve egy AMPLS keresztül.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Annak kiértékelése, hogy mely Azure Monitor erőforrásoknak legyen privát hivatkozása

Tekintse át a Azure Monitor összes erőforrását:

- Engedélyezi az erőforrás a naplók betöltését csak adott virtuális hálózatok található erőforrásokból?
- Csak bizonyos virtuális hálózatok lévő ügyfelek tudják lekérdezni az erőforrást?

Ha az ezekre a kérdésekre adott válasz igen, állítsa be a korlátozásokat a Log Analytics munkaterületek [konfigurálása](#configure-log-analytics) és a [Application Insights-összetevők konfigurálása](#configure-application-insights) és az erőforrások hozzárendelése egyetlen vagy több AMPLS. A figyelési erőforrásokhoz hozzáférő virtuális hálózatoknak rendelkeznie kell egy privát végponttal, amely csatlakozik a megfelelő AMPLS.
Ne feledje, hogy ugyanazokat a munkaterületeket vagy alkalmazásokat több AMPLS is összekapcsolhatjuk, hogy azok a különböző hálózatok számára is elérhetők legyenek.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Erőforrások figyelése a hálózat kisegítő lehetőségeinek együttes használatával

Mivel minden VNet csak egy AMPLS-erőforráshoz tud csatlakozni, össze kell csoportosítani azokat a figyelési erőforrásokat, amelyeknek elérhetőnek kell lenniük ugyanazon hálózatok számára. A csoportosítás kezelésének legegyszerűbb módja, ha VNet egy AMPLS hoz létre, és kiválasztja a hálózathoz való kapcsolódáshoz szükséges erőforrásokat. Az erőforrások csökkentése és a kezelhetőség javítása érdekében azonban érdemes lehet egy AMPLS használni a hálózatokon keresztül.

Ha például a belső virtuális hálózatok VNet1 és VNet2 csatlakozniuk kell a munkaterületekhez, a Workspace1-t és a Workspace2-t, Application Insights Application Insights pedig a 3. összetevőt, akkor a három erőforrást ugyanahhoz a AMPLS kell rendelni. Ha a VNet3 csak a Workspace1 elérését szeretné elérni, hozzon létre egy másik AMPLS-erőforrást, rendeljen hozzá Workspace1, és kapcsolódjon a VNet3 az alábbi ábrán látható módon:

![Topológia AMPLS diagramja](./media/private-link-security/ampls-topology-a-1.png)

![B AMPLS-topológia ábrája](./media/private-link-security/ampls-topology-b-1.png)

## <a name="example-connection"></a>Példa a kapcsolatok

Első lépésként hozzon létre egy Azure Monitor privát hivatkozás hatókör-erőforrást.

1. Nyissa meg az **erőforrás létrehozása** a Azure Portalban, és keresse meg **Azure monitor privát hivatkozás hatókörét**.

   ![Azure Monitor privát hivatkozás hatókörének keresése](./media/private-link-security/ampls-find-1c.png)

2. Kattintson a **Létrehozás**gombra.
3. Válasszon egy előfizetést és egy erőforráscsoportot.
4. Adja meg a AMPLS nevét. Érdemes olyan nevet használni, amely törli a hatókört és a biztonsági határt, hogy valaki ne szakítsa meg véletlenül a hálózati biztonsági határokat. Például: "AppServerProdTelem".
5. Kattintson a **felülvizsgálat + létrehozás**gombra. 

   ![Azure Monitor privát hivatkozás hatókörének létrehozása](./media/private-link-security/ampls-create-1d.png)

6. Hagyja meg az érvényesítési fázist, majd kattintson a **Létrehozás**gombra.

## <a name="connect-azure-monitor-resources"></a>Azure Monitor-erőforrások összekötése

A AMPLS először a privát végpontokhoz csatlakoztathatja, majd Azure Monitorheti az erőforrásokat, vagy fordítva, de a kapcsolódási folyamat gyorsabban halad, ha a Azure Monitor-erőforrásokat indítja el. A Azure Monitor Log Analytics-munkaterületek és Application Insights-összetevők AMPLS való összekapcsolásának menete

1. A Azure Monitor privát hivatkozás hatókörében kattintson a bal oldali menüben található **Azure monitor erőforrások** elemre. Kattintson a **Hozzáadás** gombra.
2. Adja hozzá a munkaterületet vagy összetevőt. A **Hozzáadás** gombra kattintva megjelenik egy párbeszédpanel, ahol kiválaszthatja Azure monitor erőforrásait. Böngészhet az előfizetések és az erőforráscsoportok között, vagy beírhatja a nevét a szűréshez. Válassza ki a munkaterületet vagy összetevőt, és kattintson az **alkalmaz** gombra a hatókörhöz való hozzáadásához.

    ![A hatókör kiválasztása UX képernyőkép](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Kapcsolódás privát végponthoz

Most, hogy rendelkezik a AMPLS kapcsolódó erőforrásokkal, hozzon létre egy privát végpontot a hálózat csatlakoztatásához. Ezt a feladatot a [Azure Portal Private link Centerben](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)vagy a Azure monitor privát hivatkozás hatókörén belül hajthatja végre, ahogy az ebben a példában is látható.

1. A hatókör-erőforrásban kattintson a bal oldali erőforrás menüben található **privát végponti kapcsolatok** elemre. A végpont létrehozási folyamatának elindításához kattintson a **privát végpont** elemre. A privát kapcsolat központban elindított kapcsolatokat itt is jóváhagyhatja, ha kiválasztja őket, és a **jóváhagyás**gombra kattint.

    ![A Private Endpoint Connections UX képernyőképe](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, amelyben élni kíván. A régiónak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, amelyhez csatlakozni fog.

3. Kattintson a **Tovább gombra: erőforrás**. 

4. Az erőforrás képernyőjén

   a. Válassza ki az **előfizetést** , amely tartalmazza a Azure monitor saját hatókörű erőforrását. 

   b. Az **erőforrástípus mezőben**válassza a **Microsoft. ininsights/privateLinkScopes**lehetőséget. 

   c. Az **erőforrás** legördülő menüben válassza ki a korábban létrehozott saját hivatkozás hatókörét. 

   d. Kattintson a **Tovább gombra: konfigurációs >**.
      ![Képernyőkép a privát végpont létrehozása elemről](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. A konfigurációs ablaktáblán

   a.    Válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyhez csatlakozni szeretne a Azure monitor erőforrásaihoz. 
 
   b.    Válassza az **Igen** lehetőséget a **saját DNS-zónába való integráláshoz**, és hagyja, hogy automatikusan létrehozzon egy új saját DNS zónát. 
 
   c.    Kattintson az **Áttekintés + létrehozás** elemre.
 
   d.    Az érvényesítési fázis engedélyezése. 
 
   e.    Kattintson a **Létrehozás** lehetőségre. 

    ![Képernyőkép a Select Private Endpoint2 létrehozásáról](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Ezzel létrehozott egy új privát végpontot, amely ehhez a Azure Monitor privát kapcsolati hatókörhöz csatlakozik.

## <a name="configure-log-analytics"></a>Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Azure Monitor Log Analytics munkaterület-erőforrás a bal oldali menüpont **hálózati elkülönítése** . Ebben a menüben két különböző állapotot is megadhat. 

![LA hálózati elkülönítés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt a Log Analytics-erőforrást bármely olyan Azure Monitor magánhálózati kapcsolati hatókörhöz, amelyhez hozzáféréssel rendelkezik. Kattintson a **Hozzáadás** gombra, és válassza ki a Azure monitor privát hivatkozás hatókörét.  A kapcsolódáshoz kattintson az **alkalmaz** gombra. Az összes csatlakoztatott hatókör megjelenik ezen a képernyőn. Ez a kapcsolat lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt a munkaterületet. A kapcsolat létrehozása ugyanaz, mint a [Azure monitor erőforrások csatlakoztatásakor](#connect-azure-monitor-resources)a hatókörhöz való csatlakozáskor.  

Másodszor, azt is szabályozhatja, hogy ez az erőforrás Hogyan érhető el a fent felsorolt privát kapcsolati hatókörökön kívülről. Ha a **nem**értékre állítja a **nyilvános hálózati hozzáférés engedélyezése lehetőséget** , akkor a csatlakoztatott hatókörökön kívüli gépek nem tölthetnek fel adatot erre a munkaterületre. Ha a **nyilvános hálózati hozzáférés engedélyezése a** **nem**értékre van állítva, akkor a hatókörön kívüli gépek nem férhetnek hozzá a munkaterületen lévő adatforrásokhoz. Ez az adat magában foglalja a munkafüzetek, irányítópultok, a lekérdezési API-alapú ügyfél-élmények, a Azure Portali elemzések és egyéb funkciók elérését. A Log Analytics-adatfelhasználást használó Azure Portalon kívül futó tapasztalatoknak is futniuk kell a privát csatolt VNET belül.

A hozzáférés ilyen módon történő korlátozása csak a munkaterületen lévő adatértékekre vonatkozik. A konfiguráció módosításait, beleértve a hozzáférési beállítások be-és kikapcsolását is, Azure Resource Manager kezeli. A megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával korlátozhatja a hozzáférést a Resource Managerhez. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](roles-permissions-security.md).

> [!NOTE]
> A munkaterületre a [diagnosztikai beállításokon](diagnostic-settings.md) keresztül feltöltött naplók és mérőszámok egy biztonságos privát Microsoft-csatornán haladnak át, és ezeket a beállításokat nem szabályozzák.

## <a name="configure-application-insights"></a>Application Insights konfigurálása

Nyissa meg az Azure Portalt. A Azure Monitor Application Insights összetevő-erőforrás a bal oldali menüpont **hálózati elkülönítése** . Ebben a menüben két különböző állapotot is megadhat.

![AI hálózati elkülönítés](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt a Application Insights-erőforrást, hogy Azure Monitor privát hivatkozás hatókörét, amelyhez hozzáféréssel rendelkezik. Kattintson a **Hozzáadás** gombra, és válassza ki a **Azure monitor privát hivatkozás hatókörét**. A kapcsolódáshoz kattintson az Alkalmaz gombra. Az összes csatlakoztatott hatókör megjelenik ezen a képernyőn. Ez a kapcsolat lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt az összetevőt. A kapcsolat létrehozása ugyanaz, mint a [Azure monitor erőforrások csatlakoztatásakor](#connect-azure-monitor-resources)a hatókörhöz való csatlakozáskor. 

Másodszor, azt is szabályozhatja, hogy ez az erőforrás Hogyan érhető el a korábban felsorolt privát kapcsolati hatókörökön kívülről. Ha a **nyilvános hálózati hozzáférés engedélyezése** a **nem**értékre van állítva, akkor a csatlakoztatott hatókörökön kívüli gépek vagy SDK-k nem tölthetnek fel adatot ebbe az összetevőbe. Ha a **nyilvános hálózati hozzáférés engedélyezése a** **nem**értékre van állítva, akkor a hatókörön kívüli gépek nem férnek hozzá a Application Insights erőforrásban lévő adatforrásokhoz. Az adatok közé tartozik az APM-naplók, a metrikák és az élő metrikai stream elérésének, valamint a többek között a munkafüzetek, az irányítópultok, a lekérdezési API-alapú ügyfél-élmények, a Azure Portal elemzések és egyebek. 

Vegye figyelembe, hogy a portálon kívüli felhasználási élményeknek is futniuk kell a felügyelt munkaterheléseket tartalmazó privát csatolt VNET belül. 

A megfigyelt számítási feladatokat üzemeltető erőforrásokat a privát hivatkozáshoz kell hozzáadnia. Az alábbi [dokumentációból](https://docs.microsoft.com/azure/app-service/networking/private-endpoint) megtudhatja, hogyan teheti meg ezt a app Services.

A hozzáférés ezen a módon való korlátozása csak a Application Insights erőforrásban lévő értékekre vonatkozik. A konfiguráció módosításait, beleértve a hozzáférési beállítások be-és kikapcsolását is, Azure Resource Manager kezeli. Ehelyett a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával korlátozza a hozzáférést a Resource Managerhez. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](roles-permissions-security.md).

> [!NOTE]
> A munkaterület-alapú Application Insights teljes biztonsága érdekében le kell zárnia a Application Insights erőforráshoz és a mögöttes Log Analytics munkaterülethez való hozzáférést.
>
> A kód szintű diagnosztika (Profiler/Debugger) esetében meg kell adnia a saját Storage-fiókját a privát kapcsolat támogatásához. Ehhez a [dokumentációban](https://docs.microsoft.com/azure/azure-monitor/app/profiler-bring-your-own-storage) olvashat.

## <a name="use-apis-and-command-line"></a>API-k és parancssor használata

A korábban ismertetett folyamatot Azure Resource Manager sablonok és parancssori felületek segítségével automatizálhatja.

A privát hivatkozások hatókörének létrehozásához és kezeléséhez használja [az az monitor Private-link-scope](https://docs.microsoft.com/cli/azure/monitor/private-link-scope?view=azure-cli-latest)lehetőséget. Ezzel a paranccsal hatóköröket hozhat létre, Log Analytics munkaterületeket és Application Insights összetevőket rendelhet hozzá, és hozzáadhat/eltávolíthat/engedélyezhet privát végpontokat.

A hálózati hozzáférés kezeléséhez használja a jelzőket `[--ingestion-access {Disabled, Enabled}]` és `[--query-access {Disabled, Enabled}]` [log Analytics munkaterületeket](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) , vagy [Application Insights összetevőket](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Egyéni naplók gyűjtése privát kapcsolaton keresztül

A Storage-fiókok az egyéni naplók betöltési folyamatában használatosak. Alapértelmezés szerint a szolgáltatás által felügyelt Storage-fiókok használatosak. Az egyéni naplók privát hivatkozásokon való betöltéséhez azonban saját Storage-fiókokat kell használnia, és hozzá kell rendelnie őket Log Analytics munkaterülethez. Az ilyen fiókok [parancssorból](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest)történő beállításával kapcsolatos további részletekért tekintse meg a következő témakört:.

A saját Storage-fiók létrehozásával kapcsolatos további információkért lásd: [felhasználói tulajdonú Storage-fiókok a naplók](private-storage.md) betöltéséhez

## <a name="restrictions-and-limitations"></a>Korlátozások és korlátozások

### <a name="agents"></a>Ügynökök

A Windows-és Linux-ügynökök legújabb verzióit magánhálózatok használatával kell használni a biztonságos telemetria betöltéséhez Log Analytics munkaterületek számára. A régebbi verziók nem tölthetik fel a belső hálózaton lévő megfigyelési adatok feltöltését.

**Log Analytics Windows-ügynök**

Használja a Log Analytics Agent 10.20.18038.0 vagy újabb verzióját.

**A Log Analytics Linux-ügynöke**

Használja az ügynök 1.12.25 vagy újabb verzióját. Ha nem tudja, futtassa a következő parancsokat a virtuális gépen.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

A Azure Monitor-portál használatának, például a Application Insights és a Log Analytics használatához engedélyeznie kell a Azure Portal és a Azure Monitor bővítmények számára a magánhálózatok elérését. Adja hozzá a **AzureActiveDirectory**, a **AzureResourceManager**, a **AzureFrontDoor. FirstParty**és a **AzureFrontDoor. frontend** [szolgáltatás címkéit](../../firewall/service-tags.md) a tűzfalhoz.

### <a name="programmatic-access"></a>Szoftveres hozzáférés

Ha a REST APIt, a [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) -t vagy a PowerShellt a magánhálózaton lévő Azure monitor használatával szeretné használni, adja hozzá a**AzureActiveDirectory** és a **AzureResourceManager** [szolgáltatáshoz](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)a tűzfalhoz.  

A címkék hozzáadásával olyan műveleteket hajthat végre, mint például a naplózási adatok lekérdezése, Log Analytics munkaterületek és AI-összetevők létrehozása és kezelése.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-letöltések a Content Delivery Networkből

Csomagolja be a JavaScript-kódot a parancsfájlba, hogy a böngésző ne kísérelje meg a kód letöltését a CDN-ből. Példa a [githubon](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="log-analytics-solution-download"></a>Log Analytics megoldás letöltése

Ha engedélyezni szeretné, hogy a Log Analytics ügynök letöltse a megoldási csomagokat, adja hozzá a megfelelő teljes tartományneveket a tűzfal engedélyezési listájához. 


| Felhőalapú környezet | Ügynök erőforrása | Portok | Irány |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | Kimenő
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Kimenő
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Kimenő

### <a name="browser-dns-settings"></a>Böngésző DNS-beállításai

Ha privát kapcsolaton keresztül csatlakozik a Azure Monitor-erőforrásokhoz, ezen erőforrás felé irányuló forgalomnak a hálózaton konfigurált privát végponton kell haladnia. A magánhálózati végpont engedélyezéséhez frissítse a DNS-beállításokat a [Kapcsolódás privát végponthoz](#connect-to-a-private-endpoint)című részben leírtak szerint. Egyes böngészők a beállított beállítások helyett a saját DNS-beállításait használják. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végpontokhoz, és teljesen megkerüli a privát hivatkozást. Győződjön meg arról, hogy a böngészők beállításai nem felülbírálják vagy gyorsítótárazzák a régi DNS-beállításokat. 

## <a name="next-steps"></a>További lépések

- Tudnivalók a [privát tárterületről](private-storage.md)
