---
title: Mi az Azure webalkalmazás tűzfala az Azure CDN-en?
description: Ismerje meg, hogy az Azure CDN-szolgáltatáson található Azure webalkalmazás-tűzfalhogyan védi a webalkalmazásokat a rosszindulatú támadásokkal szemben.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754296"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Azure webalkalmazás-tűzfal az Azure tartalomkézbesítési hálózatán

Az Azure Web Application Firewall (WAF) az Azure Content Delivery Network (CDN) a Microsoft központi védelmet nyújt a webes tartalom. A WAF megvédi a webes szolgáltatásokat a gyakori biztonsági rések és biztonsági rések ellen. A szolgáltatás magas rendelkezésre állású marad a felhasználók számára, és segít a megfelelőségi követelmények teljesítésében.

> [!IMPORTANT]
> Waf az Azure CDN-ben a Microsoft jelenleg nyilvános előzetes verzióban, és egy előzetes szolgáltatásiszint-szerződés biztosított. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A WAF az Azure CDN-en egy globális és központosított megoldás. Az Azure hálózati peremhálózati helyeken világszerte telepítve van. A WAF megakadályozza a támadási forrásokhoz közeli rosszindulatú támadásokat, mielőtt azok elérnék az eredetét. A teljesítmény feláldozása nélkül globális védelmet kap. 

A WAF-szabályzat könnyen hivatkozik az előfizetés bármely CDN-végpontjára. Az új szabályok perceken belül üzembe helyezhetők, így gyorsan reagálhat a fenyegetésminták módosítására.

