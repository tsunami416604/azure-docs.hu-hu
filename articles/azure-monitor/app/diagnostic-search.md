---
title: Keresés használata az Azure Application Insightsban | Microsoft dokumentumok
description: Keressen és szűrje a webalkalmazás által küldött nyers telemetriai adatokat.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275983"
---
# <a name="using-search-in-application-insights"></a>Keresés az Application Insightsban

A keresés az [Application Insights](../../azure-monitor/app/app-insights-overview.md) szolgáltatása, amelyet az egyes telemetriai elemek, például oldalmegtekintések, kivételek vagy webes kérések kereséséhez és feltárásához használhat. És megtekintheti a naplónyomait és a kódolt eseményeket.

(Az adatokkal kapcsolatos összetettebb lekérdezésekhez használja az [Analytics programot.)](../../azure-monitor/log-query/get-started-portal.md)

## <a name="where-do-you-see-search"></a>Hol látja a Keresés?

### <a name="in-the-azure-portal"></a>Az Azure Portalon

A diagnosztikai keresést az alkalmazás áttekintése lapon (a felső sávon található) vagy a bal oldalon található vizsgálat alapján nyithatja meg.

![Keresés lap](./media/diagnostic-search/view-custom-events.png)

Az Eseménytípusok legördülő menüjében megtekintheti a telemetriai elemek listáját– kiszolgálói kérelmeket, oldalnézeteket, kódolt egyéni eseményeket és így tovább. Az eredmények listájának tetején egy összefoglaló diagram látható, amely az események számát mutatja az idő múlásával.

Az új események bekerüléséhez kattintson a legördülő menüre vagy a Frissítés parancsra.

### <a name="in-visual-studio"></a>Visual Studióban

A Visual Studio-ban van egy Application Insights Search ablak is. Ez a leghasznosabb a hibakeresés által létrehozott telemetriai események megjelenítéséhez. De azt is megjelenítheti a közzétett alkalmazásból az Azure Portalon gyűjtött eseményeket.

Nyissa meg a Keresés ablakot a Visual Studio-ban:

![Visual Studio nyitott Application Insights keresés](./media/diagnostic-search/32.png)

A Keresés ablak a webportálhoz hasonló funkciókkal rendelkezik:

![A Visual Studio Application Insights keresési ablaka](./media/diagnostic-search/34.png)

A Művelet nyomon követése lap kérés vagy oldalnézet megnyitásakor érhető el. A "művelet" olyan eseménysorozat, amely egyetlen kérelemhez vagy oldalnézethez van társítva. Például a függőségi hívások, kivételek, nyomkövetési naplók és egyéni események egyetlen művelet részét képezhetik. A Művelet nyomon követése lapon grafikusan láthatók az események időzítése és időtartama a kéréshez vagy az oldalnézethez viszonyítva.

## <a name="inspect-individual-items"></a>Egyedi cikkek vizsgálata

Jelölje ki a telemetriai elemeket a kulcsmezők és a kapcsolódó elemek megtekintéséhez.

![Képernyőkép egy egyéni függőségi kérelemről](./media/diagnostic-search/telemetry-item.png)

Ezzel elindítja a végpontok között a tranzakciók részletei nézetet.

## <a name="filter-event-types"></a>Eseménytípusok szűrése

Nyissa meg az Eseménytípusok legördülő menüjét, és válassza ki a megtekinteni kívánt eseménytípusokat. (Ha később vissza szeretné állítani a szűrőket, kattintson az Alaphelyzet gombra.)

Az eseménytípusok a következők:

* **Nyomkövetési** - [diagnosztikai naplók,](../../azure-monitor/app/asp-net-trace-logs.md) beleértve a TrackTrace, log4Net, NLog és System.Diagnostic.Trace hívásokat.
* **Kérelem** – A kiszolgálóalkalmazás által fogadott HTTP-kérelmek, beleértve az oldalakat, parancsfájlokat, képeket, stílusfájlokat és adatokat. Ezek az események a kérés- és válaszáttekintő diagramok létrehozásához használatosak.
* **A** - webes ügyfél által küldött[oldalnézet-telemetriai adatok,](../../azure-monitor/app/javascript.md)amelyek lapnézeti jelentések létrehozására szolgálnak.
* **Egyéni esemény** – Ha a használat [figyelése](../../azure-monitor/app/api-custom-events-metrics.md)érdekében beillesztett hívásokat a TrackEvent() programba, itt kereshet bennük.
* **Kivétel** – Nem fogott [kivételek a kiszolgálón,](../../azure-monitor/app/asp-net-exceptions.md)és azok, amelyeket a TrackException() használatával naplóz.
* **FüggőségI** - [hívások a kiszolgálóalkalmazásból](../../azure-monitor/app/asp-net-dependencies.md) más szolgáltatásokhoz, például REST API-khoz vagy adatbázisokhoz, valamint AJAX-hívásokhoz az [ügyfélkódból.](../../azure-monitor/app/javascript.md)
* **Elérhetőség** – A [rendelkezésre állási tesztek eredményei.](../../azure-monitor/app/monitor-web-app-availability.md)

## <a name="filter-on-property-values"></a>Tulajdonságértékek szűrése

Az eseményeket a tulajdonságaik értékei alapján szűrheti. Az elérhető tulajdonságok a kiválasztott eseménytípusoktól függenek. Kattintson a szűrő ikonra ![Szűrő ikon](./media/diagnostic-search/filter-icon.png) kezdeni.

