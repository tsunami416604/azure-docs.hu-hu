---
ms.topic: include
ms.openlocfilehash: c30dbe25252a18e1edaed5ec81d86cc1dd976250
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513834"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel

Fizessen elő a virtuális gépekhez, és pénzt takaríthat meg az Azure Reserved Virtual Machine (VM) Instances. További információkért lásd: [Azure Reserved VM Instances ajánlat](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Azure fenntartott példányok is elérhetőek a [az Azure portal](https://portal.azure.com). A fenntartott példány megvásárlása:
-   Meg kell egy tulajdonosi szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre.
-   Vállalati előfizetés esetén a fenntartott példányok vásárlási engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com).
-   Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg a fenntartott példányok.

## <a name="determine-the-right-vm-size-before-purchase"></a>Határozza meg a megfelelő Virtuálisgép-méret vásárlás előtt
A mérőszám alkategóriája és a termék mezők a használati adatok nem tesz különbséget a prémium szintű storage, amelyek nem használják a prémium szintű tárolás Virtuálisgép-méretek használata Virtuálisgép-méretek közötti, ezen mező segítségével határozza meg a virtuális gép méretét a foglalásvásárlást vezethet, helytelen Foglalás megvásárlása, és nem ruházzák fel Önt foglalás kedvezményeket. Az alábbi módszerek egyikével határozza meg a megfelelő Virtuálisgép-méret a foglalásvásárlást.
- Tekintse meg a további információ > ServiceType mező a használati fájlban vagy az API használati adatokat. Ez a mező a megfelelő Virtuálisgép-méretet biztosít, amelyet használhat a premium storage virtuális gépek üzembe helyezésekor.
- Pontos, Powershell-lel, az Azure Resource Manager virtuális gép mérete információ jelenik meg, vagy a virtuális gép részletei az Azure Portalon.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Fenntartott virtuálisgép-példány vásárlása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** az új fenntartott példányt vásárolni.
4. Adja meg a kötelező mezőket. Futó virtuális gépek a kiválasztott attribútumok minősítéséhez beolvasni a fenntartott példány kedvezményt megfelelő példányok. A Virtuálisgép-példányok, amely a kedvezmény a tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |Ez a fenntartott példány neve.| 
    |Előfizetés|A fenntartott példány díjköteles használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a fenntartott példány előzetes költségei. Az előfizetés típusa kétféle lehet: nagyvállalati szerződés (ajánlatszám: MS-AZR-0017P) vagy használatalapú fizetéses (ajánlatszám: MS-AZR-0003P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|    
    |Hatókör       |A fenntartott példány hatóköre egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <ul><li>Ebben az előfizetésben virtuális gépek egyetlen előfizetés – a fenntartott példány kedvezmény érvényes. </li><li>Közös – a fenntartott példány kedvezményt alkalmazza a rendszer olyan előfizetéseket, a számlázási környezetben futó virtuális gépek. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és előfizetéseken belül a regisztráció (kivéve a fejlesztési és tesztelési előfizetések) magában foglalja. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.</li></ul>|
    |Hely    |Az Azure-régió, amelyet a fenntartott példány jelez.|    
    |Virtuális gép mérete     |A Virtuálisgép-példányok méretétől.|
    |Optimalizálás:     |Virtuálisgép-példányok rugalmassága mérete alkalmazza a foglalási kedvezményt többi virtuális géphez ugyanazon [VM-méret csoport](https://aka.ms/RIVMGroups). Kapacitás prioritása fenntartja az Adatközpont-kapacitással az üzemelő példányok. Ez kínál a további megbízhatósági arra, hogy indítsa el a Virtuálisgép-példányok, amikor szüksége van rájuk. Kapacitás prioritása csak akkor használható, ha a Foglalás hatóköre egy előfizetés. |
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |A fenntartott példány belül megvásárolt példányok száma. A mennyiség a futó kérheti le a számlázási kedvezményt Virtuálisgép-példányok számát. Például ha 10 Standard_D2 virtuális gépeket futtat az USA keleti régiójában, majd kell megadni mennyiség 10 az összes futó gépek juttatása maximalizálása érdekében. |
5. Megtekintheti a fenntartott példányok költségeinek kiválasztásakor **költség kiszámítása**.

    ![Képernyőkép a fenntartott példány megvásárlása elküldése előtt](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Válassza a **Beszerzés** lehetőséget.
7. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

    ![Képernyőkép a fenntartott példány megvásárlása elküldése után](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>További lépések 
Rendszer automatikusan alkalmazza a fenntartott példány kedvezményt a futó virtuális gépek, amelyek megfelelnek a fenntartott példány hatóköre és attribútumok számát. Keresztül a fenntartott példány hatóköre frissítheti [az Azure portal](https://portal.azure.com), PowerShell, CLI és az API-n keresztül. 

Ismerje meg, hogyan kezelheti a fenntartott példányok, lásd: [kezelése az Azure-beli fenntartott példányok](../articles/billing/billing-manage-reserved-vm-instance.md).

Az Azure fenntartott példányok kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure Reserved VM Instances?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Az Azure-beli fenntartott példányok kezelése](../articles/billing/billing-manage-reserved-vm-instance.md)
- [A fenntartott példányokra vonatkozó kedvezmény alkalmazásának ismertetése](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Fenntartott példányok használata használatalapú fizetéses előfizetésnél](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Fenntartott példányok használata vállalati regisztrációnál](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [A fenntartott példányok díjában nem szereplő Windows-szoftverköltségek](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Fenntartott példányok a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
