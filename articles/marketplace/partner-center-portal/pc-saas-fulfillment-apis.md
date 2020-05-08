---
title: SaaS-teljesítési API-k a Microsoft kereskedelmi piactéren
description: A bevezetési API-k, amelyek lehetővé teszik a SaaS-ajánlatok integrálását Microsoft AppSource és az Azure piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858086"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>SaaS-teljesítési API-k a Microsoft kereskedelmi piactéren

A SaaS-teljesítési API-k lehetővé teszik a független szoftvergyártók számára a SaaS-alkalmazások integrálását Microsoft AppSource és az Azure piactéren. Ezek az API-k lehetővé teszik az ISV-alkalmazások számára az összes kereskedelmi támogatással rendelkező csatorna részvételét: közvetlen, partner által vezetett (viszonteladói) és mező-vezérelt. A Microsoft AppSource és az Azure Marketplace-en található, a transacter SaaS-ajánlatok listázásához szükségesek.

> [!WARNING]
> Az API jelenlegi verziója 2. verzió, amelyet minden új SaaS-ajánlathoz használni kell.  Az API 1. verziója elavult, és a rendszer fenntartja a meglévő ajánlatok támogatását.

## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API a következő üzleti modell-képességeket támogatja: képes vagy:

* Több csomagot is megadunk egy ajánlathoz. Ezek a csomagok különböző funkciókkal rendelkeznek, és eltérő díjszabással rendelkezhetnek.
* Adjon meg egy ajánlatot webhelyenként vagy felhasználónkénti számlázási modell alapján.
* Adja meg a havi és az éves (fizetős előzetes) számlázási lehetőségeket.
* Egy egyeztetett üzleti szerződés alapján privát díjszabást biztosíthat az ügyfeleknek.


## <a name="next-steps"></a>További lépések

Ha még nem tette meg, regisztráljon az SaaS-alkalmazást a [Azure Portal](https://ms.portal.azure.com) az [Azure ad-alkalmazás regisztrálása](./pc-saas-registration.md)című részben leírtak szerint.  Ezt követően használja az interfész legújabb verzióját a fejlesztéshez: [SaaS beteljesülés API 2-es verziója](./pc-saas-fulfillment-api-v2.md).
