---
title: Mi az Azure webalkalmazás-tűzfal Azure bejárati ajtajának? (Előzetes verzió)
description: Ismerje meg az Azure webalkalmazás-tűzfal, for Azure bejárati ajtajának szolgáltatás rosszindulatú támadások ellen védi a webes alkalmazások.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 122e9687ee313edff34e5a4fd9a44b1026a63811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478786"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Mi az Azure webalkalmazás-tűzfal Azure bejárati ajtajának?

Az Azure webalkalmazási tűzfala (WAF) központi védelmet biztosít az Azure Front Door használatával globálisan biztosított webalkalmazások számára. A tűzfal célja a webszolgáltatások gyakori támadások és biztonsági rések elleni védelme, valamint a szolgáltatás magas rendelkezésre állásának biztosítása a megfelelőségi követelmények teljesítése mellett.


Webalkalmazások egyre inkább az olyan rosszindulatú támadások, mint szolgáltatás árvizek, SQL-injektálási támadások és többhelyes parancsfájlok futtatására szolgáltatásmegtagadásos ki. A rosszindulatú támadások szolgáltatás-kimaradás és adatvesztést okozhat, alkalmazástulajdonosok webes jelentős fenyegetést.

Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás reagálhat a gyorsabb biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett, hogy az egyes webalkalmazások védelmét biztosítaná.

WAF számára a bejárati ajtó a globális és központosított megoldás. Az Azure-beli hálózati biztonsági a különféle helyeket világszerte központilag telepítették, és minden bejövő kérelem bejárati ajtajának kézbesítette engedélyezve van a WAF webalkalmazás számára a hálózat szélén van megvizsgálni. WAF megközelíti a támadás források rosszindulatú támadások megelőzése, mielőtt belép a virtuális hálózat és a méretezett globális védelmet nyújt a teljesítmény feláldozása nélkül. A WAF-házirend egyszerűen lehet kapcsolódni az előfizetés bármely bejárati ajtajának profil, és új szabályoknak is üzembe helyezhetők percen belül, így gyorsan reagálhat a változó fenyegetések mintákat.

![Az Azure webalkalmazás-tűzfal](./media/waf-overview/web-application-firewall-overview2.png)

