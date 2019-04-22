---
title: Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 9f48303396d1ecd03fdffd2c6ab1e0c122615a21
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005740"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése
Miután telepítette a webappot vagy a webhelyet bármely kiszolgálóra, webes teszteket állíthat be az alkalmazás rendelkezésre állásának és válaszkészségének megfigyeléséhez. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Riasztást jelenít meg, ha az alkalmazás nem válaszol, vagy lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. Nem kell semmit hozzáadnia a tesztelt webhelyhez. Még csak arra sincs szükség, hogy a saját webhelye legyen: tesztelhet egy olyan REST API-t is, amelyet használni szokott.

A rendelkezésre állási teszteknek két típusuk van:

* [URL-ping teszt](#create): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](#multi-step-web-tests): ezt a Visual Studio Enterprise segítségével hozhatja létre és feltöltheti a portálra.

Alkalmazás-erőforrásonként legfeljebb 100 rendelkezésre állási tesztet hozhat létre.


## <a name="create"></a>Erőforrás megnyitása a rendelkezésre állási tesztek jelentéseihez

**Ha már konfigurálta az Application Insights szolgáltatást** a webapphoz, nyissa meg a hozzá tartozó Application Insights-erőforrást [az Azure Portalon](https://portal.azure.com).

**Vagy ha a jelentéseket új erőforrásban szeretné megtekinteni,** lépjen az [Azure Portalra](https://portal.azure.com), és hozzon létre egy Application Insights-erőforrást.

![Erőforrás létrehozása > Fejlesztői eszközök > Application Insights](./media/monitor-web-app-availability/1create-resource-appinsights.png)

Az új erőforrás Áttekintés paneljének megnyitásához kattintson az **Összes erőforrás** lehetőségre.

## <a name="setup"></a>URL-ping teszt létrehozása
Nyissa meg a Rendelkezésre állás panelt, és adjon hozzá egy tesztet.

![Töltse ki legalább a webhelye URL-címét](./media/monitor-web-app-availability/2addtest-url.png)

* **Az URL-cím** bármilyen tesztelni kívánt weblap lehet, de láthatónak kell lennie a nyilvános internetről. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.
* **Függő kérelmek elemzése**: Ha ez a beállítás be van jelölve, a teszt lekéri a tesztelt lemezképek, parancsprogramok, stílusfájljait és más fájlok esetében a tesztelt weblap. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt meghiúsul, ha nem tölthető le sikeresen az összes erőforrás a teljes teszt időtúllépése előtt. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le.

* **Újrapróbálkozások engedélyezése**:  Ha ez a beállítás be van jelölve, a teszt meghiúsul, akkor a rendszer rövid idő után. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. Ezt a beállítást javasoljuk. Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.

* **Tesztelési gyakoriság**: Megadja, hogy milyen gyakran fut a teszt mindegyik teszthelyen. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.

* A **Teszthelyek** olyan helyek, ahonnan a kiszolgálóink webes kérelmeket küldenek az Ön URL-címére. Az ajánlott teszthelyszín minimális száma: öt biztosítására, hogy megkülönböztethesse problémák webhelye a hálózati hibáktól. Legfeljebb 16 hely választható ki.

> [!NOTE]
> * Javasoljuk, hogy legalább öt, több helyről történő teszteléséhez. Ez a vakriasztások eredményeképpen előfordulhat, hogy egy adott helyen az átmeneti hibák elkerülése érdekében. Ezen kívül található, hogy az optimális konfigurálás az, hogy a teszt helyek száma egyenlő a riasztási hely küszöbértéke + 2 rendelkezik. 
> * Annak lehetővé tétele egy szigorúbb ellenőrzés "Függő kérelmek elemzése" lehetőséget az eredményeket. A teszt sikertelen lehet olyan esetekben, előfordulhat, hogy az nem lesz észrevehető a helyet manuálisan böngészésekor.

* **Sikeresség feltétele**:

    **Teszt időkorlátja**: Csökkenti az értéket, riasztást kap a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.

    **HTTP-válasz**: Akkor számít sikeres, a visszaadott állapotkód. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.

    **Tartalmi egyezés**: egy sztring, például „Üdvözöljük!” Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell.

* **Riasztási hely küszöbértéke**: Ajánlott legalább 3 vagy 5 helyeket. Az optimális riasztási hely küszöbértéke és tesztelési helyek közötti kapcsolat **riasztási hely küszöbértéke** = **teszthelyszín száma** – 2-ától legalább öt tesztelése helyek.

## <a name="multi-step-web-tests"></a>Többlépéses webes tesztek
Olyan forgatókönyveket is figyelhet, amelyek egy URL-címek sorozatából állnak. Ha például egy értékesítési webhelyet figyel, tesztelheti, hogy megfelelően működik-e a termékek kosárba helyezése.

> [!NOTE]
> A többlépéses webes tesztek díjkötelesek. [Díjszabási séma](https://azure.microsoft.com/pricing/details/application-insights/)
> 

Többlépéses teszt létrehozásához rögzíteni kell a forgatókönyvet a Visual Studio Enterprise segítségével, majd fel kell tölteni a felvételt az Application Insights-ba. Az Application Insights időközönként visszajátssza a forgatókönyvet, és ellenőrzi a válaszokat.

> [!NOTE]
> * A tesztekben nem használhat kódolt függvényeket vagy hurkokat. A .webtest szkriptnek teljes egészében tartalmaznia kell a tesztet. Ehelyett azonban standard beépülő modulok is használhatók.
> * Kizárólag angol karakterek támogatottak a többlépéses webes tesztekben. Ha más nyelven használja a Visual Studiót, kérjük, frissítse a webes teszt definíciófájlját a nem angol karakterek fordításához/kihagyásához.
>

#### <a name="1-record-a-scenario"></a>1. Forgatókönyv rögzítése
A webes munkamenet rögzítéséhez használja a Visual Studio Enterprise-t.

1. Hozzon létre egy webes teljesítményt tesztelő projektet.

    ![A Visual Studio Enterprise kiadásban hozzon létre egy projektet a webes teljesítmény és a terheléstesztelés sablonjából.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

   * *Nem jelenik meg a webes teljesítmény- és terheléstesztelés sablonja?* – Zárja be a Visual Studio Enterprise-t. Nyissa meg a **Visual Studio telepítőjét** a Visual Studio Enterprise-telepítés módosításához. Az **Individual Components** (Egyedi összetevők) területen válassza a **Web Performance and load testing tools** (Webes teljesítmény- és terheléstesztelési eszközök) lehetőséget.

2. Nyissa meg a .webtest fájlt, és kezdje meg a rögzítést.

    ![Nyissa meg a .webtest fájlt, és kattintson a Record (Rögzítés) gombra.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Végezze el a felhasználói műveleteket, amelyeket a teszt során szimulálni kíván: nyissa meg a webhelyet, tegyen a kosárba egy terméket stb. Ezután állítsa le a tesztet.

    ![A webes teszt rögzítője az Internet Explorerben fut.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    A forgatókönyv ne legyen hosszú. Legfeljebb 100 lépésből állhat, és 2 percig tarthat.
4. Szerkessze a tesztet:

   * Adjon hozzá ellenőrzéseket a kapott szöveg- és válaszkódok ellenőrzéséhez.
   * Távolítson el minden felesleges interakciót. A képekhez, illetve a hirdető vagy nyomkövetési webhelyekhez tartozó függő kérelmeket is el lehet távolítani.

     Ne feledje, hogy csak a teszt szkriptjét szerkesztheti, tehát nem adhat hozzá egyéni kódot, és nem hívhat meg más webes teszteket. Ne szúrjon be hurkokat a tesztbe. Használhatja a normál webes teszt beépülő modulokat.
5. A működés ellenőrzéséhez futtassa a tesztet a Visual Studióban.

    A webes teszt futtatója megnyit egy webböngészőt, és megismétli a rögzített műveleteket. Győződjön meg arról, hogy a várakozásainak megfelelően működik.

    ![A Visual Studióban nyissa meg a .webtest fájlt, majd kattintson a Run (Futtatás) gombra.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. A webes teszt feltöltése az Application Insightsba
1. Az Application Insights portálon, a rendelkezésre állási panelen kattintson a teszt hozzáadása.

    ![Az a rendelkezésre állás panelt válassza a Hozzáadás teszt.](./media/monitor-web-app-availability/3addtest-web.png)
2. Válassza ki a többlépéses tesztet, majd töltse fel a .webtest fájlt.

    Válassza ki a tesztelési helyeket, a tesztelés gyakoriságát és riasztási paramétereit ugyanúgy, mint a ping teszteknél.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Idő és véletlenszerű számok beiktatása a többlépéses tesztbe
Tegyük fel, hogy egy olyan eszközt tesztel, amely időfüggő adatokat fogad, például részvényeket egy külső adatcsatornától. A webes teszt rögzítésekor meghatározott időpontokat kell használni, de ezeket a teszt StartTime és EndTime paramétereiként kell beállítani.

![Egy webes teszt paraméterekkel.](./media/monitor-web-app-availability/appinsights-72webtest-parameters.png)

A teszt futtatásakor az EndTime paraméternek mindig az aktuális időnek kell lennie, a StartTime paraméternek pedig a 15 perccel korábbi időnek.

A webes teszt beépülő modulok lehetővé teszik az idő paraméterezését.

1. Adjon hozzá egy webes teszt beépülő modult minden kívánt változóparaméter-értékhez. A webes teszt eszköztárában válassza ki a **Webes teszt beépülő modul hozzáadása** elemet.

    ![Válassza ki a Webes teszt beépülő modul hozzáadása elemet, majd válasszon egy típust.](./media/monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Ebben a példában a Dátum és idő beépülő modul két példányát használjuk. Az egyik példány a „15 perccel ezelőtt” a másik pedig a „most” paraméterértékhez tartozik.
2. Nyissa meg a beépülő modulok tulajdonságait. Adjon meg egy nevet, és állítsa be úgy, hogy az aktuális időt használja. Az egyiknél állítsa be a következőt: Add Minutes = -15.

    ![Beállított Name, Use Current Time, és Add Minutes.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. A webes teszt paramétereiben a beépülő modulok nevére a {{plug-in name}} segítségével hivatkozzon.

    ![A tesztparaméterében használja a {{plug-in name}} elemet.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ezután töltse fel a tesztet a portálra. Ez a teszt minden futtatásánál a dinamikus értékeket használja.


## <a name="monitor"></a>A rendelkezésre állási teszt eredményeinek megtekintése

Az Áttekintés lap a tesztek sikerességi arányát jeleníti meg, a részletek lapon szerepelnek a pontdiagram és a részletes.

Néhány perc elteltével kattintson a **Frissítés** elemre a teszteredmények megtekintéséhez.

![A részletek panelen Teszteredményekből](./media/monitor-web-app-availability/4refresh.png)

A pontdiagram mintákat mutat be azokból a teszteredményekből, amelyek adatokat tartalmaznak a diagnosztikai teszt lépéseiről. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. Vigye a mutatót a zöld vagy a piros pontokra a teszt időbélyegzőjének, időtartamának, helyének és nevének megjelenítéséhez. Kattintson a pontdiagram bármelyik pontjára a részletes teszteredmények megtekintéséhez.  

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

A nyers eredményeken kívül két rendelkezésre állási metrika is elérhető a Metrikaböngészőben: 

1. Rendelkezésre állás: Tesztek százalékos aránya, hogy sikerült-e, különböző összes végrehajtott teszt közül. 
2. Tesztek időtartama: Teszt átlagos időtartama az összes végrehajtott teszt közül.

Az eredményeket szűrheti a teszt neve vagy a hely alapján adott tesztek és/vagy helyek trendjeinek elemzéséhez.

## <a name="edit"></a> Tesztek megtekintése és szerkesztése

A részletek lapról egy adott teszt válassza a három pontot, jobb szélen szerkesztéséhez, ideiglenesen letilthatja, törlése vagy webteszt letöltése. Konfigurációs módosítások propagálása akár 20 percig is eltarthat.

Válassza ki **teszt részleteinek megtekintése** látható a pontdiagram és az adott tesztet hely részleteit egy adott tesztelési környezetből.

![Teszt részletek megtekintése, szerkesztése és a egy webes teszt letiltása](./media/monitor-web-app-availability/5viewdetails.png)

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

![Tiltsa le a webes teszt](./media/monitor-web-app-availability/6disable.png)
![teszt szerkesztése](./media/monitor-web-app-availability/8edittest.png)

## <a name="failures"></a>Ha hibákat lát
Kattintson egy piros pontra.

![Kattintson egy piros pontra](./media/monitor-web-app-availability/open-instance-3.png)

Egy rendelkezésre állási teszt eredménye a tranzakció részleteit láthatja minden összetevőben. Itt a következőket teheti:

* Megvizsgálhatja a kiszolgálótól érkezett választ.
* Diagnosztizálhatja a hibákat a sikertelen rendelkezésre állási teszt feldolgozásakor begyűjtött kapcsolódó kiszolgálóoldali telemetriát.
* Probléma jelentkezik, vagy munkatétel a Git- vagy Azure célra szolgáló kártyákat, a probléma nyomon követéséhez. A hiba tartalmazni fog egy hivatkozást erre az eseményre.
* Megnyithatja a webes teszt eredményét a Visual Studióban.

Ismerje meg, további információt a végpontok közötti tranzakció diagnosztikája élmény [Itt](../../azure-monitor/app/transaction-diagnostics.md).

Kattintson ide a részletek a kiszolgálóoldali kivétel a szintetikus rendelkezésre állási teszt sikertelen okozó kivétel a sorban. Is beszerezheti a [hibakereső pillanatképek](../../azure-monitor/app/snapshot-debugger.md) gazdagabb kód szintű diagnosztikai.

![Kiszolgálóoldali diagnosztikát](./media/monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Rendelkezésre állási riasztás
A rendelkezésre állási adatok használatával a klasszikus riasztások élmény a következő típusú riasztási szabályok veheti fel:
1. X, Y helyek hibák jelentéskészítési időszakban kívül
2. Összesített rendelkezésre állási százaléka csepp a küszöbérték alatti.
3. Teszt átlagos időtartama növekszik meghaladja a küszöbértéket

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Riasztás x-en kívül Y helyek jelentéskészítési hibák
Az X Y helyek alapértelmezés szerint engedélyezve van a riasztási szabály kívül a [új egyesített riasztások élmény](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), amikor létrehoz egy új rendelkezésre állási teszt. Kikapcsolhatja a "klasszikus" lehetőség kiválasztásával, vagy letiltani a riasztási szabály kiválasztása.

![Hozzon létre felhasználói élményt](./media/monitor-web-app-availability/appinsights-71webtestUpload.png)

> [!NOTE]
>  Az a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), a riasztási szabály fontossága és értesítési beállításai [Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **kell** riasztásainak konfigurált. A következő lépések nélkül csak a portálon értesítések fog kapni.

1. A rendelkezésre állási teszt mentése, után a részleteinek lapon kattintson a három épp most módosította a teszt által. Kattintson a "riasztás szerkesztése".
![Szerkesztés mentés után](./media/monitor-web-app-availability/9editalert.png)

2. Állítsa be a kívánt súlyossági szintet, a szabály leírását és ennél is fontosabb – a műveletcsoport, amely rendelkezik az értesítési beállításokat szeretné használni a riasztási szabály.
![Szerkesztés mentés után](./media/monitor-web-app-availability/10editalert.png)


> [!NOTE]
> * A Műveletcsoportok, hogy értesítést kapjon, ha a riasztás aktiválásakor a fenti lépéseket követve konfigurálja. Ebben a lépésben nélkül csak értesítéseket fog kapni a portálon a szabály aktiválásakor.
>

### <a name="alert-on-availability-metrics"></a>Riasztás a rendelkezésre állási metrikák
Használatával a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), szegmentált összesített rendelkezésre állási riasztás és a teszt időtartamának metrikákat is:

1. Application Insights-erőforrás a metrikák felületen válassza ki és a egy rendelkezésre állási metrika:  ![Rendelkezésre állási metrikák kiválasztása](./media/monitor-web-app-availability/selectmetric.png)

2. Lehetőséget a menüből léphet az új felhasználói felület, amelyen kiválaszthatja meghatározott vizsgálatok vagy a riasztási szabály beállítása a riasztásokat konfigurálni. A Műveletcsoportok a riasztási szabály is konfigurálhatja.
    ![Rendelkezésre állási riasztások konfigurálása](./media/monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Riasztás egyéni elemzési lekérdezések
Használatával a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), is riasztás [egyéni napló lekérdezések](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Az egyéni lekérdezések az minden olyan tetszőleges feltételt, amely segít a legmegbízhatóbb jel rendelkezésre állási problémák is riasztás. Ez akkor is különösen, ha a TrackAvailability SDK-val egyéni rendelkezésre állási eredmények küld. 

> [!Tip]
> * A metrikák, a rendelkezésre állási adatokat tartalmaz, előfordulhat, hogy küldjön az TrackAvailability SDK meghívásával egyéni rendelkezésre állási eredményeket. A riasztási egyéni rendelkezésre állási eredmények riasztás metrikák támogatás is használhatja.
>

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

![Titkos ügyfélkulcs-minta](./media/monitor-web-app-availability/110.png)

1. Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
2. Nyerje ki a tulajdonosi jogkivonatot a válaszból.
3. Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.

Győződjön meg róla, hogy a webes teszt egy tényleges ügyfél – saját alkalmazása van az AAD-ben –, valamint használja a clientId + appkey értékét. A teszt alatt álló szolgáltatása is rendelkezik saját alkalmazással az aad-ben: az alkalmazásazonosító URI-ját az alkalmazás megjelenik a webes teszt, az erőforráscsoport mezőjében.

### <a name="open-authentication"></a>Nyílt hitelesítés
Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása.

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

* Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához.
* Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
* A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak.
* Rögzítsen egy webes tesztet a Visual Studióval.
* Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben.
  (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)

## <a name="performance-tests"></a>Teljesítménytesztek
Terheléstesztelést futtathat a webhelyén. Csakúgy, mint a rendelkezésre állási teszt esetében, egyszerű vagy több lépésből álló kéréseket küldhet a világ minden táján található helyekről. A rendelkezésre állási teszttől eltérően sok kérés lesz elküldve, több párhuzamos felhasználót szimulálva.

Alatt **konfigurálása**, lépjen a **Teljesítménytesztelés** kattintson új teszt létrehozásához.

![Új teljesítményteszt létrehozása](./media/monitor-web-app-availability/11new-performance-test.png)

A teszt befejezése után a válaszidők és a sikerességi arány jelenik meg.

![Teljesítményteszt eredményei](./media/monitor-web-app-availability/12performance-test.png)

> [!TIP]
> A teljesítményteszt hatásainak megfigyelésére használja az [Élő streamet](../../azure-monitor/app/live-stream.md) és a [Profilkészítőt](../../azure-monitor/app/profiler.md).
>

## <a name="automation"></a>Automation
* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../../azure-monitor/platform/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="qna"></a> GYAKORI KÉRDÉSEK

* *Webhely megfelelőnek tűnik, de látom tesztelési hibák? Miért van az Application Insights riasztási velem?*

    * Rendelkezik a teszt "Parse függő kérelmek" engedélyezve van? Erőforrások, például parancsfájlokat, szigorú ellenőrzés eredményez, amely képeket stb. Előfordulhat, hogy az ilyen típusú hibák nem észrevehető, egy böngészőben. Ellenőrizze az összes képet, szkriptet, stíluslapot és a lap által betöltött többi fájlt. Ha ezek közül bármelyik hibás, a teszt sikertelenként lesz jelentve, még akkor is, ha a fő HTML-oldal megfelelően töltődik be. Ha a tesztet érzéketlenné szeretné tenni az ilyen erőforráshibákkal szemben, egyszerűen törölje a „Függő kérelmek elemzése” jelölőnégyzet jelölését a tesztkonfigurációban. 

    * Az átmeneti hálózati jelekből és hasonlókból származó zajok esélyének csökkentése érdekében győződjön meg róla, hogy be van jelölve az „Enable retries for test failures” (Újrapróbálkozások engedélyezése teszthibák esetén) beállítás. Teszt több helyről is, és meggátolja az indokolatlan riasztásokat okozó helyspecifikus problémák elkerülése érdekében megfelelően kezelheti a riasztási szabály küszöbértékét.

    * Kattintson bármelyik a piros pötty a rendelkezésre állási származó, vagy minden rendelkezésre állási hiba a keresési ablak miért azt jelenti, hogy a hiba részleteinek megtekintéséhez. A vizsgálat eredményének, valamint a kapcsolódó kiszolgálóoldali telemetriát (Ha engedélyezve van) segíthet megérteni, hogy miért a teszt sikertelen volt. Átmeneti hibák gyakori okai hálózati és kapcsolati problémák. 

    * A teszt időtúllépése volt? Tesztek 2 perc múlva le azt. A ping vagy többlépéses teszt hosszabb, mint 2 percet vesz igénybe, ha azt hibaként tartozik. Vegye figyelembe, hogy a teszt ossza több címkéket, is elvégezheti a rövidebb időtartam.

    * Hiba, vagy csak néhány jelentettek az összes hely? Ha csak néhány jelentett hibák, valószínűleg hálózati/a CDN-hibák miatt. Újra piros pontra kattint segíthet megérteni, hogy miért érdemes az a hely jelentett hibák.

* *E nem tudták beszerezni, ha a riasztás aktiválódik, vagy nem oldják fel e-mailt vagy mindkettő?*

    Ellenőrizze a klasszikus riasztások beállítását, ellenőrizze az e-maileket közvetlenül szerepel a listán, vagy a terjesztési lista értesítések fogadására van beállítva. Ha igen, majd ellenőrizze a terjesztési lista konfigurációját megkaphatja a külső e-mailek megerősítéséhez. A levelezési rendszergazda előfordulhat, hogy van-e bármilyen konfigurált szabályzatok, előfordulhat, hogy a probléma kiváltó is ellenőrizheti.

* *Nem kaptam a webhook értesítést?*

    Ellenőrizze, hogy az alkalmazás a webhook értesítés érhető el, és sikeresen dolgozza fel a webhook-kérelmeket. Lásd: [ez](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) további információt.

* *Az időszakos teszt meghiúsult egy protokollmegsértési hibával?*

    A „protokollmegsértés... A CR karakter után LF karakternek kell következnie” hiba a kiszolgáló (vagy a függőségek) problémáját jelzi. Ez akkor történik, amikor hibás formátumú fejlécek vannak beállítva a válaszban. Ezt a terheléselosztók vagy a CDN-ek okozhatják. Előfordulhat, hogy néhány fejléc nem a CRLF-fel jelzi a sor végét, ami nem felel meg a HTTP-specifikációnak, ezért az ellenőrzés meghiúsul a .NET WebRequest szintjén. Vizsgálja meg, hogy nincsenek-e a válaszban nem megfelelő fejlécek.
    
    Megjegyzés: Az URL nem hiúsul meg, amelyek olyan böngészőkön a HTTP-fejléceket. A hiba részletes leírását a következő blogbejegyzésben találja: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Nem látok kapcsolódó kiszolgálóoldali telemetriát a teszthibák diagnosztizálásához*
    
    Ha a kiszolgálóoldali alkalmazásához be van állítva az Application Insights, akkor ezt okozhatja az, hogy [mintavételezés](../../azure-monitor/app/sampling.md) van folyamatban. Válasszon ki egy másik rendelkezésre állási eredményt.

* *Meghívhatok egy kódot a webes tesztből?*

    Nem. A .webtest fájlnak tartalmaznia kell a teszt lépéseit. Nem hívhat meg más teszteket, és nem használhat hurkokat. Azonban számos beépülő modul van, amely hasznos lehet.

* *Támogatott a HTTPS?*

    A TLS 1.1 és a TLS 1.2 támogatott. Jelenleg nem ellenőrizzük a HTTPS-tanúsítvány hibákat.  

* *Van különbség a „webes tesztek” és a „rendelkezésre állási tesztek” között?*

    A két kifejezés hasonló értelmű, felcserélhető. A „rendelkezésre állási teszt” általánosabb fogalom, amely az URL-ping tesztek mellett a többlépéses webes teszteket is magában foglalja.
    
* *Rendelkezésre állási teszteket szeretnék használni a tűzfal mögött futó belső kiszolgálón.*

    Két lehetséges megoldás létezik:
    
    * Konfigurálhatja úgy a tűzfalat, hogy az engedélyezze a [webes tesztügynökök IP-címeiről](../../azure-monitor/app/ip-addresses.md) érkező bejövő kéréseket.
    * Saját kód megírásával rendszeresen ellenőrizheti a belső kiszolgálót. Futtassa a kódot a tűzfal mögötti tesztkiszolgáló háttérfolyamataként. A tesztelési folyamat az eredményeket a Core SDK-csomag [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API-jával küldheti el az Application Insightsba. Ehhez szükség van arra, hogy a tesztkiszolgáló kimenő hozzáféréssel rendelkezzen az Application Insights betöltési végpontjához, de ez jóval kisebb biztonsági kockázatot jelent a bejövő kérések engedélyezéséhez képest. Az eredmények nem jelennek meg a rendelkezésre állási webes tesztek paneljein, de rendelkezésre állási eredményként megtekinthetők az Elemzés, a Keresés és a Metrikaböngésző panelen.

* *A többlépéses teszt feltöltése sikertelen*

    Néhány ok, Ez azért fordulhat elő:
    * A méretkorlát: 300 KB.
    * A hurkok nem támogatottak.
    * A más webes tesztekre mutató hivatkozások nem támogatottak.
    * Az adatforrások nem támogatottak.

* *A többlépéses teszt nem fejeződik be*

    Egy teszt legfeljebb 100 kérelemből állhat. A teszt leáll, ha két percnél tovább fut.

* *Hogyan futtathatok tesztet ügyféltanúsítványokkal?*

    Sajnos azt nem támogatjuk.

## <a name="who-receives-the-classic-alert-notifications"></a>Ki kapja a (klasszikus) riasztási értesítések?

Ez a szakasz csak klasszikus riasztások vonatkozik, és segít optimalizálni a riasztási értesítések biztosítják, hogy csak a kívánt címzettek megkapják az értesítéseket. Ismerje meg jobban a különbség a [klasszikus riasztások](../platform/alerts-classic.overview.md), majd tekintse át a riasztások új kezelőfelülete a [riasztások áttekintő cikkben](../platform/alerts-overview.md). Riasztás szabályozhatja az új riasztások az értesítési használata élményt [Műveletcsoportok](../platform/action-groups.md).

* A klasszikus riasztási értesítéseket meghatározott címzettek használatát javasoljuk.

* Sikertelen X kívül Y helyen, a riasztások a **tömeges/csoport** jelölőnégyzetet, a beállítást, ha engedélyezve van, küld rendszergazda/társadminisztrátor szerepkörrel rendelkező felhasználók számára.  Lényegében _összes_ rendszergazdái a _előfizetés_ értesítéseket kap.

* A rendelkezésre állási metrikák (vagy bármely, függetlenül attól, hogy az Application Insights metrikáit) riasztásaihoz a **tömeges/csoport** jelölőnégyzetet, a beállítás engedélyezve van, ha elküldi az előfizetésben tulajdonosi, közreműködői vagy olvasói szerepkörrel rendelkező felhasználók. Gyakorlatilag _összes_ az előfizetés az Application Insights-erőforráshoz hozzáféréssel rendelkező felhasználók terjed ki, és értesítéseket kap. 

> [!NOTE]
> Ha jelenleg használja a **tömeges/csoport** jelölőnégyzetet, a beállítást, és tiltsa le, nem állíthatja vissza a módosítást.

Az új riasztás élmény/közel valós idejű riasztások használja, ha értesítse a felhasználókat a szerepkörökhöz alapján kell. A [Műveletcsoportok](../platform/action-groups.md), bármelyik (nem egyesíthet egyetlen lehetőségként) közreműködői vagy tulajdonosi vagy olvasó szerepkört konfigurálható e-mail értesítések küldéséhez felhasználók számára.



## <a name="next"></a>Következő lépések
[Diagnosztikai naplók keresése][diagnostic]

[Hibaelhárítás][qna]

[A webes tesztügynökök IP-címei](../../azure-monitor/app/ip-addresses.md)

<!--Link references-->

[azure-availability]: ../../insights-create-web-tests.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
