---
title: WaF-házirendek létrehozása az Application Gateway alkalmazásátjáróhoz
description: Megtudhatja, hogy miként hozhat létre webalkalmazás-tűzfal házirendeket az Alkalmazásátjáróhoz.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086983"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Webalkalmazás-tűzfal házirendek létrehozása az Alkalmazásátjáróhoz

A WAF-házirend figyelőkkel való társítása lehetővé teszi, hogy egyetlen WAF mögött több webhelyet is megvédjenek a különböző házirendek. Ha például öt hely van a WAF mögött, öt külön WAF-házirend (mindegyik figyelőhöz egy) rendelkezhet a kizárások, az egyéni szabályok és a felügyelt szabályrendszer egy webhelyre vonatkozó testreszabásához anélkül, hogy a másik négyet meglenne. Ha azt szeretné, hogy egyetlen szabályzat vonatkozzon az összes webhelyre, egyszerűen társíthatja a szabályzatot az Application Gateway-hez, nem pedig az egyes figyelőkhöz, hogy globálisan alkalmazható legyen. A házirendek útvonalalapú útválasztási szabályra is alkalmazhatók. 

Annyi irányelvet hozhat, amennyit csak akar. Miután létrehozott egy szabályzatot, azt egy alkalmazásátjáróhoz kell társozni, hogy életbe lépjen, de az alkalmazásátjárók és figyelők bármilyen kombinációjával társítható. 

