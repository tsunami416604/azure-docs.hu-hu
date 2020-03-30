---
title: Mi az Azure webalkalmazás tűzfal az Azure Bejárati ajtaján?
description: Ismerje meg, hogy az Azure Bejárati ajtaján található Azure webalkalmazás-tűzfal hogyan védi a webalkalmazásokat a rosszindulatú támadásokkal szemben.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77915639"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure webalkalmazás-tűzfal az Azure bejárati ajtaján

Az Azure Web Application Firewall (WAF) az Azure Bejárati ajtaján központosított védelmet nyújt a webalkalmazások számára. A WAF megvédi a webes szolgáltatásokat a gyakori biztonsági rések és biztonsági rések ellen. A szolgáltatás magas rendelkezésre állású marad a felhasználók számára, és segít a megfelelőségi követelmények teljesítésében.

A WAF on Front Door egy globális és központosított megoldás. Az Azure hálózati peremhálózati helyeken világszerte telepítve van. A WAF-kompatibilis webalkalmazások a Bejárati ajtó által a hálózat szélén küldött minden bejövő kérést megvizsgálnak. 

A WAF megakadályozza a támadási forrásokhoz közeli rosszindulatú támadásokat, mielőtt azok belépnének a virtuális hálózatba. A teljesítmény feláldozása nélkül globális védelmet kap. A WAF-szabályzat könnyen hivatkozhat az előfizetés bármely Bejárati ajtajának profiljára. Az új szabályok perceken belül üzembe helyezhetők, így gyorsan reagálhat a fenyegetésminták módosítására.

