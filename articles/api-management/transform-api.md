---
title: Az API-k átalakítása és védelme az Azure API Management szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan védheti meg az API-kat kvótákkal és szabályozási (sebességhatároló) házirendekkel.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: e50c5d942bdbafc60bf0e2b8c74b008ac12b3bc6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084980"
---
# <a name="transform-and-protect-your-api"></a>Az API-k átalakítása és védelme

Az oktatóanyag bemutatja, hogyan alakíthatja át úgy az API-kat, hogy azok ne fedjenek fel privát háttérinformációkat. Előfordulhat például, hogy szeretné elrejteni a háttérrendszeren futó technológiával kapcsolatos információkat. Vagy az is lehet, hogy el kívánja rejteni az API-k HTTP-válaszának szövegtörzsében megjelenő URL-címeket, és átirányítani azokat az APIM-átjáróhoz.

Ez az oktatóanyag továbbá ismerteti, milyen könnyű védelmet biztosítani a háttérbeli API-k számára a hívásszám korlátjának konfigurálásával az Azure API Management segítségével. Korlátozhatja például az API hívásainak számát, hogy a fejlesztők ne vegyék túlzottan igénybe. További információt az [API Management-szabályzatokkal kapcsolatos](api-management-policies.md) cikkben olvashat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> -   Az API átalakítása a válaszfejlécek eltávolításához
> -   Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire
> -   API-k védelme hívásszám-korlátozási szabályzat (szabályozás) hozzáadásával
> -   Az átalakítások tesztelése