Ha egy adott tulajdonság nem ad értéket, annak ugyanaz a hatása, mint az összes érték kiválasztásának. Kikapcsolja a szűrést azon a tulajdonságon.

Figyelje meg, hogy a szűrőértékektől jobbra lévő számok azt mutatják, hogy hány előfordulás van az aktuális szűrt készletben.

## <a name="find-events-with-the-same-property"></a>Azonos tulajdonsággal rendelkező események keresése

Ha az azonos tulajdonságértékű elemeket meg szeretné találni, írja be a keresősávba, vagy kattintson a jelölőnégyzetre, amikor átnézi a tulajdonságokat a szűrőlapon.

![Kattintson egy tulajdonság jelölőnégyzetére a Szűrő lapon](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Keresés az adatokközött

> [!NOTE]
> Összetettebb lekérdezések írásához nyissa meg a [**Naplók (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) a Keresési panel tetejéről.
>

A tulajdonságértékek bármelyikében kereshet kifejezéseket. Ez akkor hasznos, ha [egyéni eseményeket](../../azure-monitor/app/api-custom-events-metrics.md) írt tulajdonságértékekkel.

Érdemes lehet beállítani egy időtartományt, mivel a rövidebb tartományban végzett keresések gyorsabbak.

![Diagnosztikai keresés megnyitása](./media/diagnostic-search/search-property.png)

Teljes szavak keresése, nem karakterláncok. Speciális karakterek csatolása idézőjelek segítségével.

| Sztring | *Nem* található | Találat |
| --- | --- | --- |
| HomeController.A |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Egyesült Államok|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Az alábbiakat használhatja a keresési kifejezések:

| Mintalekérdezés | Hatás |
| --- | --- |
| `apple` |Az időtartományban található összes olyan esemény megkeresése, amelynek mezői tartalmazzák az "alma" szót |
| `apple AND banana` <br/>`apple banana` |Keresse meg a mindkét szót tartalmazó eseményeket. Használja a nagy "ÉS", nem "és". <br/>Rövid forma. |
| `apple OR banana` |Keresse meg azokat az eseményeket, amelyek mindkét szót tartalmazzák. Használja az "OR", nem "vagy". |
| `apple NOT banana` |Keresse meg az okat tartalmazó eseményeket, de a másikat nem. |

## <a name="sampling"></a>Mintavételezés

Ha az alkalmazás nagy mennyiségű telemetriai adatokat hoz létre (és a ASP.NET SDK 2.0.0-beta3 vagy újabb verzióját használja), az adaptív mintavételi modul automatikusan csökkenti a portálra küldött kötetet az események nek csak egy reprezentatív történek elküldésével. Az ugyanahhoz a kérelemhez kapcsolódó események et azonban csoportként kijelölik vagy nem jelölik ki, így navigálhat a kapcsolódó események között.

[Ismerkedés a mintavételezéssel](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Munkaelem létrehozása

Létrehozhat egy hibát a GitHubon vagy az Azure DevOps-ban bármely telemetriai elem részleteivel.

Lépjen a tranzakció végpontok között a tranzakciók részletes nézetébe, kattintson bármelyik telemetriai elemre, majd válassza **a Munkaelem létrehozása lehetőséget.**

![Kattintson az Új munkaelem gombra, szerkesztse a mezőket, majd kattintson az OK gombra.](./media/diagnostic-search/work-item.png)

Az első alkalommal, amikor ezt teszi, a rendszer megkéri, hogy konfigurálja az Azure DevOps-szervezetre és -projektre mutató hivatkozást.

(A hivatkozást a Munkaelemek lapon is beállíthatja.)

## <a name="send-more-telemetry-to-application-insights"></a>További telemetriai adatok küldése az Application Insightsba

Az Application Insights SDK által küldött beépített telemetriai adatok mellett a következőket teheti:

* Rögzítsd a naplónyomait a [.NET](../../azure-monitor/app/asp-net-trace-logs.md) vagy a [Java](../../azure-monitor/app/java-trace-logs.md)rendszerben lévő kedvenc naplózási keretrendszerből. Ez azt jelenti, hogy kereshet a naplónyomkövetések között, és összefügghet oldalnézetekkel, kivételekkel és egyéb eseményekkel.
* [Kódot írhat](../../azure-monitor/app/api-custom-events-metrics.md) egyéni események, oldalmegtekintések és kivételek küldéséhez.

[Megtudhatja, hogyan küldhet naplókat és egyéni telemetriai adatokat az Application Insights.](../../azure-monitor/app/asp-net-trace-logs.md)

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Mennyi adatot őriz meg a rendszer?

Lásd a [Korlátok összegzését](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hogyan láthatom a POST-adatokat a szerverkéréseimben?

Nem naplózzuk automatikusan a POST adatokat, de használhatja [a TrackTrace-et vagy a naplóhívásokat.](../../azure-monitor/app/asp-net-trace-logs.md) Helyezze a POST adatokat az üzenet paraméterbe. Az üzenetre nem szűrhet ugyanúgy, mint a tulajdonságokat, de a méretkorlát hosszabb.

## <a name="next-steps"></a><a name="add"></a>További lépések

* [Összetett lekérdezések írása az Analytics szolgáltatásban](../../azure-monitor/log-query/get-started-portal.md)
* [Naplók és egyéni telemetriai adatok küldése az Application Insightsba](../../azure-monitor/app/asp-net-trace-logs.md)
* [Rendelkezésre állási és válaszidő-tesztek beállítása](../../azure-monitor/app/monitor-web-app-availability.md)
* [hibaelhárítással](../../azure-monitor/app/troubleshoot-faq.md)
