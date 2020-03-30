---
title: Munkamenet-affinitási problémák elhárítása
titleSuffix: Azure Application Gateway
description: Ez a cikk arról nyújt tájékoztatást, hogyan háríthatók el a munkamenet-affinitási problémák az Azure Application Gateway alkalmazásban
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047982"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Az Azure Application Gateway munkamenet-affinitási problémáinak elhárítása

Ismerje meg, hogyan diagnosztizálhatja és oldhatja meg a munkamenet-affinitási problémákat az Azure Application Gateway segítségével.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="possible-problem-causes"></a>Lehetséges problémák okai

A cookie-alapú munkamenet-affinitás fenntartásának problémája a következő fő okok miatt fordulhat elő:

- A "Cookie-alapú affinitás" beállítás nincs engedélyezve
- Az alkalmazás nem tudja kezelni a cookie-alapú affinitást
- Az alkalmazás cookie-alapú affinitást használ, de a kérelmek továbbra is pattognak a háttérkiszolgálók között

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Annak ellenőrzése, hogy engedélyezve van-e a "Cookie-alapú affinitás" beállítás

Előfordulhat, hogy a munkamenet-affinitási problémák akkor merülnek fel, ha elfelejti engedélyezni a "Cookie-alapú affinitás" beállítást. Annak megállapításához, hogy engedélyezte-e a "Cookie-alapú affinitás" beállítást az Azure Portal HTTP-beállítások lapján, kövesse az alábbi utasításokat:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. A **bal oldali navigációs** ablakban kattintson a **Minden erőforrás elemre.** Kattintson az alkalmazásátjáró nevére a Minden erőforrás panelen. Ha a kiválasztott előfizetésben már több erőforrás is szerepel, megadhatja az alkalmazásátjáró nevét a **Szűrő név szerint...** mezőbe.