![Házirendek](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Előfeltételek

-   Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
-   Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
-   Hajtsa végre a következő rövid útmutatót: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
-   Ezenkívül hajtsa végre a következő oktatóanyaggal: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Az API átalakítása a válaszfejlécek eltávolításához

Ez a szakasz azt mutatja be, hogyan rejtheti el a HTTP-fejléceket, amelyeket nem kíván megjeleníteni a felhasználók számára. Ebben a példában a következő fejlécek lesznek törölve a HTTP-válaszból:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintése:

1. Az APIM-szolgáltatáspéldányban válassza az **API-k** lehetőséget (az **API Management** területen).
2. Kattintson a **Demo Conference API** elemre az API-k listájában.
3. A képernyő felső részén kattintson a **Teszt** fülre.
4. Válassza a **GetSpeakers** műveletet.
5. A képernyő alján kattintson a **Küldés** gombra.

Az eredeti válasznak így kell kinéznie:

![Házirendek](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Az átalakítási szabályzat beállítása

![Kimenő szabályzat beállítása](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Válassza a **Demo Conference API** lehetőséget.
2. A képernyő felső részén válassza a **Tervezés** lapot.
3. Válassza a **Minden művelet** lehetőséget.
4. A **Kimenő feldolgozás** szakaszban kattintson a **</>** ikonra.
5. Vigye a kurzort a **&lt;kimenő&gt;** elemen belülre.
6. A jobb oldali ablak **Átalakítási szabályzatok** területén kattintson kétszer a **+ HTTP-fejléc beállítása** elemre (két szabályzatkódrészlet beszúrásához).

   ![Házirendek](./media/transform-api/transform-api.png)

7. Módosítsa **<outbound>** kódját a következő módon:

       <set-header name="X-Powered-By" exists-action="delete" />
       <set-header name="X-AspNet-Version" exists-action="delete" />

   ![Házirendek](./media/transform-api/set-policy.png)

8. Kattintson a **Mentés** gombra.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire

Ez a szakasz bemutatja, hogy az API HTTP-válaszának szövegtörzsében megjelenő URL-címek hogyan rejthetőek el és irányíthatóak át az APIM-átjáróhoz.

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintése:

1. Válassza a **Demo Conference API** lehetőséget.
2. A képernyő felső részén kattintson a **Teszt** fülre.
3. Válassza a **GetSpeakers** műveletet.
4. A képernyő alján kattintson a **Küldés** gombra.

    Az eredeti válasz a következőhöz hasonló:

    ![Házirendek](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Az átalakítási szabályzat beállítása

1.  Válassza a **Demo Conference API** lehetőséget.
2.  Válassza a **Minden művelet** lehetőséget.
3.  A képernyő felső részén válassza a **Tervezés** lapot.
4.  A **Kimenő feldolgozás** szakaszban kattintson a **</>** ikonra.
5.  Vigye a kurzort a **&lt;kimenő&gt;** elemen belülre.
6.  A jobb oldali ablak **Átalakítási szabályzatok** területén kattintson a **+ Sztring keresése és cseréje a szövegtörzsben** elemre.
7.  A **find-and-replace** kódban (az **\<outbound\>** elemben) cserélje le az URL-címet az APIM-átjáróéra. Példa:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>API-k védelme hívásszám-korlátozási szabályzat (szabályozás) hozzáadásával

Ez a szakasz bemutatja, hogyan lehet védelmet biztosítani a háttérbeli API-k számára a hívásszám korlátjának konfigurálásával. Korlátozhatja például az API hívásainak számát, hogy a fejlesztők ne vegyék túlzottan igénybe. Ebben a példában a korlát minden előfizetési azonosító esetében 15 másodpercenként 3 hívás. 15 másodperc elteltével a fejlesztő újrapróbálhatja az API hívását.

![Bejövő szabályzat beállítása](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  Válassza a **Demo Conference API** lehetőséget.
2.  Válassza a **Minden művelet** lehetőséget.
3.  A képernyő felső részén válassza a **Tervezés** lapot.
4.  Az a **bejövő feldolgozás** területén kattintson a **</>** ikonra.
5.  Vigye a kurzort a **&lt;bejövő&gt;** elemen belülre.
6.  A jobb oldali ablak **Hozzáférés-korlátozási szabályzatok** területén kattintson a **+ Hívások számának korlátozása kulcsonként** elemre.
7.  Módosítsa a **rate-limit-by-key** kódot (az **\<inbound\>** elemben) a következőre:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Az átalakítások tesztelése

Jelenleg, ha a kódszerkesztőben megtekinti a kódot, a szabályzatok így néznek ki:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

A szakasz további részében teszteljük a szabályzatátalakításokat, amelyeket beállítottunk.

### <a name="test-the-stripped-response-headers"></a>Az eltávolított válaszfejlécek tesztelése

1. Válassza a **Demo Conference API** lehetőséget.
2. Kattintson a **Teszt** fülre.
3. Kattintson a **GetSpeakers** műveletre.
4. Kattintson a **Küldés** gombra.

    Láthatja, hogy a fejlécek el lettek távolítva:

    ![Házirendek](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>A lecserélt URL-cím tesztelése

1. Válassza a **Demo Conference API** lehetőséget.
2. Kattintson a **Teszt** fülre.
3. Kattintson a **GetSpeakers** műveletre.
4. Kattintson a **Küldés** gombra.

    Láthatja, hogy az URL-cím le lett cserélve.

    ![Házirendek](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Hívásszám-korlát (szabályozás) tesztelése

1. Válassza a **Demo Conference API** lehetőséget.
2. Kattintson a **Teszt** fülre.
3. Kattintson a **GetSpeakers** műveletre.
4. Kattintson a **Küldés** gombra háromszor egymás után.

    Miután 3 alkalommal elküldte a kérelmet, a **429 Túl sok kérelem** választ kapja.

5. Várjon 15 másodpercet, majd kattintson ismét a **Küldés** gombra. Ezúttal a **200 OK** választ kapja.

    ![Throttling](./media/transform-api/test-throttling.png)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> -   Az API átalakítása a válaszfejlécek eltávolításához
> -   Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire
> -   API-k védelme hívásszám-korlátozási szabályzat (szabályozás) hozzáadásával
> -   Az átalakítások tesztelése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az API monitorozása](api-management-howto-use-azure-monitor.md)
