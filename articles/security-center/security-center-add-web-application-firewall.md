---
title: Webalkalmazási tűzfal hozzáadása az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatainak **webalkalmazási tűzfal hozzáadása** és **alkalmazásvédelem véglegesítése**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2018
ms.author: terrylan
ms.openlocfilehash: 8824e9f7481ed92eb482c14280e79a94be3c9372
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300715"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Webalkalmazási tűzfal hozzáadása az Azure Security Centerben
Az Azure Security Center javasolhatja egy webalkalmazási tűzfal (WAF) hozzáadása egy Microsoft-partner, a webes alkalmazások biztonságossá tételéhez. Ez a dokumentum végigvezeti egy példát a alkalmazni ezt a javaslatot.

A WAF ajánlás bármely nyilvános IP-Címmel (példány szintű IP vagy Load elosztott terhelésű IP), amely rendelkezik egy társított hálózati biztonsági csoport nyílt bejövő webes porttal (80,443) jelennek meg.

A Security Center javasolja, hogy a WAF elleni támadásokat a webalkalmazások virtuális gépeken és a külső App Service Environment (ASE) alatt üzembe helyezett célcsoportkezelés segítségével üzembe helyezi a [elkülönített](https://azure.microsoft.com/pricing/details/app-service/windows/) service-csomag. Az Izolált csomaggal alkalmazását egy privát, dedikált Azure-környezetben üzemeltetheti, és ideális választás olyan alkalmazásokhoz, amelyeknél szempont a biztonságos kapcsolat a helyi hálózathoz, valamint a további teljesítmény és skálázhatóság. Folyamatban van egy izolált környezetben található az alkalmazás mellett az alkalmazás kell rendelkeznie a terheléselosztó külső IP-címet. ASE kapcsolatos további információkért tekintse meg a [App Service Environment dokumentációja](../app-service/environment/intro.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. A **javaslatok**válassza **webalkalmazási tűzfallal webes alkalmazások biztonságossá tételéhez**.
   ![Webes alkalmazás biztonságossá tétele][1]
2. A **védelme webalkalmazási tűzfallal webalkalmazásait**, jelöljön ki egy webes alkalmazást. **Webalkalmazási tűzfal hozzáadása** nyílik meg.
   ![Webalkalmazási tűzfal hozzáadása][2]
3. Választhatja egy meglévő webalkalmazási tűzfalhoz használja, ha rendelkezésre áll, vagy létrehozhat egy újat. Ebben a példában érhetők el semmilyen meglévő alsóbb ezért hozunk létre, hogy a WAF.
4. A WAF létrehozásához integrált partnereink által kínált listájából válasszon ki egy megoldást. Ebben a példában kiválasztjuk **Barracuda Web Application Firewall**.
5. **Barracuda Web Application Firewall** nyílik meg, így a partneri megoldással kapcsolatos információkat. Kattintson a **Létrehozás** gombra.

   ![Tűzfal-információk panel][3]

6. **Új webalkalmazási tűzfal** megnyílik, ahol hajthatja végre **Virtuálisgép-konfiguráció** lépéseit, és adja meg **WAF-információk**. Válassza ki **Virtuálisgép-konfiguráció**.
7. A **Virtuálisgép-konfiguráció**, akkor adja meg a virtuális gépet, amely a WAF üzembe helyezése szükséges adatokat.
   ![Virtuálisgép-konfiguráció][4]
8. Lépjen vissza **új webalkalmazási tűzfal** válassza **WAF-információk**. A **WAF-információk**, konfigurálja magát a WAF. 7. lépés lehetővé teszi, hogy konfigurálja a virtuális gép, amelyen a WAF fut, és 8. lépés lehetővé teszi, hogy helyezze üzembe a WAF magát.

## <a name="finalize-application-protection"></a>Alkalmazásvédelem véglegesítése
1. Lépjen vissza **javaslatok**. Egy új bejegyzést jött létre, miután létrehozta a WAF nevű **alkalmazásvédelem véglegesítése**. Ez a bejegyzés jelzi, hogy ténylegesen ezzel elvégeztük a WAF az Azure virtuális hálózaton belül úgy, hogy az alkalmazás megvédheti a folyamat végrehajtásához szükséges.

   ![Alkalmazásvédelem véglegesítése][5]

2. Válassza ki **alkalmazásvédelem véglegesítése**. Egy új panel nyílik meg. Láthatja, hogy nincs-e egy webalkalmazást, amely átirányítva a forgalom rendelkeznie kell.
3. Válassza ki a webes alkalmazást. Megnyílik egy panel, amely a webalkalmazási tűzfal beállításának véglegesítése vonatkozó lépéseket biztosít. Hajtsa végre a lépéseket, és válassza ki **korlátozzák a forgalmat**. A Security Center majd elvégzi a háttérszolgáltatást fel.

   ![Forgalom korlátozása][6]

> [!NOTE]
> A Security Center több webalkalmazás védheti meg ezeket az alkalmazásokat a meglévő WAF-példányok hozzáadásával.
>
>

A naplókat, hogy a WAF-ból már teljes mértékben integráltuk. A Security Center elindíthat automatikus gyűjtése és elemzése a naplókat, hogy az Ön számára fontos biztonsági riasztásokat,-kkel.

## <a name="next-steps"></a>További lépések
Ez a dokumentum láthatta, hogyan valósíthat meg a Security Center javaslatait "Add a web application." Webalkalmazási tűzfal konfigurálásával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Webalkalmazás-tűzfal (WAF) konfigurálása App Service Environment környezetben](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
