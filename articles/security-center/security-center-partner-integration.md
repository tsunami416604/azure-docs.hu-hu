---
title: "Partnerintegráció az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8dddfc8929ab1a0c44522ed2a2596e2c82e3987d
ms.contentlocale: hu-hu
ms.lasthandoff: 05/01/2017


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

- Végpontvédelem ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)) 
- Webalkalmazás-tűzfal ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) és [App Gateway WAF](https://azure.microsoft.com/en-us/blog/azure-web-application-firewall-waf-generally-available/)) 
- Újgenerációs tűzfalmegoldások ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/) és hamarosan [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) és [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Sebezhetőség-felmérés ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) – előzetes verzió).  

Idővel a Security Center kibővíti a partnerek számát a meglévő kategóriákban, és új kategóriák is elérhetők lesznek. 

## <a name="how-to-deploy-a-partner-solution"></a>Hogyan helyezhetők üzembe a partnermegoldások?

Az Azure-környezet konfigurációja és az Ön által meghatározott biztonsági szabályzat alapján a Security Center javasolhatja egy partnermegoldás üzembe helyezését. A javaslatok végigvezetik a partnermegoldás kiválasztásának és telepítésének folyamatán. Az általános üzembe helyezési folyamat az adott megoldástípusnak és partnernek megfelelően eltérő lehet. További információért kövesse az alábbi hivatkozásokat:

- [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md)
- [Újgenerációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md)
- [Endpoint Protection telepítése](security-center-install-endpoint-protection.md)
- [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>A partnermegoldások kezelése

Miután a partnermegoldást üzembe helyezte, információkat tekinthet meg az állapotáról, és alapszintű felügyeleti feladatokat végezhet a Security Center fő irányítópultjának Partnermegoldás csempéjén. A partnermegoldások Security Centerben végzett kezelésével kapcsolatos további információkért tekintse meg a [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) című cikket.

![Partnerintegráció](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedett a partnermegoldások az Azure Security Centerrel való integrációjával. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások típus szerint az Azure Security Centerben](security-center-alerts-type.md)
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

