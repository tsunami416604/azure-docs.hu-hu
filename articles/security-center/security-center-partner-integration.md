---
title: "Biztonsági megoldások integrálása az Azure Security Centerbe | Microsoft Docs"
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
ms.date: 10/23/2017
ms.author: yurid
ms.openlocfilehash: 847a872661bea31b774814188c7707260a16e620
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center segítségével az integrált partnermegoldások egy optimalizált folyamat mentén helyezhetőek üzembe. A kártevőirtó, sebezhetőségfelmérő és hasonló megoldások esetében a Security Center képes biztosítani a szükséges ügynököt a virtuális gépeken, a tűzfalberendezések esetében pedig elintézi a szükséges hálózati konfigurációs feladatok nagy részét.
- **Integrált észlelések**: A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Jelenleg a következő integrált biztonsági megoldások érhetők el:

- Végpontvédelem ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, [Microsoft Antimalware az Azure Cloud Services és Virtual Machines szolgáltatásokhoz](https://docs.microsoft.com/azure/security/azure-security-antimalware), Windows Defender és System Center Endpoint Protection (SCEP))
- Webalkalmazás-tűzfal ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) és [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Új generációs tűzfalmegoldások ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) és [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Biztonságirés-felmérés ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Csatlakoztathat egyéb biztonsági adatforrásokat is, köztük a következőket:

- Azure AD Identity Protection
- Helyszínen vagy más felhőkben futó számítógépek
- A Common Event Format (CEF) formátumot támogató biztonsági megoldások
- Microsoft Advanced Threat Analytics

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

Az üzembe helyezés után megtekintheti az integrált Azure biztonsági megoldás állapotinformációit, valamint alapszintű felügyeleti feladatokat hajthat végre. Emellett egyéb típusú biztonsági adatforrásokat is csatlakoztathat, például Common Event Format (CEF) formátumú Azure Active Directory Identity Protection-riasztásokat és tűzfalnaplókat. A Security Center irányítópultján válassza a Biztonsági megoldások lehetőséget.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz a Security Centerhez jelenleg csatlakozó biztonsági megoldásokat, valamint az egyes megoldások állapotával kapcsolatos adatokat tartalmazza.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>Felderített megoldások

A **Felderített megoldások** szakaszban az összes, az Azure-on keresztül hozzáadott megoldás látható. Emellett azok a megoldások is láthatók, amelyek csatlakoztatását a Security Center javasolja.

![Felderített megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

A Security Center automatikusan felderíti az Azure-ban futó egyéb biztonsági megoldásokat. Ez az Azure-beli megoldásokat tartalmazza, például az [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) megoldást, valamint az Azure-ban futó partnermegoldásokat. Ha integrálni szeretné ezeket a megoldásokat a Security Centerrel, válassza a **CSATLAKOZTATÁS** lehetőséget.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató a Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [A Microsoft Advanced Threat Analytics csatlakoztatása az Azure Security Centerhez](security-center-ata-integration.md)
* [Az Azure Active Directory Identity Protection csatlakoztatása az Azure Security Centerhez](security-center-aadip-integration.md)
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Partnermegoldások monitorozása a Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
