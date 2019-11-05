---
title: Mi az az Azure Reservations?
description: Megismerheti az Azure Reservationst és annak díjszabását a virtuális gépek, az SQL Database-adatbázisok, az Azure Cosmos DB és más erőforrások költségeinek csökkentéséhez.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 02a4ccc1bd7c18c36a7203f7d81cce8923ecf59a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499244"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Az Azure Reservationsszel csökkentheti költségeit, ha egy vagy három évre előre kötelezettséget vállal a virtuális gépekre, az SQL Database számítási kapacitására, az Azure Cosmos DB átviteli sebességére vagy más Azure-erőforrásokra. A kötelezettségvállalással kedvezményt kaphat a felhasznált erőforrásokra. A Reservationsszel a használatalapú fizetéshez képest jelentősen, akár 72%-kal csökkentheti a virtuális gép, az SQL Database-számítások, az Azure Cosmos DB és más erőforrások költségeit. A Reservations számlázási kedvezményt nyújt, és nincs hatással az erőforrások futtatási állapotára.

A foglalásért fizethet előre vagy havonta. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie. A havi fizetés csak az Azure Reservations esetében érhető el, harmadik féltől származó termékek esetében nem.

Foglalásokat az [Azure Portalon](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) vásárolhat.

## <a name="why-buy-a-reservation"></a>Miért érdemes foglalást vásárolnia?

Ha hosszú ideig futó virtuális gépekkel, Azure Cosmos DB-vel vagy SQL Database-adatbázisokkal rendelkezik, egy foglalás vásárlása a legköltséghatékonyabb lehetőség. Ha például egy szolgáltatás négy példányát futtatja folyamatosan foglalás nélkül, akkor használatalapú fizetéses díjakat fizet. Ha ezen erőforrásokhoz vásárol egy foglalást, azonnal megkapja a foglalási kedvezményt. Az erőforrásokért ezután nem használatalapú fizetéses díjakat kell fizetnie.

## <a name="charges-covered-by-reservation"></a>A foglalás által fedezett költségek

Szolgáltatáscsomagok:

- **Fenntartott virtuálisgép-példány** – A foglalás csak a virtuális gép számítási költségeit fedezi. Nem fedezi azonban a további szoftverek, a hálózatkezelés és a tárolás költségeit.
- **Fenntartott Azure Cosmos DB-kapacitás** – A foglalás az erőforrások számára kiosztott átviteli sebességet fedezi. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit.
- **Fenntartott SQL Database virtuális mag** – A foglalás csak a számítási költségeket foglalja magában. A licenc számlázása külön történik.
- **SQL Data Warehouse** – A foglalás a cDWU-használatot fedezi. Nem fedezi azonban az SQL Data Warehouse használatával kapcsolatos tárolási és hálózatkezelési költségeket.
- **App Service-bélyeg díja** – A foglalás a bélyeghasználatot fedezi. Nem érvényes azonban a feldolgozókra, így a bélyeghez társított egyéb erőforrásokért külön kell fizetnie.

