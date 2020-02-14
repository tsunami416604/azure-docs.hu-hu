---
title: Fenntartott kapacitás a Azure Cosmos DB a Cost optimalizálásához
description: Megtudhatja, hogyan vásárolhat Azure Cosmos DB fenntartott kapacitást a számítási költségek mentéséhez.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 17f6787cddf2800663d37b153b3e1b8cf33390f8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201176"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>A Cost és a fenntartott kapacitás optimalizálása Azure Cosmos DB

A fenntartott Azure Cosmos DB-kapacitással pénzt takaríthat meg, ha Azure Cosmos DB-erőforrásokat foglal le egy vagy három évre. Ha rendelkezik fenntartott Azure Cosmos DB-kapacitással, kedvezményt kaphat az Azure Cosmos DB-erőforrások teljes kiépítésére. Példák erőforrásokra, amelyek adatbázisok és tárolók (táblák, gyűjtemények és gráfok).

Azure Cosmos DB fenntartott kapacitás jelentős mértékben csökkentheti Cosmos DB költségeit,&mdash;akár 65%-ot is igénybe vehet, egy vagy három éves előzetes kötelezettségvállalással. A fenntartott kapacitás számlázási kedvezményt biztosít, és nem befolyásolja a Azure Cosmos DB erőforrások futtatókörnyezeti állapotát.

Azure Cosmos DB fenntartott kapacitás az erőforrások számára kiépített átviteli sebességre vonatkozik. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit. A foglalások megvásárlása után a foglalási attribútumoknak megfelelő átviteli sebességért nem számolunk fel díjakat az utólagos elszámolású díjakért. A foglalásokkal kapcsolatos további információkért tekintse meg az [Azure foglalásait](../cost-management-billing/reservations/save-compute-costs-reservations.md) ismertető cikket.

A [Azure Portal](https://portal.azure.com)Azure Cosmos db fenntartott kapacitást is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/monthly-payments-reservations.md). Fenntartott kapacitás vásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.  
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Cosmos DB fenntartott kapacitást.

## <a name="determine-the-required-throughput-before-purchase"></a>A szükséges átviteli sebesség meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett Azure Cosmos DB erőforrások teljes átviteli sebességén kell alapulnia. A Azure Portal javasolt foglalási méretek a következő lépésekkel tekinthetők meg:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).  

2. Válassza **a minden szolgáltatás** > **fenntartások** > **Hozzáadás**lehetőséget.

3. A **vásárlási foglalások** ablaktáblán válassza a **Azure Cosmos db**lehetőséget.

4. Az ajánlott foglalások megtekintéséhez válassza az **ajánlott** lapot:

A következő attribútumok alapján szűrheti a javaslatokat:

- **Időtartam** (1 év vagy 3 év)
- **Számlázási gyakoriság** (havi vagy előzetes)
- **Átviteli sebesség típusa** (ru vs multi-Master ru)

Emellett a hatókörre vonatkozó javaslatok egyetlen erőforráscsoport, egy előfizetés vagy a teljes Azure-regisztráción belül lehetnek. A használati javaslatokat az elmúlt 7 nap, 30 nap vagy 60 nap alapján lehet megjeleníteni.

Példa erre a javaslatra:

![Fenntartott kapacitásra vonatkozó javaslatok](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Az 3 000 000 RU/s foglalás megvásárlására vonatkozó javaslat azt jelzi, hogy a 3 éves foglalások között a 3 000 000 RU/s foglalás mérete maximalizálja a megtakarítás mértékét. Ebben az esetben a javaslat kiszámítása a Azure Cosmos DB használat elmúlt 30 napja alapján történik. Ha ez az ügyfél azt várja, hogy az elmúlt 30 nap Azure Cosmos DB használat a jövőbeli használatra jellemző, a megtakarítás a 3 000 000 RU/s foglalás megvásárlásával maximalizálható.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).  

2. Válassza **a minden szolgáltatás** > **fenntartások** > **Hozzáadás**lehetőséget.  

3. Az új foglalás megvásárlásához a **foglalások** ablaktáblán válassza a **Azure Cosmos db** lehetőséget.  

4. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   ![A fenntartott kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Mező  |Leírás  |
   |---------|---------|
   |Hatókör   |   Ez a beállítás azt szabályozza, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Azt is meghatározza, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/>  Ha a **megosztott**lehetőséget választja, a foglalási kedvezményt a rendszer a számlázási környezetben található bármely előfizetésben futtató Azure Cosmos db példányokra alkalmazza. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a közös hatókör az összes olyan előfizetés, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabással rendelkezik.  <br/><br/>  Ha az **egyszeri előfizetést**választja, a foglalási kedvezmény a kijelölt előfizetésben Azure Cosmos db példányokra lesz alkalmazva. <br/><br/> Ha **egyetlen erőforráscsoportot**választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben lévő Azure Cosmos db példányokra alkalmazza, és az adott előfizetésen belül a kiválasztott erőforráscsoportot. <br/><br/> A foglalás hatókörét a fenntartott kapacitás megvásárlása után módosíthatja.  |
   |Előfizetést  |   Az Azure Cosmos DB fenntartott kapacitás kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P): nagyvállalati előfizetések esetén a díjakat a beléptetés pénzügyi kötelezettségvállalásának egyenlegére, vagy felszámított összegként kell levonni. <br/><br/> Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P): egyéni előfizetés utólagos elszámolású díjszabással, a díjak számlázása a hitelkártyára vagy a számla fizetési módjára történik az előfizetésben.    |
   | Erőforráscsoport | Az erőforráscsoport, amelyhez a fenntartott kapacitási kedvezmény érvényes. |
   |Időszak  |   Egy vagy három év.   |
   |Átviteli sebesség típusa   |  Az átviteli sebesség kérési egységként van kiépítve. Megvásárolhatja a kiépített átviteli sebességre vonatkozó foglalást mindkét beállítás esetében – az egyrégiós írások és a több régióba írt írások esetében is. Az átviteli sebesség típusának két értéke van: 100 RU/s/óra és 100 multi-Master RU/s/óra.|
   | Fenntartott kapacitási egységek| A lefoglalni kívánt átviteli sebesség mennyisége. Ezt az értéket kiszámíthatja az összes Cosmos DB erőforráshoz (például adatbázisokhoz vagy tárolóhoz) szükséges átviteli sebesség meghatározásával régiónként. Ezután szorozzuk meg a Cosmos-adatbázishoz társítandó régiók számával. Például: Ha minden régióban 5, 1 000 000 RU/s-vel rendelkező régió található, akkor a foglalási kapacitás megvásárlásához válassza a 5 000 000 RU/mp lehetőséget. |


