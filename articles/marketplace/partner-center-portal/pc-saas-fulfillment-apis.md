---
title: SaaS teljesítési API-k | Azure Piactér
description: Bemutatja a teljesítési API-k verzióit, amelyek lehetővé teszik a SaaS-ajánlatok integrálását az Azure Piactérrel.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275730"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment API-k

A SaaS-teljesítési API-k lehetővé teszik a független szoftverszállítók (ISV-k) számára, hogy integrálják SaaS-alkalmazásaikat az Azure Marketplace-en. Ezek az API-k lehetővé teszik, hogy az ISV-alkalmazások részt vegyenek az összes kereskedelmi legengedélyezettebb csatornában: közvetlen, partner által vezetett (viszonteladó) és mezővezérelt csatornákon.  Ezek a követelmények a tranzakciós SaaS-ajánlatok listázása az Azure Marketplace-en.

> [!WARNING]
> Az API jelenlegi verziója a 2-es verzió, amelyet minden új SaaS-ajánlathoz használni kell.  Az API 1-es verziója elavult, és a meglévő ajánlatok támogatása érdekében karbantartás alatt áll.


## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API a következő üzleti modell-képességeket támogatja; képes vagy:

* Adjon meg több tervet egy ajánlathoz. Ezek a csomagok különböző funkciókkal rendelkeznek, és eltérő áron is beárazhatók.
* Ajánlat biztosítása webhelyenként vagy felhasználónkénti számlázási modell alapján.
* Havi és éves (előre fizetett) számlázási lehetőségek megadása.
* Privát árképzés biztosítása az ügyfél számára egyeztetett üzleti megállapodás alapján.


## <a name="next-steps"></a>További lépések

Ha még nem tette meg, regisztrálja a SaaS-alkalmazást az [Azure Portalon](https://ms.portal.azure.com) az [Azure AD-alkalmazás regisztrálása című részben leírtak szerint.](./pc-saas-registration.md)  Ezt követően használja a legújabb verzióját ezen a felületen a fejlesztés: [SaaS teljesítése API Version 2](./pc-saas-fulfillment-api-v2.md).