![Az Azure webalkalmazás tűzfala](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF politika és szabályok

Konfigurálhat egy WAF-házirendet, és a védelmet egy vagy több CDN-végponthoz társíthatja. A WAF-házirend kétféle biztonsági szabályból áll:

- egyéni szabályokat, amelyeket létrehozhat.

- felügyelt szabálykészletek, amelyek az Azure által felügyelt előre konfigurált szabályok gyűjteménye.

Ha mindkettő jelen van, az egyéni szabályok feldolgozása a felügyelt szabálykészletben lévő szabályok feldolgozása előtt lesz. A szabály egyegyezési feltételből, prioritásból és műveletből áll. A támogatott művelettípusok a következők: *ALLOW*, *BLOCK*, *LOG*és *ReDIRECT*. Létrehozhat egy teljesen testre szabott szabályzatot, amely megfelel az adott alkalmazásvédelmi követelményeknek a felügyelt és az egyéni szabályok kombinálásával.

A házirenden belüli szabályok feldolgozása prioritási sorrendben történik. Prioritás Egy egyedi szám, amely meghatározza a feldolgozandó szabályok sorrendjét. A kisebb számok magasabb prioritásúak, és ezeket a szabályokat a nagyobb értékű szabályok előtt értékeli ki a rendszer. Ha egy szabály egyeztetése van, a szabályban definiált megfelelő művelet lesz alkalmazva a kérelemre. Az ilyen egyezés feldolgozása után az alacsonyabb prioritású szabályok at nem dolgozzák fel tovább.

Az Azure CDN-en üzemeltetett webalkalmazáshoz egyszerre csak egy WAF-szabályzat társítható. CdN-végpontazonban rendelkezhet waf-házirendek nélkül. Ha egy WAF-szabályzat van jelen, a rendszer replikálja az összes peremhálózati helyünkre, hogy világszerte egységes biztonsági házirendeket biztosítson.

## <a name="waf-modes"></a>WAF módok

A WAF-házirend beállítható úgy, hogy a következő két módban fusson:

- *Észlelési mód:* Észlelési módban a WAF nem tesz más műveletet, mint a figyelést, és naplózza a kérelmet és az egyező WAF-szabályt a WAF-naplókhoz. Bekapcsolhatja a naplózási diagnosztika a Bejárati ajtó. A portál használatakor nyissa meg a **Diagnosztika szakaszt.**

- *Megelőzési mód*: Megelőzési módban a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. Ha egyezést talál, a rendszer nem számít ki további alacsonyabb prioritású szabályokat. Az egyező kérelmek et is naplózza a WAF-naplók.

## <a name="waf-actions"></a>WAF-műveletek

Az alábbi műveletek közül választhat, ha egy kérelem megfelel egy szabály feltételeinek:

- *Allow*: A kérelem áthalad a WAF-on, és a háttér-endre továbbítja. További alacsonyabb prioritású szabályok nem tilthatják le ezt a kérést.
- *Blokk*: A kérelem le van tiltva, és a WAF választ küld az ügyfélnek anélkül, hogy a kérést a háttérrendszerbe továbbítana.
- *Napló*: A kérelem be van jelentkezve a WAF-naplókba, és a WAF folytatja az alacsonyabb prioritású szabályok kiértékelését.
- *Átirányítás*: A WAF átirányítja a kérelmet a megadott URI-ra. A megadott URI egy házirendszintű beállítás. Konfigurálás után az *átirányítási* műveletnek megfelelő összes kérelmet elküldi az adott URI-nak.

## <a name="waf-rules"></a>WAF-szabályok

A WAF-házirend kétféle biztonsági szabályból állhat:

- *egyéni szabályok:* saját magad által létrehozott szabályok 
- *felügyelt szabálykészletek*: Az Azure által felügyelt, előre konfigurált szabálykészlet

### <a name="custom-rules"></a>Egyéni szabályok

Az egyéni szabályok egyeztetési és díjszabályozó szabályokkal rendelkezhetnek.

A következő egyéni egyezési szabályokat állíthatja be:

- *IP-engedélyezési lista és tiltólista*: Az ügyfél IP-címeinek vagy IP-címtartományainak listája alapján szabályozhatja a webalkalmazásokhoz való hozzáférést. Mind az IPv4, mind az IPv6-címtípusok támogatottak. Ez a lista beállítható úgy, hogy letiltsa vagy engedélyezze azokat a kérelmeket, amelyeknél a forrás IP-címe megegyezik a listában szereplő IP-címekkel.

- *Földrajzi alapú hozzáférés-vezérlés:* A webalkalmazásokhoz való hozzáférést az ügyfél IP-címéhez társított országkód alapján szabályozhatja.

- *HTTP-paramétereken alapuló hozzáférés-vezérlés*: A HTTP/HTTPS kérelem paraméterekben karakterláncegyezésekre alapozhatja a szabályokat.  Például lekérdezési karakterláncok, POST args, Request URI, Request Header és Request Body.

- *Metódusalapú hozzáférés-vezérlés kérése*: A szabályokat a kérelem HTTP-kérelemmetódusára alapozza. Például GET, PUT vagy HEAD.

- *Méretmegkötés:* A szabályok at a kérelem bizonyos részeinek hosszára alapozhatja, például a lekérdezési karakterláncra, az Uri-ra vagy a kérelemtörzsre.

A díjszabályozó szabály korlátozza a rendellenesen nagy forgalmat bármely ügyfél IP-címéről.

- *Sebességkorlátozó szabályok:* Beállíthatja az ügyfél IP-címéről egy perces időtartam alatt engedélyezett webes kérelmek számának küszöbértékét. Ez a szabály különbözik az IP-lista alapú engedélyezési/letiltási egyéni szabálytól, amely lehetővé teszi az összes kérést, vagy letiltja az ügyfél IP-címéből érkező összes kérelmet. A sebességkorlátok kombinálhatók további egyezési feltételekkel, például a HTTP(S) paraméteregyezéssel a részletes sebességszabályozáshoz.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészletek

Az Azure által felügyelt szabálykészletek egyszerű módot biztosítanak a biztonsági fenyegetések közös készlete elleni védelem üzembe helyezésére. Mivel ezeket a szabályeket az Azure kezeli, a szabályok szükség szerint frissülnek az új támadási aláírások elleni védelem érdekében. Az Azure által felügyelt alapértelmezett szabálykészlet a következő fenyegetéskategóriákra vonatkozó szabályokat tartalmazza:

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
Az alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van *észlelési* módban a WAF-házirendekben. Az alapértelmezett szabálykészleten belül letilthatja vagy engedélyezheti az egyes szabályokat, hogy megfeleljenek az alkalmazáskövetelményeinek. Szabályonként meghatározott műveleteket is beállíthat (ALLOW/BLOCK/REDIRECT/LOG). A felügyelt alapértelmezett szabálykészlet alapértelmezett művelete a *Blokk*.

Az egyéni szabályok mindig az alapértelmezett szabálykészlet szabályainak kiértékelése előtt kerülnek alkalmazásra. Ha egy kérelem megfelel egy egyéni szabálynak, a megfelelő szabályművelet lesz alkalmazva. A kérelem le van tiltva, vagy átjutott a háttér-ba. Más egyéni szabályok at vagy az alapértelmezett szabálykészlet ben lévő szabályokat nem dolgozza fel a rendszer. Az alapértelmezett szabálykészletet a WAF-házirendekből is eltávolíthatja.

## <a name="configuration"></a>Konfiguráció

Az Azure Portalon, a REST API-kon, az Azure Resource Manager-sablonokon és az Azure PowerShellen keresztül konfigurálhatja és telepítheti az összes WAF-szabálytípust.

## <a name="monitoring"></a>Figyelés

A WAF ÉS a CDN figyelése integrálva van az Azure Monitorszolgáltatással a riasztások nyomon követéséhez és a forgalmi trendek egyszerű figyeléséhez.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy WAF-szabályzatot az Azure CDN-nel az Azure Portal használatával](waf-cdn-create-portal.md)
