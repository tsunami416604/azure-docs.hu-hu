---
title: "Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs"
description: "Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2d36bc4f9305590e7cc835bb813daf193d071fd1
ms.openlocfilehash: 455d260248c5bcdb8f597484a98fec9320c81d19


---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése
Miután telepítette a webalkalmazást vagy a weboldalt bármely kiszolgálóra, webes teszteket állíthat be az alkalmazás rendelkezésre állásának és válaszkészségének megfigyeléséhez. Az [Azure Application Insights](app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Riasztást jelenít meg, ha az alkalmazás nem válaszol, vagy lassan válaszol.

![Példa webes tesztre](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Webes teszteket állíthat be bármely olyan HTTP- vagy HTTPS- végponthoz, amely a nyilvános internetről érhető el. Nem kell semmit hozzáadnia a tesztelt webhelyhez. Még csak arra sincs szükség, hogy a saját webhelye legyen: tesztelhet egy olyan REST API-t is, amelyet használni szokott.

A webes teszteknek két típusa létezik:

* [URL-ping teszt](#create): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](#multi-step-web-tests): ezt a Visual Studio Ultimate vagy a Visual Studio Enterprise segítségével hozhatja létre és feltöltheti a portálra.

Alkalmazás-erőforrásonként legfeljebb 10 webes tesztet hozhat létre.

## <a name="a-namecreatea1-create-a-resource-for-your-test-reports"></a><a name="create"></a>1. Erőforrás létrehozása tesztjelentésekhez
Hagyja ki ezt a lépést, ha már [beállított egy Application Insights-erőforrást][start] ehhez az alkalmazáshoz, és ha ugyanezen a helyen szeretné megtekinteni a rendelkezésre állási jelentéseket.

Regisztráljon a [Microsoft Azure](http://azure.com)-ba, lépjen az [Azure Portalra](https://portal.azure.com), és hozzon létre egy Application Insights-erőforrást.

![Új > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Az új erőforrás Áttekintés paneljének megnyitásához kattintson az **Összes erőforrás** lehetőségre.

## <a name="a-namesetupa2-create-a-url-ping-test"></a><a name="setup"></a>2. URL-ping teszt létrehozása
Az Application Insights-erőforrásban keresse meg a Rendelkezésre állás csempét. Kattintson rá az alkalmazás Webes tesztek paneljének megnyitásához, és adjon hozzá egy webes tesztet.

![Töltse ki legalább a webhelye URL-címét](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **Az URL-címnek** láthatónak kell lennie a nyilvános internetről. Tartalmazhat egy lekérdezési karakterláncot&#151;,így például használhatja az adatbázisát. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.
* **Függő kérelmek elemzése**: A rendszer lekéri a lap képeit, szkriptjeit, stílusfájljait és más erőforrásait a teszt részeként, és a rögzített válaszidő magába foglalja ezeket az időket. A teszt meghiúsul, ha nem tölthető le sikeresen az összes erőforrás a teljes teszt időtúllépése előtt.
* **Újrapróbálkozások engedélyezése**: Amikor a teszt meghiúsul, a rendszer rövid idő után újrapróbálja. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. (Ezt a beállítást javasoljuk. Átlagosan körülbelül a hibák 80%-a eltűnik az újrapróbálkozáskor.)
* **Teszt gyakorisága**: Beállítja, hogy milyen gyakran fut a teszt mindegyik teszthelyen. Öt perces gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.
* A **Teszthelyek** olyan helyek, ahonnan a kiszolgálóink webes kérelmeket küldenek az Ön URL-címére. Többet válasszon, hogy megkülönböztethesse webhelye problémáit a hálózati hibáktól. Legfeljebb 16 hely választható ki.
* **Sikeresség feltétele**:

    **Teszt időkorlátja:** Ha csökkenti az értéket, riasztást kap a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.

    **HTTP-válasz**: A visszaadott, sikert jelző állapotkód. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.

    **Tartalmi egyezés**: egy karakterlánc, például „Üdvözöljük!” Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű karakterláncnak kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell.
* Alapértelmezés szerint akkor kap **riasztásokat**, ha öt perc alatt három helyen fordulnak elő hibák. Ha egy helyen fordul elő a hiba, azt valószínűleg hálózati probléma okozza, és nem a hellyel van probléma. A küszöbérték érzékenységét állítani lehet, és azt is módosíthatja, hogy kinek küldje a rendszer az e-maileket.

    Be lehet állítani egy [webhookot](../monitoring-and-diagnostics/insights-webhooks-alerts.md), amelyet a rendszer egy riasztás megjelenésekor hív meg. (Vegye figyelembe, hogy jelenleg a lekérdezési paraméterek nem továbbítódnak Tulajdonságokként.)

### <a name="test-more-urls"></a>További URL-címek tesztelése
Adjon hozzá további teszteket. Például a kezdőlap tesztelése mellett egy keresés URL-címének tesztelésével ellenőrizni lehet, hogy az adatbázis működik-e.

## <a name="a-namemonitora3-see-your-web-test-results"></a><a name="monitor"></a>3. A webes teszt eredményeinek megtekintése
Néhány perc elteltével az eredmények a Webes teszt panelben láthatóak.

![Az összegzés eredményei a kezdőpanelen](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Kattintson az összegző diagram bármely sávjára az időszak részletesebb megjelenítéséhez.

Ezek a diagramok az alkalmazás összes webes tesztjének eredményeit egyesítik.

## <a name="a-namefailuresaif-you-see-failures"></a><a name="failures"></a>Ha hibákat lát
Kattintson egy piros pontra.

![Kattintson egy piros pontra](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

Vagy görgessen le, és kattintson egy tesztre, amelynél 100% alatti a sikeresség mértéke.

![Kattintson egy adott webes tesztre](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Megnyílnak a teszt eredményei.

![Kattintson egy adott webes tesztre](./media/app-insights-monitor-web-app-availability/16-1test.png)

A teszt több helyről fut&#151;válasszon ki egyet, ahol 100% alattiak az eredmények.

![Kattintson egy adott webes tesztre](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)

Görgessen le a **Sikertelen tesztek** részhez, és válasszon ki egy eredményt.

Kattintson az eredményre a portálon való kiértékeléséhez, és tekintse meg a sikertelenség okát.

![A webes teszt futtatásának eredménye](./media/app-insights-monitor-web-app-availability/18-availDetails.png)

Másik lehetőségként letöltheti az eredményfájlt, és megvizsgálhatja a Visual Studióban.

*Úgy tűnik, hogy rendben van, mégis sikertelenként lett jelentve?* Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha ezek közül bármelyik hibás, a teszt sikertelenként lesz jelentve, még akkor is, ha a fő HTML-oldal megfelelően töltődik be.

### <a name="open-the-server-request-and-exceptions"></a>Kiszolgálókérések és kivételek megnyitása

Egy adott teszt részletes tulajdonságaiból megnyithatja a kéréseket és egyéb eseményeket, például a kivételeket tartalmazó kiszolgálóoldali jelentést.

![A webes teszt futtatásának eredménye](./media/app-insights-monitor-web-app-availability/web-test-linked-to-server-telemetry.png)

Ha nem lát kapcsolódó elemeket, elképzelhető, hogy [mintavételezés](app-insights-sampling.md) van folyamatban.

## <a name="multi-step-web-tests"></a>Többlépéses webes tesztek
Olyan forgatókönyveket is figyelhet, amelyek egy URL-címek sorozatából állnak. Ha például egy értékesítési webhelyet figyel, tesztelheti, hogy megfelelően működik-e a termékek kosárba helyezése.

> [!NOTE] 
> A többlépéses webes tesztek díjkötelesek. [Díjszabási séma](http://azure.microsoft.com/pricing/details/application-insights/)
> 

Többlépéses teszt létrehozásához rögzíteni kell a forgatókönyvet a Visual Studióval, majd fel kell tölteni a felvételt az Application Insightsba. Az Application Insights időközönként visszajátssza a forgatókönyvet, és ellenőrzi a válaszokat.

Vegye figyelembe, hogy a tesztekben nem használhat kódolt függvényeket: a forgatókönyv lépéseinek szkriptekként kell szerepelniük a .webtest fájlban.

#### <a name="1-record-a-scenario"></a>1. Forgatókönyv rögzítése
A webes munkamenet rögzítéséhez használja a Visual Studio Enterprise vagy Ultimate verzióját.

1. Hozzon létre egy webes teljesítményt tesztelő projektet.

    ![A Visual Studióban hozzon létre egy projektet a webes teljesítmény és a terheléstesztelés sablonjából.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Nyissa meg a .webtest fájlt, és kezdje meg a rögzítést.

    ![Nyissa meg a .webtest fájlt, és kattintson a Record (Rögzítés) gombra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Végezze el a felhasználói műveleteket, amelyeket a teszt során szimulálni kíván: nyissa meg a webhelyet, tegyen a kosárba egy terméket stb. Ezután állítsa le a tesztet.

    ![A webes teszt rögzítője az Internet Explorerben fut.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    A forgatókönyv ne legyen hosszú. Legfeljebb 100 lépésből állhat, és 2 percig tarthat.
4. Szerkessze a tesztet:

   * Adjon hozzá ellenőrzéseket a kapott szöveg- és válaszkódok ellenőrzéséhez.
   * Távolítson el minden felesleges interakciót. A képekhez, illetve a hirdető vagy nyomkövetési webhelyekhez tartozó függő kérelmeket is el lehet távolítani.

     Ne feledje, hogy csak a teszt szkriptjét szerkesztheti, tehát nem adhat hozzá egyéni kódot, és nem hívhat meg más webes teszteket. Ne szúrjon be hurkokat a tesztbe. Használhatja a normál webes teszt beépülő modulokat.
5. A működés ellenőrzéséhez futtassa a tesztet a Visual Studióban.

    A webes teszt futtatója megnyit egy webböngészőt, és megismétli a rögzített műveleteket. Győződjön meg arról, hogy a várakozásainak megfelelően működik.

    ![A Visual Studióban nyissa meg a .webtest fájlt, majd kattintson a Run (Futtatás) gombra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. A webes teszt feltöltése az Application Insightsba
1. Az Application Insights portálon hozzon létre egy új webes tesztet.

    ![A webes teszt panelen válassza a Hozzáadás elemet.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Válassza ki a többlépéses tesztet, majd töltse fel a .webtest fájlt.

    ![Válassza ki a többlépéses webtesztet.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Válassza ki a tesztelési helyeket, a tesztelés gyakoriságát és riasztási paramétereit ugyanúgy, mint a ping teszteknél.

Tekintse meg a teszt eredményeit és a hibákat ugyanúgy, mint az egyetlen URL-címre kiterjedő teszteknél.

A hibák gyakori oka, hogy a teszt túl hosszú ideig fut. A teszt nem tarthat tovább két percnél.

Ne feledje, hogy az oldal összes erőforrásának megfelelően kell betöltődnie ahhoz, hogy a teszt sikeres legyen, beleértve a szkripteket, stíluslapokat, képeket stb.

Vegye figyelembe, hogy a .webtest fájlnak a teljes webes tesztet tartalmaznia kell: a tesztben nem használhat kódolt függvényeket.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Idő és véletlenszerű számok beiktatása a többlépéses tesztbe
Tegyük fel, hogy egy olyan eszközt tesztel, amely időfüggő adatokat fogad, például részvényeket egy külső adatcsatornától. A webes teszt rögzítésekor meghatározott időpontokat kell használni, de ezeket a teszt StartTime és EndTime paramétereiként kell beállítani.

![Egy webes teszt paraméterekkel.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

A teszt futtatásakor az EndTime paraméternek mindig az aktuális időnek kell lennie, a StartTime paraméternek pedig a 15 perccel korábbi időnek.

A webes teszt beépülő modulok lehetővé teszik az idő paraméterezését.

1. Adjon hozzá egy webes teszt beépülő modult minden kívánt változóparaméter-értékhez. A webes teszt eszköztárában válassza ki a **Webes teszt beépülő modul hozzáadása** elemet.

    ![Válassza ki a Webes teszt beépülő modul hozzáadása elemet, majd válasszon egy típust.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Ebben a példában a Dátum és idő beépülő modul két példányát használjuk. Az egyik példány a „15 perccel ezelőtt” a másik pedig a „most” paraméterértékhez tartozik.
2. Nyissa meg a beépülő modulok tulajdonságait. Adjon meg egy nevet, és állítsa be úgy, hogy az aktuális időt használja. Az egyiknél állítsa be a következőt: Add Minutes = -15.

    ![Beállított Name, Use Current Time, és Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. A webes teszt paramétereiben a beépülő modulok nevére a {{plug-in name}} segítségével hivatkozzon.

    ![A tesztparaméterében használja a {{plug-in name}} elemet.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ezután töltse fel a tesztet a portálra. Ez a teszt minden futtatásánál a dinamikus értékeket használja.

## <a name="dealing-with-sign-in"></a>A bejelentkezés kezelése
Ha a felhasználók bejelentkeznek az alkalmazásába, számos lehetősége van a bejelentkezés szimulálására a bejelentkezés után megjelenő lapok teszteléséhez. A használt megközelítés az alkalmazás által kínált biztonság típusától függ.

Minden esetben ajánlott létrehozni egy fiókot az alkalmazásában tesztelési célra. Ha lehetséges, korlátozza az engedélyt ezen a tesztfiókon, így elkerülheti, hogy a webes tesztek hatással legyenek a tényleges felhasználókra.

### <a name="simple-username-and-password"></a>Egyszerű felhasználónév és jelszó
Rögzítsen egy webes tesztet a szokásos módon. Először törölje a cookie-kat.

### <a name="saml-authentication"></a>SAML-hitelesítés
Használhatja a webes tesztek számára elérhető SAML beépülő modult.

### <a name="client-secret"></a>Titkos ügyfélkulcs
Ha az alkalmazás bejelentkezési módja titkos ügyfélkódot igényel, használja azt a módot. Az Azure Active Directory (AAD) mint egy példa olyan szolgáltatásra, amely titkos ügyfélkulccsal történő bejelentkezést biztosít. Az AAD-ben a titkos ügyfélkulcs az alkalmazáskulcs.

Itt egy alkalmazáskulcsot használó Azure-webalkalmazás webes tesztelésre találhat példát:

![Titkos ügyfélkulcs-minta](./media/app-insights-monitor-web-app-availability/110.png)

1. Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
2. Nyerje ki a tulajdonosi jogkivonatot a válaszból.
3. Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.

Győződjön meg róla, hogy a webes teszt egy tényleges ügyfél – saját alkalmazása van az AAD-ben –, valamint használja a clientId + appkey értékét. A teszt alatt álló szolgáltatása is rendelkezik saját alkalmazással az AAD-ben: az alkalmazás appID URI-ja a webes teszt „resource” mezőjében látható.

### <a name="open-authentication"></a>Nyílt hitelesítés
Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása.

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

* Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához.
* Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
* A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak.
* Rögzítsen egy webes tesztet a Visual Studióval.
* Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben.
  (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)

## <a name="a-nameedita-edit-or-disable-a-test"></a><a name="edit"></a> Teszt szerkesztése vagy letiltása
Nyisson meg egy egyéni tesztet annak szerkesztéséhez vagy letiltásához.

![Webes teszt szerkesztése vagy letiltása](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Érdemes letiltani a webes teszteket, miközben karbantartást végez a szolgáltatáson.

## <a name="performance-tests"></a>Teljesítménytesztek
Terheléstesztelést futtathat a webhelyén. Csakúgy, mint a rendelkezésre állási teszt esetében, egyszerű vagy több lépésből álló kéréseket küldhet a világ minden táján található helyekről. A rendelkezésre állási teszttől eltérően sok kérés lesz elküldve, több párhuzamos felhasználót szimulálva.

Az Áttekintés panelről nyissa meg a **Beállítások**, **Teljesítménytesztek** elemet. Teszt létrehozásakor egy kérést kap arra, hogy csatlakozzon a Visual Studio Team Services-fiókhoz, vagy hozzon létre egyet.

A teszt befejezése után a válaszidők és a sikerességi arány jelenik meg.

## <a name="automation"></a>Automatizálás
* [Használjon PowerShell-szkripteket a webes teszt automatikus beállításához](app-insights-powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../monitoring-and-diagnostics/insights-webhooks-alerts.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
* *Meghívhatok egy kódot a webes tesztből?*

    Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.
* *Támogatott a HTTPS?*

    A TLS 1.1 és a TLS 1.2 támogatott.
* *Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?*

    A két kifejezés ugyanazt jelenti.
* *Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.*

    Konfigurálja úgy a tűzfalat, hogy engedélyezi a [webes tesztügynökök IP-címeiről](app-insights-ip-addresses.md) érkező kéréseket.
* *A többlépéses teszt feltöltése sikertelen*

    A méretkorlát: 300 KB.

    A hurkok nem támogatottak.

    A más webes tesztekre mutató hivatkozások nem támogatottak.

    Az adatforrások nem támogatottak.
* *A többlépéses teszt nem fejeződik be*

    Egy teszt legfeljebb 100 kérelemből állhat.

    A teszt leáll, ha két percnél tovább fut.
* *Hogyan futtathatok tesztet ügyféltanúsítványokkal?*

    Sajnos azt nem támogatjuk.

## <a name="a-namevideoavideo"></a><a name="video"></a>Videó
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
>
>

## <a name="a-namenextanext-steps"></a><a name="next"></a>Következő lépések
[Diagnosztikai naplók keresése][diagnostic]

[Hibaelhárítás][qna]

[A webes tesztügynökök IP-címei](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=Jan17_HO1-->


