---
title: Hozzon létre bejárati ajtó a HTTP – HTTPS átirányításról, az Azure portal használatával
description: Ismerje meg, hogyan hozhat létre egy bejárati ajtajának átirányított érkező HTTP – HTTPS az Azure portal használatával.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332595"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Hozzon létre bejárati ajtó a HTTP – HTTPS átirányításról, az Azure portal használatával

Az Azure portal segítségével hozzon létre egy [bejárati ajtajának](front-door-overview.md) az SSL-lezárást tanúsítvánnyal. Útválasztási szabály HTTP-forgalom átirányítása HTTPS segítségével.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy bejárati ajtajának meglévő webalkalmazás erőforrás
> * Egyéni tartomány SSL-tanúsítvány hozzáadása 
> * Az egyéni tartomány HTTPS átirányítás beállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Hozzon létre egy bejárati ajtajának meglévő webalkalmazás erőforrás

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Keresse meg **bejárati ajtajának** és miután megtalálta az erőforrás típusa, kattintson a keresés használatával **létrehozás**.
4. Válasszon egy előfizetést majd használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Vegye figyelembe, hogy a hely, a felhasználói felületen kéri a csak az erőforráscsoportnak. Összes megtörténik a bejárati ajtajának konfigurációs [Azure bejárati ajtajának POP-helyek](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Alapvető beállítások konfigurálása az új bejárati ajtajának](./media/front-door-url-redirect/front-door-create-basics.png)

5. Kattintson a **tovább** , adja meg a konfigurációs lapon. A konfigurációját a bejárati ajtajának három lépésben – alapértelmezett előtérbeli gazdagép hozzáadásakor. háttérrendszerek hozzáadása a háttérkészlet, majd hozza létre az útválasztási viselkedés előtérbeli gazdagéphez hozzárendelni útválasztási szabályok történik. 

     ![Bejárati ajtajának konfigurációtervező](./media/front-door-url-redirect/front-door-designer.png)

