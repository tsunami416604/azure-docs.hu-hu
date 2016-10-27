<properties
   pageTitle="Application Gateway webalkalmazási tűzfal | Microsoft Azure"
   description="Ez az oldal egy áttekintést nyújt az Application Gateway webalkalmazási tűzfal működéséről."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amsriva"/>


# <a name="application-gateway-web-application-firewall-(preview)"></a>Application Gateway webalkalmazási tűzfal (előzetes verzió)

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások.
Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A webes támadások elleni központi védelem egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/WAF1.png)

Az Application Gateway egy alkalmazáskézbesítési vezérlőként működik, és SSL-lezárást, cookie-alapú munkamenet-affinitást, ciklikus időszeleteléses terheléselosztást, tartalomalapú útválasztást, valamint több webhely és biztonsági fejlesztés üzemeltetésének képességét kínálja. Az Application Gateway által kínált biztonsági fejlesztések között szerepel az SSL-házirend kezelése és a végpontok közötti SSL-támogatás. Az ADC-ajánlatba közvetlenül integrált WAF (webalkalmazási tűzfal) bevezetésével megerősítjük a szolgáltatás alkalmazásbiztonsági képességeit. Ez egy könnyen konfigurálható központi helyet biztosít a webalkalmazások kezeléséhez és a gyakori webes biztonsági rések elleni védelemhez.

A WAF konfigurálása az Application Gatewayen az alábbi előnyöket nyújtja:

- A háttérkód módosítása nélkül védheti a webalkalmazásokat a webes biztonsági résektől és támadásoktól.
- Egyszerre több webalkalmazást védhet egy Application Gateway mögött. Az Application Gateway akár 20 webhely üzemeltetését is támogatja egyetlen átjáró mögött, amelyek mind védhetők a webes támadásokkal szemben.
- Az Application Gateway WAF-naplók által létrehozott valós idejű jelentés segítségével figyelheti a webalkalmazást a támadások tekintetében.
- Egyes megfelelőség-ellenőrzési funkciókhoz szükséges, hogy az összes internetkapcsolattal rendelkező végpontot WAF-megoldás védje. Az engedélyezett webalkalmazási tűzfallal rendelkező Application Gateway használatával teljesítheti ezeket a megfelelőségi követelményeket.

## <a name="overview"></a>Áttekintés

Az Application Gateway WAF egy új termékváltozatban (WAF SKU) érhető el, emellett előre konfigurálva van hozzá a ModSecurity és OWASP alapvető szabálykészlet, hogy alapvető védelmet nyújtson az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

- SQL-injektálás elleni védelem
- Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem
- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem
- HTTP protokoll megsértése elleni védelem
- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem
- HTTP szolgáltatásmegtagadás (többek között HTTP-elárasztás és lassú HTTP szolgáltatásmegtagadás-védelem) elleni védelem
- Robotprogramok, webbejárók és képolvasók elleni védelem
- Alkalmazások (vagyis Apache, IIS stb.) gyakori konfigurációs hibáinak észlelése

## <a name="waf-modes"></a>WAF-üzemmódok

Az Application Gateway WAF az alábbi két üzemmódban való futtatásra konfigurálható:

- **Észlelés üzemmód** – Amikor az Application Gateway WAF észlelés üzemmódban való futtatásra van konfigurálva, figyel és egy naplófájlba naplóz minden veszélyforrás-riasztást. A Diagnosztika szakasz segítségével ellenőrizze, hogy be van-e kapcsolva az Application Gateway naplózási diagnosztikája. Emellett ellenőrizze, hogy a WAF-napló ki van-e választva és be van-e kapcsolva.
- **Megelőzés üzemmód** – Amikor az Application Gateway WAF megelőzés üzemmódban való futtatásra van konfigurálva, aktívan blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy 403-as jogosulatlan hozzáférési kivételt kap, és a kapcsolat megszakad. A megelőzés üzemmód továbbra is naplózza az ilyen támadásokat a WAF-naplókban.

## <a name="application-gateway-waf-reports"></a>Application Gateway WAF – Jelentések

Az Application Gateway WAF részletes jelentéseket biztosít az összes észlelt fenyegetésről. A naplózás integrálva van az Azure Diagnostics Logs szolgáltatásba, a riasztások pedig JSON formátumban vannak rögzítve.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/waf2.png)

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF – A termékváltozat díjszabása

Az előzetes verzió ideje alatt az Application Gateway WAF használata nem von maga után további díjakat. A díjak felszámítása továbbra is a már meglévő alapszintű termékváltozat díja alapján történik. A WAF termékváltozat díjáról az általánosan elérhetővé válás időpontjában adunk tájékoztatást. Azon ügyfelek esetében, akik az Application Gateway WAF termékváltozatban való központi telepítését választották, csak az általánosan elérhetővé válásról tett bejelentést követően keletkeznek költségek a WAF termékváltozatra vonatkozóan.

## <a name="next-steps"></a>Következő lépések

Miután részletesebben megismerte a WAF képességeit, tekintse meg a [Webalkalmazási tűzfal konfigurálása Application Gatewayen](application-gateway-web-application-firewall-portal.md) című cikket.



<!--HONumber=Oct16_HO3-->


