---
title: A fenntartott kapacitással rendelkező Azure Cosmos DB erőforrások költségeit optimalizálhatja
description: Megtudhatja, hogyan vásárolhat Azure Cosmos DB fenntartott kapacitást a számítási költségek mentéséhez.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: 0ee43fe0996c05f4e59f6107ba52fac19b83cdef
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756950"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>A Cost és a fenntartott kapacitás optimalizálása Azure Cosmos DB

Azure Cosmos DB fenntartott kapacitással pénzt takaríthat meg, ha akár egy évig, akár három évig Azure Cosmos DB erőforrásokra vonatkozó foglalást végez. A Azure Cosmos DB fenntartott kapacitással kedvezményt kaphat a Cosmos DB erőforrások számára kiosztott teljesítményről. Az erőforrások például adatbázisok és tárolók (táblák, gyűjtemények és diagramok).

Azure Cosmos DB fenntartott kapacitás jelentős mértékben csökkentheti a Cosmos DB költségeit &mdash;up 65%-kal a normál árakon egy éves vagy hároméves előzetes kötelezettségvállalással. A fenntartott kapacitás számlázási kedvezményt biztosít, és nem befolyásolja a Azure Cosmos DB erőforrások futtatókörnyezeti állapotát.

Azure Cosmos DB fenntartott kapacitás az erőforrások számára kiépített átviteli sebességre vonatkozik. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit. A foglalások megvásárlása után a foglalási attribútumoknak megfelelő átviteli sebességért nem számolunk fel díjakat az utólagos elszámolású díjakért. A foglalásokkal kapcsolatos további információkért tekintse meg az [Azure foglalásait](../billing/billing-save-compute-costs-reservations.md) ismertető cikket.

A [Azure Portal](https://portal.azure.com)Azure Cosmos db fenntartott kapacitást is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../billing/billing-monthly-payments-reservations.md). Fenntartott kapacitás vásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.  
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Cosmos DB fenntartott kapacitást.

## <a name="determine-the-required-throughput-before-purchase"></a>A szükséges átviteli sebesség meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett Azure Cosmos DB erőforrások teljes átviteli sebességén kell alapulnia. A szükséges átviteli sebesség a következő módokon határozható meg:

* A Azure Cosmos DB-fiókok,-adatbázisok és-gyűjtemények teljes kiosztott átviteli sebességének lekérése az összes régióban. Kiértékelheti például a napi átlagos kiosztott átviteli sebességet úgy, hogy letölti a napi használati utasítást a `https://account.azure.com`ból.

* Ha Ön Nagyvállalati Szerződés (EA) ügyfél, letöltheti a használati fájlt, és lekérheti a Azure Cosmos DB átviteli sebesség részleteit. A használati fájl **További információ** szakaszában tekintse meg a **szolgáltatástípus** értékét.

* Összefoglalhatja az átlagos átviteli sebességet a Azure Cosmos DB-fiókokban lévő azon munkaterhelések esetében, amelyeket a következő egy vagy három évre vonatkozóan futtatni vár. Ezt a mennyiséget használhatja a foglaláshoz.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).  

2. Válassza **a minden szolgáltatás**  > **fenntartások**  > **Hozzáadás**lehetőséget.  

3. Az új foglalás megvásárlásához a **foglalások** ablaktáblán válassza a **Azure Cosmos db** lehetőséget.  

4. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   ![A fenntartott kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Mező  |Leírás  |
   |---------|---------|
   |Hatókör   |   Ez a beállítás azt szabályozza, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Azt is meghatározza, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/>  Ha a **megosztott**lehetőséget választja, a foglalási kedvezményt a rendszer a számlázási környezetben található bármely előfizetésben futtató Azure Cosmos db példányokra alkalmazza. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a közös hatókör az összes olyan előfizetés, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabással rendelkezik.  <br/><br/>  Ha az **egyszeri előfizetést**választja, a foglalási kedvezmény a kijelölt előfizetésben Azure Cosmos db példányokra lesz alkalmazva. <br/><br/> Ha **egyetlen erőforráscsoportot**választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben lévő Azure Cosmos db példányokra alkalmazza, és az adott előfizetésen belül a kiválasztott erőforráscsoportot. <br/><br/> A foglalás hatókörét a fenntartott kapacitás megvásárlása után módosíthatja.  |
   |Előfizetés  |   Az Azure Cosmos DB fenntartott kapacitás kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P): nagyvállalati előfizetések esetén a díjakat a beléptetés pénzügyi kötelezettségvállalásának egyenlegére, vagy felszámított összegként kell levonni. <br/><br/> Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P): egyéni előfizetés utólagos elszámolású díjszabással, a díjak számlázása a hitelkártyára vagy a számla fizetési módjára történik az előfizetésben.    |
   | Erőforráscsoport | Az erőforráscsoport, amelyhez a fenntartott kapacitási kedvezmény érvényes. |
   |Időtartam  |   Egy vagy három év.   |
   |Átviteli sebesség típusa   |  Az átviteli sebesség kérési egységként van kiépítve. Megvásárolhatja a kiépített átviteli sebességre vonatkozó foglalást mindkét beállítás esetében – az egyrégiós írások és a több régióba írt írások esetében is. Az átviteli sebesség típusának két értéke van: 100 RU/s/óra és 100 multi-Master RU/s/óra.|
   | Fenntartott kapacitási egységek| A lefoglalni kívánt átviteli sebesség mennyisége. Ezt az értéket kiszámíthatja az összes Cosmos DB erőforráshoz (például adatbázisokhoz vagy tárolóhoz) szükséges átviteli sebesség meghatározásával régiónként. Ezután szorozzuk meg a Cosmos-adatbázishoz társítandó régiók számával. Például: Ha minden régióban 5, 1 000 000 RU/s-vel rendelkező régió található, akkor a foglalási kapacitás megvásárlásához válassza a 5 000 000 RU/mp lehetőséget. |


5. Az űrlap kitöltése után a lefoglalt kapacitás megvásárlásához szükséges árat számítjuk ki. A kimenet a kiválasztott beállításokkal kapott kedvezmény százalékos arányát is megjeleníti. Kattintson a **kiválasztás** elemre.

6. **A foglalások ablaktáblán** tekintse át a foglalás kedvezményét és árát. A foglalási díj az összes régióban kiépített átviteli sebességű Azure Cosmos DB erőforrásokra vonatkozik.  

   ![Fenntartott kapacitás összefoglalása](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Válassza a **felülvizsgálat + vásárlás** lehetőséget, majd **Vásároljon most**. A vásárlás sikerességét a következő oldal látja:

A foglalás megvásárlása után azonnal alkalmazni kell a foglalás feltételeinek megfelelő meglévő Azure Cosmos DB-erőforrásokra. Ha nem rendelkezik meglévő Azure Cosmos DB erőforrásokkal, a foglalás akkor érvényes, ha új Cosmos DB-példányt telepít, amely megfelel a foglalás feltételeinek. A foglalás időtartama mindkét esetben a sikeres vásárlás után azonnal elindul.

A foglalás lejáratakor a Azure Cosmos DB példányok továbbra is futnak, és az utólagos elszámolású díjszabás szerint számolunk fel díjat.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

A megfelelő fenntartott kapacitás azonosításával kapcsolatos segítségért lásd: [a foglalási kedvezmény Azure Cosmos DBre való alkalmazásának megismerése](../billing/billing-understand-cosmosdb-reservation-charges.md).

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Következő lépések

A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő Azure Cosmos DB erőforrásokra. A foglalás hatókörét a Azure Portal, a PowerShell, az Azure CLI vagy az API segítségével frissítheti.

*  Ha szeretné megtudni, hogyan alkalmazza a fenntartott kapacitás kedvezményeit Azure Cosmos DBre, tekintse meg [Az Azure foglalási kedvezmény ismertetése](../billing/billing-understand-cosmosdb-reservation-charges.md)című témakört.

* Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

   * [Mik azok az Azure-foglalások?](../billing/billing-save-compute-costs-reservations.md)  
   * [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)  
   * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../billing/billing-understand-reserved-instance-usage.md)
   * [Azure-foglalások a partner Center CSP programban](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
