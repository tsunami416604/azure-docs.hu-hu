---
title: Web application firewall kérelem méretbeli korlátokat és kizárási listák az Azure Application Gateway – Azure portal |} A Microsoft Docs
description: Ez a cikk bemutatja a webes alkalmazás tűzfal kérelem méretbeli korlátokat, és kizárási sorolja fel az Azure Portalon az Application Gateway-konfiguráció.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965505"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Webes alkalmazás tűzfal kérelem méretbeli korlátokat és a kizárási listákat (nyilvános előzetes verzió)

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ez a cikk ismerteti WAF kérelem méretbeli korlátokat, és kizárási konfigurációs sorolja fel.

> [!IMPORTANT]
> WAF kérelem méretbeli korlátokat és-kizárási listák jelenleg nyilvános előzetes verzióban érhető el. Ebben az előzetes verzióban egy szolgáltatásiszint-szerződés nélkül, és nem javasolt éles számítási feladatok esetében. Bizonyos szolgáltatások nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>WAF kérelem blobméretének korlátjai
Webalkalmazási tűzfal konfigurálása kérelem méretbeli korlátokat belül alsó és felső határai lehetővé teszi. A következő két korlátok beállítások érhetők el:
- Tudásbázis és a vezérlők kivételével minden fájl általános kérés méretkorlát feltölti a kérések maximális törzs mérete mező van megadva. Ez a mező között lehet 1 KB-os minimális 128 KB-os maximális értéknél. Az alapértelmezett érték a kérelem törzsében mérete 128 KB-os.
- A fájl feltöltési korlát mezőben MB-ban van megadva, és azt szabályozza, hogy a maximális megengedett fájlfeltöltési méretet. Ebben a mezőben legfeljebb 500 MB és a egy minimális értéke 1 MB lehet. A fájl feltöltési korlátot alapértelmezett értéke 100 MB.

WAF is biztosít egy konfigurálható forgatógomb, a kérelem törzsében ellenőrzés engedélyezése vagy letiltása. A kérelem törzsében ellenőrzés alapértelmezés szerint engedélyezve van. Ha a kérelem törzsében ellenőrzés be van kapcsolva, WAF nem értékeli a HTTP üzenet törzsének tartalmát. Ezekben az esetekben a WAF URI, fejlécek és cookie-k a WAF-szabályok érvényesítése továbbra is. Ha a kérelem törzsében ellenőrzés be van kapcsolva, kérelem maximális törzs mérete mező nem alkalmazható, és nem állítható be. A kérelem törzsében ellenőrzés kikapcsolása lehetővé teszi, hogy üzeneteket kell küldeni a WAF 128 KB-nál nagyobb. Azonban az üzenet törzse nem ellenőrzik a biztonsági réseket.

## <a name="waf-exclusion-lists"></a>WAF kizárási listák

WAF kizárási listák engedélyezése a felhasználók számára bizonyos attribútumainak WAF során hagyja ki. Ilyenek például az Active Directory beszúrt jogkivonatokat, amelyek a hitelesítéshez, vagy a beírt jelszavak. Ilyen attribútumok különleges karaktereket tartalmaz, amelyek a WAF-szabályok a hamis pozitív kezdeményezheti a hibalehetőség. Attribútum hozzáadása a WAF-kizárási lista után azt van nem vették figyelembe bármely konfigurált és az aktív WAF-szabály által. A hatókör kizárási listák globálisak.
WAF kizárási listák kérelemfejlécek, a kérelem cookie-k vagy a kérelem lekérdezési karakterlánc argumentumok adhat hozzá. Megadhatja, hogy egy pontosan kérheti a fejléc, a cookie-k vagy a lekérdezési karakterlánc atribut match, vagy megadhatja a részleges egyezéseket. A támogatott egyezés feltételek operátorok a következők: 
- **Egyenlő**: Ez az operátor pontosan egyezik szolgál. Tegyük fel, nevű fejléc kiválasztására szolgáló **bearerToken**"** használata egyenlő operátor választó állítja be a **bearerToken**. 
- **Kezdődik**: Ez az operátor megfelel a megadott választó érték kezdődő összes mezőt. 
- **Végződik**: Ez az operátor megfelel az összes kérelem mező, amely a megadott választó érték végén. 
- **Tartalmaz**: Ez az operátor megfelel a megadott választó értéket tartalmazó összes kérelem mezők.

Minden esetben egyeztetésekor a rendszer megkülönbözteti a kis-és nagybetű nincs megkülönböztetve, és a reguláris kifejezés használata nem engedélyezett választók.

## <a name="next-steps"></a>További lépések

Miután konfigurálta a WAF-beállítások, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).