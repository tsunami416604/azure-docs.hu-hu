---
title: HTTP nyers naplók Azure CDN
description: Ez a cikk a Azure CDN HTTP nyers naplókat ismerteti.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040164"
---
# <a name="azure-cdn-http-raw-logs"></a>HTTP nyers naplók Azure CDN
A nyers naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A nyers naplók eltérnek a tevékenység naplóitól. A Tevékenységnaplók biztosítják az Azure-erőforrásokon végzett műveletek láthatóságát. A nyers naplók az erőforrás műveleteinek rekordját adják meg. A nyers napló részletes információkat nyújt a CDN által fogadott összes kérelemről. 

> [!IMPORTANT]
> A HTTP nyers naplók szolgáltatás a Microsoft Azure CDN számára érhető el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="configuration---azure-portal"></a>Konfiguráció – Azure Portal

Nyers naplók konfigurálása a Azure CDNhoz a Microsoft profilból: 

1. A Azure Portal menüben válassza a **minden erőforrás**elemet  >>  **\<your-CDN-profile>** .

2. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.

3. Válassza a **+ diagnosztikai beállítások hozzáadása**elemet.

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > A nyers naplók csak a profil szintjén érhetők el, míg az összesített HTTP-állapotkódok naplói a végpont szintjén érhetők el.

4. A **diagnosztikai beállítások**területen adja meg a diagnosztikai **beállítások neve**alatt a diagnosztikai beállítás nevét.

5. Válassza ki a **naplót** , és állítsa be a megőrzési napokat.

6. Válassza ki a **célhely részleteit**. A cél beállításai a következők:
    * **Küldés a Log Analyticsnek**
        * Válassza ki az **előfizetést** és a **log Analytics munkaterületet**.
    * **Archiválás egy Storage-fiókba**
        * Válassza ki az **előfizetést** és a **Storage-fiókot**.
    * **Stream az Event hub-ba**
        * Válassza ki az **előfizetést**, az **Event hub-névteret**, az **Event hub nevét (nem kötelező)** és az **Event hub-szabályzat nevét**.

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-02.png)

7. Válassza a **Mentés** lehetőséget.

## <a name="configuration---azure-powershell"></a>Konfiguráció – Azure PowerShell

A [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) használatával konfigurálja a nyers naplók diagnosztikai beállításait.

Az adatmegőrzési adatok meghatározása a parancsban a **-RetentionInDays** beállítással történik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnosztikai naplók engedélyezése a Storage-fiókokban

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Ha egy Storage-fiókban szeretné engedélyezni a diagnosztikai naplókat, adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnosztikai naplók engedélyezése Log Analytics munkaterülethez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics munkaterülethez tartozó diagnosztikai naplók engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnosztikai naplók engedélyezése az Event hub-névtérhez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Az Event hub-névtér diagnosztikai naplóinak engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Nyers naplók tulajdonságai

A Microsoft-szolgáltatásból Azure CDN jelenleg nyers naplókat biztosít. A nyers naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával: 

