---
title: Mi az az Azure Reservations?
description: Ismerkedjen meg Azure Reservations és díjszabással a virtuális gépekre, az SQL-adatbázisokra, a Azure Cosmos DB és az egyéb erőforrás-költségekre való mentéshez.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: c63447ff0a3329e0cc0dc0605984ae4f26e9c25f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359234"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Azure Reservations a virtuális gépek egy-vagy hároméves, SQL Database számítási kapacitásának, Azure Cosmos DB átviteli sebességének vagy más Azure-erőforrásoknak az előfizetésével segítheti a pénz megtakarítását. Az előzetes fizetés lehetővé teszi, hogy kedvezményt kapjon a felhasznált erőforrásokhoz. A foglalások jelentősen csökkenthetik a virtuális gépeket, az SQL Database számítási, Azure Cosmos DB-és egyéb erőforrás-költségeit, akár 72%-ot az utólagos elszámolású árakon. A foglalások számlázási kedvezményt biztosítanak, és nem érintik az erőforrások futtatókörnyezeti állapotát.

Foglalást a [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)vásárolhat.

## <a name="why-buy-a-reservation"></a>Miért érdemes foglalást vásárolni?

Ha olyan virtuális gépekkel, Azure Cosmos DBekkel vagy SQL-adatbázisokkal rendelkezik, amelyek hosszú időn keresztül futnak, a foglalás megvásárlásával a leghatékonyabb megoldást nyújtja. Ha például egy szolgáltatás négy példányát folyamatosan, foglalás nélkül futtatja, az utólagos elszámolású díjszabás szerint számítunk fel díjat. Ha ezen erőforrások foglalását vásárolja meg, azonnal megkapja a foglalási kedvezményt. Az erőforrásokra az utólagos elszámolású díjszabásban már nem számítunk fel díjat.

## <a name="charges-covered-by-reservation"></a>A foglalás által lefedett díjak

Szolgáltatási csomagok:

- **Fenntartott virtuálisgép-példány** – a foglalás csak a virtuális gépek számítási költségeire vonatkozik. Nem fedi le a további szoftver-, hálózatkezelési és tárolási díjakat.
- **Azure Cosmos db fenntartott kapacitás** – a foglalás az erőforrások számára kiépített átviteli sebességre vonatkozik. Nem fedi le a tárolási és hálózati költségeket.
- **SQL Database fenntartott virtuális mag** – csak a számítási költségek szerepelnek a foglalásban. A licenc számlázása külön történik.

