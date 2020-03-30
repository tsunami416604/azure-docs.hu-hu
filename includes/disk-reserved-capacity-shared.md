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
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590747"
---
Mentse az Azure Disk Storage-használatot a fenntartott kapacitással. Az Azure Disk Storage-foglalások és az Azure fenntartott virtuálisgép-példányok segítségével csökkentheti a virtuális gép (VM) teljes költségeit. A foglalási kedvezmény automatikusan vonatkozik a kiválasztott foglalási hatókör megfelelő lemezeire. Az automatikus alkalmazás miatt nem kell foglalást hozzárendelnie egy felügyelt lemezhez a kedvezmények leszámításához.

A kedvezmények a lemezhasználattól függően óránként kerülnek alkalmazásra. A fel nem használt fenntartott kapacitás nem vihető át. Az Azure Disk Storage foglalási kedvezményei nem vonatkoznak a nem felügyelt lemezekre, az ultralemezekre vagy a lapblob-felhasználásra.

## <a name="determine-your-storage-needs"></a>Határozza meg a tárolási igényeket

Mielőtt megvásárolna egy foglalást, határozza meg a tárolási igényeket. Jelenleg az Azure Disk Storage-foglalások csak bizonyos Prémium Szintű SSD-sK-k esetén érhetők el. A prémium szintű SSD termékváltozata határozza meg a lemez méretét és teljesítményét.

A tárolási igények meghatározásakor ne gondoljon a lemezekre a kapacitás alapján. Például nem rendelkezhet p40-es lemezfoglalással, és ezzel fizethet két kisebb P30 lemezért. Foglalás vásárlásakor csak a termékváltozatonkénti lemezek teljes számára vásároljon foglalást.

Lemezfoglalás lemeztermékváltozatonként történik. Ennek eredményeképpen a foglalási felhasználás a lemez termékkészletének egységén alapul a megadott méret helyett.

Tegyük fel például, hogy egy P40 lemezt foglal le, amely 2 TiB kiépített tárolókapacitással rendelkezik. Azt is feltételezik, hogy csak két P30 lemezt foglal le. A P40-es foglalás ebben az esetben nem veszi figyelembe a P30-felhasználást, és a P30 lemezeken a használatalapú fizetés díját kell fizetnie.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Beszerzési szempontok

A lemezfoglalás vásárlásának mérlegelésekor a következő eljárásokat javasoljuk:

- A használati adatok elemzése segít meghatározni, hogy mely foglalásokat érdemes megvásárolnia. Győződjön meg arról, hogy a kiépített vagy használt lemezkapacitás helyett nyomon követi a lemezsku-k használatát.
- Vizsgálja meg a lemezfoglalást a virtuális gép foglalásával együtt. Javasoljuk, hogy a maximális megtakarítás érdekében foglaljon mind a virtuális gép, mind a lemezhasználat érdekében. Kezdheti a megfelelő virtuális gép-foglalás meghatározásával, majd a lemezfoglalás kiértékelésével. Általában egy szabványos konfigurációval rendelkezik az egyes számítási feladatokhoz. Egy SQL Server kiszolgáló például két P40 adatlemezzel és egy P30 operációs rendszerlemezzel rendelkezhet.
  
  Ez a fajta minta segíthet meghatározni a lefoglalt összeget lehet vásárolni. Ez a megközelítés egyszerűsítheti a kiértékelési folyamatot, és biztosíthatja, hogy a virtuális gép és a lemezek egy-egy összehangolt tervvel rendelkezzen. A csomag olyan szempontokat tartalmaz, mint az előfizetések vagy a régiók.

## <a name="purchase-restrictions"></a>Beszerzési korlátozások

A foglalási kedvezmények jelenleg nem érhetők el a következőkre:

- Nem felügyelt lemezek vagy lapblobok.
- Szabványos SSD-k vagy szabványos merevlemez-meghajtók (HDD-k).
- P30-nál kisebb prémium ssd-sk-ek: P1, P2, P3, P4, P6, P10, P15 és P20 SSD skus.
- Lemezek az Azure Government, az Azure Germany vagy az Azure China régióban.

Ritka körülmények között az Azure korlátozza az új foglalások vásárlását a lemezsinuszok egy részhalmazára, mert egy régióban alacsony kapacitású.

## <a name="buy-a-disk-reservation"></a>Lemezfoglalás vásárlása