Az Application Gateway Azure WAF is engedélyezhető. További információkért lásd: [webalkalmazási tűzfal](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>WAF-házirenden és szabályokon

A WAF-szabályzat konfigurálása, és társítsa a szabályzatot egy vagy több bejárati ajtajának az előtér-kiszolgálókon a védelem. A WAF-házirend kétféle típusú biztonsági szabályok áll:

- Egyéni szabályokat, hogy az ügyfél készült.

- Azure által felügyelt, melyek felügyelt szabálykészletek előre konfigurált szabályok készletét.

Ha mindkettő meg adva, egyéni szabályok feldolgozásának felügyelt szabálykészletben szabályok feldolgozása előtt. Egy szabály jön létre egyeztetési feltételt, a prioritást és a egy műveletet. A művelet támogatott típusok a következők: BLOKK, NAPLÓZÁSI és ÁTIRÁNYÍTÁSI engedélyezése. Létrehozhat egy teljes mértékben testre szabott szabályzatot az adott alkalmazás védelmi igényeinek megfelelő felügyelt és az egyéni szabályok kombinálásával.

A házirendben szabályok feldolgozása prioritási sorrendben egy egyedi egész számot határozza meg a szabályok feldolgozása prioritási esetén. Kisebb egész szám azt jelzi, hogy a magasabb prioritású, és azokat, mielőtt egy nagyobb egész számot tartalmazó szabályok kiértékelése. Ha egy szabály egyezik, a megfelelő műveletet a szabályban megadott alkalmazza a rendszer a kérelem. Az ilyen egyezést feldolgozását követően alacsonyabb prioritású szabályok feldolgozása nem tovább.

Egy webalkalmazás által bejárati ajtajának rendelkezhet társított egyszerre csak egy WAF házirend. Azonban a bejárati ajtajának konfigurációs lehet társítva WAF szabályzatok nélkül. A WAF-szabályzat esetén a rendszer replikálja az összes világszerte összhangot a biztonsági szabályzatok az edge-hely.

## <a name="waf-modes"></a>WAF-üzemmódok

WAF házirend beállítható úgy, hogy futtassa a következő két módban:

- **Észlelés üzemmód:** Észlelési módban történő futtatásakor a WAF figyelők kívül bármely más műveleteket pedig nem vesz, és a WAF-naplókban naplózza a kérést, és az egyező WAF-szabálya. Bekapcsolhatja a bejárati ajtó a naplózási diagnosztikáját (portál használata esetén ez érhető el a a **diagnosztikai** szakaszban az Azure Portalon).

- **Megelőzés üzemmód:** Megelőzési módban történő futtatására konfigurálásakor a WAF megadott műveletet hajtja végre, ha a kérelem megfelel valamelyik szabálynak, és ha egyezést talál, az alacsonyabb prioritásúak nincsenek további szabályok kiértékelése. Egyeztetett kérések is naplózza a WAF-naplókban.

## <a name="waf-actions"></a>WAF-műveletek

Futtassa a egy művelet, amikor egy kérelem megfelel egy adott szabály feltételeit WAF ügyfeleink közül választhat:

- **Engedélyezése:**  Kérelem áthalad a WAF és háttér-továbbíthatja a rendszer. További alacsonyabb prioritású szabályok letilthatja ezt a kérelmet.
- **Blokkolás:** A kérelem le van tiltva, és a WAF az ügyfél választ küld a kérést a háttéralkalmazás nélkül.
- **Log:**  Kérelem be van jelentkezve a WAF-naplókban és WAF továbbra is fennáll, alacsonyabb prioritású szabályok kiértékelése.
- **Átirányítási:** WAF átirányítja a kérést a megadott URI azonosító. A megadott URI-ja szintű házirend-beállítás. Beállítások konfigurálása után az összes olyan kérelmek, amelyek megfelelnek a **átirányítási** művelet küld erre az URI.

## <a name="waf-rules"></a>WAF-szabályok

A WAF-házirend állhat kétféle típusú biztonsági szabályok – egyéni szabályokat, az ügyfél és a felügyelt szabálykészletek által készített Azure által felügyelt, előre konfigurált szabályok készletét.

### <a name="custom-authored-rules"></a>Egyéni szabályok lett létrehozva.

Egyéni szabályok WAF módon konfigurálhatja:

- **IP engedélyezése és letiltása listában:** Konfigurálhat egyéni szabályok a webalkalmazások ügyfél IP-címek vagy IP-címtartományok listája alapján való hozzáférés szabályozásához. IPv4- és IPv6-cím-típusok támogatottak. Ez a lista beállítható úgy, hogy letiltja vagy engedélyezi ezeket a kérelmeket, ha a forrás IP-cím megegyezik-e a listán szereplő IP-címet.

- **Földrajzi alapú hozzáférés-vezérlés:** Beállíthatja, hogy ki férhet hozzá a webes alkalmazások, az ügyfél IP-címmel társított országkód alapuló egyéni szabályok.

- **HTTP paraméterek-alapú hozzáférés-vezérlés:** Beállíthatja, hogy a HTTP/HTTPS kérelem paraméterek, például lekérdezési karakterláncok, POST argumentum, kérelem URI azonosítója, fejléc és kérelem törzse egyező karakterlánc alapuló egyéni szabályok.

- **A kérelem metódus-alapú hozzáférés-vezérlés:** A kérelem például a GET, PUT vagy HEAD HTTP-kérési metódust alapuló egyéni szabályok konfigurálásával.

- **Méret korlátozás:** Konfigurálhat egy kérelem-lekérdezési karakterlánccá az URI-t, például meghatározott részeit hossza az egyéni szabályokat vagy a kérelem törzse.

- **A sebesség korlátozása szabályok:** A sebesség-vezérlési szabály, hogy minden ügyfél IP-rendellenes nagy forgalmat. Az ügyfél IP-cím egy egy perces időszakra engedélyezett webes kérelmek száma konfigurálhatja egy küszöbértéket. Különbözik-IP list-alapú engedélyezésére és letiltására egyéni szabályt, amely vagy engedélyezi az összes vagy blokkolja az összes ügyfél IP-cím kérése. Sebességkorlátozással további egyezési feltételei részletes sebesség szabályozása egyező HTTP (S)-paraméterek például kombinálható.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészletek

Azure által felügyelt szabálykészletek tulajdonságkészlettel rendelkezik biztonsági fenyegetések elleni védelem telepítése egyszerű módot biztosítanak. Az ilyen szabálykészletek Azure által felügyelt, mivel a szabályok frissülnek új támadási aláírások elleni védelem érdekében szükség szerint. A nyilvános előzetes verzióban az Azure által felügyelt alapértelmezett szabálykészletet tartalmazza a következő kategóriák fenyegetés elleni szabályok:

- Többhelyes scripting
- Java-támadások
- Helyi fájl
- PHP-injektálási támadások
- Távoli parancs végrehajtása
- Távolifájl-beszúrásos
- Munkamenet-rögzítés
- SQL-injektálás elleni védelem
- A támadók protokoll

Az alapértelmezett szabálykészletet verziószáma növelik a támadási új vírusdefiníciókat a szabálykészlet hozzáadásakor.
Alapértelmezett szabálykészletet az észlelési mód a WAF-szabályzatok alapértelmezés szerint engedélyezve van. Az alapértelmezett szabálykészletet az alkalmazás igényeinek belüli egyéni szabály engedélyezése vagy tiltása. Beállíthat egy szabályban bizonyos műveletek (engedélyezése/LETILTÁSA/ÁTIRÁNYÍTÁSI/napló). Alapértelmezett művelet LETILTÁSA az. Emellett egyéni szabályok konfigurálhatók WAF ugyanabban a szabályzatban, ha olyan előre konfigurált szabályai az alapértelmezett szabálykészletet.
Egyéni szabályok mindig érvényesek, az alapértelmezett szabály beállítása a szabályok kiértékelése előtt. Ha kérelem egy egyéni szabály megegyezik, megfelelő szabályművelet alkalmazza, és a kérelem letiltott vagy háttérbeli, anélkül, hogy további egyéni szabályokat vagy a szabályok az alapértelmezett szabálykészletet meghívását át. Ezenkívül lehetősége van a WAF-szabályzatok alapértelmezett szabálykészletet eltávolítása.


### <a name="bot-protection-rule-preview"></a>A robot védelmét szolgáló szabály (előzetes verzió)

Egy felügyelt Bot védelmi szabálykészlet a WAF az egyéni műveletek is végezhetők rajtuk az ismert kártékony IP-címekről érkező kérelmek esetén is engedélyezhető. Az IP-címek vannak a Microsoft Fenyegetésfelderítő adatcsatorna forrása. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Microsoft fenyegetésfelderítő működteti, és több szolgáltatást, többek között az Azure Security Center által használt.

![A robot védelmi Sadu Pravidel](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> A robot védelmi sadu pravidel jelenleg nyilvános előzetes verzióban és a egy előzetes szolgáltatói szerződést. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bot védelem engedélyezve van, ha a rendszer naplózza a bejövő kéréseket, amelyek megfelelnek a robotok kártékony ügyfél IP-címek FrontdoorWebApplicationFirewallLog bejelentkezéskor. WAF-naplókban elérhessék a tárfiókból, event hub vagy a log analytics. 

## <a name="configuration"></a>Konfiguráció

Konfigurálása és telepítése az összes WAF szabálytípusokat teljes mértékben támogatott az Azure portal, REST API-k, az Azure Resource Manager-sablonokkal és az Azure PowerShell használatával.

## <a name="monitoring"></a>Figyelés

Figyelés számára a bejárati ajtajának WAF integrálva van az Azure monitorral nyomon követheti a riasztások és a forgalom tendenciák kimutatása érdekében.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [bejárati ajtajának az Azure portal használatával a WAF-házirend konfigurálása](waf-front-door-create-portal.md)