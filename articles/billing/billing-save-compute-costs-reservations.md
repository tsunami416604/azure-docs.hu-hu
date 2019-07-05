---
title: Mi az az Azure Reservations?
description: További tudnivalók az Azure-foglalásokat és a díjszabás menteni a virtuális gépeken, az SQL-adatbázisok, Azure Cosmos DB és más erőforrás-használati díjak.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cd0a70aa0fb5096c5b0157ae078c961da03109bc
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565314"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Az Azure-foglalások segítséget pénzt takaríthat meg, egy éves előre és, vagy virtuális gépeket, SQL Database három éves számítási kapacitását, az Azure Cosmos DB átviteli sebesség vagy más Azure-erőforrások. Előre kell fizetnie lehetővé teszi a kedvezményt szeretne kapni a használt erőforrások. Foglalások jelentősen csökkentheti a virtuális gép, SQL-adatbázis számítási erőforrásokat, az Azure Cosmos dB-ben, vagy más erőforrást költségek akár 72 % a használatalapú fizetéssel. Foglalások számlázási engedményt adja meg, és az erőforrások futási állapotát nem befolyásolják.

Vásárolhat egy foglalást a [az Azure portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Miért érdemes megvásárolni a foglalást?

Ha virtuális gépek, az Azure Cosmos DB vagy az SQL-adatbázisok, amelyek hosszú ideig futnak, vásárlása egy foglalást, leginkább költséghatékony lehetőséget ad. Például egy fenntartása nélkül négy szolgáltatáspéldányok folyamatosan futtatásakor kell fizetnie, utólagos elszámolású díjszabás szerint. Ha vásárol egy foglalást adott erőforrásokhoz tartozó, azonnal megjelenik a foglalási kedvezményt. Az erőforrások már nem számoljuk el az utólagos elszámolású díjszabás szerint.

## <a name="charges-covered-by-reservation"></a>Foglalás alá tartozó díjak

Szolgáltatáscsomagok:

- **Fenntartott virtuálisgép-példány** -foglalást csak terjed ki a virtuális gép számítási költségeket. További szoftverek, hálózati vagy tárolási díjakat nem fed le.
- **Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás** -foglalást átviteli sebesség kiosztott részéért, az erőforrások ismerteti. A tárolási és hálózati díjak nem fedi le.
- **Az SQL Database szolgáltatás számára fenntartott virtuális mag** – csak a számítási költségek megtalálhatók a foglalást. A licenc külön kell fizetni.

Windows virtuális gépek és az SQL Database, a licencelési költségek is foglalkozik [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Aki nem jogosult egy foglalás megvásárlása?

Csomag vásárlása egy előfizetés-tulajdonosi szerepkör (MS-AZR - 0017P vagy MS-AZR - 0148P) vállalati vagy használatalapú fizetéses előfizetésre (MS-AZR - 003P vagy MS-AZR - 0023P) kell rendelkeznie. Felhőszolgáltatók használhatja az Azure Portalon vagy [Partnerközpont](/partner-center/azure-reservations) megvásárolható Azure-foglalásokat.

Nagyvállalati szerződéssel rendelkező ügyfeleink a nagyvállalati szerződéssel rendelkező rendszergazdák vásárlások korlátozhatja tiltsa le a **fenntartott példányok hozzáadása** a nagyvállalati szerződések portáljának lehetőség. Nagyvállalati szerződéssel rendelkező rendszergazdák tulajdonosának kell lennie egy előfizetés számára legalább egy nagyvállalati szerződéses előfizetésében vásárolhat egy foglalást. A beállítás akkor hasznos, amelyek egy másik költséghelyek lefoglalását beszerzési központi csapat nagyvállalatok számára. A vásárlás után központi csapatok költség center tulajdonosok adhat hozzá a foglalást. Tulajdonosok majd gazdagépcsoportjaira a foglalást saját előfizetésekhez. A központi csapat az előfizetés-tulajdonosi hozzáféréssel rendelkezik, ahol a Foglalás megvásárlása esetén nem szükséges.

A foglalási kedvezményt csak a vállalati CSP és az utólagos elszámolású díjszabás szerint az egyes csomagok keretében vásárolt előfizetésekhez tartozó erőforrások vonatkozik.

## <a name="scope-reservations"></a>Hatókör-foglalások

Egy foglalást egy előfizetést vagy az erőforrás-csoportokhoz is körét. A hatókör beállításáról a Foglalás kiválasztja ahol a Foglalás megtakarítások vonatkoznak. Egy erőforráscsoport, a Foglalás hatókörét, Foglalás kedvezmények csak vonatkoznak az erőforráscsoport – nem a teljes előfizetés.

### <a name="reservation-scoping-options"></a>Foglalás hatókörének meghatározásához beállításai

Az erőforrás felmerülő, csoport hatóköre egy foglalást, igényeitől függően három lehetőség közül választhat:

- **Erőforrás-csoport hatóköre egyetlen** – vonatkozik a foglalási kedvezményt a megfelelő erőforrások csak a kiválasztott erőforráscsoportban.
- **Egyetlen előfizetéses hatókört** – alkalmazza a foglalási kedvezményt a megfelelő erőforrások a kijelölt előfizetésben.
- **A megosztott hatókör** – alkalmazza a foglalási kedvezményt támogatásra jogosult előfizetések, a számlázási környezetben lévő erőforrások megfelelő. Nagyvállalati szerződéssel rendelkező ügyfeleknek a számlázási környezetben a regisztrációt. Az utólagos elszámolású díjszabás szerint az egyes előfizetések a számlázási hatóköre a fiók rendszergazdája által létrehozott összes jogosult előfizetéssel.

Foglalás kedvezmények hatására a használat során Azure feldolgozza a foglalást, a következő sorrendben:

1. Foglalások is meghatározhat egy erőforráscsoportot
2. Hatókör-foglalások
3. Megosztott hatókör foglalások

Egyetlen több foglalásokat, attól függően, hogyan hatókörét a foglalások beszerezheti a Foglalás kedvezményeket jelentenek.

### <a name="scope-a-reservation-to-a-resource-group"></a>Egy erőforrás-csoportba foglalás hatókörét

A Foglalás egy erőforráscsoportba gazdagépcsoportjaira azt megvásárolni a foglalást, vagy állítsa be a hatókört a vásárlás után. Egy erőforráscsoport, a Foglalás hatóköre egy előfizetés tulajdonosa kell lennie.

Állítsa be a hatókört, lépjen a [foglalás megvásárlása](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) oldal az Azure Portalon. Ezután válassza ki a foglalás típusát, amely szeretne vásárolni. Az a **válassza ki a vásárolni kívánt terméket** kiválasztási képernyő, módosítsa a **hatókör** értéket a következőre **egyetlen erőforráscsoportra** , és válasszon ki egy erőforráscsoportot.

![Virtuális gép foglalás megvásárlása kijelölés megjelenítő példa](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Az erőforráscsoport, a virtuális gépek lefoglalását a vásárlási javaslatok láthatók. Javaslatok az elmúlt 30 nap használati adatainak elemzésével számítjuk ki. Vásárlási javaslat történik, ha a fenntartott példányokkal erőforrások futtatásával járó költségeket olcsóbb, mint az utólagos elszámolású díjszabás szerint erőforrások futtatásával járó költségeket. Foglalás vásárlási javaslatok kapcsolatos további információkért lásd: a [beolvasni a fenntartott példányok vásárlási javaslatok a használati minta alapján](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) blogbejegyzést.

A hatókör vásárolhat egy foglalást után bármikor frissítheti. Ehhez nyissa meg a foglalást, kattintson a **konfigurációs** és a fenntartás rescope. A Foglalás rescoping nem kereskedelmi ügylet. A Foglalás időtartama nem változik. A hatókör frissítésével kapcsolatos további információkért lásd: [frissítése a hatókör egy foglalás megvásárlása után](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Példa egy foglalás hatókörének módosítása](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Figyelik és optimalizálják a Foglalás használat

A Foglalás használat többféle módon – Azure portal, API-kon keresztül vagy használati adatok révén követheti nyomon. A foglalások, amely rendelkezik a hozzáférést az összes megtekintéséhez lépjen a **foglalások** az Azure Portalon. A foglalások rács az utolsó rögzített kihasználtsági százaléka a Foglalás látható. Kattintson a Foglalás a Foglalás hosszú távú használat megjelenítéséhez.

Foglalás kihasználtsági segítségével is beszerezheti [API-k](billing-reservation-apis.md#see-reservation-usage) és a [használati adatok](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) Ha Ön rendelkezik nagyvállalati szerződéssel.

Ha úgy látja, hogy az erőforráscsoport a kihasználtság hatókörű foglalás van, akkor a Foglalás hatóköre egy előfizetés, vagy ossza meg a számlázási környezetben keresztül frissítheti. A Foglalás felosztása, és az eredményül kapott foglalások alkalmazni a különböző erőforráscsoportokban is.

### <a name="other-considerations"></a>Egyéb szempontok

Ha nem rendelkezik megfelelő egy erőforráscsoportban lévő erőforrásokat, a foglalást lehet eredményeztek. A foglalás automatikusan egy másik erőforráscsoportba vagy előfizetésbe nem vonatkozik, ha nincs az alacsony kihasználtságú.

A Foglalás hatóköre nem automatikusan frissíti, ha az erőforráscsoport egy előfizetésből egy másikba. A Foglalás rescope kell. Ellenkező esetben a Foglalás fog eredményeztek.

## <a name="discounted-subscription-and-offer-types"></a>Kedvezményes előfizetés és az ajánlat típus

Foglalás kedvezményeket kínálnak típusok, és a alkalmazni a következő jogosult előfizetéseket.

- Nagyvállalati Szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P)
- Az utólagos elszámolású díjszabás szerint az egyes csomagok (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P)
- CSP-előfizetésekben

Egy előfizetésben a más típusú futtató erőforrások nem kapja meg a foglalási kedvezményt.

## <a name="how-is-a-reservation-billed"></a>Hogyan történik a Foglalás?

A Foglalás díját a fizetési módot az előfizetéshez kötött. Ha egy vállalati előfizetéssel rendelkezik, a fenntartás költsége a pénzügyi kötelezettségvállalási egyenleg vonni. Ha a pénzügyi kötelezettségvállalási egyenleg nem fedi le a fenntartás költsége, a számlázás a kerettúllépést. Ha rendelkezik egy egyedi terv az utólagos elszámolású díjszabás szerint az előfizetés, azonnal számlázott a hitelkártyát a fiókjában rendelkezik. Ha számla ellenében a számlázás, láthatja a díjak a következő számla.

## <a name="how-reservation-discount-is-applied"></a>Hogyan alkalmazza a foglalási kedvezményt

A foglalási kedvezményt az attribútumokat, akkor válassza, ha a foglalást vásárolhat egyező erőforrás-használat vonatkozik. Az attribútumok közé tartozik a hatókör, ahol a megfelelő virtuális gépek, az SQL adatbázisok, Azure Cosmos DB vagy egyéb erőforrások futtat. Például ha azt szeretné, a foglalási kedvezményt négy Standard D2 virtuális gépek az USA nyugati régiójában, majd válassza ki az előfizetést, ahol a virtuális gépek futnak.

A foglalási kedvezményt a "*használata – it-vagy-elveszíti-it*". Ha nem rendelkezik az erőforrások használata az adott órán belül, majd elveszíti a Foglalás mennyiségét az adott órában. Nem sokat fel nem használt fenntartott órán keresztül továbbítja.

Ha leállítja egy erőforrást, a foglalási kedvezményt automatikusan alkalmazza a megadott hatókörben lévő másik egyező erőforrás. A megadott hatókörben lévő nem megfelelő erőforrások találhatók, akkor a szolgáltatás számára fenntartott órák *elveszett*.

Például előfordulhat, hogy később erőforrás létrehozása és rendelkezik egy megfelelő foglalást, amely a eredményeztek. Ebben a példában a foglalási kedvezményt automatikusan alkalmazza az új egyező erőforrás.

A virtuális gépek futnak, a regisztráció /-fiókon belül különböző előfizetésben található, majd válassza ki azt a hatókört, megosztott. Megosztott hatókör lehetővé teszi, hogy a foglalási kedvezményt a alkalmazni lehet előfizetések között. A hatókör, egy foglalás megvásárlása után módosíthatja. További információkért lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).

Enterprise, CSP, társított erőforrások csak alkalmazza a foglalási kedvezményt vagy az Önnel használatalapú-as előfizetések díjszabása. Egy előfizetésben a más típusú futtató erőforrások nem kapja meg a foglalási kedvezményt.

## <a name="when-the-reservation-term-expires"></a>Ha a Foglalás időtartama lejár

A Foglalás az előfizetési időszak végén lejár a számlázási kedvezményeket, és a virtuális gép, SQL database, Azure Cosmos DB vagy egyéb erőforrás elszámolása a használatalapú – mint-akkor lépjen ár. Az Azure-foglalások nem automatikus megújítási. Bevezetés a számlázási kedvezményeket a folytatáshoz meg kell vásárolnia egy új foglalást a jogosult szolgáltatások és szoftverek.

## <a name="discount-applies-to-different-sizes"></a>Különböző méretekre vonatkozik kedvezmény

Ha vásárol egy foglalást, a kedvezmény alkalmazhat más példányaira, amelyek az ugyanazon méretcsoport attribútumokkal. Ez a funkció példány mérete rugalmasan néven ismert. A kedvezmény lefedettség rugalmasságát foglalás és az attribútumokat, válassza ki, amikor azt megvásárolni a foglalást függ.

Szolgáltatáscsomagok:

- Fenntartott VM-példányok: Amikor megvásárolni a foglalást, és válassza ki **optimalizált**: **példány mérete rugalmasan**, a kedvezmény lefedettség választja virtuális gép méretétől függ. A Foglalás az azonos méretű adatsorozat-csoport a virtuális gépek (VM)-méretek is alkalmazhat. További információkért lásd: [virtuális gép mérete rugalmasan Reserved VM Instances](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Az SQL Database szolgáltatás számára fenntartott kapacitás: A kedvezmény lefedettség attól függ, hogy a kiválasztott teljesítményszint. További információkért lásd: [megismerheti, hogyan kell alkalmazni az Azure a foglalási kedvezményt](billing-understand-reservation-charges.md).
- Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás: A kedvezmény lefedettség attól függ, hogy a kiosztott átviteli sebesség. További információkért lásd: [megismerheti, hogyan kell alkalmazni az Azure Cosmos DB-foglalási kedvezményt](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- További információ az Azure-fenntartások a következő cikkekben:
    - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
    - [Az utólagos elszámolású díjszabás szerint az előfizetés használati foglalás adatai](billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
    - [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
    - [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](/partner-center/azure-reservations)

- További információ a foglalások service-csomagok:
    - [Virtuális gépek az Azure Reserved VM Instances szolgáltatással](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Az Azure Cosmos DB-erőforrásokat az Azure Cosmos DB lefoglalt kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL-adatbázis számítási erőforrásokat az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md) tudjon meg többet a foglalásokat a szoftverfrissítési csomagok:
    - [Red Hat Azure foglalásai közül szoftvercsomagok – alap](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Az Azure-foglalások SUSE szoftvercsomagok – alap](../virtual-machines/linux/prepay-suse-software-charges.md)
