---
title: Gyakori kérdések és terminológiák a Partnerközpont kereskedelmi piactéri elemzéséhez
description: Ismerje meg, hogyan oldhat meg a kereskedelmi piactér elemzésével kapcsolatos gyakori kérdéseket. Tartalmaz egy adatszótárt az elemzési terminológiához.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: b7367e58de818c20723c02a6763b1bf1e3b18f24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251826"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Gyakori kérdések és terminológiák a kereskedelmi piactér elemzéséhez

Ez a cikk a Partnerközpontban található elemzési üzenetekkel kapcsolatos gyakori kérdésekkel foglalkozik, és az elemzési terminológiáról is tartalmaz szótárat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a rész választ ad a Partnerközpontban a **No Analytics Available Yet** üzenetekkel kapcsolatos gyakori kérdésekre.

**Nem tudom megtekinteni az elemzési adataimat a Partnerközpontban. Amikor elérem ezeket az oldalakat, az alábbi üzenet jelenik meg. Miért van ez?**

![Még nincs adat az ajánlataihoz](./media/analytics-faq-no-data.png)

Miért lehet, hogy ezt az üzenetet kapja:

- Jelenleg nincs enek akvizíció a közzétett ajánlatokhoz a piacon. Ez azt jelentheti, hogy ajánlatai a piactéren vannak, és a termék megjelenítési oldalain lévő ügyfelektől szereznek megtekintéseket, de az ügyfelek még nem tettek lépéseket a vásárlásukés telepítésük érdekében.
- Lehet, hogy az ajánlat közzététele folyamatban van, és még nem él. Az ügyfelek csak élő ajánlatokat szerezhetnek be. Az ajánlatok állapotának ellenőrzéséhez olvassa el az Áttekintés az Elemzés irányítópulton című [témakört.](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) További információt a [Kereskedelmi piactér elemzéseinek összefoglaló irányítópultja](./summary-dashboard.md)című témakörben talál.
- Ajánlatai szerepelhetnek **kapcsolatfelvételi**ként , amelyek csak listaajánlatok, és a piactéren lévő ügyfelek nem vásárolhatnak meg. Bár ezek az ajánlatok érdeklődőket hoznak létre, és meg vannak osztva Önnel, a rendelések nem jönnek létre ezekhez az ajánlatokhoz, mivel nem vásárolhatók meg. Az ajánlatlista típusának ellenőrzéséhez lépjen a beállítási oldalra.

**Tudom, hogy vannak elemzési adataim, de az alábbi üzenet jelenik meg:**

![Nincs adat az adott dátumtartományra vonatkozóan](./media/analytics-faq-data-range.png)

Ha ezt az üzenetet kapja, az azt jelenti, hogy rendelkezik elemzési adatokkal, de a kiválasztott dátumtartományhoz nem. A 2010 óta eltelt adatok megtekintéséhez válasszon másik dátumtartományt vagy egyéni dátumtartományt. További információt a [kereskedelmi piactér elemzéseinek Összefoglaló irányítópultjának](./summary-dashboard.md)Dátumtartomány szakaszában talál.

## <a name="dictionary-of-data-terms"></a>Adatkifejezések szótára