Az Azure Disk Storage-foglalásokat az [Azure Portalon](https://portal.azure.com/)keresztül vásárolhatja meg. A foglalást előre vagy havi fizetéssel is kifizetheti. A havi fizetéssel történő vásárlásról a [Havi fizetéssel történő foglalások vásárlása](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)című témakörben talál további információt.

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Vásárlás foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ablaktáblát az Azure Portalon.

1. Válassza ki **az Azure felügyelt lemezek** et a foglalás megvásárlásához.

    ![Ablaktábla a foglalások megvásárlásához](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Adja meg a következő táblázatban leírt szükséges értékeket:

   |Elem  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási előnyt. Ez az érték azt is meghatározza, hogy a foglalás hogyan lesz alkalmazva az adott előfizetésekre. <br/><br/> Ha a Megosztott lehetőséget **választja,** a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a számlázási környezetminden előfizetésében. A számlázási környezet az Azure-ra való feliratkozás módjától függ. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú ügyfelek esetében a megosztott hatókör tartalmazza az összes egyéni előfizetést a fiókadminisztrátor által létrehozott használatalapú díjokkal.  <br/><br/>  Ha **az egyetlen előfizetés**t választja, a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a kiválasztott előfizetésben. <br/><br/> Ha **az Egyetlen erőforráscsoport**lehetőséget választja, a foglalási engedmény az Azure Storage-kapacitásra vonatkozik a kiválasztott előfizetésben és az adott előfizetés kiválasztott erőforráscsoportjában. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Storage-foglalás kifizetéséhez használt előfizetés. A kiválasztott előfizetés fizetési módja a költségek felszámítására szolgál. Az előfizetésnek a következő típusok egyikének kell lennie:<br/><ul><li> Nagyvállalati szerződés (ms-AZR-0017P és MS-AZR-0148P ajánlatszámok). Vállalati előfizetés esetén a díjakat levonják a beléptetés pénzügyi kötelezettségvállalási egyenlegéből, vagy túllépésként számítják fel.</li><br/><li>Egyéni előfizetés felosztó-kirovó díjszabással (MS-AZR-0003P és MS-AZR-0023P ajánlatszámok). A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.</li></ul>    |
   | **Lemezek** | A létrehozni kívánt termékváltozat. |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Számlázási gyakoriság** | Milyen gyakran számlázzák ki a fiókot a foglalásért. A lehetőségek közé tartozik **a havi** és **az előzetes**. |

    ![Ablaktábla a megvásárolni kívánt termék kiválasztásához.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Miután megadta a foglalás értékeit, az Azure Portal megjeleníti a költségeket. A portál on is mutatja az árengedmény százalékos több mint pay-as-you-billing. Válassza a **Tovább** lehetőséget a **Beszerzési foglalások** ablaktáblán való folytatáshoz.

1. A **Beszerzési foglalások** ablaktáblán elnevezheti a foglalást, és kiválaszthatja a foglalásteljes mennyiségét. A foglalások száma a lemezek számához van rendelve. Ha például száz lemezt szeretne lefoglalni, írja be a **100-as mennyiség** értéket. **100**

1. Tekintse át a foglalás teljes költségét.

    ![A Beszerzési foglalások ablaktábla](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

A foglalás megvásárlása után a rendszer automatikusan alkalmazza a foglalási feltételeknek megfelelő lemeztároló-erőforrásokra. Ha még nem hozott létre lemeztárolási erőforrásokat, a foglalás minden olyan erőforrás létrehozásakor érvényes, amely megfelel a foglalási feltételeknek. Mindkét esetben a foglalási időszak közvetlenül a sikeres vásárlás után kezdődik.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozások kal lemondhatja, kicserélheti vagy visszatérítheti a foglalásokat. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata

Foglalás lejártakor a foglalás keretében használt Azure Disk Storage-kapacitást a használatalapú díjfizetéskor számlázzuk ki. A foglalások nem újulnak meg automatikusan.

A foglalás lejárta előtt 30 nappal, majd a lejárati dátumon ismét e-mailben értesítést kap. A foglalás által biztosított költségmegtakarítás előnyeinek további kihasználásához újítsa meg azt legkésőbb a lejárati dátumig.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Mik azok az Azure-foglalások?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A foglalási kedvezmény Azure Disk Storage-ra való alkalmazásának megismerése](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