Ha az Application Gateway egy szabályzatot alkalmazott, és majd egy másik szabályzatot alkalmaz egy figyelőaz adott Application Gateway, a figyelő szabályzatlép érvénybe, de csak a figyelő(k), hogy ők vannak hozzárendelve. Az Application Gateway szabályzat továbbra is vonatkozik az összes többi figyelők, amelyek nem rendelkeznek egy adott szabályzat hozzájuk rendelt. 

   > [!NOTE]
   > A webhelyenkénti és az URI-WAF-házirendek nyilvános előzetes verzióban vannak. Ez azt jelenti, hogy erre a szolgáltatásra a Microsoft kiegészítő használati feltételei vonatkoznak. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A Web Application Firewall összes új WAF-beállítása (egyéni szabályok, felügyelt rulset konfigurációk, kizárások stb.) a WAF-házirendben él. Ha már rendelkezik WAF-fel, ezek a beállítások továbbra is fennállhatnak a WAF konfigurációban. Az új WAF-házirendre való áttéréslépéseit a jelen cikk a [WAF-konfiguráció áttelepítése WAF-házirendbe](#migrate) című témakörben ismerteti. 

## <a name="create-a-policy"></a>Szabályzat létrehozása

Először hozzon létre egy alapszintű WAF-szabályzatot egy felügyelt alapértelmezett szabálykészlettel (DRS) az Azure Portalhasználatával.

1. A portál bal felső részén válassza az **Erőforrás létrehozása**lehetőséget. Keressen a **WAF (Webhely)** elemre, válassza **a Webalkalmazás tűzfal a**lehetőséget, majd a **Létrehozás gombot.**
2. A **WAF-házirend** lap létrehozása lapon **az Alapjak** lapon adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

   |Beállítás  |Érték  |
   |---------|---------|
   |Házirend     |Regionális WAF (Application Gateway)|
   |Előfizetés     |Az előfizetés nevének kiválasztása|
   |Erőforráscsoport     |Az erőforráscsoport kiválasztása|
   |Házirend neve     |Írja be a WAF-házirend egyedi nevét.|
3. A **Társítás** lapon adja meg az alábbi beállítások egyikét, majd válassza a **Hozzáadás**lehetőséget:

   |Beállítás  |Érték  |
   |---------|---------|
   |Alkalmazásátjáró társítása     |Válassza ki az Application Gateway-profil nevét.|
   |Hallgatói munkatársak     |Jelölje ki az Application Gateway Figyelő nevét, majd válassza a **Hozzáadás lehetőséget.**|

   > [!NOTE]
   > Ha olyan szabályzatot rendel az Application Gateway(vagy figyelőhöz), amely már rendelkezik házirenddel, az eredeti szabályzat felülírja, és az új házirend váltja fel.
4. Válassza **a Véleményezés + létrehozás**lehetőséget, majd a **Létrehozás lehetőséget.**

   ![A WAF irányelveinek alapjai](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF-szabályok konfigurálása (nem kötelező)

WaF-házirend létrehozásakor alapértelmezés szerint *észlelési* módban van. Észlelési módban a WAF nem blokkolja a kérelmeket. Ehelyett a megfelelő WAF-szabályok a WAF-naplókba kerülnek. A WAF működés közbeni megtekintéséhez módosítsa a módbeállításokat *Megelőzés*beállításra. Megelőzés módban a kiválasztott CRS ruleset-ben meghatározott egyeztetési szabályok le vannak tiltva és/vagy be vannak jelentkezve a WAF-naplókba.

## <a name="managed-rules"></a>Felügyelt szabályok

Az Azure által felügyelt OWASP-szabályok alapértelmezés szerint engedélyezve vannak. Ha egy szabálycsoporton belül le szeretne tiltani egy szabályt, bontsa ki a szabálycsoporton belüli szabályokat, jelölje be a szabályszám előtti jelölőnégyzetet, és a fenti lapon válassza a **Letiltás** lehetőséget.

[![Felügyelt](../media/create-waf-policy-ag/managed-rules.png) szabályok](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az **Egyéni szabály hozzáadása** lehetőséget az Egyéni **szabályok** lapon. Ezzel megnyitja az egyéni szabály konfigurációs lapját. A következő képernyőképen egy példa egyéni szabály van beállítva, hogy blokkolja a kérelmet, ha a lekérdezési karakterlánc tartalmazza a szöveg *blockme*.

[![Egyéni szabály](../media/create-waf-policy-ag/edit-custom-rule.png) szerkesztése](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>A WAF-konfiguráció áttelepítése WAF-házirendbe

Ha meglévő WAF-fájllal rendelkezik, előfordulhat, hogy észrevett néhány változást a portálon. Először meg kell határoznia, hogy milyen szabályzatot engedélyezett a WAF-on. Három lehetséges állapot létezik:

- Nincs WAF-házirend
- Csak egyéni szabályok házirendje
- WAF-házirend

A portálon megállapíthatja, hogy a WAF melyik állapotban van. Ha a WAF-beállítások láthatók, és az Application Gateway nézetből módosíthatók, a WAF 1-es állapotban van.

[![WAF-konfiguráció](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Ha a **Webalkalmazás tűzfal lehetőséget választja,** és az egy társított házirendet jelenít meg, a WAF állapota 2 vagy 3. Miután navigált a szabályzat, ha **csak** az egyéni szabályokat és a társított alkalmazásátjárók, akkor ez egy egyéni szabályok csak szabályzat.

![WAF egyéni szabályok](../media/create-waf-policy-ag/waf-custom-rules.png)

Ha a házirend-beállítások at és a felügyelt szabályokat is megjeleníti, akkor ez egy teljes webalkalmazás-tűzfal házirend. 

![WAF házirend-beállítások](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Áttelepítés a WAF-házirendre

Ha csak a WAF-szabályzattal rendelkezik, akkor érdemes lehet áthelyezni az új WAF-szabályzatra. A továbbiakban a tűzfalházirend támogatja a WAF-házirendbeállításokat, a felügyelt szabálybeállításokat, a kizárásokat és a letiltott szabálycsoportokat. Lényegében az alkalmazásátjárón belül korábban elvégzett összes WAF-konfiguráció a WAF-házirenden keresztül történik. 

Csak az egyéni szabály szerkesztése csak a WAF-házirend le van tiltva. A WAF-beállítások szerkesztéséhez, például a szabályok letiltásához, kizárások hozzáadásához stb., át kell térnie egy új, legfelső szintű tűzfalházirend-erőforrásra.

Ehhez hozzon létre egy *webalkalmazás-tűzfal házirendet,* és társítsa azt az alkalmazásátjáró(k)hoz és a választott figyelő(k)hez. Ennek az új házirendnek pontosan meg **kell** egyeznie az aktuális WAF konfigurációval, ami azt jelenti, hogy minden egyéni szabályt, kizárást, letiltott szabályt stb. Miután rendelkezik az Alkalmazásátjáróhoz társított szabályzattal, folytathatja a WAF-szabályok és -beállítások módosítását. Ezt az Azure PowerShell használatával is megteheti. További információt a [WAF-házirend társítása meglévő Application Gateway-rel című témakörben talál.](associate-waf-policy-existing-gateway.md)

Szükség esetén egy áttelepítési parancsfájl segítségével áttelepíthető egy WAF-házirendbe. További információt a [Webapplication Firewall házirendek áttelepítése az Azure PowerShell használatával című](migrate-policy.md)témakörben talál.

## <a name="next-steps"></a>További lépések

További információ a [Web Application Firewall CRS szabálycsoportjairól és szabályairól.](application-gateway-crs-rulegroups-rules.md)
