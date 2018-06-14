---
title: Csatlakozás az Azure Security Center a Microsoft Azure Alkalmazásátjáró |} Microsoft Docs
description: Megtudhatja, hogyan integrálják Application Gateway és az Azure Security Center az erőforrások összesített biztonságának növeléséhez.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854492"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Csatlakozás az Azure Security Center a Microsoft Azure Alkalmazásátjáró
Ez a dokumentum segít az integráció konfigurálása a webalkalmazási tűzfal Application Gateway (WAF) és a Security Center.

## <a name="why-connect-application-gateway"></a>Miért kell csatlakoztatni az Alkalmazásátjáró?
Az alkalmazás átjáró WAF webalkalmazások védje a közös web-alapú támadások, például az SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának. A Security Center integrálódik az Alkalmazásátjáró megelőzése és a környezetben nem védett webalkalmazások veszélyek észlelését.

## <a name="how-do-i-configure-this-integration"></a>Ez az integráció konfigurálása
A Security Center felderíti a korábban telepített WAF az előfizetést. Ezek a megoldások csatlakozni a Security Center integrációs riasztások engedélyezéséhez.

> [!NOTE]
> Az alkalmazás átjáró WAF kiépíthető a Security Center **javaslatok** leírtak [webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md).
>
>

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

2. Az a **Microsoft Azure menü**, jelölje be **Security Center**. Megnyílik a **Security Center – Áttekintés** képernyő.

3. A **áttekintése**, jelölje be **biztonsági megoldások**.

  ![A Security Center áttekintése](./media/security-center-connect-application-gateway/overview.png)

4. A **megoldások felderített** Microsoft Szolgáltatottszoftver-alapú webalkalmazási tűzfal található, és válassza ki **CONNECT**.

  ![Felderített megoldások](./media/security-center-connect-application-gateway/connect.png)

5. **Csatlakozás WAF megoldás** nyílik meg.  Válassza ki **Connect** integrálják a WAF és a Security Center.

  ![WAF-megoldás csatlakoztatása](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan integrálható a biztonsági központban az alkalmazás átjáró WAF. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [A Security Center biztonsági megoldások integrálása](security-center-partner-integration.md)
* [Csatlakozás a Microsoft Advanced Threat Analytics Security Center](security-center-ata-integration.md)
* [Csatlakozás az Azure Active Directory azonosító adatok védelmét a Security Center](security-center-aadip-integration.md)
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md).
* [Partnermegoldások monitorozása a Security Centerrel](security-center-partner-solutions.md).
* [Azure Security Center – gyakori kérdések](security-center-faq.md)
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/).
