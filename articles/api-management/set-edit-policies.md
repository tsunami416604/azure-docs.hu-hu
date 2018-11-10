---
title: Útmutató az Azure API Management házirendek beállítása vagy szerkesztése |} A Microsoft Docs
description: Ez a témakör bemutatja az Azure API Management házirendek beállítása vagy szerkesztése.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008266"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Útmutató az Azure API Management házirendek beállítása vagy szerkesztése

A szabályzat-definíció egy XML-dokumentum, amely leírja a bejövő és kimenő utasítások sorozata. Az XML-fájl a definíció ablakban közvetlenül szerkesztheti. A házirend-ablak jobb oldalán, a listában megadott közül is választhat egy előre definiált szabályzattal. Az aktuális hatókörben érvényes utasításokat engedélyezve van és vannak kiemelve. A megfelelő XML egy engedélyezett utasítás kattintva hozzáadja a kurzor a definíció nézetben a helyen. 

Szabályzatokkal kapcsolatos részletes információkért lásd: [az Azure API Management házirendek](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Állítsa be, vagy egy szabályzat szerkesztése

Állítsa be, vagy a szabályzatot, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Tallózzon az APIM-példányra.
3. Kattintson az **API-k** lapfülre.

    ![Házirend szerkesztése](./media/set-edit-policies/code-editor.png)

4. Válassza ki az egyik korábban importált API-t.
5. Válassza ki a **Tervezés** fület.
6. Válassza ki egy műveletet, amelyhez a szabályzatot alkalmazni szeretné. Ha meg szeretné alkalmazni a szabályzatot minden művelet, válassza ki a **minden művelet**.
7. Válassza ki a **</>** (kód szerkesztése) ikonra a **bejövő feldolgozás** vagy **kimenő feldolgozás** szakaszban.
8. Illessze be a kívánt szabályzat kódja a megfelelő blokkok egyikét.
         
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

Házirendek is konfigurálhatók, globálisan vagy egy termék, API vagy művelet a hatókörben. A szabályzat konfigurálásának megkezdéséhez, ki kell választania, amikor legyen érvényes a szabályzat hatóköre.

A házirend-hatókörök a következő sorrendben értékeli ki:

1. Globális hatókörű
2. A termék hatókör
3. API-hatókör
4. Műveleti hatókör

Házirendek utasítási elhelyezését szerint értékeli ki a `base` elem, ha jelen. Globális szabályzat rendelkezik szülővel házirend és a használatával a `<base>` , az elem nem lesz hatása.

A házirendeket a házirendszerkesztőben az aktuális hatókör megtekintéséhez kattintson **számítsa ki újra a kijelölt hatókörre érvényes szabályzat**.

### <a name="global-scope"></a>Globális hatókörű

Globális hatókör konfigurálva van a **minden API-k** található az APIM-példányra.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és keresse meg az APIM-példányra.
2. Kattintson a **minden API-k**.

    ![Globális hatókörű](./media/api-management-howto-policies/global-scope.png)

3. Kattintson a háromszögre ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Adjon hozzá vagy szerkesztheti a szabályzatokat.
6. Kattintson a **Mentés** gombra. 

    A módosítások azonnal az API Management-átjáró propagálva.

### <a name="product-scope"></a>A termék hatókör

Termék hatókör konfigurálva van a kijelölt terméket.

1. Kattintson a **termékek**.

    ![A termék hatókör](./media/api-management-howto-policies/product-scope.png)

2. Válassza ki a termék kívánt a alkalmazni a házirendeket.
3. Kattintson a **házirendek**.
4. Adjon hozzá vagy szerkesztheti a szabályzatokat.
5. Kattintson a **Mentés** gombra. 

### <a name="api-scope"></a>API-hatókör

API-hatókör konfigurálva van a **minden művelet** a kijelölt API.

1. Válassza ki a **API** szabályzatokat alkalmazni szeretné.

    ![API-hatókör](./media/api-management-howto-policies/api-scope.png)

2. Válassza ki a **Minden művelet** lehetőséget.
3. Kattintson a háromszögre ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Adjon hozzá vagy szerkesztheti a szabályzatokat.
6. Kattintson a **Mentés** gombra. 

### <a name="operation-scope"></a>Műveleti hatókör 

Műveleti hatókör konfigurálva van a kiválasztott művelet.

1. Válasszon egy **API**.
2. Válassza ki a házirendek a alkalmazni kívánt műveletet.

    ![Műveleti hatókör](./media/api-management-howto-policies/operation-scope.png)

3. Kattintson a háromszögre ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Adjon hozzá vagy szerkesztheti a szabályzatokat.
6. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>További lépések

Az alábbi kapcsolódó témakörök:

+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)