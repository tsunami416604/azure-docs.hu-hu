---
title: Fenntartott kapacitás az Azure Cosmos DB-ben a költségek optimalizálásához
description: Ismerje meg, hogyan vásárolhat Azure Cosmos DB fenntartott kapacitást a számítási költségek megtakarításához.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505963"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Költségek optimalizálása lefoglalt kapacitással az Azure Cosmos DB-ben

A fenntartott Azure Cosmos DB-kapacitással pénzt takaríthat meg, ha Azure Cosmos DB-erőforrásokat foglal le egy vagy három évre. Ha rendelkezik fenntartott Azure Cosmos DB-kapacitással, kedvezményt kaphat az Azure Cosmos DB-erőforrások teljes kiépítésére. Példák erőforrásokra, amelyek adatbázisok és tárolók (táblák, gyűjtemények és gráfok).

Az Azure Cosmos DB fenntartott kapacitása jelentősen&mdash;csökkentheti a Cosmos DB költségeit akár 65 százalékkal a rendszeres árak on egy éves vagy hároméves előzetes kötelezettségvállalás. A fenntartott kapacitás számlázási engedményt biztosít, és nincs hatással az Azure Cosmos DB-erőforrások futásidejű állapotára.

Az Azure Cosmos DB fenntartott kapacitása az erőforrások számára kiosztott átviteli kapacitást fedezi. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit. Amint megvásárol egy foglalást, a foglalási attribútumokkal megegyező átviteli díjakat a továbbiakban nem számítjuk fel a felosztó-kiosztó díjszabás szerint. A foglalásokról további információt az [Azure-foglalások](../cost-management-billing/reservations/save-compute-costs-reservations.md) ról szóló cikkben talál.

Az Azure Cosmos DB fenntartott kapacitását az [Azure Portalon](https://portal.azure.com)vásárolhatja meg. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/monthly-payments-reservations.md). Fenntartott kapacitás vásárlása:

* Legalább egy vállalati vagy egyéni előfizetéstulajdonosi szerepkörben kell lennie, ha felosztó-ki-felárak vannak.  
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
* A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak Azure Cosmos DB fenntartott kapacitást.

## <a name="determine-the-required-throughput-before-purchase"></a>A vásárlás előtti szükséges átviteli szint meghatározása

A lefoglalt kapacitásvásárlás méretének a meglévő vagy a hamarosan üzembe helyezendő Azure Cosmos DB-erőforrások óránkénti által használandó átviteli mennyiségén kell alapulnia. Például: Vásároljon 30 000 RU/s fenntartott kapacitást, ha ez a konzisztens óránkénti használati minta. Ebben a példában a 30 000 RU/s feletti kiosztott átviteli sebességet a használatalapú fizetésdíj használatával számlázunk. Ha a kiosztott átviteli kapacitás egy óra alatt 30 000 RU/s alatt van, akkor az adott órára fenntartott extra kapacitás kárba vész.

A vásárlási javaslatokat az óránkénti használati minta alapján számítjuk ki. Az elmúlt 7, 30 és 60 nap használatelemzése történik, és ajánlott a megtakarítást maximalizáló fenntartott kapacitásvásárlás. Az ajánlott foglalási méreteket az Azure Portalon az alábbi lépések segítségével tekintheti meg:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)  

2. Válassza az **Összes szolgáltatás** > **foglalásának** > **hozzáadása lehetőséget.**

3. A **Vásárlások foglalások** ablaktáblán válassza az **Azure Cosmos DB**lehetőséget.

4. Az ajánlott foglalások megtekintéséhez válassza az **Ajánlott** lapot:

A javaslatokat a következő attribútumok alapján szűrheti:

- **Időtartam** (1 év vagy 3 év)
- **Számlázási gyakoriság** (havi vagy előzetes)
- **Átviteli adattípus** (VT és többfős VT)Throughput Type (RU's vs Multi-master RU's)

Emellett a javaslatok at egyetlen erőforráscsoporton, egyetlen előfizetésen vagy a teljes Azure-regisztráción belül lehet. 

Íme egy példa javaslat:

![Fenntartott kapacitásra vonatkozó ajánlások](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Ez az ajánlás egy 30 000 RU/s foglalás megvásárlására azt jelzi, hogy a 3 éves foglalások között a 30 000 RU/s foglalási méret maximalizálja a megtakarítást. Ebben az esetben a javaslat az Azure Cosmos DB-használat elmúlt 30 napja alapján kerül kiszámításra. Ha az ügyfél azt várja, hogy az Azure Cosmos DB használatának elmúlt 30 napja reprezentatív a jövőbeli használatra nézve, akkor a 30 000 RU/s-os foglalás megvásárlásával maximalizálják a megtakarításokat.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB lefoglalt kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)  

2. Válassza az **Összes szolgáltatás** > **foglalásának** > **hozzáadása lehetőséget.**  

3. A **Vásárlások foglalások** ablaktáblán válassza az **Azure Cosmos DB** lehetőséget egy új foglalás megvásárlásához.  

