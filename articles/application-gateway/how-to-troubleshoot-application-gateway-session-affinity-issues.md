---
title: Az Azure Application Gateway munkamenet-affinitási problémák hibaelhárítása
description: Ez a cikk információt nyújt az Azure Application Gateway a munkamenet-affinitási problémák elhárítása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: d98834abaf925cdabd312869615a60f64049826d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808657"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Az Azure Application Gateway munkamenet-affinitási problémák elhárítása

Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a munkamenet-affinitási problémák az Azure Application Gateway szolgáltatással.

## <a name="overview"></a>Áttekintés

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="possible-problem-causes"></a>A probléma lehetséges oka

A cookie-alapú munkamenet-affinitás fenntartása a probléma a következő fő okok miatt fordulhat elő:

- "Cookie-alapú affinitás" beállítás nincs engedélyezve
- Az alkalmazás nem tudja kezelni a cookie-alapú affinitás
- Az alkalmazás használ cookie-alapú affinitás, de továbbra is a háttérkiszolgálók közötti Pattogás kérelmek

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Ellenőrizze, hogy engedélyezve van-e a "Cookie-alapú affinitás" beállítás

Egyes esetekben a munkamenet-affinitási problémák fordulhatnak elő, ha elfelejti a "Cookie-alapú affinitás" beállításnak az engedélyezéséhez. Annak megállapításához, hogy engedélyezte-e a "Cookie-alapú affinitás" beállítás a HTTP-beállítások lapon, az Azure Portalon, kövesse az utasításokat:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

2. Az a **bal oldali navigációs** ablaktáblán kattintson a **összes erőforrás**. Kattintson az alkalmazás átjáró neve, az összes erőforrás panelen. Ha az előfizetést, amely a kijelölt már több erőforrás szerepel, az átjáró alkalmazásnév is megadhatja a **Szűrés név alapján...** mezőbe.

3. Válassza ki **HTTP-beállítások** lapjára **beállítások**.

   ![troubleshoot-session-affinity-issues-1](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-1.png)

4. Kattintson a **appGatewayBackendHttpSettings** meg, hogy ellenőrizze a jobb oldalon **engedélyezve** Cookie-alapú affinitás.

   ![troubleshoot-session-affinity-issues-2](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-2.jpg)



Értékét is ellenőrizheti a "**CookieBasedAffinity**" értékre van állítva *engedélyezve*alatt "**backendHttpSettingsCollection**" a következő módszerek egyikével:

- Futtatás [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings?view=azurermps-4.1.0) a PowerShellben
- Nézze át a JSON-fájlt az Azure Resource Manager-sablon használatával

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Az alkalmazás nem tudja kezelni a cookie-alapú affinitás

#### <a name="cause"></a>Ok

Az application gateway csak hajthat végre a munkamenet-alapú affinitás a cookie-k használatával.

#### <a name="workaround"></a>Áthidaló megoldás

Az alkalmazás nem tudja kezelni a cookie-alapú affinitás, ha egy külső vagy belső azure load balancert vagy más külső megoldást kell használnia.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Az alkalmazás használ cookie-alapú affinitás, de továbbra is a háttérkiszolgálók közötti Pattogás kérelmek

#### <a name="symptom"></a>Jelenség