6. Kattintson a " **+** " ikonra a _előtérbeli gazdagépek_ hozzon létre egy előtérbeli gazdaszolgáltatást, adjon meg egy globálisan egyedi nevet az alapértelmezett előtérbeli gazdagép számára a bejárati ajtajának (`\<**name**\>.azurefd.net`). Kattintson a **Hozzáadás** , folytassa a következő lépéssel.

     ![Egy előtér-gazdagép hozzáadása](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kattintson a " **+** " ikonra a _háttérkészletek_ háttérkiszolgáló-készlet létrehozása. Adja meg a háttérkészlet nevét, és kattintson a " **-integrációs háttérkomponensek felvétele**".
8. Válassza ki a háttérrendszer gazdagép típusként _App Service-ben_. Válassza ki az előfizetést, ahol a webalkalmazást üzemeltetik és a legördülő menüből válassza ki a konkrét webalkalmazás **háttérrendszer állomásnév**.
9. Kattintson a **Hozzáadás** a háttérrendszer mentéséhez, és kattintson a **Hozzáadás** újra a háttérkiszolgáló-készlet konfigurációs mentésére.   ![A háttérkészlet-integrációs háttérkomponensek felvétele](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Kattintson a " **+** " ikonra a _útválasztási szabályok_ hozhat létre egy útvonalat. Adjon meg egy nevet az útvonal tegyük fel, hogy a "HttpToHttpsRedirect", és adja meg a _elfogadott protokollok_ mezőt **"Csak HTTP"** . Győződjön meg arról, hogy a megfelelő _előtérbeli gazdagép_ van kiválasztva.  
11. Az a _Útvonaladatok_ szakaszában a _Útvonaltípus_ való **átirányítási**, ügyeljen arra, hogy a _átirányítási típus_ értékre van állítva  **Megtalálható (302)** és _protokoll átirányítási_ értékre van állítva **csak HTTPS**. 
12. Kattintson a Hozzáadás gombra a HTTP – HTTPS átirányítás útválasztási szabályának mentéséhez.
     ![Adja hozzá a HTTP a HTTPS-átirányítási útvonal](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adjon hozzá egy másik útválasztási szabályt, a HTTPS-forgalom kezelésére. Kattintson a " **+** " jelentkezzen be a _útválasztási szabályok_ , és adjon meg egy nevet az útvonal tegyük fel, hogy a "DefaultForwardingRoute", és állítsa a _elfogadott protokollok_ mező a **"Csak HTTPS"** . Győződjön meg arról, hogy a megfelelő _előtérbeli gazdagép_ van kiválasztva.
14. A útvonal részleteket tartalmazó területen állítsa be a _Útvonaltípus_ való **előre**, győződjön meg arról, hogy a megfelelő háttérkészlet van kiválasztva, és a _továbbítási protokoll_ értékre van állítva  **Csak HTTPS**. 
15. Kattintson a Hozzáadás gombra a kérelem továbbítása az útválasztási szabály mentéséhez.
     ![Előre a HTTPS-forgalmat útvonal hozzáadása](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kattintson a **felülvizsgálat + létrehozása** , majd **létrehozás**, a bejárati ajtajának profil létrehozásához. Nyissa meg az egyszer létrehozott erőforrást.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Egyéni tartomány hozzáadása a bejárati ajtajának és a HTTPS engedélyezése
Az alábbi lépéseket mutatni, hogyan lehet egyéni tartomány hozzáadása egy meglévő erőforráson bejárati ajtajának, és engedélyeznie kell a HTTP – HTTPS átirányításról. 

### <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása

Ebben a példában hozzá egy CNAME-rekordot a `www` altartományt (például `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME-rekordot a bejárati ajtajának alapértelmezett előtérbeli gazdagépre altartomány hozzárendelése (`<name>.azurefd.net`, ahol `<name>` a bejárati ajtajának profil neve).

Az a `www.contoso.com` tartomány, például, adjon hozzá egy CNAME-rekordot, amely leképezi a név `www` való `<name>.azurefd.net`.

Miután hozzáadta a CNAME rekordot, a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Egyéni tartomány CNAME bejárati ajtajának](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Előkészítheti az egyéni tartományt állít be a bejárati ajtó

1. A Tervező bejárati ajtajának lapon kattintson a "+" ikont a Frontend gazdagépeken szakasz egy új egyéni tartomány hozzáadása. 
2. Adja meg a teljes egyéni DNS-nevet az egyéni állomás neve mezőben, például `www.contosonews.com`. 
3. Ha a CNAME-leképezés az a bejárati ajtó a tartomány érvényesítve van, kattintson a **Hozzáadás** az egyéni tartomány hozzáadása.
4. Kattintson a **mentése** gombra a módosítások küldéséhez.

![Egyéni tartomány menü](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Az egyéni tartomány HTTPS engedélyezése

1. Kattintson az egyéni tartományban vettek fel, és a szakaszában **egyéni tartományi HTTPS**, állítsa a állapotát **engedélyezve**.
2. Hagyhatja az **felügyeleti tanúsítványtípus** beállítása _felügyelt bejárati ajtajának_ az ingyenes tanúsítványt tartani, felügyelt, és autorotated bejárati ajtajának által. Azt is beállíthatja a saját egyéni az Azure Key Vaultban tárolt SSL-tanúsítvány használatára. Ez az oktatóanyag feltételezi, hogy bejárati ajtajának használata által kezelt tanúsítvány.
![Egyéni tartomány HTTPS engedélyezése](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kattintson a **frissítés** menteni a kijelölést, majd **mentése**.
4. Kattintson a **frissítése** után néhány percet, és kattintson újra a Tanúsítványtelepítés előrehaladásának megtekintéséhez az egyéni tartományon. 

> [!WARNING]
> HTTPS engedélyezése az egyéni tartományt is igénybe vehet néhány perc és is függ a tartomány tulajdonjogának ellenőrzési, ha a CNAME nincs közvetlenül leképezve bejárati ajtajának gazdagépre `<name>.azurefd.net`. Tudjon meg többet [HTTPS engedélyezése egyéni tartomány](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Az útválasztási szabályokat az egyéni tartomány konfigurálása

1. Kattintson a korábban létrehozott átirányítási útválasztási szabályt.
2. Kattintson a legördülő listában az előtér-gazdagépek, és válassza ki az egyéni tartomány a alkalmazni ezt az útvonalat, valamint a tartományhoz.
3. Kattintson az **Update** (Frissítés) elemre.
4. Végezze el a többi útválasztási szabály is ugyanazt a műveletet, a továbbítási útvonal, az egyéni tartomány hozzáadása a.
5. Kattintson a **mentése** gombra a módosítások küldéséhez.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- Tudjon meg többet [bejárati ajtajának az átirányítási URL-cím](front-door-url-redirect.md).