4. Töltse ki a szükséges mezőket az alábbi táblázatban leírtak szerint:

   ![Töltse ki a lefoglalt kapacitás űrlapot](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Mező  |Leírás  |
   |---------|---------|
   |Hatókör   |   Az a beállítás, amely azt szabályozza, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási előnyt. Azt is szabályozza, hogy a foglalás hogyan vonatkozik az adott előfizetésekre. <br/><br/>  Ha a Megosztott lehetőséget **választja,** a foglalási kedvezmény az Azure Cosmos DB-példányokra vonatkozik, amelyek a számlázási környezetben bármely előfizetésben futnak. A számlázási környezet az Azure-ra való feliratkozás módjától függ. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú ügyfelek számára a megosztott hatókör az összes egyéni előfizetés, amely a fiókrendszergazdája által létrehozott használatalapú díjszabást támogatja.  <br/><br/>  Ha **az egyetlen előfizetés**t választja, a foglalási kedvezmény az Azure Cosmos DB-példányokra vonatkozik a kiválasztott előfizetésben. <br/><br/> Ha **az Egyetlen erőforráscsoport**lehetőséget választja, a foglalási kedvezmény a kiválasztott előfizetésben és az adott előfizetésen belül az Azure Cosmos DB-példányokra lesz alkalmazva. <br/><br/> A lefoglalt kapacitás megvásárlása után módosíthatja a foglalási hatókört.  |
   |Előfizetés  |   Az Azure Cosmos DB fenntartott kapacitásának kifizetésére használt előfizetés. A kiválasztott előfizetés fizetési módja a költségek felszámítására szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati szerződés (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P): Nagyvállalati előfizetés esetén a díjakat levonják a beléptetés pénzügyi kötelezettségvállalási egyenlegéből, vagy túllépésként számítanak fel. <br/><br/> Egyéni előfizetés felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P): A felosztó-kiosztó díjak at az előfizetés hitelkártyájára vagy számlafizetési módjára számláznak.    |
   | Erőforráscsoport | Az az erőforráscsoport, amelyre a lefoglalt kapacitásengedmény vonatkozik. |
   |Időtartam  |   Egy vagy három éve.   |
   |Átviteli típus   |  Átviteli átviteli igény igény egységekként van kiépítve. A kiépített átviteli sebességű mindkét beállításhoz vásárolhat egy foglalást – egyrégiós írások, valamint több régió írási műveletek. Az átviteli típus két érték közül választhat: 100 RU/s/s óránként és 100 többfős RU/s óránként.|
   | Fenntartott kapacitásegységek| A lefoglalni kívánt átviteli kapacitás mennyisége. Ezt az értéket úgy számíthatja ki, hogy régiónként határozza meg az összes Cosmos DB-erőforráshoz (például adatbázisokhoz vagy tárolókhoz) szükséges átviteli értéket. Ezután szorozza meg a Cosmos-adatbázishoz társítani kívánt régiók számával. Például: Ha öt régióval rendelkezik, ahol minden régióban 1 millió RU/s van, válassza ki az 5 millió RU/s-ot a foglalási kapacitás megvásárlásához. |


5. Az űrlap kitöltése után kiszámítja a lefoglalt kapacitás megvásárlásához szükséges árat. A kimenet azt is mutatja, hogy a kiválasztott opciókkal a kedvezmény százalékos arányát kapja. Következő kattintás **a Kijelölés gombra**

6. A **Vásárlás foglalások** ablaktáblán tekintse át az engedményt és a foglalás árát. Ez a foglalási ár az Azure Cosmos DB-erőforrásokra vonatkozik, amelyek átviteli hatóát az összes régióban kikell építeni.  

   ![Fenntartott kapacitás összegzése](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Válassza **a Véleményezés + vásárlás,** majd a vásárlás **most**lehetőséget. Ha a vásárlás sikeres, a következő oldal jelenik meg:

Foglalás megvásárlása után azonnal alkalmazza a meglévő Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a foglalás feltételeinek. Ha nem rendelkezik meglévő Azure Cosmos DB-erőforrásokkal, a foglalás akkor lesz érvényes, ha egy új Cosmos DB-példányt telepít, amely megfelel a foglalás feltételeinek. Mindkét esetben a foglalás időtartama közvetlenül a sikeres vásárlás után kezdődik.

Amikor a foglalás lejár, az Azure Cosmos DB-példányok továbbra is futnak, és a rendszeres felosztó-kirovó díjszabás szerint kerülnek számlázásra.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>További lépések

A foglalási kedvezmény automatikusan az Azure Cosmos DB-erőforrások, amelyek megfelelnek a foglalási hatókör és attribútumok automatikusan. Frissítheti a foglalás hatókörét az Azure Portalon, a PowerShellen, az Azure CLI-n vagy az API-n keresztül.

*  Ha meg szeretné tudni, hogy a fenntartott kapacitásengedmények hogyan vonatkoznak az Azure Cosmos [DB-re, olvassa el az Azure-foglalási kedvezmény ismertetése című témakört.](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)

* Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

   * [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-foglalások a Partnerközpont CSP-programjában](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
