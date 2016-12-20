---
title: "Partnerintegráció az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 0c946ce6a96f2e3644b9890dad5d60a35ad4bcb7
ms.openlocfilehash: 10184c52d532eb56e66212fafdea3d059b0c43e3


---
# <a name="partner-integration-in-azure-security-center"></a>Partnerintegráció az Azure Security Centerben
Ez a dokumentum ismerteti, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése és integrált Azure-élmény nyújtása érdekében, és hogyan használja ki az Azure Marketplace partnertanúsítványainak és számlázási funkcióinak előnyeit.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Miért érdemes a partnermegoldásokat a Security Centerből üzembe helyezni?

A négy fő érv a Security Center partnerintegrációjának használata mellett:

- **Egyszerű telepítés**: A Security Center javaslatait követve sokkal egyszerűbb üzembe helyezni a partnermegoldást. Az üzembe helyezési folyamat történhet teljesen automatikusan alapértelmezett konfigurációt és hálózati topológiát használva, vagy az ügyfelek választhatják a félig automatikus lehetőséget is, amellyel a konfiguráció rugalmasabb és testre szabható.
- **Integrált észlelések**: A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotfigyelés és -kezelés**: Az integrált állapotesemények lehetővé teszik az ügyfelek számára az összes partnermegoldás gyors áttekintését. Az alapszintű felügyeletből könnyen elérhető a speciális konfiguráció a partnermegoldás használatával.
- **Exportálás SIEM-be**: Az ügyfelek mostantól CEF formátumban exportálhatják az összes Security Center- és partnerriasztást helyszíni SIEM rendszerekbe a Microsoft Azure Log Integration (előzetes verzió) használatával


## <a name="what-partners-are-integrated-with-security-center"></a>Mely partnerek integrálhatók a Security Centerrel?
A Security Center jelenleg a következő partnerekkel integrálható:

- Endpoint Protection (Trend Micro), 
- Webalkalmazási tűzfal (Barracuda, F5, Imperva és hamarosan Microsoft WAF és Fortinet), 
- Újgenerációs tűzfalmegoldások (Check Point, Barracuda és hamarosan Fortinet és Cisco). 
- Sebezhetőség-felmérési megoldások (Qualys – előzetes verzió). 

Idővel a Security Center kibővíti a partnerek számát a meglévő kategóriákban, és új kategóriák is elérhetők lesznek. 

## <a name="how-to-deploy-a-partner-solution"></a>Hogyan helyezhetők üzembe a partnermegoldások?

A Security Centerben már üzembe helyezett partnermegoldások könnyen elérhetőek a Security Center irányítópultjának Partnermegoldás csempéjéről:

![Partnerintegráció](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)

Ha szeretne üzembe helyezni egy új partnermegoldást a Security Center javaslata alapján, hajtsa végre az alábbi lépéseket:

> [!NOTE]
> A következő példa lépései azt feltételezik, hogy Ön rendelkezik olyan számítási feladattal, amelyet szeretne webalkalmazási tűzfallal megvédeni.

1. A Security Center irányítópultján kattintson a **Javaslatok** csempére.
2. A **Javaslatok** panelen kattintson a **Webalkalmazási tűzfal hozzáadása** elemre.
3. Kattintson az alkalmazás nevére a **Webalkalmazási tűzfal hozzáadása** panel alatt.
4. A **Webalkalmazási tűzfal hozzáadása** panelen kattintson az **Új létrehozása** elemre.
5. Az **Új webalkalmazási tűzfal létrehozása** panel megjeleníti azon jelenlegi partnerek listáját, amelyek webalkalmazási tűzfal képességet nyújtanak.
6. Válassza ki a megfelelő partnermegoldást, és kövesse a lépéseket (a lépések partnerenként eltérőek lehetnek).

Az általános üzembe helyezési élmény az adott partnernek megfelelően eltérő lehet. A partnermegoldások a Security Centerben való kezelésével kapcsolatos további információkért lásd a[Partnermegoldások az Azure Security Centerrel való figyeléséről](security-center-partner-solutions.md) szóló cikket.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedett a partnermegoldások az Azure Security Centerrel való integrációjával. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások típus szerint az Azure Security Centerben](security-center-alerts-type.md)
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Nov16_HO5-->


