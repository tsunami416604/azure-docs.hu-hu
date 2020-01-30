---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847133"
---
Mentse a prémium SSD-használatot a fenntartott kapacitással, a fenntartott virtuálisgép-példányokkal együtt, így csökkentheti a virtuális gépek teljes költségét. A foglalási kedvezményt a rendszer automatikusan alkalmazza a kijelölt foglalási hatókörben lévő megfelelő lemezekre, és a kedvezmények beszerzéséhez nem szükséges foglalást hozzárendelni egy felügyelt lemezhez. A rendszer óránként alkalmazza a kedvezményeket a lemez használatba vételével, és a fel nem használt fenntartott kapacitás nem halad át. A felügyelt lemezes foglalási kedvezmény nem vonatkozik a nem felügyelt lemezekre, az ultra-lemezekre vagy az oldal blob-felhasználására.

## <a name="determine-your-storage-needs"></a>A tárolási igények meghatározása

A foglalás megvásárlása előtt meg kell határoznia, hogy milyen tárterületre van szüksége. Jelenleg a lemezes foglalás csak a prémium szintű SSD-SKU-k esetében érhető el. A prémium szintű SSD SKU a lemez méretének és teljesítményének meghatározása. A tárolási igények meghatározásakor nem javasoljuk, hogy a lemezeket a teljes kapacitásnak megfelelően gondolja, nem használhatja a foglalást egy nagyobb méretű lemezre (például P40), és ezt két kisebb lemez (P30) használatáért kell fizetnie. Egy foglalás megvásárlásakor csak a lemezek teljes számát kell megvásárolnia SKU-ra.

A lemezes foglalás a lemezes SKU alapján történik, ezért a foglalási felhasználás a megadott méret helyett a lemezes SKU egységén alapul. Ha például 1 P40 2 TiB kiosztott kapacitással rendelkezik, de csak 2 P30-lemezt foglalt le, akkor a két P30-felhasználás nem kerül a P40-foglalásba, és az utólagos elszámolású díjszabást kell fizetnie.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Vásárlási szempontok

A következő ajánlott eljárásokat javasoljuk a lemezes foglalás megvásárlásakor:

- A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia. A kiépítés vagy a felhasznált lemez kapacitása helyett ellenőrizze, hogy a lemez SKU-ban használja-e a használatot. 
- Ellenőrizze a lemez foglalását a virtuális gép foglalásával együtt. Javasoljuk, hogy a maximális megtakarítás céljából foglaljon le a virtuális gépek és a lemezek használatáról. A virtuális gép megfelelő lefoglalásának meghatározásával elkezdheti a lemezes foglalás kiértékelését. Általánosságban elmondható, hogy az egyes számítási feladatokhoz egy standard konfiguráció tartozik, például egy SQL-kiszolgáló két P40 adatlemezzel és egy P30 operációsrendszer-lemezzel rendelkezik. Ez a fajta minta segíthet megállapítani a megvásárolt foglalások mennyiségét. Ez a megközelítés leegyszerűsíti a kiértékelési folyamatot, és gondoskodik arról, hogy az előfizetések, a régiók és egyebek tekintetében a virtuális gépekhez és a lemezekhez igazított terv is tartozik. 

## <a name="purchase-restrictions"></a>Vásárlási korlátozások

A foglalási kedvezmények jelenleg nem érhetők el a következő lemezekhez:
- Nem felügyelt lemezek/Blobok
- standard SSD vagy standard HDD
- Prémium SSD SKU-nál kisebb P30 – foglalások nem érhetők el P1/P2/P3/P4/P6/P10/P15/P20 prémium SSD SKU esetén.
- Felhők – a foglalások nem vásárolhatók meg az Azure gov, Germany és China régiókban.
- Kapacitás korlátozásai – ritka körülmények között az Azure korlátozza a lemezes SKU-alkészletek új foglalásának megvásárlását a régió alacsony kapacitása miatt.

## <a name="buy-a-disk-reservation"></a>Lemezes foglalás vásárlása

