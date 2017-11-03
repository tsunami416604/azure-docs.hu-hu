---
title: "Webalkalmazási tűzfal hozzáadása az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslatait ** hozzáadása a webes alkalmazás tűzfal ** és ** véglegesítése alkalmazás védelmi **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: terrylan
ms.openlocfilehash: e858db97c3e7a832ad01e16a60d486a758109d7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Webalkalmazási tűzfal hozzáadása az Azure Security Centerben
Az Azure Security Center javasolhatja, hogy a webes alkalmazás tűzfalat (WAF) hozzáadása egy Microsoft-partner biztonságossá tételéhez a webes alkalmazások. Ez a dokumentum útmutatást nyújt a példa bemutatja, hogyan Ez a javaslat alkalmazása.

WAF ajánlás bármely nyilvános felé néző IP-cím (példány szint IP vagy terhelés eloszlik IP), amely rendelkezik egy társított hálózati biztonsági csoportot a nyissa meg a bejövő webes portokkal (80,443) jelenik meg.

A Security Center javasolja, hogy egy WAF a webes alkalmazások, virtuális gépek és az App Service Environment-környezet célzó támadások elleni védelemre való ellátásához. Az App Service környezetben (ASE) van egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) terv lehetőséget az Azure App Service egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására az Azure App Service apps biztosító szolgáltatás. ASE kapcsolatos további tudnivalókért tekintse meg a [App Service környezetben dokumentáció](../app-service/environment/intro.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **biztonságossá webalkalmazását webalkalmazási tűzfal használatával**.
   ![Biztonságos webes alkalmazás][1]
2. Az a **a webes alkalmazások webalkalmazási tűzfal biztonságos** panelen válassza ki a webalkalmazás. A **webalkalmazási tűzfal hozzáadása** panel nyílik meg.
   ![Webalkalmazási tűzfal hozzáadása][2]
3. Választhat egy létező webalkalmazási tűzfal használata, ha elérhető, vagy létrehozhat egy újat. Ebben a példában érhetők el nem létező WAFs, létrehozhatunk egy WAF.
4. Egy WAF létrehozásához válassza a megoldás az integrált partnereink listájából. Ez a példa azt válassza **Barracuda webalkalmazási tűzfal**.
5. A **Barracuda webalkalmazási tűzfal** panel nyílik meg, biztosítva a partnermegoldáshoz kapcsolatos információkat. Válassza ki **létrehozása** az adatokat a panelen.

   ![Tűzfal információk panel][3]

6. A **új webalkalmazási tűzfal** panel megnyitása, ahol elvégezhető **Virtuálisgép-konfiguráció** lépéseit, és adja meg **WAF információk**. Válassza ki **Virtuálisgép-konfiguráció**.
7. Az a **Virtuálisgép-konfiguráció** panelen meg lépett fel a virtuális gép, amelyen a WAF szükséges adatokat.
   ![Virtuálisgép-konfiguráció][4]
8. Lépjen vissza a **új webalkalmazási tűzfal** panelhez, és válassza **WAF információk**. Az a **WAF információt** panelen konfigurálja a WAF magát. 7. lépés lehetővé teszi, hogy konfigurálja a virtuális gép, amelyen a WAF fut, és 8. lépés lehetővé teszi, hogy hozzon létre a WAF magát.

## <a name="finalize-application-protection"></a>Alkalmazás védelem véglegesítése
1. Lépjen vissza a **javaslatok** panelen. Egy új bejegyzést jött létre, miután létrehozta a WAF nevű **alkalmazás védelem véglegesítése**. Ez a bejegyzés azt jelzi, hogy végre kell hajtania a folyamat ténylegesen az Azure virtuális hálózaton belül WAF elvégeztük így megvédheti a az alkalmazás lehetővé teszi.

   ![Alkalmazás védelem véglegesítése][5]

2. Válassza ki **alkalmazás védelem véglegesítése**. Ekkor megnyílik egy új panel. Láthatja, hogy van-e egy webes alkalmazás, amely a forgalom átirányítása megtörténik rendelkeznie kell.
3. Válassza ki a webalkalmazás. Ekkor megnyílik egy panel, amely lehetővé teszi az lépéseket a véglegesítése a webalkalmazás tűzfal beállítása. Hajtsa végre a lépéseket, majd válassza ki **korlátozzák a forgalmat**. A Security Center majd elvégzi a vezetékezést fel.

   ![Korlátozzák a forgalmat.][6]

> [!NOTE]
> Adja hozzá ezeket az alkalmazásokat a meglévő WAF-központitelepítések védelmet biztosíthat a Security Center több webalkalmazás.
>
>

A naplók az adott WAF most már teljes mértékben. A Security Center automatikusan összegyűjtése és elemzése a naplókat, hogy az Ön számára fontos biztonsági riasztások azt is surface elindíthatja.

## <a name="next-steps"></a>Következő lépések
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