| Tulajdonság              | Leírás                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| HttpMethod            | A kérelem által használt HTTP-metódus.                                                                                                                                                                     |
| HttpVersion           | A kérelem vagy a kapcsolatok típusa.                                                                                                                                                                   |
| RequestUri            | A fogadott kérelem URI-ja.                                                                                                                                                                         |
| RequestBytes          | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét.                                                                                                   |
| ResponseBytes         | A háttér-kiszolgáló által válaszként küldött bájtok.                                                                                                                                                    |
| UserAgent             | Az ügyfél által használt böngésző típusa.                                                                                                                                                               |
| ClientIp (Ügyfél IP-címe)              | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte.                                                                                                                                                  |
| Eltelt idő             | A művelet végrehajtásának időtartama (ezredmásodpercben).                                                                                                                                            |
| SecurityProtocol      | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás.                                                                                                                           |
| Végpont              | A CDN-végpont gazdagépe a szülő CDN-profil alatt van konfigurálva.                                                                                                                                   |
| Háttérbeli gazdagép neve     | Annak a háttér-gazdagépnek vagy-forrásnak a neve, ahol a kérelmeket küldik.                                                                                                                                |
| Elküldve a Origin shieldbe </br> (elavult) * **lásd a lenti elavult megjegyzést.** | Ha az értéke igaz, az azt jelenti, hogy a rendszer a lekérést a peremhálózati pop helyett a forrás pajzs gyorsítótárából választta. A Origin Shield egy szülő gyorsítótár, amely a gyorsítótár találati arányának növelésére szolgál.                                       |
| isReceivedFromClient | Ha az értéke igaz, az azt jelenti, hogy a kérelem az ügyféltől érkezett. Ha false (hamis) értékű, a kérelem a szélén (gyermek POP) található, és a forrás pajzstól (szülő POP) válaszol. 
| HttpStatusCode        | A proxy által visszaadott HTTP-állapotkód.                                                                                                                                                        |
| HttpStatusDetails     | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található.                                                                                              |
| Pop                   | A felhasználói kérésre válaszoló Edge-pop. A pop-rövidítések a megfelelő metrók repülőtéri kódjai.                                                                                   |
| Gyorsítótár állapota          | Azt jelzi, hogy az objektumot a gyorsítótárból adták-e vissza, vagy a forrásból származik-e.                                                                                                             |
> [!NOTE]
> A naplók a Log Analytics profil alatt tekinthetők meg egy lekérdezés futtatásával. A minta lekérdezés A következőhöz hasonlóan fog kinézni: AzureDiagnostics | where kategória = = "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Elküldve a Origin Shield elavulttá
A RAW log tulajdonság **isSentToOriginShield** elavult, és egy új mező **isReceivedFromClient**vált. Ha már használja az elavult mezőt, használja az új mezőt. 

A nyers naplók közé tartoznak a CDN Edge (Child POP) és a Origin Shield által létrehozott naplók. A Origin Shield olyan szülő csomópontokra utal, amelyek stratégiailag a világ minden részén találhatók. Ezek a csomópontok a forrás-kiszolgálókkal kommunikálnak, és csökkentik a forgalom terhelését a forrásnál. 

A forrásként szolgáló védelemre kerülő minden kérelem esetében 2 naplós bejegyzés található:

* Egy az Edge-csomópontok számára
* Az egyik a Origin Shield. 

Az Edge-csomópontok és a forrás pajzs alapján a kilépések és válaszok megkülönböztetése érdekében a megfelelő adatok beolvasásához használja a isReceivedFromClient mezőt. 

Ha az érték hamis, akkor az azt jelenti, hogy a kérést a rendszer a forrás pajzsról az Edge-csomópontokra válaszol. Ez a megközelítés hatékonyan hasonlítható össze a számlázási adattal rendelkező nyers naplók összehasonlításával. Nem számítunk fel díjat a forrásként szolgáló pajzs és a peremhálózati csomópont közötti kimenő forgalomért. A peremhálózati csomópontok és az ügyfelek közötti kimenő forgalomért számítunk fel díjat. 

**Kusto lekérdezési minta a forrásként szolgáló Log Analyticsban generált naplók kizárásához.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> A HTTP nyers naplók funkció automatikusan elérhető minden, a **2020. február 25**. után létrehozott vagy frissített profilhoz. A korábban létrehozott CDN-profilok esetében az egyiknek frissítenie kell a CDN-végpontot a naplózás beállítása után. Például megnyithatja a Geo-szűrést a CDN-végpontok területen, és blokkolhatja az országokat és régiókat, amelyek nem relevánsak a számítási feladatokhoz és a mentéshez. 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben engedélyezte a HTTP nyers naplókat a Microsoft CDN szolgáltatáshoz.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Elemzés](cdn-log-analysis.md) Azure CDN használati minták.

* További információ a [Azure monitorról](https://docs.microsoft.com/azure/azure-monitor/overview).

* [Log Analytics konfigurálása Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
