---
title: A Azure Portal használatával HTTP-vel HTTPS-átirányítással hozhat létre egy bejárati ajtót
description: Megtudhatja, hogyan hozhat létre átirányított forgalmat a HTTP-ről HTTPS-re a Azure Portal használatával.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878000"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>A Azure Portal használatával HTTP-vel HTTPS-átirányítással hozhat létre egy bejárati ajtót

A Azure Portal használatával létrehozhat egy [bejárati ajtót](front-door-overview.md) a TLS-lezáráshoz szükséges tanúsítvánnyal. Egy útválasztási szabály a HTTP-forgalom HTTPS-re való átirányítására szolgál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Bejárati ajtó létrehozása meglévő Web App-erőforrással
> * Egyéni tartomány hozzáadása TLS/SSL-tanúsítvánnyal 
> * HTTPS-átirányítás beállítása az egyéni tartományon

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Bejárati ajtó létrehozása meglévő Web App-erőforrással

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Keresse meg a **bejárati ajtót** a keresősáv használatával, és ha megtalálta az erőforrás típusát, kattintson a **Létrehozás**gombra.
4. Válasszon egy előfizetést, majd használjon egy meglévő erőforráscsoportot, vagy hozzon létre újat. Megjegyzés: a felhasználói felületen megkérdezett hely csak az erőforráscsoport számára érhető el. A bejárati ajtó konfigurációját a rendszer az összes [Azure-beli elülső ajtó pop-helyein](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)üzembe helyezi.

    ![Új bejárati ajtó alapalapjainak konfigurálása](./media/front-door-url-redirect/front-door-create-basics.png)

5. A **tovább** gombra kattintva megadhatja a konfiguráció lapot. A bejárati ajtó konfigurálása három lépésben történik – egy alapértelmezett előtér-gazdagép hozzáadása, a háttérrendszer-készletben lévő hátterek hozzáadása, majd az útválasztási szabályok létrehozása a frontend-gazdagép útválasztási viselkedésének leképezéséhez. 

     ![Bejárati ajtó konfigurációs tervezője](./media/front-door-url-redirect/front-door-designer.png)

6. A előtér-**+** gazdagépek létrehozásához kattintson a "" _ikonra az előtér_ -állomás létrehozásához, adjon meg egy globálisan egyedi nevet a bejárati`\<**name**\>.azurefd.net`ajtóhoz tartozó alapértelmezett előtér-gazdagéphez (). Kattintson a **Hozzáadás** gombra, és folytassa a következő lépéssel.

     ![Előtér-gazdagép hozzáadása](./media/front-door-url-redirect/front-door-create-fehost.png)

