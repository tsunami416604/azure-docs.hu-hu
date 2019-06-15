---
title: HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure portal újraírási |} A Microsoft Docs
description: Az Azure portal használata az Azure Application Gateway a HTTP-fejléceket, a kérelmek és válaszok az átjárón áthaladó újraírási konfigurálása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947166"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure portal újraírása

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure portal használatával egy [Application Gateway v2 szintű Termékváltozatot](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) példány a HTTP-fejléceket, a kérelmek és válaszok újraírási.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz egy Application Gateway v2 SKU példány ebben a cikkben szereplő lépések végrehajtásához. A fejlécek újraírását a v1-termékváltozat nem támogatott. Ha nem rendelkezik a v2 szintű Termékváltozatot, hozzon létre egy [Application Gateway v2 szintű Termékváltozatot](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) példány megkezdése előtt.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

HTTP-fejléc újraírási konfigurálásához hajtsa végre az alábbi lépéseket kell.

1. A HTTP-fejléc újraírási szükséges objektumok létrehozása:

   - **Művelet újraírási**: A kérés és a kérés üzenetfejlécének mezői újraírási kívánt és az új értéket a fejlécek meghatározására szolgál. Társíthatja egy vagy több újraírási feltételek újraírási művelettel.

   - **A feltétel újraírási**: Választható konfiguráció. Újraírási feltételek kiértékelése a HTTP (S)-kérelmek és válaszok tartalmát. Az újraírási művelet fordul elő, ha a HTTP (S) kérelem vagy válasz megegyezik a újraírási feltétel.

     Ha egynél több feltétel társítása egy műveletet, a művelet csak akkor, ha a feltételek mindegyike teljesül következik be. Más szóval a művelet egy logikai és műveletet.

   - **Újraírási szabályt**: Több újraírási műveletet tartalmaz / újraírási feltétel kombinációi.

   - **Feladatütemezési szabály**: Segít a újraírási szabályok hajtsa végre a sorrend határozza meg. Ez a konfiguráció akkor hasznos, ha több újraírási szabályok újraírási készlet rendelkezik. Egy szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály első fut. Ha két újraírási szabályok szabály feladatütemezési ugyanazt az értéket rendel, akkor a végrehajtás sorrendje nem determinisztikus.

   - **Set újraírási**: Több újraírási szabályok, amelyek lesz társítva a egy kérelem-útválasztási szabályt tartalmaz.

2. Csatoljon egy útválasztási szabály beállítása átírása. Az újraírási konfigurációs van csatolva, a forrás figyelőt az útválasztási szabályt. Ha egy alapszintű útválasztási szabályt használ, a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használatakor a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ebben az esetben csak vonatkozik egy helyet a megadott elérési út területéhez.

Hozzon létre több HTTP-fejléc újraírási csoportokat, és minden egyes újraírási több kérésfigyelőt beállítása a alkalmazni. Azonban csak az egyik értéke egy adott hallgató újraírási alkalmazhat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="configure-header-rewrite"></a>Fejléc újraírási konfigurálása

Ebben a példában egy átirányítási URL-cím a háttér-alkalmazás által küldött HTTP-válasz location fejlécébe újraírásával módosítjuk.

1. Válassza ki **összes erőforrás**, majd válassza ki az application gateway.

2. Válassza ki **újraírja** a bal oldali panelen.

3. Válassza ki **set Újraírási**:

   ![Újraírási készlet hozzáadása](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Adja meg a módosítsa úgy a készlet nevét, és társíthatja azt egy útválasztási szabályt:

   - Adja meg a beállított átírása nevét a **neve** mezőbe.
   - Választhat egyet vagy többet a szabály szerepel a **tartozó útválasztási szabályok** listája. Kiválaszthatja, hogy csak azok a szabályok, amelyek még nem lett más újraírási csoportok társítva. A szabályokat, amelyek már hozzá lettek rendelve, és az egyéb újraírási készletek halványan jelennek meg.
   - Kattintson a **Tovább** gombra.
   
     ![Név és a társítás hozzáadása](media/rewrite-http-headers-portal/name-and-association.png)

5. Hozzon létre egy újraírási szabályt:

   - Válassza ki **Hozzáadás újraírási szabályt**.

     ![Átírási szabály hozzáadása](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Adjon meg egy nevet a újraírási szabályt a **Újraírási szabálynév** mezőbe. Adjon meg egy számot a a **feladatütemezési szabály** mezőbe.

     ![Adja hozzá a újraírási szabály neve](media/rewrite-http-headers-portal/rule-name.png)

6. Ebben a példában a location fejlécet azt fogjuk újraírási csak akkor, ha egy azurewebsites.net hivatkozást tartalmaz. Ehhez a kiértékelését, hogy a válasz location fejlécébe tartalmaz azurewebsites.net feltétel hozzáadása:

   - Válassza ki **feltétel hozzáadása** , és válassza ki a box tartalmazó a **Ha** utasításokat annak kibontásához.

     ![Feltétel hozzáadása](media/rewrite-http-headers-portal/add-condition.png)

   - Az a **ellenőrzéséhez változó típusa** listáról válassza ki **HTTP-fejléc**.

   - Az a **fejléc típus** listáról válassza ki **válasz**.

   - Mivel ebben a példában azt mérlegeli a location fejlécet, amely egy közös fejlécének, válassza ki a **közös fejlécének** alatt **fejlécnév**.

   - Az a **közös fejlécének** listáról válassza ki **hely**.

   - A **kis-és nagybetűket**válassza **nem**.

   - Az a **operátor** listáról válassza ki **egyenlő (=)** .

   - Adjon meg egy Reguláriskifejezés-mintának. Ebben a példában a mintát fogjuk használni `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Kattintson az **OK** gombra.

     ![Konfigurálja az If feltétel](media/rewrite-http-headers-portal/condition.png)

7. A location fejlécet újraírási művelet hozzáadásához:

   - Az a **művelettípus** listáról válassza ki **beállítása**.

   - Az a **fejléc típus** listáról válassza ki **válasz**.

   - A **fejlécnév**válassza **közös fejlécének**.

   - Az a **közös fejlécének** listáról válassza ki **hely**.

   - Adja meg a fejléc értéke. Ebben a példában használunk `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként. Ezt az értéket lecseréli *azurewebsites.net* a *contoso.com* location fejlécébe.

   - Kattintson az **OK** gombra.

     ![Művelet hozzáadása](media/rewrite-http-headers-portal/action.png)

8. Válassza ki **létrehozás** készlet létrehozása az újraírási:

   ![Válassza létrehozása](media/rewrite-http-headers-portal/create.png)

9. Az Újraírási set nézet nyílik meg. Ellenőrizze, hogy a létrehozott újraírási set újraírási készletek listájában:

   ![Újraírási nézet beállítása](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>További lépések

Néhány gyakori alkalmazási helyzetek beállításával kapcsolatos további információkért lásd: [közös fejlécének újraírási forgatókönyvek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).