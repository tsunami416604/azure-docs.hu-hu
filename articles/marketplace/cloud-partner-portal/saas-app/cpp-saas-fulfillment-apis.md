---
title: SaaS-teljesítési API-k – az Azure Marketplace-en |} A Microsoft Docs
description: Vezet be, amelyek lehetővé teszik, hogy integrálása az SaaS API-kat kínál az Azure piactéren való teljesítése verzióit.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798732"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment API-k

Az SaaS teljesítése API-kkal a független szoftvergyártók (ISV), a SaaS-alkalmazások integrálása az Azure piactéren. Ezen API-k engedélyezése minden engedélyezett kereskedelmi csatornán részt ISV-alkalmazások: a közvetlen partner által irányított (viszonteladói) és a mező által vezetett.  Azok az Azure Marketplace-en transactable SaaS-ajánlatok listázása előfeltétele.

> [!WARNING]
> Ez az API jelenlegi verziója a 2-es verzió, amely kell használni minden új SaaS kínál.  1. verzióját az API elavult, és támogatja a meglévő ajánlatok fenntartani.


## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API támogatja a következő üzleti modell képességeket; képes vagy:

* Adjon meg egy több terveket. Az alábbi díjcsomagok különböző funkcióval rendelkeznek, és előfordulhat, hogy kell ezekhez különböző árak.
* Adjon meg egy ajánlatot az egy webhelyenként vagy egy számlázási modell történik felhasználónként.
* Adja meg a havi és éves (előre fizetett) számlázási lehetőségekkel.
* Adja meg saját díjszabása alapján egyeztetett üzleti megállapodás ügyfél számára.


## <a name="next-steps"></a>További lépések

Még nem tette meg, ha regisztrálja az SaaS-alkalmazásnak a a [az Azure portal](https://ms.portal.azure.com) leírtak [az Azure AD alkalmazás regisztrálása](./cpp-saas-registration.md).  Ezt követően a legújabb verzióját használja, ez az interfész fejlesztéshez: [SaaS teljesítése API-Version 2](./cpp-saas-fulfillment-api-v2.md).
