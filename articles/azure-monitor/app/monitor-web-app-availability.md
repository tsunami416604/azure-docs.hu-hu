---
title: Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 074b5c175305131cd67cc6660d13756a83386c11
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819298"
---
# <a name="monitor-the-availability-of-any-website"></a>Bármely webhely rendelkezésre állásának monitorozása

A webalkalmazás/webhely üzembe helyezése után ismétlődő teszteket állíthat be a rendelkezésre állás és a válaszadás figyeléséhez. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. Nem kell módosítania a tesztelni kívánt webhelyet. Valójában még nem kell a saját webhelynek lennie. A szolgáltatástól függő REST API rendelkezésre állását ellenőrizheti.

### <a name="types-of-availability-tests"></a>A rendelkezésre állási tesztek típusai:

A rendelkezésre állási tesztek három típusa létezik:

* [URL-ping teszt](#create-a-url-ping-test): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](availability-multistep.md): a webes kérések sorozatának rögzítése, amely az összetettebb forgatókönyvek tesztelésére használható. A többlépéses webes tesztek a Visual Studio Enterprise-ban jönnek létre, és a portálra lesznek feltöltve a végrehajtáshoz.
* [Egyéni nyomkövetési rendelkezésre állási tesztek](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Ha úgy dönt, hogy egyéni alkalmazást hoz létre a rendelkezésre állási tesztek futtatásához, akkor a `TrackAvailability()` metódus használatával küldheti el az eredményeket Application Insights.

**Application Insights erőforráson akár 100 rendelkezésre állási tesztet is létrehozhat.**

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

A rendelkezésre állási teszt létrehozásához először létre kell hoznia egy Application Insights erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakasszal [egy URL ping teszt létrehozásához](#create-a-url-ping-test).

A Azure Portal válassza az **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights** lehetőséget, és [hozzon létre egy Application Insights-erőforrást](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>URL-ping teszt létrehozása

Az "URL ping test" név egy kis helytelen felhasználóneve. Ez a teszt nem teszi elérhetővé az ICMP (Internet Control Message Protocol) használatát a hely rendelkezésre állásának ellenőrzéséhez. Ehelyett több speciális HTTP-kérés funkciót használ annak ellenőrzéséhez, hogy a végpont válaszol-e. Emellett méri az adott válaszhoz kapcsolódó teljesítményt, és lehetővé teszi az egyéni sikerességi feltételek megadását, amely fejlettebb funkciókkal, például a függő kérelmek elemzésével és az újrapróbálkozások engedélyezésével párosul.

Az első rendelkezésre állási kérelem létrehozásához nyissa meg a rendelkezésre állás panelt, és válassza a **teszt létrehozása**lehetőséget.

![Töltse ki legalább a webhelye URL-címét](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Teszt létrehozása

|Beállítás| Magyarázat
|----|----|----|
|**URL-cím** |  Az URL-cím bármely tesztelni kívánt weblap lehet, de a nyilvános internetről is láthatónak kell lennie. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.|
|**Függő kérelmek elemzése**| Tesztelje a weboldalhoz tartozó képeket, parancsfájlokat, stílusú fájlokat és egyéb fájlokat, amelyek a tesztelés alatt állnak. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt sikertelen lesz, ha a teljes teszt időkorlátján belül nem tölthetők le ezek az erőforrások. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le. Ha engedélyezi ezt a beállítást, a rendszer szigorúbb ellenőrzését eredményez. Előfordulhat, hogy a teszt sikertelen lehet, ami nem észlelhető a hely manuális böngészése során.
|**Újrapróbálkozások engedélyezése**|Ha a teszt sikertelen, a rendszer rövid idő után újrapróbálkozik. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. **Ezt a beállítást javasoljuk**. Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.|
|**Tesztelési gyakoriság**| Beállítja, hogy a teszt milyen gyakran fusson az egyes tesztelési helyekről. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Tesztelési helyszínek**| Azok a helyek, ahonnan a kiszolgálók webes kéréseket küldenek az URL-címre. Az **ajánlott tesztelési helyszínek minimális száma öt** annak biztosítására, hogy a hálózati problémák alapján megkülönböztetni tudja a webhelyén felmerülő problémákat. Legfeljebb 16 hely választható ki.

**Ha az URL-cím nem látható a nyilvános internetről, dönthet úgy, hogy szelektíven megnyitja a tűzfalat, hogy csak a tesztelési tranzakciókat engedélyezze**. Ha többet szeretne megtudni a rendelkezésre állási tesztelési ügynökökkel kapcsolatos tűzfal-kivételekről, tekintse meg az [IP-cím útmutatóját](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Javasoljuk, hogy több helyről, **legalább öt hellyel**tesztelje a tesztelést. Ennek célja, hogy megakadályozza a hamis riasztásokat, amelyek egy adott hellyel kapcsolatos átmeneti problémákhoz vezethetnek. Emellett azt is találtuk, hogy az optimális konfiguráció a **teszt helyeinek száma a riasztási hely küszöbértéke + 2**.

### <a name="success-criteria"></a>Sikerességi feltételek

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időtúllépése** |Csökkentse ezt az értéket, hogy figyelmeztessen a lassú válaszokra. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalom egyezése** | Egy karakterlánc, például "Welcome!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalmi egyezés csak az angol karaktereket támogatja** |

### <a name="alerts"></a>Értesítések

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Javasoljuk, hogy a közel valós idejű riasztásokat használja. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Klasszikus** | Már nem ajánlott klasszikus riasztásokat használni az új rendelkezésre állási tesztekhez.|
|**Riasztási hely küszöbértéke**|Legalább 3/5 helyet ajánlunk. A riasztási hely küszöbértéke és a tesztelési helyek száma közötti optimális kapcsolat a **riasztási hely küszöbértéke**  =  a**tesztelési helyek száma – 2, legalább öt tesztelési hellyel.**|

## <a name="see-your-availability-test-results"></a>A rendelkezésre állási teszt eredményeinek megtekintése

A rendelkezésre állási teszt eredményei a line és a scatter ábrázolási nézetekkel is megjeleníthetők.

Néhány perc elteltével kattintson a **frissítés** gombra a tesztek eredményeinek megtekintéséhez.

![Vonal nézet](./media/monitor-web-app-availability/availability-refresh-002.png)

A scatterplot nézet a tesztelési eredmények olyan mintáit jeleníti meg, amelyek diagnosztikai tesztelési lépésekkel kapcsolatos részletességgel rendelkeznek. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. A teszt, a teszt neve és a hely megjelenítéséhez vigye a kurzort a zöld/piros pontok bármelyikére.

![Vonal nézet](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

## <a name="inspect-and-edit-tests"></a>Tesztek vizsgálata és szerkesztése

A tesztek szerkesztéséhez, ideiglenes letiltásához vagy törléséhez kattintson a teszt neve melletti három pontra. A változtatások végrehajtása után akár 20 percet is igénybe vehet, amíg a konfigurációs módosítások propagálják az összes tesztelési ügynököt.

![A teszt részleteinek megtekintése. Webes tesztek szerkesztése és letiltása](./media/monitor-web-app-availability/edit.png)

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

## <a name="if-you-see-failures"></a>Ha a hibák jelennek meg

Kattintson egy piros pontra.

![Kattintson egy piros pontra](./media/monitor-web-app-availability/open-instance-3.png)

A rendelkezésre állási teszt eredményét követve megtekintheti a tranzakció részleteit az összes összetevőben. Itt a következőket teheti:

* Megvizsgálhatja a kiszolgálótól érkezett választ.
* A sikertelen rendelkezésre állási teszt feldolgozása során gyűjtött, korrelált kiszolgálóoldali telemetria hibáinak diagnosztizálása.
* A probléma nyomon követéséhez naplózhat egy problémát vagy munkaelemet a git-vagy az Azure-táblákban. A hiba tartalmazni fog egy hivatkozást erre az eseményre.
* Megnyithatja a webes teszt eredményét a Visual Studióban.

További információ a végpontok közötti tranzakciós [diagnosztikai élményről](../../azure-monitor/app/transaction-diagnostics.md).

Kattintson a kivétel sorra, és tekintse meg a szintetikus rendelkezésre állási teszt sikertelenségét okozó kiszolgálóoldali kivétel részleteit. Emellett a [hibakeresési pillanatképet](../../azure-monitor/app/snapshot-debugger.md) is megtekintheti a gazdagabb kódok szintjének diagnosztizálásához.

![Kiszolgálóoldali diagnosztika](./media/monitor-web-app-availability/open-instance-4.png)

A nyers eredmények mellett két fő rendelkezésre állási metrikát is megtekintheti [Metrikaböngészőban](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Rendelkezésre állás: Az összes végrehajtott teszt közül a sikeresen végrehajtott tesztek százalékos aránya.
2. Tesztek időtartama: A tesztek átlagos időtartama az összes végrehajtás alapján.

## <a name="automation"></a>Automation

* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../../azure-monitor/platform/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="troubleshooting"></a>Hibakeresés

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>Következő lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Több lépésből álló webes tesztek](availability-multistep.md)


