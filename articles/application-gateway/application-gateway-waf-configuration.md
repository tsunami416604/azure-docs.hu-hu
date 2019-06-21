---
title: Web application firewall kérelem méretbeli korlátokat és kizárási listák az Azure Application Gateway – Azure portal
description: Ez a cikk bemutatja a webes alkalmazás tűzfal kérelem méretbeli korlátokat, és kizárási sorolja fel az Azure Portalon az Application Gateway-konfiguráció.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 272c6d2de23b1e89caef3f9bee20a96c5c196cde
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275175"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Webes alkalmazás tűzfal kérelem méretbeli korlátokat és kizárási listák

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ez a cikk ismerteti WAF kérelem méretbeli korlátokat, és kizárási konfigurációs sorolja fel.

## <a name="waf-request-size-limits"></a>WAF kérelem blobméretének korlátjai

![Kérelem blobméretének korlátjai](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Webalkalmazási tűzfal konfigurálása kérelem méretbeli korlátokat belül alsó és felső határai teszi lehetővé. A következő két korlátok beállítások érhetők el:

- A kérelem maximális törzs mérete mező kilobájt és vezérlők általános kérés maximális mérete a kivételével minden olyan fájlt tölt fel van megadva. Ez a mező között lehet 1 KB-os minimális 128 KB-os maximális értéknél. Az alapértelmezett érték a kérelem törzsében mérete 128 KB-os.
- A fájl feltöltési korlát mezőben MB-ban van megadva, és azt szabályozza, hogy a maximális megengedett fájlfeltöltési méretet. Ez a mező rendelkezhet egy minimális értéke 1 MB és nagy SKU-példányok legfeljebb 500 MB-os, míg a közepes Termékváltozata legfeljebb 100 MB. A fájl feltöltési korlátot alapértelmezett értéke 100 MB.

WAF is biztosít egy konfigurálható forgatógomb, a kérelem törzsében ellenőrzés engedélyezése vagy letiltása. A kérelem törzsében ellenőrzés alapértelmezés szerint engedélyezve van. Ha a kérelem törzsében ellenőrzés be van kapcsolva, a WAF nem értékeli a HTTP üzenet törzsének tartalmát. Ezekben az esetekben a WAF URI, fejlécek és cookie-k a WAF-szabályok érvényesítése továbbra is. Ha a kérelem törzsében ellenőrzés be van kapcsolva, kérelem maximális törzs mérete mező nem alkalmazható, és nem állítható be. A kérelem törzsében ellenőrzés kikapcsolása lehetővé teszi, hogy üzeneteket kell küldeni a WAF 128 KB-nál nagyobb, de az üzenettörzs nem ellenőrzik a biztonsági réseket.

## <a name="waf-exclusion-lists"></a>WAF kizárási listák

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF kizárási listák lehetővé teszi bizonyos attribútumainak WAF során hagyja ki. Ilyenek például az Active Directory beszúrt jogkivonatokat, amelyek a hitelesítéshez, vagy a beírt jelszavak. Ilyen attribútumok különleges karaktereket tartalmaz, amelyek a WAF-szabályok a hamis pozitív kezdeményezheti a hibalehetőség. Egy attribútumot a WAF-kizárási listára kerül, ha a nem tekinthető minden konfigurált és az aktív WAF-szabály által. A hatókör kizárási listák globálisak.

A következő attribútumok kizárási listák lehet hozzáadni. A kiválasztott mező értékét nem kerülnek kiértékelésre WAF-szabályok alapján. A kizárás remove vizsgálata a mező értéke sorolja fel.

* Kérelem fejlécei
* Kérelem cookie-k
* Kérelem attribútum neve (argumentumok) is hozzáadhatók kizárási elemként, például:

   * Űrlapmező neve
   * XML-entitás
   * JSON-entitás
   * URL-cím lekérdezési karakterlánc argumentum

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

Az alábbi példák bemutatják, kizárások használatát.

### <a name="example-1"></a>1\. példa

Ebben a példában szeretne zárni a felhasználói ügynök fejléc. A felhasználói ügynök kérelem fejléce jellemző karakterlánc, amely lehetővé teszi, hogy a hálózati protokoll társaknak azonosítására, az alkalmazás típusát, operációs rendszer, szoftverszállító vagy a kérelmező felhasználó ügynök szoftver verziója tartalmazza. További információkért lásd: [felhasználói ügynök](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Ezt a fejlécet kiértékelése letiltása számtalan lehet. Egy karakterlánc, amely a WAF látja, és feltételezi, hogy rosszindulatú is lehet. Ha például a klasszikus SQL támadás "x = x" egy karakterláncban. Bizonyos esetekben ez lehet normál forgalom. Ezért előfordulhat, hogy kell ezt a fejlécet kizárása a WAF-értékelés.

A következő Azure PowerShell-parancsmag próbaverzióról nem tartalmazza a felhasználói ügynök fejléc:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>2\. példa

Ebben a példában szereplő érték nem tartalmazza a *felhasználói* az URL-CÍMEN keresztül a kérelemben szereplő átadott paraméter. Tegyük fel például, gyakori felhasználói mező tartalmazhat egy karakterlánc, amely a WAF kártékony tartalom tekint, így blokkol, a környezetben.  A felhasználó paraméter kizárhat ebben az esetben úgy, hogy a WAF semmit, a mezőben nem értékelhető.

A következő Azure PowerShell-parancsmag próbaverzióról nem tartalmazza a felhasználó paramétert:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Tehát ha az URL-cím **http://www.contoso.com/?user=fdafdasfda** átadott a waf-eszközzel, hogy azt nem értékeli a karakterlánc **fdafdasfda**.

## <a name="next-steps"></a>További lépések

Miután konfigurálta a WAF-beállítások, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).