7. A háttér-**+** _készletek_ létrehozásához kattintson a "" ikonra a háttér-készleteken. Adja meg a háttér-készlet nevét, majd kattintson**a "háttér hozzáadása**" elemre.
8. Válassza ki a háttér-gazdagép típusát az _app Service_-ben. Válassza ki azt az előfizetést, amelyre a webalkalmazást üzemelteti, majd válassza ki az adott webalkalmazást a legördülő menüből a **háttérbeli gazdagép neve**elemnél.
9. Kattintson a **Hozzáadás** gombra a háttér mentéséhez, majd kattintson ismét a **Hozzáadás** gombra a háttérbeli készlet konfigurációjának mentéséhez.   ![Háttér hozzáadása a háttérbeli készlethez](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Útvonal létrehozásához**+** kattintson az _útválasztási szabályok_ "" ikonjára. Adja meg az útvonal nevét, írja be a "HttpToHttpsRedirect" nevet, majd az _elfogadva protokollok_ mezőt a **"http only"** értékre. Győződjön meg arról, hogy a megfelelő _frontend-gazdagép_ van kiválasztva.  
11. A _Route Details (útvonal részletei_ ) szakaszban adja meg az **átirányítási** _útvonal típusát_ , győződjön meg arról, hogy az _átirányítás típusa_ a **következőre van beállítva (302)** , és az _átirányítási protokoll_ **csak HTTPS**értékre van beállítva. 
12. Kattintson a Hozzáadás gombra a HTTP-re irányuló útválasztási szabály mentéséhez a HTTPS-átirányítás érdekében.
     ![HTTP-HTTPS-átirányítási útvonal hozzáadása](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adjon hozzá egy másik útválasztási szabályt a HTTPS-forgalom kezelésére. Kattintson a "**+**" Bejelentkezés az _útválasztási szabályokra_ , és adja meg az útvonal nevét, mondjuk a "DefaultForwardingRoute" nevet, majd állítsa be az _elfogadott protokollok_ mezőt a **"https only"** értékre. Győződjön meg arról, hogy a megfelelő _frontend-gazdagép_ van kiválasztva.
14. Az útvonal részletei szakaszban adja meg az _útvonal típusát_ a **továbbításhoz**, győződjön meg arról, hogy a jobb oldali háttér-készlet van kiválasztva, és a _továbbítási protokoll_ **csak HTTPS**értékre van beállítva. 
15. Kattintson a Hozzáadás gombra az útválasztási szabály mentéséhez a kérelem továbbításához.
     ![Továbbítási útvonal hozzáadása a HTTPS-forgalomhoz](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőségre az előtérben lévő profil létrehozásához. A létrehozás után nyissa meg az erőforrást.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Egyéni tartomány hozzáadása a bejárati ajtóhoz és a HTTPS engedélyezése
Az alábbi lépések bemutatják, hogyan adhat hozzá egyéni tartományt egy meglévő bejárati erőforráshoz, majd engedélyezheti a HTTP-t a HTTPS-átirányításhoz. 

### <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása

Ebben a példában egy CNAME rekordot ad hozzá az `www` altartományhoz (például `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Adjon hozzá egy CNAME-rekordot egy altartománynak a bejárati ajtó alapértelmezett előtér-`<name>.azurefd.net`gazdagéphez `<name>` való hozzárendeléséhez (ahol a a bejárati ajtó profiljának neve).

A `www.contoso.com` tartomány esetében például adjon hozzá egy CNAME rekordot, amely a nevet `www` a következőre képezi le:. `<name>.azurefd.net`

Miután hozzáadta a CNAME rekordot, a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Egyedi CNAME-tartomány a bejárati ajtóhoz](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Az egyéni tartomány előkészítése a bejárati ajtón

1. Az új egyéni tartomány hozzáadásához az előtér-Tervező lapon kattintson a "+" ikonra a frontend gazdagépek szakaszban. 
2. Adja meg a teljesen minősített egyéni DNS-nevet az egyéni Állomásnév mezőben, például `www.contosonews.com`:. 
3. Ha a tartományból a bejárati ajtóhoz tartozó CNAME-leképezést érvényesíti, kattintson a **Hozzáadás** gombra az egyéni tartomány hozzáadásához.
4. Kattintson a **Save (Mentés** ) gombra a módosítások elküldéséhez.

![Egyéni tartomány menü](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományon

1. Kattintson a hozzáadott egyéni tartományra, és az **egyéni tartomány HTTPS**szakasza alatt módosítsa az állapotot **engedélyezve**értékre.
2. A **tanúsítványkezelő típusa** beállítható úgy, hogy a bejárati ajtó _felügyelve_ legyen a megőrzött, felügyelt és automatikusan áthelyezett tanúsítványokhoz. Azt is megteheti, hogy a Azure Key Vault használatával tárolt egyéni TLS/SSL-tanúsítványt használja. Ez az oktatóanyag feltételezi, hogy az előtérben felügyelt tanúsítvány használata.
![HTTPS engedélyezése az egyéni tartományhoz](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kattintson a **frissítés** elemre a kijelölés mentéséhez, majd kattintson a **Mentés**gombra.
4. Néhány perc elteltével kattintson a **frissítés** elemre, majd kattintson ismét az egyéni tartományra a tanúsítvány kiépítés állapotának megtekintéséhez. 

> [!WARNING]
> Ha engedélyezi a HTTPS-t az egyéni tartományhoz, több percet is igénybe vehet, és a tartomány tulajdonjogának ellenőrzésével is függ, ha a `<name>.azurefd.net`CNAME nem közvetlenül van leképezve az előtérben lévő gazdagépre. További információ a [https engedélyezéséről egyéni tartományhoz](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Az egyéni tartomány útválasztási szabályainak konfigurálása

1. Kattintson a korábban létrehozott átirányítási útválasztási szabályra.
2. Kattintson a előtér-gazdagépek legördülő menüjére, és válassza ki az egyéni tartományt, hogy alkalmazza ezt az útvonalat a tartományhoz is.
3. Kattintson a **Frissítés** parancsra.
4. Végezze el ugyanezt a műveletet a másik útválasztási szabályhoz is, azaz a továbbítási útvonalhoz az egyéni tartomány hozzáadásához.
5. A módosítások elküldéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További információ az [URL-átirányítás a bejárati ajtón](front-door-url-redirect.md).
