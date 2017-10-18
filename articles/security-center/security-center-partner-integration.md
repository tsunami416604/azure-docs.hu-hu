---
title: "Partner- és megoldásintegráció az Azure Security Centerben | Microsoft Docs"
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Partner- és megoldásintegráció az Azure Security Centerben

Ebben a cikkben leírjuk, hogy az Azure Security Center hogyan integrálható a partnerekkel az általános biztonság növelése érdekében. A Security Center integrált Azure-felületet kínál, és kihasználja az Azure Marketplace partnertanúsítványainak és számlázási funkcióinak előnyeit.

## <a name="deploy-partner-solutions-from-security-center"></a>Partnermegoldások üzembe helyezése a Security Centerből

Négy fő érv szól a Security Center partnerintegrációjának használata mellett:

- **Egyszerű üzembe helyezés**. A Security Center javaslatait követve sokkal egyszerűbb üzembe helyezni a partnermegoldást. Az üzembehelyezési folyamat teljesen automatizálható egy alapértelmezett beállítás és hálózati topológia használatával. Az ügyfelek választhatják a félig automatikus lehetőséget is, amely rugalmasabb és nagyobb mértékben testre szabható.
- **Integrált észlelések**. A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**. Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.
- **Exportálás SIEM-be**. Az ügyfelek Common Event Format (CEF) formátumban exportálhatják az összes Security Center- és partnerriasztást helyszíni Biztonságiadat- és eseménykezelés (SIEM) rendszerekbe az Azure Log Integration (előzetes verzió) használatával.


## <a name="partners-that-integrate-with-security-center"></a>Security Center-integrációt kínáló partnerek

A jelenleg az Azure Marketplace piactéren a Security Centerrel elérhető partnermegoldások natív integrációi a következők:

