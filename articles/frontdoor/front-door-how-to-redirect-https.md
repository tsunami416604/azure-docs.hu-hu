---
title: HTTP-HTTPS-átirányítással rendelkező bejárati ajtó létrehozása az Azure Portal használatával
description: Ismerje meg, hogyan hozhat létre egy bejárati ajtót átirányított forgalmat HTTP-HTTPS az Azure Portalon.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878000"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>HTTP-HTTPS-átirányítással rendelkező bejárati ajtó létrehozása az Azure Portal használatával

Az Azure Portal segítségével hozzon létre egy [bejárati ajtót](front-door-overview.md) a TLS-megszüntetési tanúsítvánnyal. Az útválasztási szabály a HTTP-forgalom HTTPS-re való átirányítására szolgál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Bejárati ajtó létrehozása meglévő Web App-erőforrással
> * Egyéni tartomány hozzáadása TLS/SSL tanúsítvánnyal 
> * HTTPS-átirányítás beállítása az egyéni tartományban

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Bejárati ajtó létrehozása meglévő Web App-erőforrással

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Keresse meg a **Bejárati ajtót** a keresősáv használatával, és miután megtalálta az erőforrástípusát, kattintson a **Létrehozás gombra.**
4. Válasszon egy előfizetést, majd használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Megjegyzés: a felhasználói felületen kért hely csak az erőforráscsoportra tartozik. A bejárati ajtó konfigurációja az Azure Front Door összes [POP-helyén](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)lesz telepítve.

    ![Az új bejárati ajtó alapjainak konfigurálása](./media/front-door-url-redirect/front-door-create-basics.png)

5. A **Konfigurációs** lap beírásához kattintson a Tovább gombra. A bejárati ajtó konfigurációja három lépésben történik – egy alapértelmezett előtér-állomás hozzáadása, háttérrendszerek hozzáadása háttérkészlethez, majd útválasztási szabályok létrehozása az előtér-állomás útválasztási viselkedésének leképezéséhez. 

     ![Bejárati ajtó konfigurációtervezője](./media/front-door-url-redirect/front-door-designer.png)

