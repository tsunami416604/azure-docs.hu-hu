---
title: Altartományon belüli átvétel megakadályozása Azure DNS alias-rekordokkal és Azure App Service egyéni tartományának ellenőrzése
description: Ismerje meg, hogyan kerülheti el az altartományok átvételének gyakori súlyossági kockázatát
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890824"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>A DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása

Ez a cikk ismerteti az altartományok átvételének gyakori biztonsági kockázatát, valamint azokat a lépéseket, amelyekkel elháríthatja a megoldást.


## <a name="what-is-subdomain-takeover"></a>Mi az altartomány átvétele?

Az altartományok közötti átvételek gyakori, nagy súlyosságú fenyegetést jelentenek az olyan szervezetek számára, amelyek rendszeresen hoznak létre és törölnek sok erőforrást. Az altartományok átvétele akkor fordulhat elő, ha olyan DNS-rekorddal rendelkezik, amely egy kiépített Azure-erőforrásra mutat. Az ilyen DNS-rekordokat "lelógó DNS"-bejegyzéseknek is nevezzük. A CNAME rekordok különösen sebezhetők a fenyegetéssel szemben.

Egy altartomány átvételének gyakori forgatókönyve:

1. Létrejön egy webhely. 

    Ebben a példában a `app-contogreat-dev-001.azurewebsites.net` .

1. A rendszer hozzáadja a CNAME-bejegyzést a webhelyhez mutató DNS-bejegyzéshez. 

    Ebben a példában a következő felhasználóbarát név lett létrehozva: `greatapp.contoso.com` .

1. Néhány hónap elteltével a hely már nem szükséges, ezért a rendszer törli a megfelelő DNS-bejegyzés törlése **nélkül** . 

    A CNAME DNS-bejegyzés most "lóg".

1. Szinte azonnal a hely törlése után a fenyegetést jelentő színész felfedi a hiányzó helyet, és létrehozza a saját webhelyét a következő helyen: `app-contogreat-dev-001.azurewebsites.net` .

    Most pedig a `greatapp.contoso.com` fenyegetést jelentő színész Azure-webhelyére irányuló forgalom, valamint a fenyegetést kezelő személy felügyeli a megjelenő tartalmat. 

    A rendszer kihasználta a DNS-t, és a contoso altartománya ("GreatApp") az altartomány átvételének áldozatává vált. 

