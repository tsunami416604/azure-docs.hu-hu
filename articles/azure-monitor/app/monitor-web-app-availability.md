---
title: Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670032"
---
# <a name="monitor-the-availability-of-any-website"></a>Bármely webhely elérhetőségének figyelése

Miután telepítette a webalkalmazást/webhelyet, ismétlődő teszteket állíthat be a rendelkezésre állás és a válaszidő figyelésére. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Figyelmeztetheti, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. Nem kell módosítania a tesztelt webhelyet. Sőt, nem is kell, hogy egy oldal van a saját. Tesztelheti a rendelkezésre álló REST API-t, amely tőla a szolgáltatás függ.

### <a name="types-of-availability-tests"></a>A rendelkezésre állási tesztek típusai:

A rendelkezésre állási teszteknek három típusa van:

* [URL-ping teszt](#create-a-url-ping-test): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](availability-multistep.md): Webes kérések sorozatának rögzítése, amely et le lehet játszani az összetettebb forgatókönyvek teszteléséhez. A többlépéses webes tesztek a Visual Studio Enterprise alkalmazásban jönnek létre, és végrehajtásra kerülnek a portálra.
* [Egyéni pálya rendelkezésre állási tesztek:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)Ha úgy dönt, `TrackAvailability()` hogy hozzon létre egy egyéni alkalmazást a rendelkezésre állási tesztek futtatásához, a módszer segítségével elküldheti az eredményeket az Application Insights.

**Az Application Insights-erőforrásonként legfeljebb 100 rendelkezésre állási tesztet hozhat létre.**

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Egy rendelkezésre állási teszt létrehozásához először létre kell hoznia egy Application Insights-erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakaszsal [az URL Ping teszt létrehozásához.](#create-a-url-ping-test)

Az Azure Portalon válassza az **Erőforrás-fejlesztői** > **eszközök** > **alkalmazáselemzéslétrehozása** lehetőséget, és [hozzon létre egy Application Insights-erőforrást.](create-new-resource.md)

## <a name="create-a-url-ping-test"></a>URL-ping teszt létrehozása

A név "URL ping teszt" egy kicsit helytelen elnevezés. A teszt egyértelművé tétele érdekében nem használja ki az ICMP-t (Internet Control Message Protocol) a webhely elérhetőségének ellenőrzésére. Ehelyett fejlettebb HTTP-kérelem funkciót használ annak ellenőrzésére, hogy egy végpont válaszol-e. Azt is méri a választ társított teljesítmény, és lehetővé teszi az egyéni siker feltételek et, valamint a fejlettebb funkciók, például a függő kérelmek elemzésével, és lehetővé teszi az újrapróbálkozások.

Az első rendelkezésre állási kérelem létrehozásához nyissa meg az Elérhetőség ablaktáblát, és válassza a **Teszt létrehozása lehetőséget.**

![Töltse ki legalább a webhelye URL-címét](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Teszt létrehozása

|Beállítás| Magyarázat
|----|----|----|
|**Url** |  Az URL-cím bármilyen tesztelni kívánt weblap lehet, de láthatónak kell lennie a nyilvános internetről. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.|
|**Függő kérelmek elemzése**| Tesztkérelmek képek, parancsfájlok, stílusfájlok és egyéb fájlok, amelyek részét képezik a weboldal teszt alatt. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt sikertelen, ha ezen erőforrások bármelyike nem tölthető le sikeresen a teljes teszt időtúlidején belül. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le. Ennek a beállításnak az engedélyezése szigorúbb ellenőrzést eredményez. A teszt sikertelen lehet olyan esetekben, amelyek nem feltétlenül észrevehetők a webhely manuális böngészése során.
|**Újrapróbálkozások engedélyezése**|ha a teszt sikertelen, rövid idő elteltével újra próbálkozik. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. **Javasoljuk ezt a lehetőséget**. Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.|
|**A vizsgálat gyakorisága**| Beállítja, hogy a teszt milyen gyakran fusson az egyes teszthelyeken. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Vizsgálati helyek**| Azok a helyek, ahonnan szervereink webkéréseket küldenek az Ön URL-jére. **Az ajánlott teszthelyek minimális száma öt,** hogy biztosítsuk, hogy meg tudja különböztetni a webhelyproblémáit a hálózati problémáktól. Legfeljebb 16 hely választható ki.

**Ha az URL-cím nem látható a nyilvános internetről, dönthet úgy, hogy szelektíven megnyitja a tűzfalat, hogy csak a teszttranzakciókat engedélyezze a rendszeren keresztül.** Ha többet szeretne megtudni a rendelkezésre állási tesztelőink tűzfalalólak, olvassa el az [IP-cím útmutatót.](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)

> [!NOTE]
> Javasoljuk, hogy tesztelje több **helyszínen, legalább öt helyen.** Ez megakadályozza a téves riasztásokat, amelyek egy adott helyen átmeneti problémák ból eredhetnek. Ezen kívül azt találtuk, hogy az optimális konfiguráció, hogy a **vizsgálati helyek száma egyenlő legyen a riasztási hely küszöbérték + 2**.

### <a name="success-criteria"></a>Sikerességi feltételek

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időmeghosszabbítása** |Csökkentse ezt az értéket, ha értesítést szeretne tenni a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalomegyezés** | Egy madzag, mint a "Welcome!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalomegyezéssel csak angol karaktereket támogat** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Közel valós idejű riasztások használatát javasoljuk. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Klasszikus** | Már nem javasoljuk a klasszikus riasztások az új rendelkezésre állási tesztek használata.|
|**Riasztási hely küszöbértéke**|Javasoljuk, hogy legalább 3/5 helyen. A riasztási hely küszöbértéke és a teszthelyek száma közötti optimális kapcsolat a **riasztási hely küszöbértékének** = **száma – 2, legalább öt teszthellyel.**|

## <a name="see-your-availability-test-results"></a>A rendelkezésre állási teszt eredményeinek megtekintése

A rendelkezésre állási teszt eredményei vonal- és pontdiagram-nézetekkel is láthatók.

Néhány perc múlva kattintson a **Frissítés** gombra a teszteredmények megtekintéséhez.

![Sor nézet](./media/monitor-web-app-availability/availability-refresh-002.png)

A scatterplot nézet a teszteredmények mintáit jeleníti meg, amelyekdiagnosztikai tesztlépés részletesen ismertetik őket. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. Vigye az egérmutatót a zöld/piros pöttyök bármelyike fölé a teszt, a teszt neve és helye megtekintéséhez.

![Sor nézet](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

## <a name="inspect-and-edit-tests"></a> Tesztek megtekintése és szerkesztése

A szerkesztéshez ideiglenesen tiltson le vagy töröljön egy tesztet, kattintson a tesztneve melletti három pontra. A módosítás után akár 20 percig is eltarthat, amíg a konfigurációs módosítások az összes tesztanyagra propagálnak.

![A teszt részleteinek megtekintése. Webes teszt szerkesztése és letiltása](./media/monitor-web-app-availability/edit.png)

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

## <a name="if-you-see-failures"></a>Ha hibákat lát

Kattintson egy piros pontra.

![Kattintson egy piros pontra](./media/monitor-web-app-availability/open-instance-3.png)

Egy rendelkezésre állási teszt eredménye, láthatja a tranzakció részleteit az összes összetevő között. Itt lehet:

* Megvizsgálhatja a kiszolgálótól érkezett választ.
* A sikertelen rendelkezésre állási teszt feldolgozása során gyűjtött korrelált kiszolgálóoldali telemetriai adatokkal diagnosztizálhatja a hibát.
* Naplózjon egy problémát vagy munkaelemet a Git ben vagy az Azure Boardsban a probléma nyomon követéséhez. A hiba tartalmazni fog egy hivatkozást erre az eseményre.
* Megnyithatja a webes teszt eredményét a Visual Studióban.

A tranzakciódiagnosztika végpontok nélküli diagnosztikai élményéről itt olvashat [bővebben.](../../azure-monitor/app/transaction-diagnostics.md)

Kattintson a kivétel sorra a kiszolgálóoldali kivétel részleteinek megtekintéséhez, amely a szintetikus rendelkezésre állási teszt sikertelenségét okozta. A [hibakeresési pillanatképet](../../azure-monitor/app/snapshot-debugger.md) a gazdagabb kódszintű diagnosztikához is lekaphatja.

![Kiszolgálóoldali diagnosztika](./media/monitor-web-app-availability/open-instance-4.png)

A nyers eredmények mellett két fő rendelkezésre állási metrikát is megtekinthet a [Metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)kezelőjében:

1. Rendelkezésre állás: Az összes végrehajtott teszt közül a sikeresen végrehajtott tesztek százalékos aránya.
2. Tesztek időtartama: A tesztek átlagos időtartama az összes végrehajtás alapján.

## <a name="automation"></a>Automation

* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../../azure-monitor/platform/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Többlépéses webes tesztek](availability-multistep.md)


