---
title: Mi az Azure webalkalmazási tűzfal az Azure-beli bejárati ajtóhoz? (Előzetes verzió)
description: Ismerje meg, hogy az Azure-webalkalmazási tűzfal az Azure bejárati ajtó szolgáltatás számára hogyan védi a webalkalmazásait a rosszindulatú támadásoktól.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 624a5af59a39cb0ebf647f549f3a40ed56f78b9e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73549224"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Mi az Azure webalkalmazási tűzfal az Azure-beli bejárati ajtóhoz?

Az Azure webalkalmazási tűzfala (WAF) központi védelmet biztosít az Azure Front Door használatával globálisan biztosított webalkalmazások számára. A tűzfal célja a webszolgáltatások gyakori támadások és biztonsági rések elleni védelme, valamint a szolgáltatás magas rendelkezésre állásának biztosítása a megfelelőségi követelmények teljesítése mellett.


A webalkalmazások egyre inkább a kártékony támadások, például a szolgáltatási árvizek, az SQL-injektálási támadások és a helyek közötti parancsfájlok elleni támadások célpontja. Ezek a kártékony támadások a szolgáltatás leállását és adatvesztését okozhatják, jelentős fenyegetést jelenthetnek a webalkalmazások tulajdonosainak.

Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban reagálhat a biztonsági fenyegetésekre azáltal, hogy az egyes webalkalmazások védelme helyett egy ismert biztonsági rést javít egy központi helyen.

A WAF globális és központosított megoldás. Üzembe helyezése az Azure hálózati peremhálózati helyein történik a világ minden részén, és minden bejövő kérelem egy WAF-t használó webalkalmazáshoz a bejárati ajtón keresztül, a hálózat peremén történik. Ez lehetővé teszi a WAF számára, hogy megakadályozza a rosszindulatú támadásokat a támadási forrásokhoz, mielőtt belépnek a virtuális hálózatba, és globális védelmet biztosítanak a teljesítmény feláldozása nélkül. A WAF szabályzatok könnyedén összekapcsolhatók az előfizetésben lévő bejárati ajtókkal, és az új szabályok percek alatt üzembe helyezhetők, így gyorsan reagálhat a veszélyforrások változására.

![Azure webalkalmazási tűzfal](./media/waf-overview/web-application-firewall-overview.png)