A Windows rendszerű virtuális gépek és az SQL Database esetében a licencelési költségek fedezéséhez használhatja az [Azure Hybrid Benefitet](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Ki jogosult foglalás vásárlására?

Egy csomag megvásárlásához rendelkeznie kell egy előfizetés-tulajdonosi szerepkörrel egy nagyvállalatban (MS-AZR-0017P vagy MS-AZR-0148P) vagy használatalapú fizetéses előfizetéssel (MS-AZR-0003P vagy MS-AZR-0023P) vagy Microsoft-ügyfélszerződéses előfizetéssel. A felhőszolgáltatók az Azure Portalt vagy a  [Partnerközpontot](/partner-center/azure-reservations)  használhatják az Azure Reservations megvásárlásához.

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek az EA-rendszergazdákra korlátozhatják a vásárlásokat a **Fenntartott példányok hozzáadása** beállítás EA Portalon történő letiltásával. Foglalás vásárlásához az EA-rendszergazdáknak legalább egy EA-előfizetésben előfizetés-tulajdonosnak kell lenniük. Ez a beállítás olyan vállalatok számára hasznos, amelyek azt szeretnék, hogy egy központi csapat vásárolja meg a foglalásokat különböző költséghelyek számára. A vásárlás után a központi csapatok hozzáadhatják a költséghely-tulajdonosokat a foglalásokhoz. A tulajdonosok ezután beállíthatják a foglalás hatókörét az előfizetéseikre. A központi csapatnak nem kell előfizetés-tulajdonosi hozzáféréssel rendelkeznie a foglalás vásárlási helyén.

A foglalási kedvezmény csak olyan erőforrásokra érvényes, amelyek nagyvállalati, felhőszolgáltatói (CSP), Microsoft-ügyfélszerződéses vagy egyéni, használatalapú díjas csomagokkal lettek megvásárolva.

## <a name="scope-reservations"></a>A foglalások hatókörének beállítása

A foglalásokhoz egy előfizetést vagy erőforráscsoportokat állíthat be hatókörként. A foglalás hatókörének beállítása megadja a foglalási kedvezmények alkalmazásának helyét. Ha egy erőforráscsoportot állít be a foglalás hatóköreként, akkor a foglalási kedvezmények csak az erőforráscsoportra érvényesek, nem a teljes előfizetésre.

### <a name="reservation-scoping-options"></a>A foglalás hatókörkezelési lehetőségei

Ha egy erőforráscsoportot állít be hatókörként, akkor az igényeitől függően három lehetősége van a foglalás hatókörének beállítására:

- **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
- **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
- **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A Microsoft-ügyfélszerződés ügyfelei esetében a számlázási profil a számlázási hatókör. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.

A foglalási kedvezmények a használatra történő alkalmazása során az Azure a következő sorrendben dolgozza fel a foglalást:

1. Erőforráscsoport hatókörű foglalások
2. Egyetlen hatókörrel rendelkező foglalások
3. Megosztott hatókörrel rendelkező foglalások

A foglalások hatókörének beállításától függően egy erőforráscsoport több foglalásból is kaphat foglalási kedvezményeket.

### <a name="scope-a-reservation-to-a-resource-group"></a>Erőforráscsoport beállítása foglalási hatókörként

Az erőforráscsoportot beállíthatja a foglalás hatóköreként a foglalás megvásárlásakor, illetve utána is. Egy erőforráscsoport foglalási hatókörként történő beállításához előfizetés-tulajdonosnak kell lennie.

A hatókör beállításához lépjen az Azure Portal [Foglalás vásárlása](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) lapjára. Válassza ki a megvásárolni kívánt foglalási típust. A **Megvásárolni kívánt termék kiválasztása** kiválasztási űrlapon módosítsa a Hatókör beállítását Egyetlen erőforráscsoport értékre. Ezután válasszon ki egy erőforráscsoportot.

![A virtuálisgép-foglalás vásárlásának kiválasztását bemutató példa](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Megjelennek az erőforráscsoportra vonatkozó vásárlási javaslatok a virtuálisgép-foglaláshoz. A javaslatok az elmúlt 30 nap használati adatainak elemzésén alapulnak. A rendszer akkor javasol egy adott vásárlást, ha a fenntartott példányokkal az erőforrások futtatási költsége alacsonyabb, mint a használatalapú fizetéses díjakkal. A foglalásvásárlási javaslatokkal kapcsolatos további információkért tekintse meg [a fenntartott példányok vásárlási javaslatait a használati mintázat alapján](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) bemutató cikket.

A foglalás megvásárlása után bármikor frissítheti a hatókört. Ehhez lépjen a foglalásra, kattintson a **Konfigurálás** lehetőségre, és állítsa be újból a foglalás hatókörét. A foglalás hatókörének újbóli beállítása nem számít kereskedelmi tranzakciónak. A foglalási időszak nem módosul. A hatókör frissítésével kapcsolatos további információkért lásd [a foglalás megvásárlása utáni hatókörfrissítést](billing-manage-reserved-vm-instance.md#change-the-reservation-scope) bemutató cikket.

![A foglalási hatókör módosítását bemutató példa](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>A foglaláshasználat monitorozása és optimalizálása

A foglalás használatát többféleképpen monitorozhatja: az Azure Portalon, API-kkal vagy a használati adatok alapján. Azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik, lépjen az Azure Portal **Foglalások** területére. A foglalásokat megjelenítő rács a foglalás legutóbb rögzített százalékos kihasználtságát mutatja. A foglalás hosszú távú kihasználtságának megtekintéséhez kattintson a foglalásra.

Ha Ön Nagyvállalati Szerződéssel vagy Microsoft-ügyfélszerződéssel rendelkező ügyfél, a foglalás kihasználtságát [API-kkal](billing-reservation-apis.md#see-reservation-usage) és a [használati adatok](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) alapján is lekérheti.

Ha azt tapasztalja, hogy az erőforráscsoport hatókörű foglalás kihasználtsága alacsony, akkor frissítheti a foglalás hatókörét egyetlen előfizetésre, vagy megoszthatja a számlázási környezetben. A foglalást fel is oszthatja, és a kapott foglalásokat különböző erőforráscsoportokra alkalmazhatja.

### <a name="other-considerations"></a>Egyéb szempontok

Ha nem rendelkezik egyező erőforrásokkal az erőforráscsoportban, akkor a foglalás kihasználatlan lesz. Ha alacsony a kihasználtság, a rendszer nem alkalmazza automatikusan a foglalást egy másik erőforráscsoportra vagy előfizetésre.

Ha az erőforráscsoportot áthelyezi egy másik előfizetésbe, a foglalás hatóköre nem frissül automatikusan. Ha törli az erőforráscsoportot, a hatókör nem frissül. [A foglalás hatókörét újból be kell állítania.](billing-manage-reserved-vm-instance.md#change-the-reservation-scope) Ellenkező esetben a foglalás kihasználatlan lesz.

## <a name="discounted-subscription-and-offer-types"></a>Kedvezményes előfizetés- és ajánlattípusok

A foglalási kedvezmények a következő jogosult előfizetésekre és ajánlattípusokra vonatkoznak.

- Nagyvállalati szerződés (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P)
- Microsoft-ügyfélszerződéses előfizetések.
- Egyéni csomagok a használatalapú fizetés díjszabásával (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P).
- CSP-előfizetések

A más ajánlattípusokkal rendelkező előfizetésekben futó erőforrásokra nem érvényes a foglalási kedvezmény.

## <a name="how-is-a-reservation-billed"></a>Hogy történik a foglalás számlázása?

A foglalás számlázása az előfizetésnél rögzített fizetési móddal történik. Ha elérhető, a foglalás költsége a pénzügyi keret egyenlegéből lesz levonva. Ha a pénzügyi keret egyenlege nem fedezi a foglalás költségét, akkor a többletköltségek ki lesznek Önnek számlázva. Ha használatalapú díjas, egyéni csomagban rendelkezik előfizetéssel, akkor a rendszer az előre fizetendő vásárlással azonnal megterheli a fiókjához tartozó hitelkártyát. A havi kifizetések a számlán jelennek még, és a rendszer havonta terheli meg a hitelkártyáját. Számlás fizetés esetén a díjak a következő számlán jelennek meg.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény a foglalás megvásárlásakor kiválasztott jellemzőkkel egyező erőforrás-használatra érvényes. Ebbe beletartozik az a hatókör, amelyben az egyező virtuális gépek, SQL Database-adatbázisok, Azure Cosmos DB és más erőforrások futnak. Ha például az USA nyugati régiójában szeretne foglalási kedvezményt négy Standard D2 virtuális gépre, akkor azt az előfizetést válassza ki, amelyben a virtuális gépek futnak.

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ha egy bizonyos órában nem rendelkezik egyező erőforrásokkal, akkor arra az órára elveszíti a foglalási mennyiséget. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

Előfordulhat például, hogy később létrehoz egy erőforrást, és rendelkezni fog egy egyező, kihasználatlan erőforrással. A rendszer automatikusan az új egyező erőforrásra alkalmazza a foglalási kedvezményt.

Ha a virtuális gépek eltérő előfizetésben futnak a regisztráción vagy fiókon belül, akkor a hatókört megosztottként állítsa be. A megosztott hatókör lehetővé teszi a foglalási kedvezmény több előfizetésre történő alkalmazását. Foglalásvásárlás után módosíthatja a hatókört. További információkért tekintse meg [az Azure-foglalások kezelését](billing-manage-reserved-vm-instance.md) ismertető cikket.

A foglalási kedvezmény csak nagyvállalati, Microsoft-ügyfélszerződéses, CSP- vagy használatalapú díjas előfizetésekhez társított erőforrásokra érvényes. A más ajánlattípusokkal rendelkező előfizetésekben futó erőforrásokra nem érvényes a foglalási kedvezmény.

## <a name="when-the-reservation-term-expires"></a>A foglalási időszak lejárata

A foglalási időszak végén a számlázási kedvezmény lejár, és az erőforrások számlázása használatalapú díjjal történik. Alapértelmezés szerint a foglalások automatikus megújítása nincs beállítva. A megújítási beállításokban elérhető lehetőség kiválasztásával engedélyezheti a foglalások automatikus megújítását. Az automatikus megújítás esetén a rendszer a meglévő foglalás lejáratakor megvásárol egy cserefoglalást. Alapértelmezés szerint a cserefoglalás ugyanolyan jellemzőkkel rendelkezik, mint a lejáró foglalás. Igény szering módosíthatja a számlázási gyakoriságot, az időszakot vagy a mennyiséget a megújítási beállításokban. A megújítást bármely felhasználó beállíthatja, aki tulajdonosi hozzáféréssel rendelkezik a foglaláshoz, valamint a számlázáshoz használt előfizetéshez.  

## <a name="discount-applies-to-different-sizes"></a>A kedvezmény különböző méretekre érvényes

Foglalás vásárlásakor a foglalás alkalmazható az ugyanabban a méretcsoportban található attribútumokkal rendelkező más példányokra. Ezt a jellemzőt példányméret-rugalmasságnak nevezik. A kedvezmény általi lefedettség rugalmassága a foglalás típusától és a foglalás megvásárlásakor kiválasztott attribútumoktól függ.

Szolgáltatáscsomagok:

- Reserved VM Instances: Ha a foglalás megvásárlásakor kiválasztja a **Példányméret-rugalmasságra optimalizálva** beállítást, akkor a kedvezmény általi lefedettség a kiválasztott virtuálisgép-mérettől függ. A foglalást az ugyanabban a méretsorozat-csoportban található virtuálisgép-méretekre lehet alkalmazni. További információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.
- Fenntartott SQL Database-kapacitás: A kedvezmény általi lefedettség a kiválasztott teljesítményszinttől függ. További információkért lásd [az Azure-beli foglalási kedvezmény alkalmazását](billing-understand-reservation-charges.md) ismertető cikket.
- Fenntartott Azure Cosmos DB-kapacitás: A kedvezmény általi lefedettség a kiosztott átviteli sebességtől függ. További információkért lásd [az Azure Cosmos DB-beli foglalási kedvezmény alkalmazását](billing-understand-cosmosdb-reservation-charges.md) ismertető cikket.

## <a name="reservation-notifications"></a>Foglalási értesítések

Az Azure-előfizetésért történő fizetés módjától függően e-mailes foglalási értesítést küldünk a szervezet alábbi felhasználóinak. Számos különféle eseményről küldünk értesítést, ilyenek például:

- Vásárlás
- Foglalás közelgő lejárata
- Lejárat
- Megújítás
- Lemondás
- Hatókör módosítása

Nagyvállalati előfizetéssel rendelkező ügyfelek esetében:
- Vásárlási értesítést a vásárlónak és az EA-értesítési kapcsolattartóknak küldünk.
- A foglalási életciklus egyéb értesítéseit csak az EA-értesítési kapcsolattartóknak küldjük el.
- A foglaláshoz RBAC- (IAM-) engedéllyel hozzáadott felhasználók nem kapnak semmilyen e-mailes értesítést.

Az egyéni előfizetéssel rendelkező ügyfelek esetében:
- A vásárló kap egy vásárlási értesítést.
- A vásárlás időpontjában az előfizetéshez tartozó számlázási fiók tulajdonosa kap egy vásárlási értesítést.
- A fióktulajdonos kapja meg az összes többi értesítést.


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:
    - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](billing-understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)

- További információk a szolgáltatáscsomagok foglalásaival kapcsolatban:
    - [Azure Reserved VM Instancesszel rendelkező virtuális gépek](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Fenntartott Azure Cosmos DB-kapacitással rendelkező Azure Cosmos DB-erőforrások](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrások](../sql-database/sql-database-reserved-capacity.md) További információk a szoftvercsomagok foglalásaival kapcsolatban:
    - [Azure Reservations-beli Red Hat-szoftvercsomagok](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure Reservations-beli SUSE-szoftvercsomagok](../virtual-machines/linux/prepay-suse-software-charges.md)