6. Kattintson**+** a ' ' ikonra az _előtér-állomásokon_ előtér-állomás létrehozásához, adjon meg`\<**name**\>.azurefd.net`egy globálisan egyedi nevet az alapértelmezett előtér-állomáshoz a bejárati ajtóhoz ( ). Kattintson a **Hozzáadás gombra** a következő lépéshez.

     ![Előtér-állomás hozzáadása](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Háttérkészlet**+** létrehozásához kattintson a _háttérkészletek_ ' ' ikonjára. Adja meg a háttérkészlet nevét, majd kattintson a " Háttérterület hozzáadása "**gombra.**
8. Válassza ki a háttér-állomástípust _alkalmazásszolgáltatásként._ Válassza ki azt az előfizetést, amelyben a webalkalmazás található, majd válassza ki az adott webalkalmazást a **Háttérállomás nevének**legördülő legördülő menüből.
9. Kattintson a **Hozzáadás** gombra a háttérfájl mentéséhez, majd a **Hozzáadás** gombra a háttérkészlet konfigurációjának mentéséhez.   ![Háttérterület hozzáadása háttérkészlethez](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Útvonal létrehozásához kattintson a '**+**' ikonra az _Útválasztási szabályokon._ Adja meg az útvonal nevét, mondja ki a "HttpToHttpsRedirect" kimondandót, majd állítsa az _Elfogadott protokollok_ mezőt **"CSAK HTTP"** mezőre. Győződjön meg arról, hogy a megfelelő _előtér-állomás_ van kiválasztva.  
11. Az _Útvonal részletei_ szakaszban állítsa az _Útvonaltípus_ **átirányítást,** győződjön meg arról, hogy az _átirányítás típusa_ **Found (302)** és _az Átirányítási protokoll_ beállítása **HTTPS.** 
12. Kattintson a Hozzáadás gombra a HTTP-https-átirányításhoz való mentéséhez.
     ![HTTP hozzáadása HTTPS átirányítási útvonalhoz](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adjon hozzá egy másik útválasztási szabályt a HTTPS-forgalom kezeléséhez. Kattintson**+** a ' ' jelre az _Útválasztási szabályokon,_ adja meg az útvonal nevét, mondja ki a "DefaultForwardingRoute" kijelzését, majd állítsa az _Elfogadott protokollok_ mezőt **"csak HTTPS"** értékre. Győződjön meg arról, hogy a megfelelő _előtér-állomás_ van kiválasztva.
14. Az Útvonal részletei szakaszban állítsa az _Útvonaltípusát_ **Továbbítás**állapotba, győződjön meg arról, hogy a jobb háttérkészlet van kiválasztva, és a _Továbbítási protokoll_ **beállítása csak HTTPS.** 
15. Kattintson a Hozzáadás gombra az útválasztási szabály kérelemtovábbításhoz való mentéséhez.
     ![Továbbítási útvonal hozzáadása a HTTPS-forgalomhoz](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kattintson **a Véleményezés + létrehozás,** majd a **Létrehozás**gombra a Bejárati ajtó profiljának létrehozásához. Ugrás az erőforrásra, miután létrehozta.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Egyéni tartomány hozzáadása a bejárati ajtóhoz, és https engedélyezése rajta
A következő lépések bemutatják, hogyan adhat hozzá egyéni tartományt egy meglévő bejárati ajtó erőforráshoz, majd engedélyezheti a HTTP HTTPS-átirányítást rajta. 

### <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása

Ebben a példában cname rekordot `www` ad hozzá `www.contosonews.com`az altartományhoz (például).

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

CNAME rekord hozzáadásával altartományt szeretne leképezni a bejárati`<name>.azurefd.net`ajtó `<name>` alapértelmezett előtér-állomásához ( , hol van a bejárati ajtó profil neve).

A `www.contoso.com` tartományhoz például adjon hozzá egy CNAME `www` rekordot, amely leképezi a `<name>.azurefd.net`nevet.

Miután hozzáadta a CNAME rekordot, a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![CNAME egyéni tartomány a bejárati ajtóhoz](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Az egyéni tartomány bedeszkázása a bejárati ajtón

1. Új egyéni tartomány hozzáadásához kattintson a Frontend hosts szakasz "+" ikonjára a Bejárati ablak tervezője lapon. 
2. Írja be a teljesen minősített egyéni DNS-nevet `www.contosonews.com`az egyéni állomásnév mezőbe, például . 
3. Miután a CNAME leképezés a tartományból a bejárati ajtó, kattintson a **Hozzáadás** az egyéni tartomány hozzáadásához.
4. A módosítások elküldéséhez kattintson a **Mentés** gombra.

![Egyéni tartomány menü](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományban

1. Kattintson a hozzáadott egyéni tartományra, és az **Egyéni tartomány HTTPS**szakaszában módosítsa az állapotot **Engedélyezve értékre.**
2. A **Tanúsítványkezelési típus** t a _Bejárati ajtó_ ban hagyhatja kezelni az előtér-ajtó által karbantartott, felügyelt és automatikusan elforgatott ingyenes tanúsítványhoz. Az Azure Key Vaultban tárolt egyéni TLS/SSL-tanúsítványt is használhatja. Ez az oktatóanyag feltételezi, hogy a Bejárati ajtó által felügyelt tanúsítvány használata.
![HTTPS engedélyezése egyéni tartományhoz](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. A kijelölés mentéséhez kattintson a **Frissítés** gombra, majd a **Mentés gombra.**
4. Kattintson **a Frissítés** néhány perc múlva, majd kattintson az egyéni tartomány ismét a tanúsítványkiépítés előrehaladásának megtekintéséhez. 

> [!WARNING]
> A HTTPS engedélyezése egy egyéni tartományhoz több percet is igénybe vehet, és a tartomány tulajdonjogának `<name>.azurefd.net`érvényesítésétől is függ, ha a CNAME nincs közvetlenül leképezve a bejárati ajtó gazdagépére. További információ [arról, hogyan lehet engedélyezni a HTTPS protokollt egyéni tartományban.](./front-door-custom-domain-https.md)

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Az egyéni tartomány útválasztási szabályainak konfigurálása

1. Kattintson a korábban létrehozott átirányítási útválasztási szabályra.
2. Kattintson az előtér-állomások legördülő menüre, és válassza ki az egyéni tartományt, ha ezt az útvonalat is alkalmazni szeretné a tartományra.
3. Kattintson a **Frissítés** parancsra.
4. Végezze el ugyanazt a műveletet a többi útválasztási szabály is, hogy az átirányítási útvonal hozzáadása az egyéni tartomány.
5. A módosítások elküldéséhez kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További információ az [URL-átirányításról a Bejárati ajtón.](front-door-url-redirect.md)
