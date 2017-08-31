---
title: "Partnerintegráció az Azure Security Centerben | Microsoft Docs"
description: "Megtudhatja, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében."
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
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: hu-hu
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Partnerintegráció az Azure Security Centerben

Ebben a cikkben leírjuk, hogy az Azure Security Center hogyan integrálható a partnerekkel az általános biztonság növelése érdekében. A Security Center integrált Azure-felületet kínál, és kihasználja az Azure Marketplace partnertanúsítványainak és számlázási funkcióinak előnyeit.

> [!NOTE] 
> 2017 júniusától a Security Center a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További információk: [Az Azure Security Center platform migrálása](security-center-platform-migration.md). A jelen cikkben található információk a Security Center a Microsoft Monitoring Agentre való váltás után elérhető funkcióit ismertetik.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Miért érdemes a Security Centerből üzembe helyezni a partnermegoldásokat?

A négy fő érv a Security Center partnerintegrációjának használata mellett:

- **Egyszerű üzembe helyezés**. A Security Center javaslatait követve sokkal egyszerűbb üzembe helyezni a partnermegoldást. Az üzembehelyezési folyamat teljesen automatizálható egy alapértelmezett beállítás és hálózati topológia használatával. Az ügyfelek választhatják a félig automatikus lehetőséget is, amely rugalmasabb és nagyobb mértékben testre szabható.
- **Integrált észlelések**. A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**. Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.
- **Exportálás SIEM-be**. Az ügyfelek Common Event Format (CEF) formátumban exportálhatják az összes Security Center- és partnerriasztást helyszíni Biztonságiadat- és eseménykezelés (SIEM) rendszerekbe az Azure Log Integration (előzetes verzió) használatával.


## <a name="partners-that-integrate-with-security-center"></a>Security Center-integrációt kínáló partnerek

A Security Center jelenleg ezekkel a megoldásokkal van integrálva:

- Végpontvédelem ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec és [Microsoft Antimalware az Azure Cloud Services és Virtual Machines szolgáltatásokhoz](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Webalkalmazás-tűzfal ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) és [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Új generációs tűzfalmegoldások ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) és [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Biztonságirés-felmérés ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

A Security Center tervezi a kategóriákhoz tartozó partnerek bővítését, és új kategóriák is elérhetők lesznek. 

## <a name="deploy-a-partner-solution"></a>Partnermegoldás üzembe helyezése

Az Azure-környezet beállítása és az Ön által meghatározott biztonsági szabályzat alapján a Security Center javasolhatja egy partnermegoldás üzembe helyezését. A Security Centerre vonatkozó javaslatok végigvezetik a partnermegoldás kiválasztásának és telepítésének folyamatán. Az általános üzembehelyezési folyamat az adott megoldástípusnak és a használt partnernek megfelelően eltérő lehet. További információkért tekintse át a következő cikkeket:

- [Végpontvédelem telepítése](security-center-install-endpoint-protection.md)
- [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md)
- [Új generációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md)
- [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>A partnermegoldások kezelése

Az üzembe helyezés után a megoldás állapotinformációinak megtekintéséhez és alapszintű felügyeleti feladatok elvégzéséhez a **Security Center** panelen válassza a **Partnermegoldások** lehetőséget. A partnermegoldások Security Centerben való kezelésével kapcsolatos további információkért tekintse meg a [Partnermegoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) című cikket.

![Partnerintegráció](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> A Symantec Endpoint Protection támogatása a felderítésre korlátozódik. Az állapotriasztások nem érhetők el.
>

## <a name="see-also"></a>Lásd még:

Ebben a cikkben megismerkedett a partnermegoldások Azure Security Centerrel való integrációjával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató a Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások típus szerint a Security Centerben](security-center-alerts-type.md)
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Partnermegoldások monitorozása a Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