Az Azure WAF a Application Gateway használatával is engedélyezhető. További információ: [webalkalmazási tűzfal](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>WAF szabályzat és szabályok

Beállíthat egy WAF szabályzatot, és hozzárendelheti a szabályzatot egy vagy több előtér-előtérben a védelemhez. A WAF szabályzatok a biztonsági szabályok két típusát alkotják:

- az ügyfél által létrehozott egyéni szabályok.

- felügyelt szabálykészlet, amely az Azure által felügyelt előre konfigurált szabályok gyűjteménye.

Ha mindkettő létezik, az egyéni szabályok feldolgozása a felügyelt szabálykészlet szabályainak feldolgozása előtt történik meg. A szabályok egyeztetési feltételből, prioritásból és műveletből állnak. Támogatott tevékenységtípusok: Engedélyezés, LETILTÁS, napló és átirányítás. A felügyelt és az egyéni szabályok kombinálásával teljes mértékben testre szabott szabályzatot hozhat létre, amely megfelel az adott alkalmazás védelmi követelményeinek.

A szabályzaton belüli szabályok feldolgozása egy rangsorolt sorrendben történik, ahol a prioritás egy egyedi egész szám, amely meghatározza a feldolgozott szabályok sorrendjét. A kisebb egész szám nagyobb prioritást jelöl, és a rendszer kiértékeli azokat, mielőtt a szabályok magasabb egész értékkel rendelkeznek. A szabály egyeztetése után a szabályban definiált megfelelő művelet lesz alkalmazva a kérelemre. Az ilyen egyezés feldolgozása után az alacsonyabb prioritású szabályok feldolgozása még nem történik meg.

Egy bejárati ajtó által szállított webalkalmazásnak egyszerre csak egy WAF szabályzata lehet társítva. Azonban a WAF szabályzatok nélkül is beállíthatja a bejárati ajtót. Ha van WAF-házirend, a rendszer replikálja az összes peremhálózati helyszínre, hogy biztosítsa az egységességet a globális biztonsági házirendek között.

## <a name="waf-modes"></a>WAF módok

A WAF házirend a következő két módban való futtatásra konfigurálható:

- **Észlelési mód:** Ha az észlelési módban fut, a WAF nem hajt végre más műveleteket, mint a figyelők, és naplózza a kérést és a megegyező WAF-szabályt a WAF-naplókhoz. Bekapcsolhatja a naplózási diagnosztikát a bejárati ajtóhoz (a portál használatakor ez a **diagnosztika** szakaszban érhető el a Azure Portal).

- **Megelőzési mód:** Ha a rendszer prevenciós módban történő futtatásra van konfigurálva, akkor a WAF a megadott műveletet hajtja végre, ha egy kérelem megfelel egy szabálynak, és ha talál egyezést, nem értékeli ki az alacsonyabb prioritású további szabályokat. A rendszer a WAF-naplókban is naplózza a megfeleltetett kérelmeket.

## <a name="waf-actions"></a>WAF műveletek

A WAF-ügyfelek az egyik műveletből választhatnak, ha egy kérelem megfelel egy szabály feltételeinek:

- **Engedélyezés:**  A kérelem áthalad a WAF, és a rendszer továbbítja a háttérbe. Az alacsonyabb prioritású szabályok nem tudják blokkolni ezt a kérést.
- **Letiltás:** A kérés le van tiltva, és a WAF választ küld az ügyfélnek, és nem továbbítja a kérést a háttér felé.
- **Napló:**  A rendszer naplózza a kérelmet a WAF-naplókban, és a WAF folytatja az alacsonyabb prioritású szabályok kiértékelését.
- **Átirányítás:** A WAF átirányítja a kérést a megadott URI-ra. A megadott URI egy házirendi szintű beállítás. A konfigurálást követően a rendszer az **átirányítási** műveletnek megfelelő összes kérelmet elküldi az adott URI-nak.

## <a name="waf-rules"></a>WAF-szabályok

A WAF szabályzatok két különböző típusú biztonsági szabályt tartalmazhatnak – egyéni szabályokat, amelyeket az ügyfél és a felügyelt szabályrendszerek, valamint az Azure által felügyelt előre konfigurált szabályok határoznak meg.

### <a name="custom-authored-rules"></a>Egyéni létrehozott szabályok

Az egyéni szabályokat a következőképpen állíthatja be: WAF

- **IP-engedélyezési lista és blokkolási lista:** Egyéni szabályokat is beállíthat a webalkalmazásokhoz való hozzáférés vezérlésére az ügyfél IP-címeinek vagy IP-címtartományok listája alapján. Az IPv4-és IPv6-címek egyaránt támogatottak. A lista beállítható úgy, hogy blokkolja vagy engedélyezze ezeket a kéréseket, ahol a forrás IP-cím megegyezik a listában szereplő IP-címekkel. Az ügyfél IP-címe különbözik az IP-WAF, például akkor, amikor egy ügyfél egy proxyn keresztül fér hozzá a WAF-hez. Az [IP-korlátozási szabályokat](https://docs.microsoft.com/azure/frontdoor/waf-front-door-configure-ip-restriction) az ügyfél IP-címei (RemoteAddr) vagy a WAF (SocketAddr) által látott IP-címek alapján is létrehozhatja. A SocketAddr IP-korlátozási szabályának konfigurálása jelenleg a PowerShell és az Azure CLI használatával támogatott.

- **Földrajzi alapú hozzáférés-vezérlés:** Egyéni szabályokat is beállíthat a webalkalmazásokhoz való hozzáférés vezérléséhez az ügyfél IP-címéhez társított országkód alapján.

- **Http-paraméterek-alapú hozzáférés-vezérlés:** Az egyéni szabályokat beállíthatja a HTTP/HTTPS-kérelmek paramétereinek megfelelő karakterláncok, például a lekérdezési karakterláncok, az argumentumok, a kérelem URI-ja, a kérelem fejléce és a kérelem törzse alapján.

- **Kérelem metódus-alapú hozzáférés-vezérlése:** A kérelem HTTP-kérelmi módszere alapján egyéni szabályokat is beállíthat, például GET, PUT vagy HEAD.

- **Méret megkötése:** Egyéni szabályokat a kérelem adott részeinek hosszán, például a lekérdezési karakterlánc, az URI vagy a kérelem törzse alapján is beállíthat.

- **Korlátozási szabályok:** A díjszabás-ellenőrzési szabály az ügyfél IP-címétől érkező rendellenes nagy forgalom korlátozása. Egy percen belül beállíthat egy küszöbértéket az ügyfél IP-címén engedélyezett webes kérelmek számára. Vegye figyelembe, hogy a küszöbérték feletti további kérések a kérelmek számának frissítése közben is beszerezhetők. A díjszabási korlátozás kombinálható olyan további egyeztetési feltételekkel, mint például a HTTP (S) paraméterek, amelyek megfelelnek a részletes díjszabási szabályozásnak.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészlet

Az Azure által felügyelt szabálykészlet egyszerű módszert kínál a védelem közös biztonsági fenyegetésekkel való üzembe helyezésére. Mivel az ilyen szabályrendszerek az Azure felügyeli, a szabályok az új támadási aláírások elleni védelemhez szükséges módon frissülnek. A nyilvános előzetes verzióban az Azure által felügyelt alapértelmezett szabálykészlet a következő veszélyforrás-kategóriákra vonatkozó szabályokat tartalmazza:

- Helyek közötti parancsfájlok futtatása
- Java-támadások
- Helyi fájl felvétele
- PHP-injektálási támadások
- Távoli parancs végrehajtása
- Távoli fájl belefoglalása
- Munkamenet-rögzítés
- SQL-injektálás elleni védelem
- Protokoll-támadók

Az alapértelmezett szabálykészlet verziószáma akkor nő, amikor új támadási aláírásokat adnak hozzá a szabálykészlet számára.
Az alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van a WAF házirendek észlelési módjában. Az alapértelmezett szabálykészlet egyes szabályait letilthatja vagy engedélyezheti az alkalmazásra vonatkozó követelmények teljesítéséhez. Szabályként beállíthat meghatározott műveleteket is (Engedélyezés/LETILTÁS/átirányítás/napló). Az alapértelmezett művelet a LETILTÁS. Emellett az egyéni szabályok is konfigurálhatók ugyanabban a WAF-házirendben, ha meg szeretné kerülni az alapértelmezett szabálykészlet összes előre konfigurált szabályát.
Az egyéni szabályokat a rendszer mindig alkalmazza az alapértelmezett szabálykészlet szabályainak kiértékelése előtt. Ha egy kérelem megfelel egy egyéni szabálynak, a rendszer alkalmazza a megfelelő szabályt, és a kérést letiltják vagy átadják a háttérnek a további egyéni szabályok vagy az alapértelmezett szabálykészlet szabályainak meghívása nélkül. Emellett lehetősége van az alapértelmezett szabálykészlet eltávolítására is a WAF-házirendekben.


### <a name="bot-protection-rule-preview"></a>Robot Protection-szabály (előzetes verzió)

A felügyelt robot védelmi szabálykészlet engedélyezhető a WAF számára, hogy egyéni műveleteket hajtson végre az ismert bot-kategóriákból érkező kérésekre.
3 bot-kategória támogatott: rossz robotok, jó robotok és ismeretlen robotok. A hibás robotok kategóriában az egyik szabály a clieny-címek hírneve alapján rosszindulatú robotokat észlel. Az IP-cím hírneve a Microsoft Threat Intelligence-hírcsatornából származik. A [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja. Emellett az ismert keresőmotorokra irányuló kéréseket is rosszindulatúként észleli a rendszer.
A jó robotok ellenőrzése seacrh motorokkal történik. Az ismeretlen kategóriák a kérelmeket robotokként azonosítják, de ismeretlen szándékokkal. Egyéni műveleteket is beállíthat a különböző kategóriákba tartozó robotok blokkolásához, engedélyezéséhez, naplózásához és átirányításához.

![Robot Protection-szabály beállítása](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a bot Protection engedélyezve van, a bot-szabályoknak megfelelő bejövő kérelmek naplózása a FrontdoorWebApplicationFirewallLog-naplóban történik. WAF-naplókat is elérhet a Storage-fiókból, az Event hub-ból vagy a log Analyticsből. 

## <a name="configuration"></a>Konfiguráció

Az összes WAF konfigurálása és telepítése teljes mértékben támogatott Azure Portal, REST API-k, Azure Resource Manager sablonok és Azure PowerShell használatával.

## <a name="monitoring"></a>Figyelés

A WAF figyelése a bejárati ajtón integrált Azure Monitor a riasztások nyomon követésére és a forgalmi trendek egyszerű figyelésére.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [konfigurálhat WAF szabályzatot a bejárati ajtóhoz a Azure Portal használatával](waf-front-door-create-portal.md)