A Windows rendszerű virtuális gépek és a SQL Database esetében a licencelési költségeket [](https://azure.microsoft.com/pricing/hybrid-benefit/)a Azure Hybrid Benefitával is lefedi.

## <a name="whos-eligible-to-purchase-a-reservation"></a>Ki jogosult a foglalás megvásárlására?

A csomagok megvásárlásához előfizetés-tulajdonosi szerepkörrel kell rendelkeznie egy vállalatnál (MS-AZR-0017P vagy MS-AZR-0148P) vagy utólagos elszámolású előfizetésben (MS-AZR-003P vagy MS-AZR-0023P). A felhőalapú megoldások szolgáltatói a Azure Portal vagy a [partner központ](/partner-center/azure-reservations) használatával vásárolhatják meg Azure Reservations.

A Nagyvállalati Szerződés (EA) ügyfelek korlátozni tudják az EA-rendszergazdákra irányuló vásárlásokat. Ezt úgy teheti meg, hogy letiltja a **fenntartott példányok hozzáadása** lehetőséget az EA portálon. Egy foglalás megvásárlásához a nagyvállalati szerződéssel rendelkező rendszergazdának legalább egy EA-előfizetéshez előfizetés-tulajdonosnak kell lennie. Ez a lehetőség olyan vállalatok számára hasznos, akik egy központi csapatot szeretnének vásárolni a különböző költséghely-foglalások megvásárlásához. A vásárlást követően a központosított csapatok felvehetik a Cost Center-tulajdonosokat a foglalásokhoz. A tulajdonosok ezután a foglalást a saját előfizetésük körébe helyezhetik. A központi csapatnak nem kell előfizetés-tulajdonosi hozzáféréssel rendelkeznie a foglalás megvásárlásához.

A foglalási kedvezmény csak a nagyvállalati, a felhőalapú megoldás-szolgáltató (CSP) és az utólagos elszámolású csomagok keretében vásárolt előfizetésekhez kapcsolódó erőforrásokra vonatkozik.

## <a name="scope-reservations"></a>Hatókör-foglalások

A foglalás hatóköre egy előfizetéshez vagy erőforráscsoporthoz is felhasználható. A foglalás hatókörének beállítása kiválasztja a foglalás megtakarításának helyét. Amikor a foglalást egy erőforráscsoporthoz alkalmazza, a foglalási kedvezmények csak az erőforráscsoporthoz vonatkoznak – nem a teljes előfizetésre.

### <a name="reservation-scoping-options"></a>Foglalási hatókör beállításai

Az erőforráscsoport hatókörében három lehetőség közül választhat a foglalás hatóköréhez, igény szerint:

- **Egy erőforráscsoport hatóköre** – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.
- **Egyszeri előfizetés hatóköre** – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.
- **Megosztott hatókör** – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Nagyvállalati Szerződés ügyfelek esetében a számlázási környezet a beléptetés. Az utólagos elszámolású előfizetések esetében a számlázási hatókör a fiók rendszergazdája által létrehozott összes jogosult előfizetés.

A foglalási kedvezmények a használatra való alkalmazása során az Azure a következő sorrendben dolgozza fel a foglalást:

1. Egy erőforráscsoport hatókörén belüli foglalások
2. Önálló hatókörű foglalások
3. Megosztott hatókörű foglalások

Egy erőforráscsoport több foglalásból is kaphat foglalási kedvezményeket, attól függően, hogy a foglalások hogyan terjednek ki.

### <a name="scope-a-reservation-to-a-resource-group"></a>Egy erőforráscsoport foglalásának hatóköre

A foglalást a foglalás megvásárlásakor, illetve a hatókör megvásárlása után is beállíthatja egy erőforráscsoport hatókörére. A foglalás egy erőforráscsoporthoz való hozzárendeléséhez előfizetés-tulajdonosnak kell tartoznia.

A hatókör beállításához nyissa meg a Azure Portal [vásárlási foglalás](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) lapját. Válassza ki a megvásárolni kívánt foglalási típust. A válassza ki azt a terméket, amely számára meg **szeretné vásárolni** a kiválasztási űrlapot, módosítsa a hatókör értékét egy erőforráscsoport értékre. Ezután válasszon ki egy erőforráscsoportot.

![Példa a virtuális gép foglalásának megvásárlására](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

A virtuális gépi foglalásban található erőforráscsoport esetében a vásárlási javaslatok jelennek meg. A javaslatok kiszámítása az elmúlt 30 napban történő használat elemzésével történik. Ha a fenntartott példányokkal rendelkező erőforrások futtatásának költségei olcsóbbak, mint az erőforrások az utólagos elszámolású díjszabással való futtatásának költségei. A foglalások vásárlási javaslataival kapcsolatos további információkért lásd [a használati minta alapján fenntartott példányok vásárlására vonatkozó javaslatok](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)beszerzése című témakört.

A hatókört bármikor frissítheti a foglalás megvásárlása után. Ehhez nyissa meg a foglalást, kattintson a **konfiguráció**lehetőségre, és a foglalás újrahatóköre elemre. A foglalás Rescoping nem kereskedelmi tranzakció. A foglalási időszak nem módosul. A hatókör frissítésével kapcsolatos további információkért lásd: [a hatókör frissítése a foglalás megvásárlása után](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![A foglalás hatókörének módosítását bemutató példa](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>A foglalások használatának monitorozása és optimalizálása

A foglalási használatot többféleképpen is nyomon követheti – Azure Portalon, API-kon vagy a használati adatokon keresztül. Ha szeretné megtekinteni az összes olyan foglalást, amelyhez hozzáféréssel  rendelkezik, lépjen a Azure Portal foglalások pontra. A foglalások rács a foglalás utolsó rögzített kihasználtságának százalékos arányát jeleníti meg. Kattintson a foglalásra a foglalás hosszú távú kihasználtságának megtekintéséhez.

A foglalás kihasználtságát [API](billing-reservation-apis.md#see-reservation-usage) -k és a [használati adatok](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) alapján is lekérheti, ha Ön nagyvállalati szerződéssel rendelkező ügyfél.

Ha azt tapasztalja, hogy az erőforráscsoport hatókörben lévő foglalásának kihasználtsága alacsony, akkor a foglalási hatókört egyetlen előfizetésre frissítheti, vagy megoszthatja azt a számlázási környezetben. Megoszthatja a foglalást, és alkalmazhatja az eredményül kapott foglalásokat a különböző erőforráscsoportok esetében is.

### <a name="other-considerations"></a>Egyéb szempontok

Ha nem rendelkezik a megfelelő erőforrásokkal egy erőforráscsoporthoz, akkor a foglalás kihasználatlan lesz. A foglalás nem vonatkozik automatikusan egy másik erőforrás-csoportra vagy-előfizetésre, ahol alacsony a kihasználtsága.

A foglalási hatókör nem frissül automatikusan, ha az erőforráscsoportot egyik előfizetésből egy másikba helyezi át. Ha törli az erőforráscsoportot, a hatókör nem frissül. [A foglalást](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)újra fel kell vennie. Ellenkező esetben a foglalás kihasználatlan lesz.

## <a name="discounted-subscription-and-offer-types"></a>Kedvezményes előfizetés és ajánlati típusok

A foglalási kedvezmények a következő jogosult előfizetésekre és ajánlat-típusokra vonatkoznak.

- Nagyvállalati szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P)
- Egyedi csomagok utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P)
- CSP-előfizetések

A más ajánlati típusokkal rendelkező előfizetésekben futó erőforrások nem kapják meg a foglalási kedvezményt.

## <a name="how-is-a-reservation-billed"></a>Hogyan történik a foglalás számlázása?

A foglalás díját az előfizetéshez kötött fizetési mód alapján számítjuk fel. Ha nagyvállalati előfizetéssel rendelkezik, a foglalási költségek a pénzügyi kötelezettségvállalás egyenlegét vonják le. Ha a pénzügyi kötelezettségvállalás egyenlege nem fedi le a foglalás költségeit, a felszámított összegért kell fizetnie. Ha előfizetéssel rendelkezik az utólagos elszámolású csomaggal, akkor a fiókjában lévő bankkártyát azonnal számlázjuk. A számla számlázása után a következő számlán megjelenő díjak láthatók.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény a foglalás megvásárlásakor kiválasztott attribútumoknak megfelelő erőforrás-használatra vonatkozik. Az attribútumok közé tartozik az a hatókör, amelyben a megfelelő virtuális gépek, SQL-adatbázisok, Azure Cosmos DB vagy más erőforrások futnak. Ha például az USA nyugati régiójában négy standard D2 virtuális gépre vonatkozó foglalási kedvezményt szeretne használni, válassza ki azt az előfizetést, amelyben a virtuális gépek futnak.

A foglalási kedvezmény "*use-this-or-Lose-it*". Ha nem rendelkezik a megfelelő erőforrásokkal, akkor az adott órára vonatkozó foglalási mennyiség elvész. Nem lehet továbbítani a használaton kívüli órákat.

Egy erőforrás leállításakor a foglalási kedvezmény automatikusan a megadott hatókörben lévő másik egyező erőforrásra vonatkozik. Ha nem található egyező erőforrás a megadott hatókörben, akkor a lefoglalt órák elvesznek.

Előfordulhat például, hogy később létrehoz egy erőforrást, és egy hozzá tartozó foglalást használ. A foglalási kedvezmény automatikusan az új egyező erőforrásra vonatkozik.

Ha a virtuális gépek különböző előfizetésekben futnak a regisztráció/fiókon belül, válassza ki a hatókört megosztottként. A megosztott hatókör lehetővé teszi, hogy a foglalási kedvezmény alkalmazható legyen az előfizetések között. A hatókört a foglalás megvásárlása után is módosíthatja. További információ: [Manage Azure Reservations](billing-manage-reserved-vm-instance.md).

A foglalási kedvezmény csak a nagyvállalati, CSP-vagy előfizetésekhez kapcsolódó erőforrásokra vonatkozik, az utólagos elszámolású díjszabással. A más ajánlati típusokkal rendelkező előfizetésekben futó erőforrások nem kapják meg a foglalási kedvezményt.

## <a name="when-the-reservation-term-expires"></a>A foglalás érvényességének lejárta után

A foglalási időszak végén a számlázási engedmény lejár. A virtuális gépet, az SQL Database-t, a Azure Cosmos DBt vagy más erőforrást az utólagos elszámolású díjszabás szerint számoljuk el. Azure Reservations ne újítsa meg automatikusan. A számlázási engedmény beszerzésének folytatásához új foglalást kell vásárolnia a jogosult szolgáltatásokhoz és szoftverekhez.

## <a name="discount-applies-to-different-sizes"></a>A kedvezmény különböző méretekre vonatkozik

A foglalás megvásárlásakor a kedvezmény az azonos méretű csoportba tartozó attribútumokkal rendelkező más példányokra is érvényes. Ez a szolgáltatás a példányok méretének rugalmassága. A kedvezményes lefedettség rugalmassága a foglalás típusától és a foglalás megvásárlásakor kiválasztott attribútumoktól függ.

Szolgáltatási csomagok:

- Fenntartott VM-példányok: Ha megvásárolja a foglalást, és kiválasztja a **példány méretének rugalmasságát**, a kedvezményes lefedettség a kiválasztott VM-mérettől függ. A foglalás a virtuális gépek (VM-EK) méretére is vonatkozhat ugyanabban a méretű adatsorozat-csoportban. További információ: [virtuális gépek méretének rugalmassága fenntartott VM](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)-példányokkal.
- SQL Database fenntartott kapacitás: A kedvezményes lefedettség a választott teljesítmény szintjétől függ. További információ: az [Azure foglalási kedvezmény alkalmazásának megismerése](billing-understand-reservation-charges.md).
- Azure Cosmos DB fenntartott kapacitás: A kedvezményes lefedettség a kiosztott átviteli sebességtől függ. További információ: [Azure Cosmos db foglalási kedvezmény alkalmazásának megismerése](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Foglalási értesítések

Az Azure-előfizetésért való fizetéstől függően e-mailben foglalási értesítéseket küldünk a szervezet következő felhasználói számára. Az értesítések küldése különböző eseményekre történik, beleértve a következőket:

- Vásárlás
- Közelgő foglalás lejárata
- Expiry
- Megújítási
- Lemondás
- Hatókör módosítása

Az EA-előfizetésekkel rendelkező ügyfelek esetében:
- A rendszer egy vásárlási értesítést küld a vásárlónak és az EA értesítési partnereinek.
- Az egyéb foglalási életciklusokra vonatkozó értesítéseket csak az EA értesítési névjegyek kapják meg.
- A RBAC (IAM) engedéllyel egy foglaláshoz hozzáadott felhasználók nem kapnak e-mail-értesítéseket.

Egyéni előfizetéssel rendelkező ügyfelek esetén:
- A vásárló vásárlási értesítést kap.
- A vásárlás időpontjában az előfizetés számlázási fiókjának tulajdonosa egy vásárlási értesítést kap.
- A fiók tulajdonosa megkapja az összes többi értesítést.


## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- További információ a Azure Reservationsról a következő cikkekkel:
    - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
    - [Az előfizetéshez tartozó foglalások használatának ismertetése az utólagos elszámolású díjszabással](billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
    - [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a partner Center Cloud Solution Provider (CSP) programban](/partner-center/azure-reservations)

- További információ a szolgáltatási csomagok foglalásáról:
    - [Virtual Machines a Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB fenntartott kapacitással rendelkező erőforrások Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database fenntartott kapacitással rendelkező számítási erőforrások SQL Database](../sql-database/sql-database-reserved-capacity.md) További információ a szoftvercsomagok foglalásáról:
    - [A Red Hat szoftvercsomag Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE-szoftvercsomagok Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
