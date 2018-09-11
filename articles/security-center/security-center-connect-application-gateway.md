---
title: Csatlakozás a Microsoft Azure Application Gateway az Azure Security Centerbe |} A Microsoft Docs
description: Ismerje meg, hogy miként integrálható az Application Gateway és az Azure Security Center az erőforrások általános biztonságának növelése érdekében.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 5638b71147592ae71c741ca86da68ddfec668af5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299066"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Csatlakozás a Microsoft Azure Application Gateway az Azure Security centerhez
Ebből a dokumentumból megismerheti, hogy az Application Gateway webalkalmazási tűzfal (WAF) és a Security Centerben való integráció konfigurálásához.

## <a name="why-connect-application-gateway"></a>Miért érdemes összekapcsolni az Application Gateway?
Az Application Gateway WAF megvédi a webalkalmazásokat az ismert webalapú támadásoktól, például SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet-eltérítés. A Security Center integrálható az Application Gateway és a környezetben nem védett webalkalmazások fenyegetések észlelését.

## <a name="how-do-i-configure-this-integration"></a>Hogyan konfigurálhatom ezt az integrációt?
A Security Center felderíti az előfizetés korábban üzembe helyezett WAF-példányokat. Ezek a megoldások csatlakozhat a Security Center riasztások integrálását.

> [!NOTE]
> Az Application Gateway WAF is bővítheti a Security Centerből **javaslatok** leírtak szerint [webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md).
>
>

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

2. A **Microsoft Azure menüben** válassza a **Security Center** elemet.

3. A **erőforrás biztonsági higiéniai**válassza **biztonsági megoldások**.

  ![Security Center – Áttekintés](./media/security-center-connect-application-gateway/overview.png)

4. A **felderített megoldások** Microsoft SaaS-alapú WebApp tűzfalat kereséséhez és kijelöléséhez **CONNECT**.

  ![Felderített megoldások](./media/security-center-connect-application-gateway/connect.png)

5. **WAF-megoldás csatlakoztatása** nyílik meg.  Válassza ki **Connect** a WAF és a Security Center integrálható.

  ![WAF-megoldás csatlakoztatása](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan integrálható a Security Center az Application Gateway WAF. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [A Security Center biztonsági megoldások integrálása](security-center-partner-integration.md)
* [Csatlakozás a Microsoft Advanced Threat Analytics a Security centerhez](security-center-ata-integration.md)
* [Az Azure Active Directory Identity Protection csatlakoztatása a Security centerhez](security-center-aadip-integration.md)
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md).
* [Partnermegoldások monitorozása a Security Centerrel](security-center-partner-solutions.md).
* [Azure Security Center – gyakori kérdések](security-center-faq.md)
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/).
