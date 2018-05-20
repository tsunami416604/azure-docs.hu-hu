---
title: Webalkalmazási tűzfal hozzáadása az Azure Security Centerben |} Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslatait **webalkalmazási tűzfal hozzáadása** és **alkalmazás védelem véglegesítése**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2018
ms.author: terrylan
ms.openlocfilehash: e28a1f6b865dae3abe2cb9dfac2921c6a2034491
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Webalkalmazási tűzfal hozzáadása az Azure Security Centerben
Az Azure Security Center javasolhatja, hogy a webes alkalmazás tűzfalat (WAF) hozzáadása egy Microsoft-partner biztonságossá tételéhez a webes alkalmazások. Ez a dokumentum útmutatást nyújt a példa bemutatja, hogyan Ez a javaslat alkalmazása.

WAF ajánlás bármely nyilvános felé néző IP-cím (példány szint IP vagy terhelés eloszlik IP), amely rendelkezik egy társított hálózati biztonsági csoportot a nyissa meg a bejövő webes portokkal (80,443) jelenik meg.

A Security Center javasolja, hogy egy WAF a webes alkalmazások és a külső App Service környezetekben (ASE) alapján telepített virtuális gépeken célzó támadások elleni védelemre való ellátásához a [elszigetelt](https://azure.microsoft.com/pricing/details/app-service/windows/) service-csomag. Az Izolált csomaggal alkalmazását egy privát, dedikált Azure-környezetben üzemeltetheti, és ideális választás olyan alkalmazásokhoz, amelyeknél szempont a biztonságos kapcsolat a helyi hálózathoz, valamint a további teljesítmény és skálázhatóság. Az alkalmazás éppen elkülönített környezetben, mellett a kell terheléselosztó külső IP-cím. ASE kapcsolatos további tudnivalókért tekintse meg a [App Service környezetben dokumentáció](../app-service/environment/intro.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. A **javaslatok**, jelölje be **biztonságossá webalkalmazását webalkalmazási tűzfal használatával**.
   ![Biztonságos webes alkalmazás][1]
2. A **a webes alkalmazások webalkalmazási tűzfal biztonságos**, jelöljön ki egy webes alkalmazást. **Webalkalmazási tűzfal hozzáadása** nyílik meg.
   ![Webalkalmazási tűzfal hozzáadása][2]
3. Választhat egy létező webalkalmazási tűzfal használata, ha elérhető, vagy létrehozhat egy újat. Ebben a példában érhetők el nem létező WAFs, létrehozhatunk egy WAF.
4. Egy WAF létrehozásához válassza a megoldás az integrált partnereink listájából. Ez a példa azt válassza **Barracuda webalkalmazási tűzfal**.
5. **Webalkalmazási tűzfal barracuda** megnyílik, így a partnermegoldáshoz kapcsolatos információkat. Kattintson a **Létrehozás** gombra.

   ![Tűzfal információk panel][3]

6. **Új webalkalmazási tűzfal** megnyitása, ahol elvégezhető **Virtuálisgép-konfiguráció** lépéseit, és adja meg **WAF információk**. Válassza ki **Virtuálisgép-konfiguráció**.
7. A **Virtuálisgép-konfiguráció**, akkor adja meg a virtuális gép, amelyen a WAF lépett szükséges adatokat.
   ![Virtuálisgép-konfiguráció][4]
8. Lépjen vissza **új webalkalmazási tűzfal** válassza **WAF információk**. A **WAF információk**, konfigurálja a WAF magát. 7. lépés lehetővé teszi, hogy konfigurálja a virtuális gép, amelyen a WAF fut, és 8. lépés lehetővé teszi, hogy hozzon létre a WAF magát.

## <a name="finalize-application-protection"></a>Alkalmazásvédelem véglegesítése
1. Lépjen vissza **javaslatok**. Egy új bejegyzést jött létre, miután létrehozta a WAF nevű **alkalmazás védelem véglegesítése**. Ez a bejegyzés azt jelzi, hogy végre kell hajtania a folyamat ténylegesen az Azure virtuális hálózaton belül WAF elvégeztük így megvédheti a az alkalmazás lehetővé teszi.

   ![Alkalmazásvédelem véglegesítése][5]

2. Válassza ki **alkalmazás védelem véglegesítése**. Ekkor megnyílik egy új panel. Láthatja, hogy van-e egy webes alkalmazás, amely a forgalom átirányítása megtörténik rendelkeznie kell.
3. Válassza ki a webalkalmazás. Ekkor megnyílik egy panel, amely lehetővé teszi az lépéseket a véglegesítése a webalkalmazás tűzfal beállítása. Hajtsa végre a lépéseket, majd válassza ki **korlátozzák a forgalmat**. A Security Center majd elvégzi a vezetékezést fel.

   ![Forgalom korlátozása][6]

> [!NOTE]
> Adja hozzá ezeket az alkalmazásokat a meglévő WAF-központitelepítések védelmet biztosíthat a Security Center több webalkalmazás.
>
>

A naplók az adott WAF most már teljes mértékben. A Security Center automatikusan összegyűjtése és elemzése a naplókat, hogy az Ön számára fontos biztonsági riasztások azt is surface elindíthatja.

## <a name="next-steps"></a>További lépések
Ez a dokumentum bemutatta megvalósításához a Security Center javaslat "Add a web application." Webalkalmazási tűzfal konfigurálásával kapcsolatos további tudnivalókért olvassa el a következőket:

* [Webalkalmazási tűzfal (WAF) App Service Environment-környezet konfigurálása](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