Az Azure-lemezek foglalását a [Azure Portal](https://portal.azure.com/)keresztül is megvásárolhatja. A foglalás elöl vagy havi fizetéssel is megfizethető. További információ a havi fizetések megvásárlásáról: [foglalások vásárlása havi fizetéssel](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) paneljére.
1. Foglalás megvásárlásához válassza az **Azure Managed Disks** lehetőséget.

    ![Disks-Reserved-Purchase-Reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   |Mező  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Azt jelzi, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Azt is meghatározza, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/> Ha a **megosztott**lehetőséget választja, a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a számlázási környezetben található bármely előfizetésben. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a megosztott hatókör magában foglalja az összes olyan előfizetést, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabású.  <br/><br/>  Ha az **egyszeri előfizetést**választja, a foglalási kedvezményt a rendszer a kijelölt előfizetés Azure Storage-kapacitására alkalmazza. <br/><br/> Ha **egyetlen erőforráscsoportot**választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben és a kiválasztott erőforráscsoporthoz alkalmazza az Azure Storage-kapacitásra. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Storage-foglalás kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P): nagyvállalati előfizetések esetén a díjakat a beléptetés pénzügyi kötelezettségvállalásának egyenlegére, vagy felszámított összegként kell levonni. <br/><br/> Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P): egyéni előfizetés utólagos elszámolású díjszabással, a díjak számlázása a hitelkártyára vagy a számla fizetési módjára történik az előfizetésben.    |
   | **Lemezek** | A létrehozni kívánt SKU. |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Számlázási gyakoriság** | Azt jelzi, hogy a fiók milyen gyakran kerül kiszámlázásra a foglaláshoz. A lehetőségek közé tartoznak a *havonta* vagy a *kezdeti*beállítások. |

    ![Premium-SSD-Reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. A foglalás paramétereinek kiválasztása után a Azure Portal megjeleníti a költségeket. A portálon az utólagos elszámolású számlázásnál is látható az engedmény százaléka. A **tovább** gombra kattintva folytassa a **foglalások megvásárlása** panelen.

1. A **foglalások ablaktáblán** megadhatja a foglalást, és kiválaszthatja a kívánt foglalások teljes mennyiségét. A foglalások száma a lemezek számához. Ha például száz lemezt szeretne lefoglalni, a **mennyiséget** 100-re változtathatja.
1. Tekintse át a foglalás teljes díjait.

    ![Premium-SSD-Reserved-selecting-SKU-Total-Purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

A foglalás megvásárlása után a rendszer automatikusan alkalmazza azokat a meglévő Azure Disk Storage-erőforrásokra, amelyek megfelelnek a foglalás feltételeinek. Ha még nem hozott létre Azure Disk Storage-erőforrásokat, akkor a foglalás akkor érvényes, ha olyan erőforrást hoz létre, amely megfelel a foglalás feltételeinek. A foglalás időtartama mindkét esetben azonnal megkezdődik a sikeres vásárlás után.

## <a name="exchange-or-refund-a-reservation"></a>Foglalás cseréje vagy visszatérítése

Bizonyos korlátozásokkal a foglalások cseréjét vagy visszatérítését is elvégezheti.

Egy foglalás cseréjéhez vagy visszafizetéséhez navigáljon a Azure Portal foglalási adataihoz. Válassza az **Exchange vagy a visszatérítés**lehetőséget, és kövesse az utasításokat egy támogatási kérelem elküldéséhez. A kérelem feldolgozása után a Microsoft e-mailt küld Önnek a kérelem befejezésének megerősítéséhez.

Azure Reservations szabályzatokkal kapcsolatos további információkért lásd: [önkiszolgáló cserék és visszatérítések Azure Reservationshoz](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Foglalás cseréje

A foglalások cseréje lehetővé teszi, hogy a foglalás fel nem használt része alapján arányosan fizetett visszatérítést kapjon. Ezután a visszatérítést egy új Azure-lemez foglalásának beszerzési árára is alkalmazhatja.
A cserére nem vonatkozik semmilyen korlátozás. A cserére ezen kívül díjat sem számolunk fel. A megvásárolt új foglalásnak egyenlőnek vagy nagyobbnak kell lennie, mint az eredeti foglalásból származó arányos kreditek összege. Az Azure-lemezek foglalása csak egy másik Azure-beli foglalásra, a többi Azure-szolgáltatás foglalására nem használható.

### <a name="refund-a-reservation"></a>Foglalás visszafizetése

Bármikor lemondhatja az Azure-lemezek foglalását. Ha megszakítja a szolgáltatást, a foglalás hátralévő időszaka alapján arányosan elszámolt visszatérítést kap, amely mínusz egy 12%-os korai lemondási díj. A maximális visszatérítés évente $50 000.
A foglalás megszakítása azonnal leállítja a foglalást, és a fennmaradó hónapokat visszaadja a Microsoftnak. A fennmaradó arányos egyenleg, a díj levonása után a rendszer a vásárlás eredeti formáját fogja visszatéríteni.

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata

Ha egy foglalás lejár, az adott foglalás keretében használt Azure-lemez kapacitása az utólagos elszámolású díjszabás szerint kerül kiszámlázásra. A foglalások nem újulnak meg automatikusan.
A foglalás lejárata előtt 30 nappal a lejárati dátummal megjelenő e-mail-értesítést fog kapni. A foglalás által biztosított költségmegtakarítások kihasználása érdekében a lejárati dátumnál újabb megújítást kell folytatni.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure Reservations?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Megtudhatja, hogyan alkalmazza a foglalási kedvezményt Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)