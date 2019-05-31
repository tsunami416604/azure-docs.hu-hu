---
title: SaaS-teljesítési API-k |} Az Azure Marketplace-en
description: Vezet be, amelyek lehetővé teszik, hogy integrálása az SaaS API-kat kínál az Azure piactéren való teljesítése verzióit.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258924"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment API-k

Az SaaS teljesítése API-kkal a független szoftvergyártók (ISV), a SaaS-alkalmazások integrálása az Azure piactéren. Ezen API-k engedélyezése minden engedélyezett kereskedelmi csatornán részt ISV-alkalmazások: a közvetlen partner által irányított (viszonteladói) és a mező által vezetett.  Azok az Azure Marketplace-en transactable SaaS-ajánlatok listázása előfeltétele.

> [!WARNING]
> Ez az API jelenlegi verziója a 2. verziója, amely kell használni minden új SaaS-ajánlatok.  1. verzióját az API elavult, és támogatja a meglévő ajánlatok fenntartani.


## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API támogatja a következő üzleti modell képességeket; képes vagy:

* Adjon meg egy több terveket. Az alábbi díjcsomagok különböző funkcióval rendelkeznek, és előfordulhat, hogy kell ezekhez különböző árak.
* Adjon meg egy ajánlatot az egy webhelyenként vagy egy számlázási modell történik felhasználónként.
* Adja meg a havi és éves (előre fizetett) számlázási lehetőségekkel.
* Adja meg saját díjszabása alapján egyeztetett üzleti megállapodás ügyfél számára.


## <a name="next-steps"></a>További lépések

Még nem tette meg, ha regisztrálja az SaaS-alkalmazásnak a a [az Azure portal](https://ms.portal.azure.com) leírtak [az Azure AD alkalmazás regisztrálása](./pc-saas-registration.md).  Ezt követően a legújabb verzióját használja, ez az interfész fejlesztéshez: [SaaS teljesítése API-Version 2](./pc-saas-fulfillment-api-v2.md).
