---
title: HTTP-kérelem- és válaszfejlécek újraírása a portálon – Azure Application Gateway
description: Megtudhatja, hogy az Azure Portal használatával hogyan konfigurálhat egy Azure Application Gateway-t az átjárón áthaladó kérelmekben és válaszokban lévő HTTP-fejlécek újraírásához
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012861"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-kérelem- és válaszfejlécek újraírása az Azure Application Gateway alkalmazással – Azure portal

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy [Application Gateway v2 Termékváltozat-példány](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) konfigurálásához a HTTP-fejlécek a kérelmek és válaszok.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések végrehajtásához rendelkeznie kell egy Application Gateway v2 termékváltozatnak. A fejlécek újraírása nem támogatott a v1 termékváltozatban. Ha nem rendelkezik a v2 termékváltozat, hozzon létre egy [Application Gateway v2 termékváltozat,](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) mielőtt elkezdené.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

A HTTP-fejléc újraírásának konfigurálásához végre kell hajtsa végre ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **Újraírási művelet:** Az újraírni kívánt kérés- és kérésfejléc-mezők és a fejlécek új értékének megadására szolgál. Egy vagy több újraírási feltételt újraírási művelethez társíthat.

   - **Újraírási feltétel**: Választható konfiguráció. Az újraírási feltételek kiértékelik a HTTP(S) kérések és válaszok tartalmát. Az újraírási művelet akkor történik meg, ha a HTTP(S) kérés vagy válasz megfelel az újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor történik meg, ha az összes feltétel teljesül. Más szóval a művelet logikai ÉS művelet.

   - **Újraírási szabály**: Több újraírási műveletet /újraírásfeltétel-kombinációt tartalmaz.

   - **Szabálysorozat**: Segít meghatározni az újraírási szabályok végrehajtásának sorrendjét. Ez a konfiguráció akkor hasznos, ha egy újraírási készletben több újraírási szabály is szerepel. Először egy alacsonyabb szabálysorozat-értékkel rendelkező újraírási szabály fut. Ha ugyanazt a szabálysorozat-értéket két újraírási szabályhoz rendeli, a végrehajtási sorrend nem determinisztikus.

   - **Újraírási készlet**: Több újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatolja az újraírási készletet egy útválasztási szabályhoz. Az újraírási konfiguráció az útválasztási szabályon keresztül csatlakozik a forrásfigyelőhöz. Ha alapútválasztási szabályt használ, a fejléc újraírási konfigurációja egy forrásfigyelőhöz van társítva, és globális fejléc-újraírás. Útalapú útválasztási szabály használata esetén a fejléc újraírási konfigurációja az URL-elérési út térképén lesz definiálva. Ebben az esetben csak a webhely adott elérési útterületén érvényes.

Több HTTP-fejléc újraírási készletet hozhat létre, és minden újraírási készletet több figyelőre alkalmazhat. De csak egy újraírási készletet alkalmazhat egy adott figyelőhöz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="configure-header-rewrite"></a>Fejléc újraírásának konfigurálása

Ebben a példában módosítjuk az átirányítási URL-címet a helyfejléc újraírásával a háttéralkalmazás által küldött HTTP-válaszban.

1. Válassza **az Összes erőforrás**lehetőséget, majd válassza ki az alkalmazásátjárót.

2. Válassza az **Átírások** lehetőséget a bal oldali ablaktáblában.

3. Válassza **az Újraírás készlet lehetőséget:**

   ![Újraírókészlet hozzáadása](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Adja meg az újraíró készlet nevét, és társítsa azt egy útválasztási szabályhoz:

   - Írja be az újraíró készlet nevét a **Név** mezőbe.
   - Jelöljön ki egy vagy több szabályt a **Társított útválasztási szabályok** listában. Csak olyan szabályokat választhat ki, amelyek nincsenek társítva más újraíró készletekkel. A más újraíró készletekkel már társított szabályok halványan jelennek meg.
   - Válassza a **Tovább lehetőséget.**
   
     ![Név és társítás hozzáadása](media/rewrite-http-headers-portal/name-and-association.png)

5. Újraírási szabály létrehozása:

   - Válassza **az Átírási szabály hozzáadása lehetőséget.**

     ![Újraírási szabály hozzáadása](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Írja be az újraírási szabály nevét az **Újraírási szabály neve** mezőbe. Írjon be egy számot a **Szabálysorozat** mezőbe.

     ![Újraírási szabály nevének hozzáadása](media/rewrite-http-headers-portal/rule-name.png)

6. Ebben a példában csak akkor írjuk át a helyfejlécet, ha azurewebsites.net tartalmaz. Ehhez adjon hozzá egy feltételt annak kiértékeléséhez, hogy a válaszban szereplő helyfejléc tartalmaz-e azurewebsites.net:

   - Válassza a **Feltétel hozzáadása lehetőséget,** majd jelölje be az **If** utasításokat tartalmazó jelölőnégyzetet a kibontásához.

     ![Feltétel felvétele](media/rewrite-http-headers-portal/add-condition.png)

   - A **ellenőrizandó változó típusa** listában válassza a **HTTP fejléc lehetőséget.**

   - A **Fejléctípus** listában válassza a **Válasz**lehetőséget.

   - Mivel ebben a példában a helyfejlécet értékeljük, amely egy gyakori fejléc, válassza a **Közös fejléc** lehetőséget a **Fejléc neve**csoportban.

   - A **Közös fejléclistában** válassza a **Hely**lehetőséget.

   - A **Kis- és nagybetűk megkülönböztetése**csoportban válassza a **Nem**lehetőséget.

   - Az **Operátor** listában válassza az **Egyenlő (=)** lehetőséget.

   - Adjon meg egy reguláris kifejezésmintát. Ebben a példában a mintát `(https?):\/\/.*azurewebsites\.net(.*)$`fogjuk használni.

   - Válassza **az OK gombot.**

     ![Ha feltétel konfigurálása](media/rewrite-http-headers-portal/condition.png)

7. A helyfejléc újraírásához adjon hozzá egy műveletet:

   - A **Művelettípus** listában válassza a **Set (Beállítás) lehetőséget.**

   - A **Fejléctípus** listában válassza a **Válasz**lehetőséget.

   - A **Fejléc neve csoportban**válassza a **Common header (Közös fejléc) lehetőséget.**

   - A **Közös fejléclistában** válassza a **Hely**lehetőséget.

   - Adja meg a fejléc értékét. Ebben a példában fejlécértékként használjuk. `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` Ez az érték lecseréli *azurewebsites.net* a helyfejlécben *lévő contoso.com.*

   - Válassza **az OK gombot.**

     ![Művelet hozzáadása](media/rewrite-http-headers-portal/action.png)

8. Az újraíró készlet létrehozásához válassza a **Létrehozás** gombot:

   ![Válassza a Létrehozás lehetőséget](media/rewrite-http-headers-portal/create.png)

9. Megnyílik az Újraírás készlet nézet. Ellenőrizze, hogy a létrehozott újraírási készlet szerepel-e az újraírókészletek listájában:

   ![Halmaz nézet újraírása](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni agyakori használati esetek beállításáról, olvassa el a [gyakori fejléc-újraírási forgatókönyvek .](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)