Engedélyezte a Cookie-alapú affinitás beállítást, amikor egy rövid nevet URL-címet az Internet Explorerben például segítségével fér hozzá az Application Gateway: [ http://website ](http://website/) , a kérés továbbra is van Pattogás háttér-kiszolgálók között.

A probléma azonosításához, kövesse az utasításokat:

1. Végezze el a webes hibakeresőt nyomkövetési nyomkövetési az "ügyfél", amelyhez csatlakozik, hogy az alkalmazás az alkalmazás Gateway(We are using Fiddler in this example) mögött.
    **Tipp** Ha nem ismeri a Fiddler használata, jelölje be a beállítást "**szeretnék hálózati forgalom gyűjtése és elemezheti webes hibakereső**" alján.

2. Ellenőrizze, és a munkamenet-naplók, annak megállapításához, hogy a cookie-kat, az ügyfél által biztosított rendelkezik-e ARRAffinity részleteinek elemzése. Ha nem találja meg a ARRAffinity részleteit, például a "**ARRAffinity =** *ARRAffinityValue*", amely azt jelenti, hogy az ügyfél nem válaszol a elrendezése cookie-k által biztosított cookie-k meghatározott belül a Application Gateway.
    Példa:

    ![troubleshoot-session-affinity-issues-3](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-4.png)

Az alkalmazás továbbra is a cookie-t beállítani minden kérelemnél, amíg nem kap választ.

#### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, mert az Internet Explorer és a más böngészőkkel nem tárolja és a cookie-t használja egy rövid nevet URL-cím.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához, hozzá kell férnie az Application Gateway teljes tartománynév használatával. Például [ http://website.com ](http://website.com/) vagy [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>További naplók hibaelhárítása

További gyűjtését, és elemezheti a problémák kapcsolódó cookie-alapú munkamenet-affinitás hibaelhárítása

### <a name="analyze-application-gateway-logs"></a>Az Application Gateway-naplók elemzése

Az Application Gateway-naplók gyűjtését, kövesse az utasításokat:

Naplózás engedélyezése az Azure Portalon

1. Az a [az Azure portal](https://portal.azure.com/), az erőforrás található, és kattintson a **diagnosztikai naplók**.

   Az Application Gateway három naplók érhetők el: Hozzáférési napló, a teljesítménynaplóban, a tűzfal naplója

2. Adatok gyűjtésére indításához kattintson **diagnosztika bekapcsolása**.

   ![troubleshoot-session-affinity-issues-5](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-5.png)

3. A **diagnosztikai beállítások** panel biztosít a diagnosztikai naplók beállításait. Ebben a példában a Log Analytics tárolja a naplókat. Kattintson a **konfigurálása** alatt **Log Analytics** beállításához a munkaterületen. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![troubleshoot-session-affinity-issues-6](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-6.png)

4. Hagyja jóvá a beállításokat, és kattintson a **mentése**.

   ![troubleshoot-session-affinity-issues-7](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Az Application Gateway hozzáférési naplók megtekintése és elemzése

1. Az Application Gateway erőforrás nézet alatt az Azure Portalon, válassza ki a **diagnosztikai naplók** a a **figyelés** szakaszban.

   ![troubleshoot-session-affinity-issues-8](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-8.png)

2. A jobb oldalon válassza ki a "**ApplicationGatewayAccessLog**" alatt a legördülő listában **kategóriába jelentkezzen.**  

   ![troubleshoot-session-affinity-issues-9](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-9.png)

3. Az Application Gateway – hozzáférési napló listában kattintson a napló elemzése és exportálni szeretne, és majd exportálja a JSON-fájlt.

4. Konvertálja a JSON-fájlt a 3. lépésben CSV-fájlba exportált, és megtekintheti őket az Excel, a Power BI vagy más adatvizualizáció eszköz.

5. Ellenőrizze a következő adatokat:

- **Ügyfélip**– Ez az, hogy a kapcsolódó ügyfelek az ügyfél IP-címét.
- **ClientPort** – Ez a forrásport, a kapcsolódó ügyfelek a kéréshez.
- **RequestQuery** – Ez azt jelenti, hogy a kérelem érkezik a célkiszolgálón.
- **Kiszolgáló útválasztásos**: Háttérkészlet-példány, hogy a kérés érkezik.
- **X-AzureApplicationGateway-LOG-ID**: Korrelációs azonosító kérelmében. Hibaelhárítás forgalmat a háttér-kiszolgálókon is használható. Példa: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **A KISZOLGÁLÓÁLLAPOT**: HTTP-válaszkód, az Application Gateway a háttérrendszer cégtől kapott.

  ![troubleshoot-session-affinity-issues-11](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-11.png)

Ha a két elem érkező Ügyfélip és az ügyfél-portot, és a rendszer elküldte őket az ugyanazt a háttér-kiszolgálót, ez azt jelenti, az Application Gateway megfelelően konfigurálva.

Ha látható a két elem érkező Ügyfélip és az ügyfél-portot, és küldi el a különböző háttér-kiszolgálókon, azt jelenti, hogy a kérelem háttérbeli kiszolgálók között, válassza ki a Pattogás "**alkalmazás használ cookie-alapú affinitás, de a kérelmek továbbra is a háttérkiszolgálók közötti Pattogás**"alul a hibaelhárítás.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Webes hibakereső lehet rögzíteni és elemezni a HTTP vagy HTTPS traffics használata

Webalkalmazás-hibakeresési eszközeivel, mint például a Fiddlert, segíthet a webes alkalmazások hibakeresése az internetes és a tesztgépek közötti hálózati forgalom rögzítésével. Ezek az eszközök lehetővé teszi a bejövő és kimenő adatok vizsgálata, a böngésző fogad vagy küld őket. A fiddler, ebben a példában, amely segíthet a webes alkalmazások, különösen a probléma típusú hitelesítés ügyféloldali hibáinak elhárítása HTTP visszajátszását lehetősége van.

A webes hibakeresővel tetszőleges. Ebben a példában használjuk a Fiddler rögzíteni és elemezni a http vagy https traffics, kövesse az utasításokat:

1. Töltse le a Fiddler eszközéhez <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Válassza ki a Fiddler4, ha rögzítését van-e telepítve a .NET-4. Ellenkező esetben válasszon Fiddler2.

2. Kattintson a jobb gombbal a telepítés végrehajtható fájlt, és a Futtatás rendszergazdaként telepítéséhez.

            ![troubleshoot-session-affinity-issues-12](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-12.png)

3. Amikor megnyitja a Fiddler, azt automatikusan el kell (figyelje meg a bal alsó sarokban lévő rögzítése) forgalom rögzítése. Nyomja le az F12 billentyűt a indítása vagy leállítása forgalom rögzítése.

        ![troubleshoot-session-affinity-issues-13](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-13.png)

4. Nagy valószínűséggel fogja visszafejtett HTTPS-forgalom iránt, és kiválasztásával engedélyezheti a HTTPS-visszafejtési **eszközök** > **Fiddler beállítások**, és jelölje be " **visszafejtése HTTPS-forgalom**".

        ![troubleshoot-session-affinity-issues-14](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-14.png)

5. A korábbi független munkamenetekben előtt a probléma lejátszás gombra kattintva eltávolíthatja **X** (icon) > **távolítsa el az összes** végrehajtásával képernyőképet van: 

        ![troubleshoot-session-affinity-issues-15](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-15.png)

6. Ha a probléma reprodukálása, mentse a fájlt, tekintse át a kiválasztásával **fájl** > **mentése** > **minden munkamenet...** . 

        ![troubleshoot-session-affinity-issues-16](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-16.png)

7. Ellenőrizze, és elemezheti a munkamenet-naplókat a probléma meghatározásához.

    Példák:

- **Példa a válasz:** Keresse meg a munkamenetnapló, amely a kérés elküldésekor az ügyfél, és az Application Gateway nyilvános IP-címet kerül, ez a napló részleteinek megtekintéséhez kattintson.  A jobb oldalon az alsó listában lévő adatok el, mi az Application Gateway adja vissza az ügyfélnek. Válassza a "RAW" lapot, és határozza meg, hogy az ügyfél fogad egy "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*. " Ha nem cookie-k, nincs beállítva a munkamenet-affinitást, vagy az Application Gateway nem alkalmazza a cookie-t az ügyfélnek.

   > [!NOTE]
   > Ez ARRAffinity értéke a cookie-azonosítóhoz, az Application Gateway az ügyfél egy adott háttérkiszolgálón küldendő állítja.

    ![troubleshoot-session-affinity-issues-17](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-17.png)

- **Példa a "b"** A következő munkamenet-naplózás az egyik, az ügyfél válaszol vissza az Application Gatewayhez, amelyet a ARRAAFFINITY meg az előző követ. Ha megfelel a ARRAffinity cookie-azonosító, a csomagot küldeni kell ugyanazon háttér-kiszolgálóra, amelyet korábban használt. Ellenőrizze a következő több sornyi lásd: e megváltoztatja az ügyfél ARRAffinity cookie-k http-kommunikációt.

    ![troubleshoot-session-affinity-issues-18](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Az azonos kommunikációs munkamenet cookie-t kell megváltoztatni. Ellenőrizze a felső mezőbe a jobb oldalon, és válassza a "Cookie-k" lapján megtekintheti, hogy az ügyfél a cookie-k használatával, és vissza az Application Gateway felé. Ha nem, akkor az ügyfél böngészője nem tartja és beszélgetés a cookie-k használatával. Az ügyfél egyes esetekben előfordulhat, hogy közé eshet.

 

## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem a probléma megoldásához nyissa meg a [támogatási jegyet](https://azure.microsoft.com/support/options/).