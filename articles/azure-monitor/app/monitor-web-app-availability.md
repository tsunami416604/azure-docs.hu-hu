---
title: Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303734"
---
# <a name="monitor-the-availability-of-any-website"></a>Minden webhely rendelkezésre állásának figyelése

Miután telepítette a webes alkalmazás vagy webhely, hogy az ismétlődő teszteket állíthat figyelése rendelkezésre állásának és válaszkészségének. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Azt is riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. Nem kell módosítaniuk a tesztelt webhelyhez. Sőt akkor még nincs a saját helyként. Tesztelheti egy REST API-t, amely a szolgáltatás függ rendelkezésre állását.

### <a name="types-of-availability-tests"></a>Rendelkezésre állási tesztek típusai:

Rendelkezésre állási tesztek három típusa van:

* [URL-ping teszt](#create-a-url-ping-test): egyszerű teszt, amelyet az Azure Portalon hozhat létre.
* [Többlépéses webes teszt](availability-multistep.md): Felvétel az összetettebb esetekhez teszteléséhez lejátszhatók webes kérések sorozata. Többlépéses webes tesztek létrehozása a Visual Studio Enterprise és feltöltése a portálra a végrehajtáshoz.
* [Egyéni követése rendelkezésre állási tesztek](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): A `TrackAvailability()` módszer használható a saját egyéni rendelkezésre állási tesztek létrehozása.

**Az Application Insights-erőforrásonként legfeljebb 100 rendelkezésre állási tesztet hozhat létre.**

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Annak érdekében, hogy a rendelkezésre állási teszt létrehozása, akkor először hozzon létre egy Application Insights-erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakaszban [egy URL-Ping teszt létrehozása](#create-a-url-ping-test).

Az Azure Portalon, válassza ki a **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights** és [hozzon létre egy Application Insights-erőforrás](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>URL-ping teszt létrehozása

A név "URL-pingteszt" egy kis egy misnomer. Ahhoz, hogy világos, ez a vizsgálat nem készít használata ICMP (Internet Control Message Protocol) a webhely rendelkezésre állását ellenőrzik. Speciális HTTP-kérelem funkciókat inkább megállapítani, hogy a végpont válaszol-e használ. Emellett méri a teljesítményt, a válaszban társított, és lehetővé teszi olyan fejlett funkciókkal, mint a függő kérelmek elemzése, valamint lehetővé teszik a újrapróbálkozások összefüggő egyéni sikerességi feltételeket.

Az első rendelkezésre állási kérelem létrehozásához nyissa meg a rendelkezésre állás panelt, és válassza ki **teszt létrehozása**.

![Töltse ki legalább a webhelye URL-címét](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Hozzon létre egy tesztet

|Beállítás| Magyarázat
|----|----|----|
|**URL-cím** |  Az URL-cím bármilyen tesztelni kívánt weblap lehet, de a nyilvános interneten láthatónak kell lennie. Az URL-cím tartalmazhat lekérdezési sztringet. Tehát például kísérletezhet egy kicsit az adatbázissal. Ha az URL feloldása egy átirányítást eredményez, legfeljebb 10 átirányításig követjük.|
|**Függő kérelmek elemzése**| Teszt lekéri a lemezképek, parancsprogramok, stílusfájljait és más fájlok esetében a tesztelt weblap. A rögzített válaszidőbe a fájlok lekérése is beleszámít. A teszt meghiúsul, ha a következő erőforrások nem tölthető le sikeresen a teljes teszt időtúllépése előtt. Ha a beállítás nincs bejelölve, a teszt csak a fájlt és a megadott URL-címet kéri le. A beállítás hatására a szigorúbb ellenőrzés engedélyezése. A teszt sikertelen lehet olyan esetekben, előfordulhat, hogy az nem lesz észrevehető a helyet manuálisan böngészésekor.
|**Újrapróbálkozások engedélyezése**|a teszt meghiúsul, akkor a rendszer rövid idő után. Csak akkor jelent hibát, ha három egymást követő kísérlet meghiúsul. Ezután a rendszer a teszteket a szokásos tesztelési gyakorisággal végzi el. Az újrapróbálkozás ideiglenesen fel van függesztve a következő sikeres műveletig. Ez a szabály függetlenül van alkalmazva minden egyes teszthelyen. **Ezt a beállítást javasoljuk, hogy**. Átlagosan körülbelül a hibák 80%-a megszűnik az újrapróbálkozáskor.|
|**Teszt gyakorisága**| Megadja, hogy milyen gyakran fut a teszt mindegyik teszthelyen. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Teszthelyek**| Azok a területek, ahol kiszolgálóink webes kéréseket küldjön az URL-cím a. **Az ajánlott teszthelyszín minimális száma az öt** biztosítására, hogy megkülönböztethesse problémák webhelye a hálózati hibáktól. Legfeljebb 16 hely választható ki.

**Ha az URL-cím nem látható a nyilvános internetről, ha szeretné, szelektív módon, hogy csak a teszt tranzakciók keresztül a tűzfalát megnyitnia**. A rendelkezésre állási teszt ügynökök esetében a tűzfalkivételeket kapcsolatos további információkért tekintse meg a [IP-cím útmutató](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Javasoljuk, hogy több helyről való tesztelés **legalább öt**. Ez a vakriasztások eredményeképpen előfordulhat, hogy egy adott helyen az átmeneti hibák elkerülése érdekében. Ezen kívül található, hogy az optimális konfigurációt, hogy a **teszthelyszín száma megegyezik a riasztási hely küszöbértéke + 2**.

### <a name="success-criteria"></a>Sikeresség feltétele

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időkorlátja** |Csökkenti az értéket, riasztást kap a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | Akkor számít sikeres, a visszaadott állapotkód. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalmi egyezés** | Egy karakterlánc, például "Üdvözöljük!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **Tartalmi egyezés kizárólag angol karakterek támogatottak** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Közel valós idejű riasztások használatát javasoljuk. Ilyen típusú riasztás konfigurálása történik, a rendelkezésre állási teszt létrehozása után.  |
|**Klasszikus** | Már nem javasolt az új rendelkezésre állási teszt klasszikus riasztások használatával.|
|**Riasztási hely küszöbértéke**|Ajánlott legalább 3 vagy 5 helyeket. Az optimális riasztási hely küszöbértéke és tesztelési helyek közötti kapcsolat **riasztási hely küszöbértéke** = **legalább öt teszthelyszín teszthelyszín - 2, száma.**|

## <a name="see-your-availability-test-results"></a>A rendelkezésre állási teszt eredményeinek megtekintése

Rendelkezésre állási teszt eredményeinek vonal- és pontdiagramok diagram nézetekben jeleníthetők meg.

Néhány perc múlva kattintson **frissítése** a teszteredmények megtekintéséhez.

![Sor megtekintése](./media/monitor-web-app-availability/availability-refresh-002.png)

A teszteredményekből nézetben látható a vizsgálati eredmények, amelyek rendelkeznek a diagnosztikai teszt lépéses részletes azokat a mintákat. A tesztmotor tárolja a hibákat tartalmazó tesztek diagnosztikai adatait. A sikeres tesztek esetében a végrehajtások részhalmazainak diagnosztikai adatait is tárolja. Vigye a mutatót a zöld vagy a piros pontokra a teszt megtekintéséhez tesztelése nevét és helyét.

![Sor megtekintése](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Ha további adatokat szeretne látni egy konkrét időszakról, válassza ki a megfelelő tesztet vagy helyet, illetve állítsa rövidebbre az időszakot. A Keresési ablak használatával megtekintheti az összes végrehajtás eredményét, vagy az Analytics lekérdezéseinek használatával egyéni jelentéseket hozhat létre ezen adatok alapján.

## <a name="inspect-and-edit-tests"></a>Tesztek megtekintése és szerkesztése

Szerkesztés, ideiglenesen letilthatja vagy törlése egy teszt kattintson a teszt neve melletti három pontra. Konfigurációs módosítások után végrehajtott módosítások az összes teszt ügynököknek propagálása akár 20 percig is eltarthat.

![Teszt részleteinek megtekintése. Szerkesztheti, és tiltsa le a webes teszt](./media/monitor-web-app-availability/edit.png)

Amikor karbantartást végez a szolgáltatáson, célszerű kikapcsolni a rendelkezésre állási teszteket és az azokhoz kapcsolódó riasztási szabályokat.

## <a name="if-you-see-failures"></a>Ha hibákat lát.

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

A nyers eredményeken kívül is megtekintheti a két fő rendelkezésre állási metrikák [Metrikaböngésző](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Rendelkezésre állás: Tesztek százalékos aránya, hogy sikerült-e, különböző összes végrehajtott teszt közül.
2. Tesztek időtartama: Teszt átlagos időtartama az összes végrehajtott teszt közül.

## <a name="automation"></a>Automation

* [Használjon PowerShell-szkripteket a rendelkezésre állási teszt automatikus beállításához](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Állítson be egy [webhookot](../../azure-monitor/platform/alerts-webhooks.md), amelyet a rendszer riasztás esetén hív meg.

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárításról szóló cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Rendelkezésre állási riasztás](availability-alerts.md)
* [Többlépéses webes tesztek](availability-multistep.md)