| Attribútum neve | Jelentések | Meghatározás|
|---|---|---|
| Azure-licenctípus | Vevő, rendelés | Az ügyfelek által az Azure megvásárlásához használt licencszerződés típusa. Más néven Csatorna |
| Azure licenctípusa: Felhőszolgáltató | Vevő, rendelés | A végfelhasználó az Azure-t és a Marketplace-ajánlatot a felhőszolgáltatón keresztül szerzi be, amely viszonteladóként működik.|
| Azure licenctípusa: Enterprise | Vevő, rendelés | A végfelhasználó az Azure-t és a Marketplace-ajánlatot egy közvetlenül a Microsofttal aláírt nagyvállalati szerződésen keresztül szerzi be.|
| Azure licenctípus: Nagyvállalati viszonteladón keresztül  | Vevő, rendelés | A végfelhasználó az Azure-t és a Marketplace-ajánlatot egy viszonteladón keresztül szerzi be, aki megkönnyíti a Microsofttal kötött nagyvállalati szerződésüket.|  |
| Azure licenctípusa: Pay as You Go| Vevő, rendelés | A végfelhasználó az Azure-t és a Marketplace-ajánlatot egy közvetlenül a Microsofttal aláírt "Csak annyiban fizetendő" szerződés keretében szerzi be az Azure-t és a Marketplace-ajánlatot.||
| Felhőpéldány neve| Rendelés| Az a Microsoft Cloud, amelyben a virtuális gép központi telepítése történt.||
| Felhőpéldány neve: Azure Global| Rendelés| A nyilvános globális Microsoft-felhő.|| |
| Felhőpéldány neve: Azure Government | Rendelés| A kormányspecifikus Microsoft-felhők a következő kormányok egyikéhez: Kína, Németország vagy az Amerikai Egyesült Államok.| |
| Ügyfél városa| Ügyfél| Az ügyfél által megadott városnév. A város eltérhet az ügyfél Azure-előfizetésében lévő várostól.||
| Ügyfél kommunikációs nyelve  | Ügyfél| Az ügyfél által a kommunikációhoz előnyben részesített nyelv.||
| Vevő vállalatneve | Vevő, rendelés | A vevő által megadott vállalatnév. A név eltérhet az ügyfél Azure-előfizetésében lévő várostól.|  |
| Vevő országa | Vevő, rendelés | A vevő által megadott országnév. Ország eltérő lehet, mint az ország az ügyfél Azure-előfizetés.|  |
| Ügyfél e-mail címe| Ügyfél| A végfelhasználó által megadott e-mail cím. Az e-mail eltérhet az ügyfél Azure-előfizetésében található e-mail címtől.||
| Vevő utóneve| Ügyfél| A vevő által megadott név. A név eltérhet az ügyfél Azure-előfizetésében megadott névtől.| |
| Ügyfél azonosítója | Vevő, rendelés | A vevőhöz rendelt egyedi azonosító. Előfordulhat, hogy egy ügyfél nulla vagy több Azure Piactér-előfizetéssel rendelkezik.|  |
| Vevő irányítószáma  | Ügyfél| A vevő által megadott irányítószám. A kód eltérhet az ügyfél Azure-előfizetésében megadott irányítószámtól.| |
| Vevő állapota| Ügyfél| Az ügyfél által megadott állapot (cím). Az állam eltérhet az ügyfél Azure-előfizetésében megadott állapottól.| |
| Beszerzés dátuma| Ügyfél| Az első nap, amikor az ügyfél megvásárolta az Ön által közzétett ajánlatot.| |
| Elveszett dátum| Ügyfél| Az utolsó dátum, amikor a vevő visszavonta a korábban megvásárolt utolsó ajánlatot.||
| Új vevő  | Rendelés| Az érték azonosítja az új ügyfél megszerzése egy vagy több ajánlatot az első alkalommal (vagy nem). Az érték "Igen" lesz, ha ugyanabban a naptári hónapban a "Beszerzett dátum" érték. Az érték "Nem" lesz, ha az ügyfél a naptári hónap előtt megvásárolta az Ön ajánlatait. |
| Az előnézeti termékváltozat| Rendelés| Az érték tudatja Önvel, ha a termékváltozatot "előnézetként" címkézte. Érték lesz "Igen", ha a termékváltozat megfelelően van címkézve, és csak az Ön által engedélyezett Azure-előfizetések üzembe helyezheti és használhatja ezt a lemezképet. Az érték "Nem" lesz, ha a termékváltozat nincs "előzetes verzióként" azonosítva.  |
| A promóciós kapcsolat opt-in| Ügyfél| Az érték tudatja Önnel, ha az ügyfél proaktív módon engedélyezte a közzétevők promóciós kapcsolatát. Jelenleg nem mutatjuk be a lehetőséget az ügyfeleknek, ezért nemet jelöltünk meg. A funkció telepítése után ennek megfelelően elkezdjük a frissítést.|
| Piactéri licenc típusa| Rendelés| A Marketplace-ajánlat számlázási módszere.||
| Piactérlicenc típusa: Számlázás az Azure-on keresztül| Rendelés| A Microsoft az Ön ügynöke ehhez a Marketplace-ajánlathoz, és az Ön nevében számláz az ügyfeleknek. (PayG hitelkártya vagy vállalati számla)||
| Piactéri licenc típusa: Hozza magával a saját licencét | Rendelés| A virtuális gép telepítése szükséges egy licenckulcsot az ügyfél által biztosított. A Microsoft nem számlázaz ügyfeleknek, hogy ajánlataikat ily módon a piactéren keresztül listázták.||
| Piactéri licenc típusa: Ingyenes| Rendelés| Az ajánlat úgy van beállítva, hogy minden felhasználó számára ingyenes legyen. A Microsoft nem számít fel díjat az ügyfeleknek az ajánlat használatáért.||
| Piactéri licenc típusa: A Microsoft mint viszonteladó  | Rendelés| A Microsoft az Ön viszonteladója ehhez a Marketplace-ajánlathoz.|  |
| Marketplace-előfizetés azonosítója | Vevő, rendelés | Az ügyfél által a Marketplace-ajánlat megvásárlásához használt Azure-előfizetéshez társított egyedi azonosító. Az azonosító korábban az Azure-előfizetés GUID azonosítója volt.||
| Offer Name  | Rendelés| A Marketplace-ajánlat neve.|| |
| Ajánlat típusa  | Rendelés| A Microsoft Piactér ajánlatának típusa.|||
| Ajánlat típusa: Felügyelt alkalmazás  | Rendelés | Használja az Azure-alkalmazást: felügyelt alkalmazásajánlat típusa, ha a következő feltételek szükségesek: Egy előfizetés-alapú megoldást telepít az ügyfél számára egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával. Ön vagy ügyfele megköveteli, hogy a megoldást egy partner nek kell kezelnie. |
| Ajánlat típusa: Azure-alkalmazás| Rendelés | Használja az Azure Application megoldássablon ajánlattípusát, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy egyszerű virtuális gépen kívül.||
| Ajánlat típusa: Consulting Service| Rendelés| Az Azure Marketplace-en található tanácsadási szolgáltatások segítségével összekapcsolhatja ügyfeleit az Azure-használattámogatásával és bővítésével.| |
| Ajánlat típusa: Konténer | Rendelés| Használja a Container-ajánlat típusát, ha a megoldás egy Kubernetes-alapú Azure-tárolószolgáltatásként kiépített Docker-tárolórendszerkép.||
| Ajánlat típusa: Dynamics 365 Üzleti Központ| Rendelés| Akkor használja ezt az ajánlattípust, ha a megoldás integrálva van a Dynamics 365 for Finance and Operations alkalmazással| |
| Ajánlat típusa: Dynamics 365 for Customer Engagement | Rendelés| Akkor használja ezt az ajánlattípust, ha a megoldás integrálva van a Dynamics 365 for Customer Engagement alkalmazással.||
| Ajánlat típusa: IoT edge modul | Rendelés| Az Azure IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Az Azure Stream Analytics), a harmadik féltől származó szolgáltatásokat vagy a saját megoldásspecifikus kódot. |
| Ajánlat típusa: Power BI alkalmazás | Rendelés| A Power BI alkalmazásajánlat-típusának használata a Power BI-val integrált alkalmazás telepítésekor.|  |
| Ajánlat típusa: SaaS alkalmazás| Rendelés| A SaaS-alkalmazásajánlat típusával lehetővé teszi az ügyfél számára, hogy előfizetésként megvásárolja a SaaS-alapú, technikai megoldást.||
| Ajánlat típusa: Virtuális gép | Rendelés| Használja a virtuális gép ajánlat típusát, amikor üzembe helyez egy virtuális berendezést az ügyfélhez társított előfizetéshez.||
| Ajánlat típusa: Visual Studio Piactér bővítmény  | Rendelés| Az Azure DevOps-bővítményfejlesztők számára korábban elérhető ajánlattípus. Az Azure DevOps-bővítményfejlesztők közvetlenül az ügyfeleknek értékesíthetik a bővítményt. A bővítményajánlatok fizetősként konfigurálhatók, vagy próbaverzióval is konfigurálhatók. |
| Rendelés érvénytelenítési dátuma| Rendelés| A Marketplace-rendelés érvénytelenítésének dátuma.||
| Rendelésazonosító| Rendelés| A Marketplace-szolgáltatás vevői rendelésének egyedi azonosítója. A virtuális gép használatalapú ajánlatai nincsenek rendeléshez társítva.| |
| Rendelés beszerzési dátuma| Rendelés| A Marketplace-rendelés létrehozásának dátuma.|||
| Megrendelés állapota| Rendelés| A Marketplace-rendelés állapota az adatok utolsó frissítésekor.|     |
| Rendelés állapota: Aktív  | Rendelés| A vevő megvásároltegy rendelést, és nem mondta vissza a rendelést.|         |
| Rendelés állapota: visszavonva | Rendelés| A vevő korábban megvásárolt egy rendelést, és ezt követően visszavonta a rendelést.||
| Szolgáltató e-mail címe| Ügyfél| A Microsoft és a végfelhasználó közötti kapcsolatban részt vevő szolgáltató e-mail címe. Ha az ügyfél viszonteladón keresztül enterprise, akkor ez lesz a viszonteladó. Ha egy felhőszolgáltató (CSP) is részt vesz, ez lesz a kripta.|
| Szolgáltató neve| Ügyfél| A Microsoft és a végfelhasználó közötti kapcsolatban részt vevő szolgáltató neve. Ha az ügyfél viszonteladón keresztül enterprise, akkor ez lesz a viszonteladó. Ha egy felhőszolgáltató (CSP) is részt vesz, ez lesz a kripta.|
| SKU| Rendelés| A termékváltozat neve a közzététel során meghatározott aknak. Egy ajánlat sok termékváltozattal rendelkezhet, de egy termékváltozat csak egyetlen ajánlathoz társítható.||
| Próba időszak záró dátuma| Rendelés| A rendelés próbaidőszakának kezdőnapja vagy vége.||

