---
title: Web application firewall kérelem méretbeli korlátokat és kizárási listák az Azure Application Gateway – Azure portal
description: Ez a cikk bemutatja a webes alkalmazás tűzfal kérelem méretbeli korlátokat, és kizárási sorolja fel az Azure Portalon az Application Gateway-konfiguráció.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a814fc6e9a72ba92d915821bd1e1694366844555
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277416"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Webes alkalmazás tűzfal kérelem méretbeli korlátokat és kizárási listák

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ez a cikk ismerteti WAF kérelem méretbeli korlátokat, és kizárási konfigurációs sorolja fel.

## <a name="waf-request-size-limits"></a>WAF kérelem blobméretének korlátjai

![Kérelem blobméretének korlátjai](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Webalkalmazási tűzfal konfigurálása kérelem méretbeli korlátokat belül alsó és felső határai teszi lehetővé. A következő két korlátok beállítások érhetők el:

- Tudásbázis és a vezérlők kivételével minden fájl általános kérés méretkorlát feltölti a kérések maximális törzs mérete mező van megadva. Ez a mező között lehet 1 KB-os minimális 128 KB-os maximális értéknél. Az alapértelmezett érték a kérelem törzsében mérete 128 KB-os.
- A fájl feltöltési korlát mezőben MB-ban van megadva, és azt szabályozza, hogy a maximális megengedett fájlfeltöltési méretet. Ez a mező rendelkezhet egy minimális értéke 1 MB és nagy SKU-példányok legfeljebb 500 MB-os, míg a közepes Termékváltozata legfeljebb 100 MB. A fájl feltöltési korlátot alapértelmezett értéke 100 MB.

WAF is biztosít egy konfigurálható forgatógomb, a kérelem törzsében ellenőrzés engedélyezése vagy letiltása. A kérelem törzsében ellenőrzés alapértelmezés szerint engedélyezve van. Ha a kérelem törzsében ellenőrzés be van kapcsolva, WAF nem értékeli a HTTP üzenet törzsének tartalmát. Ezekben az esetekben a WAF URI, fejlécek és cookie-k a WAF-szabályok érvényesítése továbbra is. Ha a kérelem törzsében ellenőrzés be van kapcsolva, kérelem maximális törzs mérete mező nem alkalmazható, és nem állítható be. A kérelem törzsében ellenőrzés kikapcsolása lehetővé teszi, hogy üzeneteket kell küldeni a WAF 128 KB-nál nagyobb, de az üzenettörzs nem ellenőrzik a biztonsági réseket.

## <a name="waf-exclusion-lists"></a>WAF kizárási listák

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF kizárási listák lehetővé teszi bizonyos attribútumainak WAF során hagyja ki. Ilyenek például az Active Directory beszúrt jogkivonatokat, amelyek a hitelesítéshez, vagy a beírt jelszavak. Ilyen attribútumok különleges karaktereket tartalmaz, amelyek a WAF-szabályok a hamis pozitív kezdeményezheti a hibalehetőség. Egy attribútumot a WAF-kizárási listára kerül, ha a nem tekinthető minden konfigurált és az aktív WAF-szabály által. A hatókör kizárási listák globálisak.

Kizárási listák is hozzáadhatók a következő attribútumokat:

* Kérelem fejlécei
* Kérelem cookie-k
* A kérelem törzse

   * Az űrlap többrészes adatait
   * XML
   * JSON

Adjon meg egy pontos kérelem fejléce, a szervezet, a cookie-t, vagy a lekérdezési karakterláncot attribútumot egyezést.  Másik lehetőségként megadhat részleges egyezéseket. A kizárás, mindig egy fejléc mezőben soha nem a hozzá tartozó érték. Kizárási szabály globális hatókör, és az összes és az összes szabály vonatkozik.

A támogatott egyezés feltételek operátorok a következők:

- **Egyenlő**:  Ez az operátor pontosan egyezik szolgál. Tegyük fel, nevű fejléc kiválasztására szolgáló **bearerToken**, az equals operátor használata állítja be a választó **bearerToken**.
- **Kezdődik**: Ez az operátor megfelel a megadott választó érték kezdődő összes mezőt.
- **Végződik**:  Ez az operátor megfelel az összes kérelem mező, amely a megadott választó érték végén.
- **Tartalmaz**: Ez az operátor megfelel a megadott választó értéket tartalmazó összes kérelem mezők.
- **Egyenlő bármelyikkel**: Ez az operátor megfelel az összes kérelem mező. * a választó érték lesz.

Minden esetben egyeztetésekor a rendszer megkülönbözteti a kis-és nagybetű nincs megkülönböztetve, és a reguláris kifejezés nem engedélyezett, mert a választók.

### <a name="examples"></a>Példák

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi Azure PowerShell-kódrészlet bemutatja, hogyan kizárásokat:

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

Az alábbi json-kódrészlet bemutatja, hogyan kizárásokat:

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>További lépések

Miután konfigurálta a WAF-beállítások, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).
