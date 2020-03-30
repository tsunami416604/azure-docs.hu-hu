---
title: Az Azure API-kezelési házirendek beállítása és szerkesztése | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan állíthatja be és szerkeszti az Azure API-kezelési szabályzatokat.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071703"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management-szabályzatok beállítása és szerkesztése

A házirend-definíció egy XML-dokumentum, amely a bejövő és kimenő utasítások sorozatát írja le. Az XML közvetlenül a definíciós ablakban szerkeszthető. A házirendablak tól jobbra található listából is kiválaszthat egy előre definiált házirendet. Az aktuális hatókörre vonatkozó utasítások engedélyezve vannak és ki vannak emelve. Ha egy engedélyezett utasításra kattint, a megfelelő XML-t adja hozzá a kurzor helyére a definíciós nézetben. 

A szabályzatokról az [Azure API Management szabályzatai](api-management-howto-policies.md)című témakörben talál részletes információt.

## <a name="set-or-edit-a-policy"></a>Házirend beállítása vagy szerkesztése

Házirend beállításához vagy szerkesztéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Tallózzon az APIM-példányra.
3. Kattintson az **API-k** lapfülre.

    ![A szabályzat szerkesztése](./media/set-edit-policies/code-editor.png)

4. Válassza ki az egyik korábban importált API-t.
5. Válassza ki a **Tervezés** fület.
6. Válassza ki azt a műveletet, amelyre alkalmazni szeretné a házirendet. Ha a házirendet az összes műveletre alkalmazni szeretné, válassza a **Minden művelet lehetőséget.**
7. Válassza **</>** ki a (kódszerkesztő) ikont a **Bejövő feldolgozás** vagy **a Kimenő feldolgozás** szakaszban.
8. Illessze be a kívánt házirendkódot a megfelelő blokkok egyikébe.

    ```XML
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
    ```
 
## <a name="configure-scope"></a>Hatókör konfigurálása

A házirendek globálisan vagy egy termék, API vagy művelet hatókörén konfigurálhatók. A házirend konfigurálásának megkezdéséhez először ki kell választania azt a hatókört, amelyre a házirendnek alkalmaznia kell.

A házirend-hatókörök kiértékelése a következő sorrendben történik:

1. Globális hatály
2. Termékkör
3. API-hatókör
4. Műveleti hatókör

A házirendeken belüli állítások értékelése az `base` elem elhelyezése szerint történik, ha az jelen van. A globális házirendnek nincs `<base>` szülőházirendje, és az elem használata nincs hatással.

Ha meg szeretné tekinteni az aktuális hatókör házirendjeit a házirendszerkesztőben, kattintson a **Kijelölt hatókör effektív házirendjének újraszámítása**elemre.

### <a name="global-scope"></a>Globális hatály

Globális hatókör van konfigurálva az **összes API-k** az APIM-példányban.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és keresse meg az APIM-példányt.
2. Kattintson **az Összes API-ra.**

    ![Globális hatály](./media/api-management-howto-policies/global-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

    A módosítások at propagáljuk az API Management átjáró azonnal.

### <a name="product-scope"></a>Termékkör

A termékhatókör a kiválasztott termékhez van konfigurálva.

1. Kattintson **a Termékek gombra.**

    ![Termékkör](./media/api-management-howto-policies/product-scope.png)

2. Válassza ki azt a terméket, amelyre házirendeket szeretne alkalmazni.
3. Kattintson **a Házirendek gombra.**
4. Házirendek hozzáadása vagy szerkesztése.
5. Kattintson a **Mentés** gombra. 

### <a name="api-scope"></a>API-hatókör

Az API-hatókör a kijelölt API **minden művelete** beállításával van konfigurálva.

1. Válassza ki azt az **API-t,** amelyhez szabályzatokat szeretne alkalmazni.

    ![API-hatókör](./media/api-management-howto-policies/api-scope.png)

2. Válassza ki a **Minden művelet** lehetőséget.
3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

### <a name="operation-scope"></a>Műveleti hatókör 

A művelethatókör a kijelölt művelethez van konfigurálva.

1. Válasszon egy **API-t**.
2. Válassza ki azt a műveletet, amelyen házirendeket szeretne alkalmazni.

    ![Műveleti hatókör](./media/api-management-howto-policies/operation-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>További lépések

Lásd a következő kapcsolódó témaköröket:

+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
