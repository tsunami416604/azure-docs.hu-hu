---
title: Előre fizetett számítási fenntartott kapacitással - Azure cache a Redis
description: Előre fizet az Azure Cache for Redis számítási erőforrások fenntartott kapacitással
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530301"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Előre fizet az Azure Cache for Redis számítási erőforrások fenntartott kapacitással

A Redis Azure Cache for Redis mostantól segít pénzt megtakarítani azáltal, hogy előre kifizeti a számítási erőforrásokat a használatalapú díjárakhoz képest. Az Azure Cache for Redis fenntartott kapacitással egy vagy három évre előzetes kötelezettséget vállal a gyorsítótárra, hogy jelentős kedvezményt kapjon a számítási költségekből. Az Azure Cache a Redis fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a szolgáltatási szintet és a kifejezést.

Nem kell hozzárendelnie a foglalást adott Azure-gyorsítótárhoz a Redis-példányok hoz. Egy már futó Azure-cache redis vagy az újonnan telepített automatikusan megkapja a fenntartott díjszabás előnyeit, a fenntartott gyorsítótár méretéig. A foglalás megvásárlásával ön egy vagy három évig előre megfizeti a számítási költségeket. Amint megvásárol egy foglalást, az Azure Cache for Redis számítási díjak, amelyek megfelelnek a foglalási attribútumok már nem kell fizetni a pay-as-you-go díjakat. A foglalás nem terjed ki a gyorsítótárhoz kapcsolódó hálózati vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és az Azure Cache for Redis számlázása a felosztó-kirovó díj. A foglalások nem újulnak meg automatikusan. A díjszabási információkért tekintse meg az [Azure Cache for Redis fenntartott kapacitásajánlat.](https://azure.microsoft.com/pricing/details/cache)

Az [Azure-portálon](https://portal.azure.com/)megvásárolhatja a Redis számára fenntartott kapacitásazure-gyorsítótárát. A lefoglalt kapacitás megvásárlása:

* Legalább egy vállalati vagy egyéni előfizetés tulajdonosi szerepkörben kell lennie, ha felosztó-ki-felárak vannak.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
* A felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak Azure Cache-t a Redis számára fenntartott kapacitáshoz.

A nagyvállalati ügyfelek és az használatalapú fizetéses ügyfelek foglalási vásárlásokért való díjfizetésével kapcsolatos részletekért [tekintse meg az Azure-foglalás használatának megértését a vállalati regisztrációhoz,](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) és [ismerje meg az Azure-foglalás használatát az használatalapú fizetéses előfizetéshez.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-cache-size-before-purchase"></a>A megfelelő gyorsítótárméret meghatározása vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezendő gyorsítótár által egy adott régióban és ugyanazon szolgáltatási szint használatával használt számítási teljes mennyiségen kell alapulnia.

Tegyük fel például, hogy egy általános célt futtat, a Gen5 – 32 virtuálismagos gyorsítótárat, és két memóriaoptimalizált memóriát, a Gen5 – 16 virtuálismagos gyorsítótárakat. Továbbá, azt feltételezzük, hogy azt tervezi, hogy telepíteni a következő hónapban egy további általános célja, Gen5 – 32 virtuális magadatbázis-kiszolgáló, és egy memória optimalizált, Gen5 – 16 vCore adatbázis-kiszolgáló. Tegyük fel, hogy tudja, hogy szüksége lesz ezekre az erőforrásokra legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 64 (2x32) virtuális mag, 1 éves foglalás egyetlen adatbázis általános célja - Gen5 és egy 48 (2x16 + 16) virtuális mag 1 év foglalás egyetlen adatbázis memória optimalizált - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure-gyorsítótár vásárlása a Redis számára fenntartott kapacitáshoz

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Válassza a **Minden szolgáltatás** > **foglalása lehetőséget.**
3. Válassza **a Hozzáadás** lehetőséget, majd a Vásárlás foglalások ablaktáblán válassza az **Azure Cache for Redis** lehetőséget a gyorsítótárak új foglalásának megvásárlásához.
4. Töltse ki a szükséges mezőket. A meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumok jogosultak a fenntartott kapacitáskedvezmény. Az Azure-gyorsítótár a Redis-példányok, amelyek a kedvezmény tkapja meg a kedvezményt a ttól függ, hogy a hatókör és a kiválasztott mennyiség.


![A fenntartott árak áttekintése](media/cache-reserved-pricing/cache-reserved-price.png)


Az alábbi táblázat a szükséges mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | Az Azure Cache a Redis fenntartott kapacitásfoglalás fizetni használt előfizetés. Az előfizetés fizetési módja az Azure Cache előzetes költségeit számítja fel a Redis számára fenntartott kapacitásfoglalásért. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyedi megállapodásnak kell lennie a felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.
| Hatókör | A foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja: </br></br> **Megosztott**, a foglalási kedvezmény az Azure Cache redis-példányok futó bármely előfizetések a számlázási környezetben. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyetlen előfizetés**, a foglalási kedvezmény az Azure Cache redis példányok ebben az előfizetésben. </br></br> **Egyetlen erőforráscsoport,** a foglalási kedvezmény az Azure Cache redis példányok a kiválasztott előfizetés és a kiválasztott erőforráscsoport az adott előfizetésen belül.
| Régió | Az Azure-régió, amely az Azure Cache redis fenntartott kapacitásfoglalás által lefedett.
| Tarifacsomag | Az Azure Cache a Redis-kiszolgálók szolgáltatási szintje.
| Időtartam | Egy év vagy három év
| Mennyiség | Az Azure Cache-en belül a Redis számára fenntartott kapacitásfoglaláshoz vásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és szolgáltatási rétegben lévő gyorsítótárak száma, amelyek le vannak foglalva, és a számlázási engedményt kapják. Ha például egy Azure-gyorsítótárat futtat vagy tervez futtatni a Redis-kiszolgálók számára, amelynek teljes gyorsítótár-kapacitása 26 GB az USA keleti régiójában, akkor a mennyiséget 26-ként kell megadnia, hogy maximalizálja az összes gyorsítótár előnyét.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>A gyorsítótár méretének rugalmassága

A gyorsítótár méretének rugalmassága segít a szolgáltatási szinten és a régión belül fel- vagy leskálázásban a fenntartott kapacitás előnyének elvesztése nélkül.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalási kedvezmény automatikusan alkalmazza az Azure Cache redis példányok, amelyek megfelelnek a foglalási hatókör és attribútumok. Frissítheti a foglalás hatókörét az Azure Portalon, a PowerShellen, az Azure CLI-n vagy az API-n keresztül.

*  Ha meg szeretné tudni, hogy a fenntartott kapacitásengedmények hogyan kerülnek alkalmazásra a Redis Azure-gyorsítótárában, [olvassa el az Azure-foglalási kedvezmény ismertetése című témakört.](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

    * [Mik azok az Azure-foglalások?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Azure-foglalások kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

