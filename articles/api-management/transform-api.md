---
title: "Az átalakítási és az Azure API Management az API védelme |} Microsoft Docs"
description: "Megtudhatja, hogyan védheti meg az API-kat kvótákkal és szabályozási (sebességhatároló) házirendekkel."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>Az átalakítási és az API védelme 

Az oktatóanyag bemutatja, hogyan az API-átalakítására, így nem fedi fel egy titkos háttér adatait. Például előfordulhat, hogy szeretné, hogy fut a háttérkiszolgálón technológiai területekre további információ elrejtése. Érdemes azt is, az eredeti URL-címek, amelyek API HTTP-válasz törzsében szerepelnek, és ehelyett átirányítja őket az APIM átjáró elrejtése.

Ez az oktatóanyag azt is bemutatja, milyen egyszerűen sávszélesség-korlátjának beállítása az Azure API Management hozzáadása a háttér-API védelmét. Érdemes lehet például egy nevezik, hogy az API-t, akkor az nem színvonalát fejlesztők hívások száma korlátozható. További információkért lásd: [API-felügyeleti házirendek](api-management-policies.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Távolítsa el a válaszfejlécek API átalakítása
> * Cserélje le a választörzs API eredeti URL-címek APIM átjáró URL-címek
> * Az API-k (sávszélesség-szabályozás) arány korlát házirend hozzáadásával védelme
> * Az átalakítás tesztelése

![Házirendek](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Távolítsa el a válaszfejlécek API átalakítása

Ez a szakasz bemutatja, hogyan elrejtése a HTTP-fejlécek nem kívánja megjeleníteni a felhasználók számára. Ebben a példában a következő fejléceket a HTTP-válasz törlődnek:

* **X-regisztráló-által**
* **X-AspNet-verzió**

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintéséhez:

1. Válassza ki a **API** fülre.
2. Kattintson a **bemutató konferencia API** az API listájában.
3. Válassza ki a **GetSpeakers** műveletet.
4. Kattintson a **teszt** lapon, a képernyő felső részén található.
5. Nyomja meg a **küldése** gomb a képernyő alján. 

    Ahogy látja, hogy az eredeti válasz néz ki:

    ![Házirendek](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Az átalakítási házirendjének beállítása

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Válassza ki **összes művelet**.
5. Válassza ki a képernyő felső részén található **tervezési** fülre.
6. Az a **kimenő feldolgozási** ablak, kattintson a háromszög (mellett a Ceruza).
7. Válassza ki **kód szerkesztése**.
    
     ![Házirend szerkesztése](./media/set-edit-policies/set-edit-policies01.png)
9. A kurzor belül a ** <outbound> ** elemet.
10. A jobb oldali ablakban a **átalakítási csoportházirendek**, kattintson a **+ beállított HTTP-fejléc** kétszer (beszúrandó két házirend kódtöredékek).

    ![Házirendek](./media/transform-api/transform-api.png)
11. Módosítsa a ** <outbound> ** kódot a következőképpen néznek ki:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Cserélje le a választörzs API eredeti URL-címek APIM átjáró URL-címek

Ez a szakasz bemutatja, hogyan eredeti URL-címek, amelyek API HTTP-válasz törzsében szerepelnek, és ehelyett átirányítja őket az APIM átjáró elrejtése.

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintéséhez:

1. Válassza ki a **API** fülre.
2. Kattintson a **bemutató konferencia API** az API listájában.
3. Válassza ki a **GetSpeakers** műveletet.
4. Kattintson a **teszt** lapon, a képernyő felső részén található.
5. Nyomja meg a **küldése** gomb a képernyő alján. 

    Ahogy látja, hogy az eredeti válasz néz ki:

    ![Házirendek](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Az átalakítási házirendjének beállítása

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Válassza ki **összes művelet**.
5. Válassza ki a képernyő felső részén található **tervezési** fülre.
6. Az a **kimenő feldolgozási** ablak, kattintson a háromszög (mellett a Ceruza).
7. Válassza ki **kód szerkesztése**.
8. A kurzor belül a ** <outbound> ** elemet.
9. A jobb oldali ablakban a **átalakítási csoportházirendek**, kattintson a **+ található, és cserélje le a karakterlánc a szervezet**.
10. Módosítsa a **< kereshet és cserélhet** kódot (a a ** <outbound> ** elem) lecseréli a APIM átjáró megfelelő URL-CÍMÉT. Példa:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Az API-k (sávszélesség-szabályozás) arány korlát házirend hozzáadásával védelme

Ez a szakasz bemutatja, hogyan adhat hozzá a háttér-API védelmet a sebességhatár beállításával. Érdemes lehet például egy nevezik, hogy az API-t, akkor az nem színvonalát fejlesztők hívások száma korlátozható. Ebben a példában az érték 3 hívásszám 15 másodpercre minden előfizetés-azonosítót. 15 másodperc után egy fejlesztő újra az API felület meghívásakor.

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Válassza ki **összes művelet**.
5. Válassza ki a képernyő felső részén található **tervezési** fülre.
6. Az a **bejövő feldolgozási** ablak, kattintson a háromszög (mellett a Ceruza).
7. Válassza ki **kód szerkesztése**.
8. A kurzor belül a ** <inbound> ** elemet.
9. A jobb oldali ablakban a **hozzáférési szoftverkorlátozó házirendek**, kattintson a **+ korlát hívás arányt kulcs**.
10. Módosítsa a **< arány-korlát-által-kulcs** kódot (a a ** <inbound> ** elem) a következő kód:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Az átalakítás tesztelése
        
Ezen a ponton a kód néz házirendeket:

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
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Ez a szakasz a többi teszteli, ez a cikk a megadott házirend átalakítások.

### <a name="test-the-stripped-response-headers"></a>Tesztelje a erezett válaszfejlécek

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Kattintson a **GetSpeakers** műveletet.
5. Válassza ki a **teszt** fülre.
6. Nyomja le az **küldése**.

    A fejlécek rendelkezik lett tisztító látható:

    ![Házirendek](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>A kicserélt URL-cím tesztelése

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Kattintson a **GetSpeakers** műveletet.
5. Válassza ki a **teszt** fülre.
6. Nyomja le az **küldése**.

    Látható az URL-cím helyett.

    ![Házirendek](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>A sávszélesség-korlátjának (sávszélesség-szabályozás) tesztelése

1. Keresse meg a APIM példányt.
2. Válassza ki a **API** fülre.
3. Kattintson a **bemutató konferencia API** az API listájában.
4. Kattintson a **GetSpeakers** műveletet.
5. Válassza ki a **teszt** fülre.
6. Nyomja le az **küldése** háromszor a sor.

    3-szor vonatkozó kérelem küldése után kapott **429-es jelű túl sok kérelem** választ.
7. Várjon, amíg a 15 másodperces vagy, és nyomja le az ENTER **küldése** újra. Szerezheti be most egy **200 OK** választ.

    ![Szabályozás](./media/transform-api/test-throttling.png)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Távolítsa el a válaszfejlécek API átalakítása
> * Cserélje le a választörzs API eredeti URL-címek APIM átjáró URL-címek
> * Az API-k (sávszélesség-szabályozás) arány korlát házirend hozzáadásával védelme
> * Az átalakítás tesztelése

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Az API-figyelése](api-management-howto-use-azure-monitor.md)
