<properties
    pageTitle="Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Azure"
    description="Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="awills"/>

# Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése

Miután telepítette a webalkalmazást bármely állomásra, webes teszteket állíthat be a rendelkezésre állásának és válaszkészségének megfigyeléséhez. A [Visual Studio Application Insights](app-insights-overview.md) rendszeres időközönként webes kéréseket küld világszerte különböző helyekről, és riasztja Önt, ha az alkalmazása lassan vagy egyáltalán nem válaszol.

![Példa webes tesztre](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Webes teszteket állíthat be bármely olyan HTTP- vagy HTTPS- végponthoz, amely a nyilvános internetről érhető el.

A webes teszteknek két típusa létezik:

* [URL-ping teszt](#set-up-a-url-ping-test): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](#multi-step-web-tests): ezt a Visual Studio Ultimate vagy a Visual Studio Enterprise segítségével hozhatja létre és feltöltheti a portálra.

Alkalmazás-erőforrásonként legfeljebb 10 webes tesztet hozhat létre.


## URL-ping teszt beállítása

### <a name="create"></a>1. Új erőforrást hoz létre?

Hagyja ki ezt a lépést, ha már [beállított egy Application Insights-erőforrást][start] ehhez az alkalmazáshoz, és ha ugyanezen a helyen szeretné megtekinteni a rendelkezésre állási adatokat.

Regisztráljon a [Microsoft Azure](http://azure.com)-ba, lépjen az [Azure Portalra](https://portal.azure.com), és hozzon létre egy Application Insights-erőforrást.

![Új > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Megnyílik az új erőforrás Áttekintés panele. Ezt bármikor megtalálhatja az [Azure Portalon](https://portal.azure.com) a **Tallózás** gombra kattintva.

### <a name="setup"></a>2. Webes teszt létrehozása

Az Application Insights-erőforrásban keresse meg a Rendelkezésre állás csempét. Kattintson rá az alkalmazás Webes tesztek paneljének megnyitásához, és adjon hozzá egy webes tesztet.

![Töltse ki legalább a webhelye URL-címét](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **Az URL-címnek** láthatónak kell lennie a nyilvános internetről. Tartalmazhat egy lekérdezési karakterláncot&#151;,így például használhatja az adatbázisát. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.
- **Függő kérelmek elemzése**: A rendszer kéri a lap képeit, szkriptjeit, stílusfájljait és más erőforrásait a teszt részeként. A teszt meghiúsul, ha nem tölthető le sikeresen az összes erőforrás a teljes teszt időtúllépése előtt.
- **Újrapróbálkozások engedélyezése**: Amikor a teszt meghiúsul, a rendszer rövid idő után újrapróbálja. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. (Ezt a beállítást javasoljuk. Átlagosan körülbelül a hibák 80%-a eltűnik az újrapróbálkozáskor.)
- **Teszt gyakorisága**: Beállítja, hogy milyen gyakran fut a teszt mindegyik teszthelyen. Öt perces gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.
- A **Teszthelyek** olyan helyek, ahonnan a kiszolgálóink webes kérelmeket küldenek az Ön URL-címére. Többet válasszon, hogy megkülönböztethesse webhelye problémáit a hálózati hibáktól. Legfeljebb 16 hely választható ki.

- **Sikeresség feltétele**:

    **Teszt időkorlátja:** Ha csökkenti az értéket, riasztást kap a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.

    **HTTP-válasz**: A visszaadott, sikert jelző állapotkód. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.

    **Tartalmi egyezés**: egy karakterlánc, például „Üdvözöljük!” Teszteljük, hogy minden válaszban előfordul-e. Egyszerű karakterláncnak kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell.


- Alapértelmezés szerint akkor kap **riasztásokat**, ha öt perc alatt három helyen fordulnak elő hibák. Ha egy helyen fordul elő a hiba, azt valószínűleg hálózati probléma okozza, és nem a hellyel van probléma. A küszöbérték érzékenységét állítani lehet, és azt is módosíthatja, hogy kinek küldje a rendszer az e-maileket.

    Be lehet állítani egy [webhookot](../azure-portal/insights-webhooks-alerts.md), amelyet a rendszer egy riasztás megjelenésekor hív meg. (Vegye figyelembe, hogy jelenleg a lekérdezési paraméterek nem továbbítódnak Tulajdonságokként.)

#### További URL-címek tesztelése

Adjon hozzá további teszteket. Például a kezdőlap tesztelése mellett egy keresés URL-címének tesztelésével ellenőrizni lehet, hogy az adatbázis működik-e.


### <a name="monitor"></a>3. Rendelkezésre állási jelentések megtekintése

1–2 perc múlva kattintson a **Frissítés** gombra a rendelkezésre állás/webes tesztek panelen. (Nem frissül automatikusan.)

![Az összegzés eredményei a kezdőpanelen](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Kattintson az összegző diagram bármely sávjára az időszak részletesebb megjelenítéséhez.

Ezek a diagramok az alkalmazás összes webes tesztjének eredményeit egyesítik.

#### A weblap összetevői

A rendszer a képek, stíluslapok, szkriptek és a tesztelt weblap egyéb statikus összetevőit a teszt részeként lekéri.  

A rögzített válaszidő az összes összetevő betöltéséhez szükséges idő.

Ha nem sikerül betölteni bármely összetevőt, a teszt sikertelenként lesz megjelölve.

## <a name="failures"></a>Ha hibákat lát...

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



## Többlépéses webes tesztek

Olyan forgatókönyveket is figyelhet, amelyek egy URL-címek sorozatából állnak. Ha például egy értékesítési webhelyet figyel, tesztelheti, hogy megfelelően működik-e a termékek kosárba helyezése.

Többlépéses teszt létrehozásához rögzíteni kell a forgatókönyvet a Visual Studióval, majd fel kell tölteni a felvételt az Application Insightsba. Az Application Insights időközönként visszajátssza a forgatókönyvet, és ellenőrzi a válaszokat.

Vegye figyelembe, hogy a tesztekben nem használhat kódolt függvényeket: a forgatókönyv lépéseinek szkriptekként kell szerepelniük a .webtest fájlban.

#### 1. Forgatókönyv rögzítése

A webes munkamenet rögzítéséhez használja a Visual Studio Enterprise vagy Ultimate verzióját. 

1. Hozzon létre egy webes teljesítményt tesztelő projektet.

    ![A Visual Studióban hozzon létre egy projektet a webes teljesítmény és a terheléstesztelés sablonjából.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Nyissa meg a .webtest fájlt, és kezdje meg a rögzítést.

    ![Nyissa meg a .webtest fájlt, és kattintson a Record (Rögzítés) gombra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Végezze el a felhasználói műveleteket, amelyeket a teszt során szimulálni kíván: nyissa meg a webhelyet, tegyen a kosárba egy terméket stb. Ezután állítsa le a tesztet.

    ![A webes teszt rögzítője az Internet Explorerben fut.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    A forgatókönyv ne legyen hosszú. Legfeljebb 100 lépésből állhat, és 2 percig tarthat.

4. Szerkessze a tesztet:
 - Adjon hozzá ellenőrzéseket a kapott szöveg- és válaszkódok ellenőrzéséhez.
 - Távolítson el minden felesleges interakciót. A képekhez, illetve a hirdető vagy nyomkövetési webhelyekhez tartozó függő kérelmeket is el lehet távolítani.

    Ne feledje, hogy csak a teszt szkriptjét szerkesztheti, tehát nem adhat hozzá egyéni kódot, és nem hívhat meg más webes teszteket. Ne szúrjon be hurkokat a tesztbe. Használhatja a normál webes teszt beépülő modulokat.

5. A működés ellenőrzéséhez futtassa a tesztet a Visual Studióban.

    A webes teszt futtatója megnyit egy webböngészőt, és megismétli a rögzített műveleteket. Győződjön meg arról, hogy a várakozásainak megfelelően működik.

    ![A Visual Studióban nyissa meg a .webtest fájlt, majd kattintson a Run (Futtatás) gombra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2. A webes teszt feltöltése az Application Insightsba

1. Az Application Insights portálon hozzon létre egy új webes tesztet.

    ![A webes teszt panelen válassza a Hozzáadás elemet.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Válassza ki a többlépéses tesztet, majd töltse fel a .webtest fájlt.

    ![Válassza ki a többlépéses webtesztet.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Válassza ki a tesztelési helyeket, a tesztelés gyakoriságát és riasztási paramétereit ugyanúgy, mint a ping teszteknél.

Tekintse meg a teszt eredményeit és a hibákat ugyanúgy, mint az egyetlen URL-címre kiterjedő teszteknél.

A hibák gyakori oka, hogy a teszt túl hosszú ideig fut. A teszt nem tarthat tovább két percnél.

Ne feledje, hogy az oldal összes erőforrásának megfelelően kell betöltődnie ahhoz, hogy a teszt sikeres legyen, beleértve a szkripteket, stíluslapokat, képeket stb.

Vegye figyelembe, hogy a .webtest fájlnak a teljes webes tesztet tartalmaznia kell: a tesztben nem használhat kódolt függvényeket.


### Idő és véletlenszerű számok beiktatása a többlépéses tesztbe

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

## A bejelentkezés kezelése

Ha a felhasználók bejelentkeznek az alkalmazásába, számos lehetősége van a bejelentkezés szimulálására a bejelentkezés után megjelenő lapok teszteléséhez. A használt megközelítés az alkalmazás által kínált biztonság típusától függ.

Minden esetben ajánlott létrehozni egy fiókot az alkalmazásában tesztelési célra. Ha lehetséges, korlátozza az engedélyt ezen a tesztfiókon, így elkerülheti, hogy a webes tesztek hatással legyenek a tényleges felhasználókra.

### Egyszerű felhasználónév és jelszó

Rögzítsen egy webes tesztet a szokásos módon. Először törölje a cookie-kat.

### SAML-hitelesítés

Használhatja a webes tesztek számára elérhető SAML beépülő modult.

### Titkos ügyfélkulcs

Ha az alkalmazás bejelentkezési módja titkos ügyfélkódot igényel, használja azt a módot. Az Azure Active Directory (AAD) mint egy példa olyan szolgáltatásra, amely titkos ügyfélkulccsal történő bejelentkezést biztosít. Az AAD-ben a titkos ügyfélkulcs az alkalmazáskulcs. 

Itt egy alkalmazáskulcsot használó Azure-webalkalmazás webes tesztelésre találhat példát:

![Titkos ügyfélkulcs-minta](./media/app-insights-monitor-web-app-availability/110.png)

1. Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
2. Nyerje ki a tulajdonosi jogkivonatot a válaszból.
3. Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.

Győződjön meg róla, hogy a webes teszt egy tényleges ügyfél – saját alkalmazása van az AAD-ben –, valamint használja a clientId + appkey értékét. A teszt alatt álló szolgáltatása is rendelkezik saját alkalmazással az AAD-ben: az alkalmazás appID URI-ja a webes teszt „resource” mezőjében látható. 

### Nyílt hitelesítés

Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása. 

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

 * Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához. 
 * Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
 * A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak. 
 * Rögzítsen egy webes tesztet a Visual Studióval. 
 * Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben.
 (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)


## <a name="edit"></a> Teszt szerkesztése vagy letiltása

Nyisson meg egy egyéni tesztet annak szerkesztéséhez vagy letiltásához.

![Webes teszt szerkesztése vagy letiltása](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Érdemes letiltani a webes teszteket, miközben karbantartást végez a szolgáltatáson.

## Teljesítménytesztek

Terheléstesztelést futtathat a webhelyén. Csakúgy, mint a rendelkezésre állási teszt esetében, egyszerű vagy több lépésből álló kéréseket küldhet a világ minden táján található helyekről. A rendelkezésre állási teszttől eltérően sok kérés lesz elküldve, több párhuzamos felhasználót szimulálva.

Az Áttekintés panelről nyissa meg a **Beállítások**, **Teljesítménytesztek** elemet. Teszt létrehozásakor egy kérést kap arra, hogy csatlakozzon a Visual Studio Team Services-fiókhoz, vagy hozzon létre egyet. 

A teszt befejezése után a válaszidők és a sikerességi arány jelenik meg.


## Automatizálás

* [Használjon PowerShell-szkripteket a webes teszt automatikus beállításához](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/). 
* Állítson be egy [webhookot](../azure-portal/insights-webhooks-alerts.md), amelyet a rendszer riasztás esetén hív meg.

## Kérdései vannak? Problémákat tapasztal?

* *Meghívhatok egy kódot a webes tesztből?*

    Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.

* *Támogatott a HTTPS?*

    A TLS 1.1 és a TLS 1.2 támogatott.

* *Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?*

    A két kifejezés ugyanazt jelenti.

* *Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.*

    Konfigurálja úgy a tűzfalat, hogy engedélyezi a [webes tesztügynökök IP-címeiről](app-insights-ip-addresses.md#availability) érkező kéréseket.

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


## <a name="video"></a>Videó

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Következő lépések

[Diagnosztikai naplók keresése][diagnostic]

[Hibaelhárítás][qna]

[A webes tesztügynökök IP-címei](app-insights-ip-addresses.md)


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=sep16_HO1-->


