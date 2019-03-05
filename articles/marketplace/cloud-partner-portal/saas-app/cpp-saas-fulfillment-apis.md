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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318935"
---
# <a name="saas-fulfillment-apis"></a>SaaS-teljesítési API-k

Az SaaS teljesítése API lehetővé teszi a független szoftvergyártók (ISV), a SaaS-alkalmazások integrálása az Azure piactéren. Ez az API lehetővé teszi, hogy minden engedélyezett kereskedelmi csatornán részt ISV-alkalmazások: a közvetlen partner által irányított (viszonteladói) és a mező által vezetett.  Az API-t annak listaelem transactable SaaS kínál az Azure Marketplace-en.

Ez az API jelenlegi verziója a 2-es verzió, amely kell használni minden új SaaS kínál.  1. verzióját az API elavult, és támogatja a meglévő ajánlatok fenntartani.


## <a name="business-model-support"></a>Üzleti modell támogatása

Ez az API támogatja a következő üzleti modell képességeket; képes vagy:

* Adjon meg egy több terveket. Az alábbi díjcsomagok különböző funkcióval rendelkeznek, és előfordulhat, hogy kell ezekhez különböző árak.
* Adjon meg egy ajánlatot az egy webhelyenként vagy egy számlázási modell történik felhasználónként.
* Adja meg a havi és éves (előre fizetett) számlázási lehetőségekkel.
* Adja meg saját díjszabása alapján egyeztetett üzleti megállapodás ügyfél számára.


## <a name="next-steps"></a>További lépések

A legújabb verzióját használja, ez az interfész fejlesztéshez: [SaaS teljesítése API-Version 2](./cpp-saas-fulfillment-api-v2.md).