- **Végpontok védelme**. [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec és [Microsoft Antimalware az Azure Cloud Services és Virtual Machines szolgáltatáshoz](https://docs.microsoft.com/azure/security/azure-security-antimalware).
- **Webalkalmazási tűzfal**. [Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) és [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/). 
- **Új generációs tűzfal**. [Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) és [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html). 
- **Sebezhetőségi felmérés**. [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/). 

A Security Center tervezi a kategóriákhoz tartozó partnerek bővítését, és új kategóriák is elérhetők lesznek. 

## <a name="deploy-a-partner-solution"></a>Partnermegoldás üzembe helyezése

Az Azure-környezet beállítása és az Ön által meghatározott biztonsági szabályzat alapján a Security Center javasolhatja egy partnermegoldás üzembe helyezését. A Security Centerre vonatkozó javaslatok végigvezetik a partnermegoldás kiválasztásának és telepítésének folyamatán. Az általános üzembehelyezési folyamat az adott megoldástípusnak és a használt partnernek megfelelően eltérő lehet. További információkért tekintse át a következő cikkeket:

- [Végpontvédelem telepítése](security-center-install-endpoint-protection.md)
- [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md)
- [Új generációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md)
- [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>A partnermegoldások kezelése

Az üzembe helyezés után a megoldás állapotinformációinak megtekintéséhez, valamint alapszintű felügyeleti feladatok elvégzéséhez a **Security Center** irányítópulton válassza a **Partnermegoldások** elemet.

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

A Security Solutions megnyitásakor megjelenő tartalmak az infrastruktúrájától függően eltérők lehetnek. Az előző képet példaként használva ez az oldal három szakaszra osztható:

- **Csatlakoztatott megoldások**. A Security Centerhez csatlakoztatott megoldásokat jeleníti meg.
- **Felderített megoldások**. A Security Centerhez nem csatlakoztatott megoldásokat jeleníti meg. Ezek a megoldások csatlakoztathatók, és ebben az esetben a **Csatlakoztatott megoldások** között fognak megjelenni. Ha a Security Center nem észlel nem csatlakoztatott megoldásokat, ez a szakasz rejtve marad.
- **Adatforrások hozzáadása**. A Security Centerhez hozzáadható Azure-beli és nem Azure-beli adatforrásokat jeleníti meg.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz a Security Centerhez aktuálisan csatlakozó összes biztonsági megoldást jeleníti meg. 

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

A megjelenített adatok az adott megoldásoktól függően eltérők lehetnek. Az egyes csempéken elérhető információk például a következők lehetnek:

- **A partner vállalatának ikonja**. Ha a Security Centerben nem látható a vállalat ikonja, úgy a partner nevének első karakterei láthatók.
- **Megoldás típusa**. A megoldás típusát jeleníti meg.
- **Számítógépnév**. A számítógép nevét jeleníti meg.
- **Állapot**. Ha a megoldás nem küld állapotjelző adatokat, a Security Center a legutolsó fogadott esemény dátumát és időpontját jeleníti meg, amely jelzi, hogy a berendezés küld-e jelentést vagy sem. Ha a Security Center nem kap állapotjelző információt egy adott megoldástól, a megoldás csempéje nem látszik ebben a szakaszban.

> [!NOTE]
> A Security Center a legutolsó fogadott esemény dátumát és időpontját jeleníti meg, amely jelzi, hogy a berendezés küld-e jelentést vagy sem. Az állapotjelző adatokat nem küldő megoldások csatlakoztatott megoldásként vannak feltüntetve, ha az elmúlt 14 napban érkezett tőlük riasztás vagy esemény.
>  

A megoldások némelyike teljesen integrálva van az Azure-ba, mások esetleg helyszíni megoldások. Mivel a Security Center támogatja a [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef) formátumot, képes csatlakozni a CEF-et használó alkalmazásokhoz, például a CEF-et támogató tűzfalakhoz. Miután a megoldást hozzáadja a Security Centerhez, a tűzfal a naplókat CEF formátumban küldi a Security Centernek, amely azokat az [Azure Log Analyticsben](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) kezeli. A tűzfal egy nem Azure-beli erőforrás, amely eseményeket igen, állapotjelző adatokat azonban nem küld. A Security Center így csupán annyi információval rendelkezik a berendezés állapotáról, hogy mikor küldött utoljára eseményt. Az összes nem Azure-beli erőforrás esetében a Security Center a csempe állapotjelző területén az utolsó esemény fogadásának dátumát és időpontját jeleníti meg. Ez az információ mutatja, hogy az adott nem Azure-beli erőforrás még küld jelentést.

### <a name="discovered-solutions"></a>Felderített megoldások

A **Felderített megoldások** szakaszban az összes, az Azure-on keresztül hozzáadott megoldás látható. Emellett azok a megoldások is láthatók, amelyek csatlakoztatását a Security Center javasolja.

![Felderített megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

A Security Center integrálható a beépített Azure-beli megoldásokkal, például az [Azure Active Directory (Azure AD) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) megoldással. Ha rendelkezik ugyan egy Azure AD Identity Protection-licenccel, azonban a megoldás nem csatlakozik a Security Centerhez, akkor az Azure AD Identity Protection a **Felderített megoldások** listájában szerepel. A megoldás a Security Centerrel való integrálásához válassza az **Azure AD Identity Protection** csempéjén a **CSATLAKOZÁS** elemet. Ekkor a következő oldal jelenik meg:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Az Azure AD Identity Protection csatlakoztatásának befejezéséhez válasszon egy munkaterületet, ahová az adatok mentve lesznek. Az Azure AD Identity Protection minden adata az ebben a lépésben kiválasztott munkaterület-régióból lesz szolgáltatva. Válassza ki a munkaterületet a munkaterület-választóval, és az adatok elkezdenek oda áramlani.

A Security Center csatlakoztatásához globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal kell rendelkeznie. Ha nem rendelkezik a megfelelő engedélyekkel, a **Csatlakozás** gomb használata le van tiltva. Megjelenik egy üzenet, amely megmagyarázza, hogy a gomb miért van letiltva.

Az Azure AD Identity Protection riasztásai a Security Center észlelési folyamatán keresztül haladnak át. Így Ön a Security Centertől is kaphat riasztásokat az Azure Active Directory Identity Protection mellett. A Security Center az összes relevánsnak tűnő riasztás összevonásával [biztonsági incidenseket](https://docs.microsoft.com/azure/security-center/security-center-incident) hoz létre. A biztonsági incidens leírásában részletesebb információkat talál a gyanús tevékenységekkel kapcsolatban.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Azure-beli és nem Azure-beli számítógépeket is integrálhat a Security Centerrel. A nem Azure-beli számítógépek hozzáadása azt jelenti, hogy helyszíni számítógépeket vagy a CEF formátumot támogató berendezéseket is hozzáadhat. 

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>Lásd még:

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató a Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások típus szerint a Security Centerben](security-center-alerts-type.md)
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Partnermegoldások monitorozása a Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
