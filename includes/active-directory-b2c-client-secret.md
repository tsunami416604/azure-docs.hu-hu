---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: c88048e3fc62c0414cac45dab9917fe7b2f57922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183320"
---
#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Az **API ACCESS csoportban**válassza a **Kulcsok lehetőséget.**
1. Írja be a kulcs leírását a **Kulcs leírása** mezőben. Például *clientsecret1*.
1. Jelölje ki az érvényesség **időtartamát,** majd kattintson a **Mentés gombra.**
1. Jegyezze fel a kulcs **ÉRTÉKE**. Ezt az értéket egy későbbi lépésben használja a konfigurációhoz.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. A **Kezelés csoportban**válassza **a Tanúsítványok & titkos kulcsok**lehetőséget.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyféltitkos kulcsot a **Leírás** mezőben. Például *clientsecret1*.
1. A **Lejár,** válassza ki azt az időtartamot, amelyre a titkos titok érvényes, majd válassza a **Hozzáadás**lehetőséget.
1. Jegyezze fel a titkos **kulcsot.** Ezt az értéket egy későbbi lépésben használja a konfigurációhoz.