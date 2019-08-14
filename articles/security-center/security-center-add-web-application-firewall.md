---
title: Webalkalmazási tűzfal hozzáadása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a Azure Security Center javaslatokat a webalkalmazási **tűzfal hozzáadása** és az **alkalmazások védelmének véglegesítése**érdekében.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706237"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Webalkalmazási tűzfal hozzáadása Azure Security Center
Azure Security Center javasoljuk, hogy a webalkalmazások biztonságossá tételéhez adjon hozzá egy webalkalmazási tűzfalat (WAF) egy Microsoft-partnerhez. Ez a dokumentum végigvezeti a javaslat alkalmazásának példáján.

Egy WAF javaslat jelenik meg a nyilvános IP-címek (a példányok szintjének IP-címe vagy a terheléselosztási IP-cím) számára, amely egy nyitott bejövő webes porttal (80 443) rendelkező hálózati biztonsági csoporttal rendelkezik.

Security Center azt javasolja, hogy egy WAF hozzon létre, amely védelmet nyújt a webalkalmazásoknak a virtuális gépeken, illetve az [elkülönített](https://azure.microsoft.com/pricing/details/app-service/windows/) szolgáltatáscsomag keretében üzembe helyezett külső app Service környezetekben való megcélzására irányuló támadások elleni védelemben. Az Izolált csomaggal alkalmazását egy privát, dedikált Azure-környezetben üzemeltetheti, és ideális választás olyan alkalmazásokhoz, amelyeknél szempont a biztonságos kapcsolat a helyi hálózathoz, valamint a további teljesítmény és skálázhatóság. Amellett, hogy az alkalmazás elszigetelt környezetben van, az alkalmazásnak rendelkeznie kell egy külső IP-cím terheléselosztó használatával. A betanító szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [app Service Environment dokumentációját](../app-service/environment/intro.md).

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem az útmutató lépésről lépésre.
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok**területen válassza a **biztonságos webalkalmazás lehetőséget a webalkalmazási tűzfal használatával**.
   ![Biztonságos webalkalmazás][1]
2. A webalkalmazások **biztonságossá tétele**a webalkalmazási tűzfallal területen válassza ki a webalkalmazást. Megnyílik **egy webalkalmazási tűzfal** .
   ![Webalkalmazási tűzfal hozzáadása][2]
3. Ha elérhető, vagy létrehozhat egy újat, választhat egy meglévő webalkalmazási tűzfalat is. Ebben a példában nem érhetők el meglévő WAF, ezért hozzunk létre egy WAF.
4. WAF létrehozásához válasszon ki egy megoldást az integrált partnerek listájából. Ebben a példában a **Barracuda webalkalmazási tűzfal**lehetőséget választjuk.
5. A **Barracuda webalkalmazási tűzfal** megnyílik, és információt nyújt a partneri megoldásról. Kattintson a **Létrehozás** gombra.

   ![Tűzfal adatai panel][3]

6. Megnyílik az **új webalkalmazási tűzfal** , ahol elvégezheti a **virtuális** gépek konfigurálásának lépéseit, és **WAF információkat**adhat meg. Válassza ki a **virtuális gép konfigurációját**.
7. A virtuálisgép- **konfiguráció**területen adja meg a WAF futtató virtuális gép felgyorsításához szükséges adatokat.

   ![Virtuális gép konfigurációja][4]
   
8. Térjen vissza az **új** webalkalmazási tűzfalhoz, és válassza ki a **WAF adatait**. A **WAF információi**alatt saját maga is konfigurálhatja a WAF. A 7. lépés lehetővé teszi, hogy konfigurálja a virtuális gépet, amelyen a WAF fut, és a 8. lépés lehetővé teszi magának a WAF a kiépítését.

## <a name="finalize-application-protection"></a>Alkalmazás védelmének véglegesítése
1. Visszatérés a **javaslatokhoz**. A WAF létrehozása után új bejegyzés lett létrehozva, amelynek neve az **alkalmazás-védelem véglegesítése**. Ez a bejegyzés tudatja Önnel, hogy végre kell hajtania az Azure-Virtual Networkon belüli WAF tényleges összekapcsolásának folyamatát, hogy az alkalmazás megvédje az alkalmazást.

   ![Alkalmazás védelmének véglegesítése][5]

2. Válassza az **alkalmazás-védelem véglegesítése**lehetőséget. Megnyílik egy új panel. Láthatja, hogy van egy webalkalmazás, amelynek át kell irányítani a forgalmát.
3. Válassza ki a webalkalmazást. Megnyílik egy panel, amely a webalkalmazási tűzfal beállításának véglegesítéséhez nyújt lépéseket. Hajtsa végre a lépéseket, majd válassza a **forgalom korlátozása**lehetőséget. Security Center ezt követően elvégezheti a kábelezést.

   ![Forgalom korlátozása][6]

> [!NOTE]
> Az alkalmazások a meglévő WAF-környezetekhez való hozzáadásával Security Center több webalkalmazást is védetté tenni.
>
>

A WAF származó naplók mostantól teljesen integráltak. Security Center elindíthatja a naplók automatikus összegyűjtését és elemzését, hogy az képes legyen a fontos biztonsági riasztások felszínére.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan implementálhatja a "webalkalmazás hozzáadása" Security Center javaslatot. A webalkalmazási tűzfal konfigurálásával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Webalkalmazás-tűzfal (WAF) konfigurálása App Service Environment környezetben](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