![Altartomány átvétele egy kiépített webhelyről](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Az altartomány átvételének kockázatai

Ha egy DNS-rekord olyan erőforrásra mutat, amely nem érhető el, a rekordot el kell távolítani a DNS-zónából. Ha még nem törölte, a "lelógó DNS" rekord, amely lehetővé teszi az altartományok átvételét.

A kilógó DNS-bejegyzések révén a fenyegetést jelentő szereplők átvehetik a társított DNS-név irányítását egy rosszindulatú webhely vagy szolgáltatás üzemeltetéséhez. A szervezet altartományában található kártékony lapok és szolgáltatások a következőket okozhatják:

- **Az altartomány tartalmának elvesztése** – negatívan megnyomható, hogy a szervezet nem tudja védeni a tartalmát, valamint a márka sérülését és a megbízhatóság elvesztését.

- **Cookie-k betakarítása a gyanútlan látogatóktól** – gyakori a webalkalmazások számára, hogy a munkamenet-cookie-kat az altartományokra (*. contoso.com) tegyék elérhetővé, ezért bármely altartomány elérheti őket. A fenyegetésekkel rendelkező szereplők az altartományok átvételével hozhatnak létre valódi keresett oldalt, megszerezhetik a gyanútlan felhasználókat a látogatásuk és a cookie-k (akár biztonságos cookie-k) betakarítására is. Gyakori tévhit, hogy az SSL-tanúsítványok használata megvédi a webhelyét és a felhasználók cookie-jait egy átvételből. Egy fenyegetést tartalmazó színész azonban használhatja a eltérített altartományt, hogy érvényes SSL-tanúsítványt kapjon és fogadjon. Az érvényes SSL-tanúsítványok hozzáférést biztosítanak számukra a cookie-k védelméhez, és tovább növelhetik a kártékony webhely vélt legitimitását.

- **Adathalászat-kampányok** – az autentikus megjelenésű altartományok használhatók az adathalászat-kampányok során. Ez a kártékony webhelyekhez és az olyan MX-rekordokhoz is igaz, amelyek lehetővé tennék a veszélyforrások számára, hogy olyan e-maileket kapjanak, amelyek egy ismert biztonságos márka megbízható altartományára irányulnak.

- **További kockázatok** – a kártékony webhelyek más klasszikus támadásokra is felhasználhatók, mint például az XSS, a CSRF, a CORS megkerülés és sok más.



## <a name="preventing-dangling-dns-entries"></a>A DNS-bejegyzések lelógók megakadályozása

Győződjön meg arról, hogy a szervezete olyan folyamatokat hozott létre, amelyekkel megelőzhető a DNS-bejegyzések bevezetése, és az eredményül kapott altartomány-átvételek a biztonsági program kulcsfontosságú részét

A jelenleg elérhető megelőző intézkedések alább láthatók.


### <a name="use-azure-dns-alias-records"></a>Azure DNS alias-rekordok használata

A Azure DNS [alias-rekordjai](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) megakadályozhatja, hogy egy DNS-rekord életciklusát egy Azure-erőforrással összekapcsolja. Vegyünk például egy olyan DNS-rekordot, amely alias-rekordként van minősítve, hogy egy nyilvános IP-címre vagy egy Traffic Manager profilra mutasson. Ha törli a mögöttes erőforrásokat, a DNS-alias rekord üres halmaz lesz. A továbbiakban nem hivatkozik a törölt erőforrásra. Fontos megjegyezni, hogy az alias-rekordokkal védhető, hogy milyen korlátokat biztosít a védelem. A lista jelenleg a következőre korlátozódik:

- Azure Front Door
- Traffic Manager-profilok
- Azure Content Delivery Network-(CDN-) végpontok
- Nyilvános IP-címek

A korlátozott szolgáltatási ajánlatok ellenére az alias-rekordok használatát javasoljuk, hogy amikor csak lehetséges, használjon alias-rekordokat.

[További](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) információ a Azure DNS alias-rekordjainak képességeiről.



### <a name="use-azure-app-services-custom-domain-verification"></a>Az Azure App Service egyéni tartományának ellenőrzése

Azure App Service DNS-bejegyzéseinek létrehozásakor hozzon létre egy asuid. altartomány A tartomány-ellenőrzési AZONOSÍTÓval rendelkező TXT-rekord. Ha egy ilyen TXT-rekord létezik, egyetlen másik Azure-előfizetés sem tudja érvényesíteni az egyéni tartományt, amelyet átvesznek. 

Ezek a rekordok nem akadályozzák meg, hogy valaki hozza létre a Azure App Servicet ugyanazzal a névvel, mint a CNAME-bejegyzésben. Anélkül, hogy bizonyítani tudja a tartománynév tulajdonjogát, a veszélyforrások nem fogadhatnak forgalmat, és nem vezérelhetik a tartalmat.

[További](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) információ arról, hogyan képezhető le egy meglévő egyéni DNS-név a Azure app Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Folyamatok létrehozása és automatizálása a fenyegetés enyhítése érdekében

Ez gyakran a fejlesztők és az operatív csapatok számára a kitakarítási folyamatok futtatására, hogy elkerülje a DNS-fenyegetéseket. Az alábbi eljárások segítségével biztosíthatja, hogy a szervezet elkerülje a fenyegetést. 

- **Megelőzési eljárások létrehozása:**

    - Tájékoztassa az alkalmazás-fejlesztőket a címek átirányításához, amikor erőforrásokat törölnek.

    - A szolgáltatások leszerelése után a szükséges ellenőrzések listájában helyezze el a "DNS-bejegyzés eltávolítása" lehetőséget.

    - Helyezzen [törlési zárolásokat](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) minden olyan erőforrásra, amely egyéni DNS-bejegyzést tartalmaz. A törlési zárolás azt jelzi, hogy a leképezést el kell távolítani az erőforrás megszüntetése előtt. Az ehhez hasonló mértékek csak a belső oktatási programok együttes használata esetén működhetnek.

- **Felderítési eljárások létrehozása:**

    - Rendszeresen tekintse át a DNS-rekordokat, és győződjön meg arról, hogy az altartományok mind le vannak képezve az Azure-erőforrásokra:

        - Létezik – lekérdezheti a DNS-zónákat az Azure altartományokra mutató erőforrásokhoz, például *. azurewebsites.net vagy *. cloudapp.azure.com (lásd [a hivatkozási listát](azure-domains.md)).
        - Ön rendelkezik a saját DNS-altartományai által megcélzott összes erőforrással.

    - Az Azure-beli teljes tartománynevek (FQDN) végpontok és az alkalmazás tulajdonosai szolgáltatás-katalógus karbantartása. A szolgáltatás-katalógus létrehozásához futtassa az alábbi Azure Resource Graph lekérdezési parancsfájlt. Ez a szkript a teljes tartománynév-végponti információit, amelyekhez hozzáfér, és egy CSV-fájlba exportálja azokat. Ha rendelkezik hozzáféréssel a bérlő összes előfizetéséhez, a szkript az alábbi minta parancsfájlban látható összes előfizetést figyelembe veszi. Ha az eredményeket egy adott előfizetésre szeretné korlátozni, szerkessze a szkriptet az ábrán látható módon.

        >[!IMPORTANT]
        > **Engedélyek** – a lekérdezés futtatása olyan felhasználóként, aki hozzáfér az összes Azure-előfizetéséhez. 
        >
        > **Korlátozások** – az Azure Resource Graph szabályozási és lapozási korlátokat tartalmaz, amelyeket érdemes figyelembe vennie, ha nagy Azure-környezettel rendelkezik. [További](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) információ a nagyméretű Azure Resource-adatkészletek használatáról. A következő minta-parancsfájl előfizetés-kötegeket használ a korlátozások elkerüléséhez.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        A típusok és `FQDNProperty` értékeik listája az előző erőforrás-gráf lekérdezésben megadott módon:

        |Erőforrás neve  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|Microsoft. Network/frontdoors|Properties. cName|
        |Azure Blob Storage|Microsoft. Storage/storageaccounts|Properties. primaryEndpoints. blob|
        |Azure CDN|Microsoft. CDN/profilok/végpontok|Properties. állomásnév|
        |Nyilvános IP-címek|Microsoft. Network/nyilvános IP|Properties. dnsSettings. FQDN|
        |Azure Traffic Manager|Microsoft. Network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Azure Container Instance|Microsoft. containerinstance/containergroups|Properties. Ip_cím. FQDN|
        |Azure API Management|Microsoft. apimanagement/szolgáltatás|Properties. hostnameConfigurations. hostName|
        |Azure App Service|Microsoft. Web/Sites|Properties. defaultHostName|
        |Azure App Service – bővítőhely|Microsoft. Web/Sites/Slots|Properties. defaultHostName|


- **Szervizelési eljárások létrehozása:**
    - A DNS-bejegyzések beolvasásakor a csapatnak meg kell vizsgálnia, hogy történt-e kompromisszum.
    - Vizsgálja meg, hogy a rendszer miért nem irányította újra az erőforrást, amikor leszerelték.
    - Törölje a DNS-rekordot, ha már nincs használatban, vagy mutasson a szervezete tulajdonában lévő megfelelő Azure-erőforrásra (FQDN).
 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a kapcsolódó szolgáltatásokról és az Azure-beli szolgáltatásokkal szembeni védelemről, tekintse meg a következő lapokat.

- [Azure DNS támogatja az egyéni tartományokhoz tartozó alias-rekordok használatát](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [A tartomány-ellenőrzési azonosító használata egyéni tartományok hozzáadásakor Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Azure PowerShell használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