3. Válassza a **HTTP-beállítások** lapot a **BEÁLLÍTÁSOK csoportban.**

   ![hibaelhárítás-session-affinitás-problémák-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Kattintson az **appGatewayBackendHttpSettings** elemre a jobb oldalon, hogy ellenőrizze, hogy a Cookie-alapú affinitás **engedélyezve** lehetőséget választotta-e.

   ![hibaelhárítás-session-affinitás-problémák-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



A "**CookieBasedAffinity**" értéke *engedélyezve*van a "**backendHttpSettingsCollection**" alatt az alábbi módszerek egyikével:

- A [Get-AzApplicationGatewayBackendhttpSetting futtatása](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) a PowerShellben
- Tekintse át a JSON-fájlt az Azure Resource Manager sablon használatával

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Az alkalmazás nem tudja kezelni a cookie-alapú affinitást

#### <a name="cause"></a>Ok

Az alkalmazásátjáró csak munkamenet-alapú affinitást hajthat végre egy cookie használatával.

#### <a name="workaround"></a>Áthidaló megoldás

Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitást, külső vagy belső azure-terheléselosztót vagy más külső megoldást kell használnia.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Az alkalmazás cookie-alapú affinitást használ, de a kérelmek továbbra is pattognak a háttérkiszolgálók között

#### <a name="symptom"></a>Hibajelenség

Engedélyezte a cookie-alapú affinitás beállítást, amikor az Alkalmazásátjárót egy rövid név URL-cím használatával éri el az Internet Explorer programban, például: [http://website](http://website/) , a kérelem továbbra is pattog a háttérkiszolgálók között.

A probléma azonosításához kövesse az alábbi utasításokat:

1. Vegyünk egy webes hibakereső nyomkövetést az "Ügyfél", amely csatlakozik az alkalmazás mögött az application gateway(Mi használ Fiddler ebben a példában).
    **Tipp** Ha nem tudja, hogyan kell használni a Hegedűs, ellenőrizze a lehetőséget :**"Szeretném összegyűjteni a hálózati forgalmat, és elemezni, hogy a web hibakereső**" alján.

2. Ellenőrizze és elemezze a munkamenet naplókat, hogy megtudja, hogy az ügyfél által biztosított cookie-k rendelkeznek-e az ARRAffinity adataival. Ha nem találja az ARRAffinity adatait, például az "**ARRAffinity=** *ARRAffinityValue*" a cookie-készletben, az azt jelenti, hogy az ügyfél nem válaszol az ARRA cookie-val, amelyet az Application Gateway biztosít.
    Példa:

    ![hibaelhárítás-session-affinitás-problémák-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![hibaelhárítás-session-affinitás-problémák-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Az alkalmazás továbbra is megpróbálja beállítani a cookie-t minden kérésre, amíg nem kap választ.

#### <a name="cause"></a>Ok

A probléma oka az, hogy az Internet Explorer és más böngészők nem tárolják és nem használják a cookie-t rövid névvel rendelkező URL-címmel.

#### <a name="resolution"></a>Megoldás:

A probléma kijavításához az Application Gatewayhez FQDN-nel férjen hozzá. Például használja [http://website.com](https://website.com/) [http://appgw.website.com](http://appgw.website.com/) a vagyat.

## <a name="additional-logs-to-troubleshoot"></a>További hibák elhárítása

További naplókat gyűjthet és elemezhet a cookie-alapú munkamenet-affinitással kapcsolatos problémák elhárításához.

### <a name="analyze-application-gateway-logs"></a>Alkalmazásátjáró-naplók elemzése

Az Application Gateway-naplók összegyűjtéséhez kövesse az alábbi utasításokat:

Naplózás engedélyezése az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg az erőforrást, majd kattintson **a Diagnosztikai naplók parancsra.**

   Az Application Gateway esetében három napló érhető el: hozzáférési napló, teljesítménynapló, tűzfalnapló

2. Az adatgyűjtés megkezdéséhez kattintson **a Diagnosztika bekapcsolása gombra.**

   ![hibaelhárítás-session-affinitás-kérdések-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A **Diagnosztikai beállítások** panel biztosítja a diagnosztikai naplók beállításait. Ebben a példában a Log Analytics tárolja a naplókat. A munkaterület beállításához kattintson a **Konfigurálás** gombra a **Log Analytics** területen. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![hibaelhárítás-session-affinitás-problémák-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Erősítse meg a beállításokat, majd kattintson a **Mentés gombra.**

   ![hibaelhárítás-session-affinitás-kérdések-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Az Application Gateway hozzáférési naplóinak megtekintése és elemzése

1. Az Azure Portalon az Application Gateway erőforrásnézetben válassza **a Diagnosztikai naplók** lehetőséget a **FIGYELÉS** szakaszban.

   ![hibaelhárítás-session-affinitás-problémák-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. A jobb oldalon válassza az **"ApplicationGatewayAccessLog**" lehetőséget a Legördülő listában a **Napló kategóriák csoportban.**  

   ![hibaelhárítás-session-affinitás-kérdések-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Az Application Gateway Access Log listájában kattintson az elemezni és exportálni kívánt naplóra, majd exportálja a JSON-fájlt.

4. A 3.

5. Ellenőrizze a következő adatokat:

- **ClientIP**– Ez az ügyfél IP-címe a csatlakozó ügyféltől.
- **ClientPort** – Ez a kérelemhez csatlakozó ügyfél forrásportja.
- **RequestQuery** – Ez azt a célkiszolgálót jelzi, amelyen a kérés érkezik.
- **Kiszolgáló-routed**: a kérelem fogadásának háttérkészlet-példánya.
- **X-AzureApplicationGateway-LOG-ID**: A kérelemhez használt korrelációs azonosító. A háttérkiszolgálókon felmerülő forgalmi problémák elhárítására használható. Például: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **KISZOLGÁLÓ-ÁLLAPOT**: HTTP-válaszkód, amelyet az Application Gateway a háttérrendszertől kapott.

  ![hibaelhárítás-session-affinitás-kérdések-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Ha azt látja, hogy két elem ugyanabból az ügyfélip-címből és ügyfélportból érkezik, és a rendszer ugyanannak a háttérkiszolgálónak küldi őket, az azt jelenti, hogy az Alkalmazásátjáró megfelelően van konfigurálva.

Ha azt látja, hogy két elem ugyanabból az ügyfélip-címből és ügyfélportból érkezik, és a rendszer elküldi őket a különböző háttérkiszolgálóknak, az azt jelenti, hogy a kérelem a háttérkiszolgálók között pattog, válassza az "**Alkalmazás cookie-alapú affinitást használ, de a kérelmek továbbra is a háttérkiszolgálók között pattognak**" lehetőséget a hibaelhárításhoz.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>A HTTP- vagy HTTPS-forgalom rögzítéséhez és elemzéséhez használjon webes hibakeresőt

Az olyan webes hibakereső eszközök, mint a Fiddler, segíthetnek a webalkalmazások hibakeresésében az internet és a tesztszámítógépek közötti hálózati forgalom rögzítésével. Ezek az eszközök lehetővé teszik, hogy vizsgálja meg a bejövő és kimenő adatokat, mint a böngésző fogadja / elküldi őket. A Fiddler ebben a példában rendelkezik a HTTP-visszajátszási lehetőséggel, amely segíthet a webalkalmazásokkal kapcsolatos ügyféloldali problémák elhárításában, különösen a hitelesítési problémák esetén.

Használja az Ön által választott webes hibakeresőt. Ebben a példában a Fiddler-t fogjuk használni a http vagy https forgalom rögzítésére és elemzésére, kövesse az utasításokat:

1. Töltse le a Fiddler eszközt a. <https://www.telerik.com/download/fiddler>

    > [!NOTE]
    > Válassza a Fiddler4 lehetőséget, ha a rögzítő számítógépen .NET 4 van telepítve. Ellenkező esetben válassza a Fiddler2 lehetőséget.

2. Kattintson a jobb gombbal a telepítő végrehajtható fájljára, és futtassa rendszergazdaként a telepítéshez.

    ![hibaelhárítás-session-affinitás-kérdések-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Amikor megnyitja a Hegedűst, automatikusan meg kell kezdenie a forgalom rögzítését (figyelje meg a Rögzítés a bal alsó sarokban). Nyomja le az F12 billentyűt a forgalom rögzítésének elindításához vagy leállításához.

    ![hibaelhárítás-session-affinitás-kérdések-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Valószínűleg érdekelni fogja a visszafejtett HTTPS-forgalom, és engedélyezheti a HTTPS-visszafejtést az **Eszközök** > **hegedűbeállításai**kiválasztásával, és jelölje be a " **Decrypt HTTPS forgalom**" jelölőnégyzetet.

    ![hibaelhárítás-session-affinitás-kérdések-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. A korábbi, nem kapcsolódó munkameneteket a probléma reprodukálása előtt eltávolíthatja, ha az **X** (ikon) > **Az összes eltávolítása** az alábbi képernyőképre kattintva: 

    ![hibaelhárítás-session-affinitás-kérdések-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. A probléma sokszorosára való kivetése után mentse a fájlt véleményezésre a **Fájlmentése** > minden munkamenet**mentése** > **lehetőséget. . .** 

    ![hibaelhárítás-session-affinitás-kérdések-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Ellenőrizze és elemezze a munkamenetnaplókat, hogy megtudja, mi a probléma.

    Példák:

- **Példa A:** Megtalál egy munkamenet-naplót, amelyet a kérés az ügyféltől küld, és az alkalmazásátjáró nyilvános IP-címére kerül, kattintson erre a naplóra a részletek megtekintéséhez.  A jobb oldalon az alsó mezőben lévő adatok az Application Gateway által az ügyfélnek visszaadott adatok. Válassza a "RAW" lapot, és állapítsa meg, hogy az ügyfél**kap-e " Set-Cookie: ARRAffinity=** *ARRAffinityValue " értéket.* Ha nincs cookie, a munkamenet-affinitás nincs beállítva, vagy az Application Gateway nem alkalmazza vissza a cookie-t az ügyfélre.

   > [!NOTE]
   > Ez az ARRAffinity érték az a cookie-azonosító, amelyet az Application Gateway beállít az ügyfél számára egy adott háttérkiszolgálóra való elküldéshez.

   ![hibaelhárítás-session-affinitás-kérdések-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **B példa:** A következő munkamenetnapló, amelyet az előző követ, az az ügyfél, amely válaszol az Application Gateway-re, amely beállította az ARRAAFFINITY-t. Ha az ARRAffinity cookie-id megegyezik, a csomagot ugyanannak a háttérkiszolgálónak kell elküldeni, amelyet korábban használt. Ellenőrizze a következő néhány sornyi http kommunikáció, hogy az ügyfél ARRAffinity cookie változik.

   ![hibaelhárítás-session-affinitás-kérdések-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Ugyanazon a kommunikációs munkamenetesetén a cookie-nak nem szabad változnia. Jelölje be a jobb oldali felső jelölőnégyzetet, és válassza a "Cookie-k" fület, és ellenőrizze, hogy az ügyfél használja-e a cookie-t, és küldje vissza az Alkalmazásátjárónak. Ha nem, az ügyfél böngésző nem tartja, és használja a cookie-t a beszélgetések. Néha az ügyfél hazudhat.

 

## <a name="next-steps"></a>További lépések

Ha az előző lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/)
