---
title: Kereskedelmi piactér Analytics – gyakori kérdések & terminológia – partneri központ
description: Választ kaphat a partner Center kereskedelmi Marketplace Analytics szolgáltatásával kapcsolatos gyakori kérdésekre. Ez a cikk az Analytics-terminológia adatszótárát tartalmazza.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: eb91e498fa757c5cec7bd466c60aaf7e8758304c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744567"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Kereskedelmi piactér analitikai terminológiája és gyakori kérdések

Ez a cikk a partner Center Analytics-üzeneteivel kapcsolatos gyakori kérdéseket tárgyalja, és az elemzési terminológia szótárát is tartalmazza.

## <a name="common-questions"></a>Gyakori kérdések

**Nem tudom megtekinteni az elemzési adataikat a partner Centerben. Amikor elérem ezeket az oldalakat, az alábbi üzenet jelenik meg. Miért van ez?**

![Még nem szerepelnek az ajánlatok adatai](./media/analytics-faq-no-data.png)

Miért lehetséges a következő üzenet beszerzése:

- Jelenleg nem léteznek beszerzések a közzétett ajánlatokhoz a piactéren. Ez azt is jelentheti, hogy az ajánlatok a piactéren működnek, és a termékek megjelenítési oldalain lévő ügyfelektől származó nézeteket szereznek, de az ügyfelek még nem tettek lépéseket a vásárlásra és a telepítésre.
- Lehetséges, hogy az ajánlat közzététele folyamatban van, és még nem él. Az ügyfelek csak élő ajánlatokat szerezhetnek be. Az ajánlatok állapotának vizsgálatához tekintse meg az Áttekintés az [irányítópult elemzése](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)című témakört. További információ: [Összefoglalás irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- Az ajánlatok **felvehetik Önnel a kapcsolatot**, csak a listában szereplő ajánlatokat, és nem vásárolhatják meg az ügyfelek a piactéren. Bár ezek az ajánlatok létrehoznak érdeklődőket, és megosztották Önnel, a megrendelések nem jönnek létre ezekhez az ajánlatokhoz, mert nem vásárolhatók meg. Az ajánlat-lista típusának megadásához lépjen a telepítés lapra.

**Tudom, hogy elemzési adataim vannak, de az alábbi üzenet jelenik meg:**

![Nincsenek adatok a megadott dátumtartomány számára](./media/analytics-faq-data-range.png)

Ha ezt az üzenetet kapja, az azt jelenti, hogy rendelkezik elemzési adatokkal, de a kiválasztott dátumtartomány nem rendelkezik adatokkal. Válasszon másik dátumtartományt vagy egyéni dátumtartományt a 2010 óta megjelenő adatok megtekintéséhez. További információkért tekintse [meg a kereskedelmi piactér Analytics összefoglaló irányítópultjának](./summary-dashboard.md)dátumtartomány szakaszát.

## <a name="dictionary-of-data-terms"></a>Az adatszótár kifejezése

| Attribútum neve | Jelentések | Definíció|
|---|---|---|
| Azure-licenc típusa | Ügyfél, megrendelés | Az ügyfelek által az Azure megvásárlásához használt licencelési szerződés típusa. Más néven csatorna |
| Azure-licenc típusa: felhőalapú megoldás szolgáltatója | Ügyfél, megrendelés | A végfelhasználók az Azure-t és a Piactért a felhőalapú megoldás szolgáltatóján keresztül szerzik be, aki viszonteladóként működik.|
| Azure-licenc típusa: Enterprise | Ügyfél, megrendelés | A végfelhasználók az Azure-t és a Piactéri ajánlatokat egy Nagyvállalati Szerződéson keresztül szerzik be, közvetlenül a Microsofttal.|
| Azure-licenc típusa: Enterprise a viszonteladón keresztül  | Ügyfél, megrendelés | A végfelhasználók az Azure-t és a Piactéri ajánlatokat egy viszonteladón keresztül szerzik be, amely megkönnyíti a Nagyvállalati Szerződés a Microsofttal.|  |
| Azure-licenc típusa: utólagos fizetés| Ügyfél, megrendelés | A végfelhasználók az Azure-t és a Piactéri ajánlatokat a "Pay as go" szerződés keretében szerzik be közvetlenül a Microsofttal.||
| Felhőbeli példány neve| Rendelés| Az a Microsoft Cloud, amelyben a virtuális gép üzembe helyezése megtörtént.||
| Felhőbeli példány neve: Azure Global| Rendelés| A nyilvános globális Microsoft-felhő.|| |
| Felhőbeli példány neve: Azure Government | Rendelés| Kormányzati specifikus Microsoft-felhők a következő kormányok egyikére: Kína, Németország vagy az Amerikai Egyesült Államok Egyesült Államok.| |
| Customer City| Ügyfél| Az ügyfél által megadott város neve. A város eltérő lehet az ügyfél Azure-előfizetésében lévő várostól.||
| Ügyfél-kommunikációs nyelv  | Ügyfél| Az ügyfél által a kommunikációhoz előnyben részesített nyelv.||
| Ügyfél vállalatának neve | Ügyfél, megrendelés | Az ügyfél által megadott cég neve. A név nem lehet más, mint az ügyfél Azure-előfizetésének városa.|  |
| Ügyfél országa/régiója | Ügyfél, megrendelés | Az ügyfél által megadott ország/régió neve. Az ország/régió eltérő lehet az ügyfél Azure-előfizetésében lévő országgal vagy régióval.|  |
| Ügyfél e-mail-címe| Ügyfél| A végfelhasználó által megadott e-mail-cím. Az e-mail nem lehet az ügyfél Azure-előfizetésében szereplő e-mail-cím.||
| Ügyfél utóneve| Ügyfél| Az ügyfél által megadott név. A név nem lehet azonos az ügyfél Azure-előfizetésében megadott névvel.| |
| Ügyfél azonosítója | Ügyfél, megrendelés | Az ügyfélhez rendelt egyedi azonosító. Előfordulhat, hogy egy ügyfél nulla vagy több Azure Marketplace-előfizetéssel rendelkezik.|  |
| Ügyfél postai irányítószáma  | Ügyfél| Az ügyfél által megadott postai irányítószám. A kód különbözhet az ügyfél Azure-előfizetésében megadott postai kóddal.| |
| Ügyfél állapota| Ügyfél| Az ügyfél által megadott állapot (címe). Az állapot különbözhet az ügyfél Azure-előfizetésében megadott állapottól.| |
| Beszerzés dátuma| Ügyfél| Az első dátum, amikor az ügyfél az Ön által közzétett összes ajánlatot megvásárolta.| |
| Elveszett dátum| Ügyfél| Az utolsó időpont, amikor az ügyfél megszakította a korábban megvásárolt összes ajánlatot.||
| Új ügyfél  | Rendelés| Az érték azonosítja azt az új ügyfelet, amely első alkalommal szerez be egy vagy több ajánlatot (vagy nem). Az érték "yes" lesz, ha a "date beszerzett" dátummal megegyező naptári hónapon belül van. Az érték "nem" lesz, ha az ügyfél a jelentett naptári hónap előtt megvásárolta az ajánlatait. |
| Előzetes verziójú SKU| Rendelés| Az érték tudatja Önnel, ha az SKU-t "Preview"-ként jelölte meg. Az érték "yes" lesz, ha az SKU-t ennek megfelelően címkézték, és csak az Ön által jóváhagyott Azure-előfizetések telepíthetők és használhatók. Az érték "nem" lesz, ha az SKU nem "Preview"-ként lett azonosítva.  |
| A promóciós kapcsolatfelvételi lehetőség| Ügyfél| Az érték tudatja Önnel, hogy az ügyfél proaktív módon választotta-e a közzétevők számára a promóciós kapcsolatfelvételt. Jelenleg nem vesszük igénybe az ügyfeleket, ezért a táblán a "nem" jelölés szerepel. A szolgáltatás üzembe helyezését követően a frissítés ennek megfelelően kezdődik.|
| Marketplace-licenc típusa| Rendelés| A Piactéri ajánlat számlázási módszere.||
| Marketplace-licenc típusa: számlázás az Azure-ban| Rendelés| A Microsoft az Ön ügynöke ehhez a Piactéri ajánlathoz, és az Ön nevében számláz ügyfeleket. (TB hitelkártya vagy vállalati számla)||
| Marketplace-licenc típusa: saját licenc használata | Rendelés| A virtuális gépnek szüksége van az ügyfél által az üzembe helyezéshez biztosított licenckulcs használatára. A Microsoft nem számlázza az ügyfeleket az ajánlatuk ezen a piactéren való listázásához.||
| Marketplace-licenc típusa: ingyenes| Rendelés| Az ajánlat úgy van konfigurálva, hogy az összes felhasználó számára szabadon legyen. A Microsoft nem számláz az ügyfeleknek az ajánlat használatáért.||
| Marketplace-licenc típusa: Microsoft as viszonteladó  | Rendelés| A Microsoft a piactér-ajánlat viszonteladója.|  |
| Piactér-előfizetés azonosítója | Ügyfél, megrendelés | Az Azure-előfizetéshez társított egyedi azonosító, amelyet az ügyfél a Piactéri ajánlat megvásárlásához használt. Az azonosító korábban az Azure-előfizetés GUID-azonosítója volt.||
| Offer Name  | Rendelés| A Piactéri ajánlat neve.|| |
| Ajánlat típusa  | Rendelés| Az Microsoft Marketplace ajánlat típusa.|||
| Ajánlat típusa: felügyelt alkalmazás  | Sorrendben | Használja az Azure-alkalmazást: felügyelt alkalmazási ajánlat típusa a következő feltételek teljesülése esetén: egy vagy több virtuális gép vagy egy teljes IaaS-alapú megoldás használatával telepítheti az ügyfél előfizetéses megoldását. Ön vagy az ügyfele megköveteli, hogy a megoldást egy partnernek kell kezelnie. |
| Ajánlat típusa: Azure-alkalmazás| Sorrendben | Használja az Azure-alkalmazás megoldás-sablonjának ajánlat típusát, ha a megoldáshoz egy egyszerű virtuális gépen kívül további üzembe helyezési és konfigurálási automatizálásra van szükség.||
| Ajánlat típusa: tanácsadási szolgáltatás| Rendelés| Az Azure piactéren elérhető tanácsadói szolgáltatások segítségével az ügyfeleket a szolgáltatásokkal összekapcsolhatják az Azure használatának támogatásához és kiterjesztéséhez.| |
| Ajánlat típusa: tároló | Rendelés| Ha a megoldás egy, a Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép, akkor használja a Container ajánlat típusát.||
| Ajánlat típusa: Dynamics 365 Business Central| Rendelés| Akkor használja ezt az ajánlatot, ha a megoldás integrálva van a Dynamics 365 for Finance and Operations szolgáltatással| |
| Ajánlat típusa: Dynamics 365 for Customer engagement | Rendelés| Akkor használja ezt az ajánlatot, ha a megoldás integrálva van a Dynamics 365-mel a Customer engagement szolgáltatáshoz.||
| Ajánlat típusa: IoT Edge modul | Rendelés| A Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| Ajánlat típusa: Power BI alkalmazás | Rendelés| Ha a Power BI integrált alkalmazást telepít központilag, használja a Power BI alkalmazási ajánlat típusát.|  |
| Ajánlat típusa: SaaS-alkalmazás| Rendelés| A SaaS app Offer típus használatával engedélyezheti az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldását.||
| Ajánlat típusa: virtuális gép | Rendelés| Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát.||
| Ajánlat típusa: Visual Studio Marketplace bővítmény  | Rendelés| Az ajánlat típusa korábban elérhető az Azure DevOps bővítmény fejlesztői számára. Az Azure DevOps Extension fejlesztőinek továbbítják a bővítményt közvetlenül az ügyfeleknek. A bővítmények a kifizetett vagy próbaverzióval is konfigurálhatók. |
| Megrendelés megszakításának dátuma| Rendelés| A Piactéri rendelés megszakításának dátuma.||
| Rendelésazonosító| Rendelés| A Piactéri szolgáltatáshoz tartozó ügyfél-rendelés egyedi azonosítója. A virtuális gépek használaton alapuló ajánlatai nincsenek rendeléshez társítva.| |
| Megrendelés vásárlásának dátuma| Rendelés| A Piactéri rendelés létrehozásának dátuma.|||
| Megrendelés állapota| Rendelés| A Piactéri megrendelés állapota az utolsó frissítés alkalmával.|     |
| Megrendelés állapota: aktív  | Rendelés| Az ügyfél megvásárolt egy rendelést, és nem szakította meg a rendelését.|         |
| Megrendelés állapota: megszakítva | Rendelés| Az ügyfél korábban megvásárolt egy rendelést, és később megszakította a rendelését.||
| Szolgáltatói E-mail cím| Ügyfél| A Microsoft és a végfelhasználó közötti kapcsolatban részt vevő szolgáltató e-mail-címe. Ha az ügyfél a viszonteladón keresztüli vállalat, akkor ez lesz a viszonteladó. Ha egy felhőalapú megoldás-szolgáltató (CSP) is részt vesz, ez lesz a CSP.|
| Szolgáltató neve| Ügyfél| A Microsoft és a végfelhasználó közötti kapcsolatban részt vevő szolgáltató neve. Ha az ügyfél a viszonteladón keresztüli vállalat, akkor ez lesz a viszonteladó. Ha egy felhőalapú megoldás-szolgáltató (CSP) is részt vesz, ez lesz a CSP.|
| SKU| Rendelés| A közzététel során definiált SKU-név. Egy ajánlat több SKU-val is rendelkezhet, de egy SKU csak egyetlen ajánlattal társítható.||
| Próbaverzió befejezési dátuma| Rendelés| Az a dátum, ameddig a rendelés próbaidőszaka lejár vagy véget ér.||

## <a name="next-steps"></a>További lépések

- A partner Center kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [a partner Center kereskedelmi piacának elemzései](./analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a [kereskedelmi piactér elemzésének minősítések és értékelések irányítópultját](./ratings-reviews.md)