![Az Azure webalkalmazás tűzfala](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF politika és szabályok

Konfigurálhatja a WAF-házirendet, és ezt a házirendet egy vagy több bejárati ajtó előtéréhez társíthatja a védelem érdekében. A WAF-házirend kétféle biztonsági szabályból áll:

- az ügyfél által készített egyéni szabályok.

- felügyelt szabálykészletek, amelyek az Azure által felügyelt előre konfigurált szabálykészlet gyűjteménye.

Ha mindkettő jelen van, az egyéni szabályok feldolgozása a felügyelt szabálykészletben lévő szabályok feldolgozása előtt lesz. A szabály egyegyezési feltételből, prioritásból és műveletből áll. A támogatott művelettípusok a következők: ALLOW, BLOCK, LOG és REDIRECT. Létrehozhat egy teljesen testre szabott szabályzatot, amely megfelel az adott alkalmazásvédelmi követelményeknek a felügyelt és az egyéni szabályok kombinálásával.

A házirenden belüli szabályok feldolgozása prioritási sorrendben történik. A prioritás egy egyedi egész szám, amely meghatározza a feldolgozandó szabályok sorrendjét. A kisebb egész érték magasabb prioritást jelöl, és ezeket a szabályokat a rendszer a magasabb egész értékkel rendelkező szabályok előtt értékeli ki. Ha egy szabály egyeztetése van, a szabályban definiált megfelelő művelet lesz alkalmazva a kérelemre. Az ilyen egyezés feldolgozása után az alacsonyabb prioritású szabályok at nem dolgozzák fel tovább.

A Bejárati ajtó által szállított webalkalmazáshoz egyszerre csak egy WAF-házirend tartozhat. Azonban lehet egy bejárati ajtó konfiguráció nélkül WAF-házirendek társítva. Ha egy WAF-szabályzat van jelen, a rendszer replikálja az összes peremhálózati helyünkre, hogy világszerte egységes biztonsági házirendeket biztosítson.

## <a name="waf-modes"></a>WAF módok

A WAF-házirend beállítható úgy, hogy a következő két módban fusson:

- **Észlelési mód:** Észlelési módban a WAF nem tesz más műveletet, mint figyeli és naplózza a kérelmet és az egyező WAF-szabályt a WAF-naplókba. Bekapcsolhatja a naplózási diagnosztika a Bejárati ajtó. A portál használatakor nyissa meg a **Diagnosztika szakaszt.**

- **Megelőzési mód:** Megelőzési módban a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. Ha egyezést talál, a rendszer nem számít ki további alacsonyabb prioritású szabályokat. Az egyező kérelmek et is naplózza a WAF-naplók.

## <a name="waf-actions"></a>WAF-műveletek

A WAF-ügyfelek választhatnak, hogy az egyik műveletből futnak, ha egy kérelem megfelel egy szabály feltételeinek:

- **Engedélyezés:**  A kérelem áthalad a WAF-on, és a háttérbe kerül. További alacsonyabb prioritású szabályok nem tilthatják le ezt a kérést.
- **Blokk:** A kérelem le van tiltva, és a WAF választ küld az ügyfélnek anélkül, hogy a kérést a háttérrendszerbe továbbítana.
- **Napló:**  A kérelem be van jelentkezve a WAF-naplókba, és a WAF folytatja az alacsonyabb prioritású szabályok kiértékelését.
- **Átirányítás:** A WAF átirányítja a kérelmet a megadott URI-ba. A megadott URI egy házirendszintű beállítás. Konfigurálás után az **átirányítási** műveletnek megfelelő összes kérelmet elküldi a rendszer az adott URI-nak.

## <a name="waf-rules"></a>WAF-szabályok

A WAF-szabályzat kétféle biztonsági szabályból állhat : egyéni szabályokból, amelyeket az ügyfél és a felügyelt szabálykészletek, az Azure által felügyelt, előre konfigurált szabálykészlet tartalmaz.

### <a name="custom-authored-rules"></a>Egyéni szerzői szabályok

A WAF egyéni szabályokat a következőképpen állíthatja be:

- **IP-engedélyezési lista és tiltólista:** A webalkalmazásokhoz való hozzáférést az ügyfél IP-címeinek vagy IP-címtartományainak listája alapján szabályozhatja. Mind az IPv4, mind az IPv6-címtípusok támogatottak. Ez a lista beállítható úgy, hogy letiltsa vagy engedélyezze azokat a kérelmeket, amelyeknél a forrás IP-címe megegyezik a listában szereplő IP-címekkel.

- **Földrajzi alapú hozzáférés-vezérlés:** A webalkalmazásokhoz való hozzáférést az ügyfél IP-címéhez társított országkód alapján szabályozhatja.

- **HTTP-paramétereken alapuló hozzáférés-vezérlés:** A HTTP/HTTPS kérelem paramétereiben karakterlánc-egyezésekre alapzattal alapulhat.  Például lekérdezési karakterláncok, POST args, Request URI, Request Header és Request Body.

- **Módszeralapú hozzáférés-vezérlés kérése:** A kérelmek HTTP-kérelem metódusán alapuló szabályok. Például GET, PUT vagy HEAD.

- **Méretmegkötés:** A szabályok alapja a kérelmek bizonyos részeinek hosszát, például a lekérdezési karakterlánc, Uri vagy a kérelem törzse.

- **Sebességkorlátozó szabályok:** A díjszabályozási szabály az, hogy korlátozza a rendellenes nagy forgalmat bármely ügyfél IP-cím. Az ügyfél IP-címéről egy perces időtartam alatt engedélyezett webes kérelmek számának küszöbértékét konfigurálhatja. Ez a szabály különbözik az IP-lista alapú engedélyezési/letiltási egyéni szabálytól, amely lehetővé teszi az összes kérést, vagy letiltja az ügyfél IP-címének összes kérését. A sebességkorlátok kombinálhatók további egyezési feltételekkel, például a HTTP(S) paraméteregyezéssel a részletes sebességszabályozáshoz.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészletek

Az Azure által felügyelt szabálykészletek egyszerű módot biztosítanak a biztonsági fenyegetések közös készlete elleni védelem üzembe helyezésére. Mivel az ilyen szabályeket az Azure kezeli, a szabályok szükség szerint frissülnek az új támadásaláírások elleni védelem érdekében. Az Azure által felügyelt alapértelmezett szabálykészlet a következő fenyegetéskategóriákra vonatkozó szabályokat tartalmazza:

- Webhelyek közötti, szkriptalapú támadás
- Java támadások
- Helyi fájl felvétele
- PHP injekció támadások
- Távoli parancs végrehajtása
- Távolifájl-beszúrás
- Munkamenet-rögzítés
- SQL-injektálás elleni védelem
- Protokolltámadók

Az alapértelmezett szabálykészlet verziószáma, amikor új támadási aláírásokat ad nak a szabálykészlethez.
Az alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van észlelési módban a WAF-házirendekben. Az alapértelmezett szabálykészleten belül letilthatja vagy engedélyezheti az egyes szabályokat, hogy megfeleljenek az alkalmazáskövetelményeinek. Szabályonként meghatározott műveleteket is beállíthat (ALLOW/BLOCK/REDIRECT/LOG).

Néha előfordulhat, hogy ki kell hagynia bizonyos kérésattribútumokat a WAF-kiértékelésből. Gyakori példa a hitelesítéshez használt Active Directory által beszúrt tokenek. Kizárási listát konfigurálhat egy felügyelt szabályhoz, szabálycsoporthoz vagy a teljes szabálykészlethez.  

Az alapértelmezett művelet a BLOKK. Ezenkívül az egyéni szabályok konfigurálhatók ugyanabban a WAF-házirendben, ha meg szeretné kerülni az alapértelmezett szabálykészlet ben lévő előre konfigurált szabályok bármelyikét.

Az egyéni szabályok mindig az alapértelmezett szabálykészlet szabályainak kiértékelése előtt kerülnek alkalmazásra. Ha egy kérelem megfelel egy egyéni szabálynak, a megfelelő szabályművelet lesz alkalmazva. A kérelem le van tiltva, vagy átjutott a háttér-ba. Más egyéni szabályok at vagy az alapértelmezett szabálykészlet ben lévő szabályokat nem dolgozza fel a rendszer. Az alapértelmezett szabálykészletet a WAF-házirendekből is eltávolíthatja.

### <a name="bot-protection-rule-set-preview"></a>Bot védelmi szabálykészlet (előzetes verzió)

Engedélyezheti a felügyelt robotvédelmi szabálykészletet, hogy egyéni műveleteket végrehajtson az ismert robotkategóriákból érkező kérelmeken. 

Három támogatott botkategória van: Rossz, Jó és Ismeretlen. A robotaláírásokat a WAF platform kezeli és dinamikusan frissíti.

A hibás robotok közé tartoznak a rosszindulatú IP-címekről származó botok és a személyazonosságukat meghamisított robotok. A rosszindulatú IP-címek a Microsoft Threat Intelligence hírcsatornából származnak, és óránként frissülnek. [Az Intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft Fenyegetésintelligenciával rendelkezik, és több szolgáltatás, köztük az Azure Security Center is használja.

Jó botok közé érvényesített keresőmotorok. Ismeretlen kategóriák közé tartozik a további bot csoportok, amelyek azonosították magukat, mint a botok. Például a piaci analizátor, a takarmányleírók és az adatgyűjtési ügynökök. 

Ismeretlen botok vannak besorolva közzétett felhasználói ügynökök további érvényesítés nélkül. Egyéni műveleteket állíthat be a különböző típusú robotok blokkolására, engedélyezésére, naplózására vagy átirányítására.

![Bot védelmi szabálykészlet](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> A robotvédelemi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és előzetes szolgáltatásiszint-szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a robotvédelem engedélyezve van, a rendszer a FrontdoorWebApplicationFirewallLog naplózza a robotszabályoknak megfelelő bejövő kérelmeket. Waf-naplókat egy tárfiókból, eseményközpontból vagy naplóelemzésből érhet el.

## <a name="configuration"></a>Konfiguráció

Az Azure Portalon, a REST API-kon, az Azure Resource Manager-sablonokon és az Azure PowerShellen keresztül konfigurálhatja és telepítheti az összes WAF-szabálytípust.

## <a name="monitoring"></a>Figyelés

A WAF frontajtós figyelése integrálva van az Azure Monitorszolgáltatással a riasztások nyomon követéséhez és a forgalmi trendek egyszerű figyeléséhez.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [webalkalmazás-tűzfalról az Azure Application Gateway-en](../ag/ag-overview.md)