## <a name="next-steps"></a>További lépések

- A Partnerközpont kereskedelmi piacterén elérhető elemzési jelentések áttekintését [a Partnerközpont Kereskedelmi piacterületén található Analytics for the commercial marketplace című témakörben találja.](./analytics.md)
- Az ajánlat marketplace-tevékenységét összegző összesített adatok grafikonjait, trendjeit és értékeit lásd: [Összefoglaló irányítópult a kereskedelmi piactér-elemzésben.](./summary-dashboard.md)
- A megrendelésekgrafikus és letölthető formátumban című témakörben talál további információt [a Rendelések irányítópultja a kereskedelmi piactér elemzésében.](./orders-dashboard.md)
- A virtuális gép (VM) kínál használati és forgalmi díjas számlázási metrikák, lásd: [Használati irányítópult a kereskedelmi piactér analytics.](./usage-dashboard.md)
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket is, a [Vevői irányítópult a kereskedelmi piactér elemzésében](./customer-dashboard.md)című témakörben talál.
- Az elmúlt 30 nap letöltési kérelmeinek listáját a [Letöltések irányítópultja a kereskedelmi piactér-elemzésben című témakörben tartalmazza.](./downloads-dashboard.md)
- Az Azure Marketplace-en és az AppSource-on az ajánlatokra vonatkozó ügyfél-visszajelzések összevont nézetét a [Minősítések és véleményezések irányítópultja című témakörben tekintheti meg a kereskedelmi piactér-elemzésekben.](./ratings-reviews.md)