5. Az űrlap kitöltése után a lefoglalt kapacitás megvásárlásához szükséges árat számítjuk ki. A kimenet a kiválasztott beállításokkal kapott kedvezmény százalékos arányát is megjeleníti. Kattintson a **kiválasztás** elemre.

6. **A foglalások ablaktáblán** tekintse át a foglalás kedvezményét és árát. A foglalási díj az összes régióban kiépített átviteli sebességű Azure Cosmos DB erőforrásokra vonatkozik.  

   ![Fenntartott kapacitás összefoglalása](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Válassza a **felülvizsgálat + vásárlás** lehetőséget, majd **Vásároljon most**. A vásárlás sikerességét a következő oldal látja:

A foglalás megvásárlása után azonnal alkalmazni kell a foglalás feltételeinek megfelelő meglévő Azure Cosmos DB-erőforrásokra. Ha nem rendelkezik meglévő Azure Cosmos DB erőforrásokkal, a foglalás akkor érvényes, ha új Cosmos DB-példányt telepít, amely megfelel a foglalás feltételeinek. A foglalás időtartama mindkét esetben a sikeres vásárlás után azonnal elindul.

A foglalás lejáratakor a Azure Cosmos DB példányok továbbra is futnak, és az utólagos elszámolású díjszabás szerint számolunk fel díjat.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Következő lépések

A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő Azure Cosmos DB erőforrásokra. A foglalás hatókörét a Azure Portal, a PowerShell, az Azure CLI vagy az API segítségével frissítheti.

*  Ha szeretné megtudni, hogyan alkalmazza a fenntartott kapacitás kedvezményeit Azure Cosmos DBre, tekintse meg [Az Azure foglalási kedvezmény ismertetése](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)című témakört.

* Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

   * [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-foglalások a partner Center CSP programban](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
