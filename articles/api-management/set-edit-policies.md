---
title: "Beállíthatja vagy szerkesztheti az Azure API-felügyeleti házirendek hogyan |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan beállíthatja vagy szerkesztheti az Azure API-felügyeleti házirendek."
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 409069cbc382610a48139df75f0f64b1682d8ee6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Hogyan állítsa be, vagy az Azure API-felügyeleti házirendek szerkesztése

A házirend-definíció egy XML-dokumentum, amely leírja a bejövő és kimenő utasítás sorozata. Az XML-fájl közvetlenül a definíció ablakban szerkeszthető. Igény szerint kiválaszthatja egy előre definiált szabályzattal, amely biztosítja a listából a házirend ablak jobb. Az aktuális hatókör vonatkozó utasítások engedélyezve van, és a kijelölt. A kurzor a definíció nézetben kattintson egy engedélyezett utasítás ad hozzá a megfelelő XML. 

Részletes információ a házirendek: [házirendek az Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Beállíthatja vagy szerkesztheti a házirendet

Beállíthatja vagy szerkesztheti a házirend, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).
2. Keresse meg a APIM példányt.
3. Kattintson a **API-k** fülre.
4. Válasszon egyet a korábban importált API-k.
5. Válassza ki a **tervezési** fülre.
6. Válassza ki, amelyhez a házirendet alkalmazni kívánt műveletet. Ha a házirend alkalmazása az összes művelethez, jelölje be **összes művelet**.
7. Kattintson a Tovább gombra a háromszög a **bejövő** vagy **kimenő** Ceruza.
8. Válassza ki a **kód szerkesztése** elemet.

    ![Házirend szerkesztése](./media/set-edit-policies/set-edit-policies01.png)

9. A kívánt házirend kód illessze be a megfelelő blokkok egyikét.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Hatókör konfigurálása

Házirendek globálisan vagy egy termék, az API-t vagy a művelet a hatókörnél konfigurálhatók. Egy házirend konfigurálásának megkezdéséhez, ki kell választania a hatókör, ahol a házirendet alkalmazni kell.

Házirend hatókörök kiértékelése a következő sorrendben:

1. Globális hatókörű
2. A termék hatókör
3. API-hatókör
4. Műveleti hatókör

A házirendek utasítási elhelyezésének kiértékelése a `base` elem, ha telepítve. Általános házirend nem szülője házirend és a használatával a `<base>` az elem nincs hatása.

A házirendek a Helyicsoportházirend-szerkesztő a jelenlegi hatókörben megtekintéséhez kattintson **számítsa ki újra a kijelölt hatókör hatékony házirend**.

### <a name="global-scope"></a>Globális hatókörű

Globális hatókörrel van konfigurálva **minden API-k** APIM példányában.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) , és keresse meg a APIM példányát.
2. Kattintson a **minden API-k**.

    ![Globális hatókörű](./media/api-management-howto-policies/global-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza ki **kód szerkesztése**.
5. Házirend hozzáadása vagy szerkesztése.
6. Nyomja le az **mentése**. 

    A módosítások azonnal továbbítja az API Management Gateway.

### <a name="product-scope"></a>A termék hatókör

A kijelölt termék termék hatókörben van konfigurálva.

1. Kattintson a **termékek**.

    ![A termék hatókör](./media/api-management-howto-policies/product-scope.png)

2. Jelölje ki a kívánt házirendek alkalmazása a terméket.
3. Kattintson a **házirendek**.
4. Házirend hozzáadása vagy szerkesztése.
5. Nyomja le az **mentése**. 

### <a name="api-scope"></a>API-hatókör

API-hatókör konfigurálva lett a **összes művelet** a kijelölt API.

1. Válassza ki a **API** szeretne alkalmazni a házirendeket.

    ![API-hatókör](./media/api-management-howto-policies/api-scope.png)

2. Válassza ki **összes művelet**
3. Kattintson a háromszög ikonra.
4. Válassza ki **kód szerkesztése**.
5. Házirend hozzáadása vagy szerkesztése.
6. Nyomja le az **mentése**. 

### <a name="operation-scope"></a>Műveleti hatókör 

A kiválasztott művelet műveleti hatókör van konfigurálva.

1. Válasszon egy **API**.
2. Válassza ki a házirendeket alkalmazni kívánt műveletet.

    ![Műveleti hatókör](./media/api-management-howto-policies/operation-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza ki **kód szerkesztése**.
5. Házirend hozzáadása vagy szerkesztése.
6. Nyomja le az **mentése**. 

## <a name="next-steps"></a>Következő lépések

Kapcsolódó témakörök hivatkozásra a következő:

+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)