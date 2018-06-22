---
ms.topic: include
ms.openlocfilehash: 32fdc23aded78ca491fbde2ef5c76247c56d0c4c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313744"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>A virtuális gépek Azure fenntartott virtuális gép osztályt előre fizetés

A virtuális gépek előre fizetés léptékű és költségtakarékosabb munkavégzésben Azure fenntartott virtuális gép (VM) osztályt. További információkért lásd: [Azure fenntartott példányok ajánlat](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Megvásárolhatja Azure fenntartott példányok a [Azure-portálon](https://portal.azure.com). Vásárolhat egy fenntartott példány:
-   Legalább egy vállalati vagy használatalapú előfizetés tulajdonosának szerepkört kell lennie.
-   A vállalati előfizetések fenntartott példány vásárlás engedélyezni kell a [EA portal](https://ea.azure.com).
-   A Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatnak a fenntartott példányok.

[!IMPORTANT]
A azonosításához az alább ismertetett módszerek egyikét kell használnia a megfelelő Virtuálisgép-méretet a Foglalás vásárolni.

## <a name="determine-the-right-vm-size-before-purchase"></a>Határozza meg a megfelelő Virtuálisgép-méretet beszerzési előtt
1. Tekintse meg a használati fájl vagy a használati API használatával határozza meg a megfelelő Virtuálisgép-méretet a Foglalás beszerzési additionalinfo részben mezője. Ne használja az értékek mérési alkategória vagy termék mezők óta a mezők nem tesznek különbséget a virtuális gépek és a nem-S verziói között.
2. Virtuálisgép-méret pontos adatok Powershell, Azure Resource Manager használatával is megkapható, vagy a virtuális gép részletezi az Azure portálon.

## <a name="buy-a-reserved-virtual-machine-instance"></a>A fenntartott virtuális gépet példánya megvásárlása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** beszerzési új fenntartott példányt.
4. Töltse ki a kötelező mezőket. Virtuális gép a kiválasztott attribútumok csak akkor lekérni a fenntartott példány kedvezményeket megfelelő példányok. A Virtuálisgép-példányok, amelyek beszerzése a kedvezményeket tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |Ez a fenntartott példány neve.| 
    |Előfizetés|Az előfizetés, a fenntartott példány fizeti. A fizetési módot, az előfizetés fel van töltve a társaságuk költségek fenntartott példány. Az előfizetés típusúnak kell lennie egy nagyvállalati szerződés (ajánlat száma: MS-AZR - 0017P) vagy használatalapú fizetés (ajánlat száma: MS-AZR - 0003P). A nagyvállalati előfizetéssel a költségek a beléptetési pénzügyi kötelezettségvállalást a egyenleg vonni vagy mint túlhasználati számítjuk fel. Használatalapú fizetés előfizetés a költségek számlázása a hitelkártya vagy számla fizetési módot, az előfizetésben való.|    
    |Hatókör       |A fenntartott példány hatókör egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha: <ul><li>Egyetlen előfizetés - a fenntartott példány kedvezményes ehhez az előfizetéshez alkalmazása a virtuális gépekhez. </li><li>A fenntartott példány kedvezményeket vonatkozik megosztott - az előfizetése a számlázási környezeten belül futó virtuális gépek. A vállalati ügyfelek a megosztott hatókör, a regisztráció és előfizetéseket (kivéve a fejlesztési és tesztelési célú előfizetések) belül a beléptetési magában foglalja. Használatalapú fizetés ügyfelek esetén a megosztott hatóköre hozta létre a fiókadminisztrátor előfizetéseket használatalapú fizetésre.</li></ul>|
    |Hely    |Az Azure-régió a fenntartott példánya jelez.|    
    |Virtuális gép mérete     |A Virtuálisgép-példány mérete.|
    |Időtartam        |Az egy vagy három év.|
    |Mennyiség    |A megvásárolt belül a fenntartott példány példányok száma. A mennyiség futó kérheti le a számlázási kedvezményeket Virtuálisgép-példányok száma. Például ha 10, Standard szintű, D2-alapú virtuális gépet az USA keleti régiója fut, majd volna meg mennyiség minden futó gépek juttatása maximalizálása 10. |
5. Megtekintheti a fenntartott példány költségét kiválasztásakor **költség**.

    ![Képernyőfelvétel a fenntartott példány beszerzési elküldése előtt](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Válassza ki **beszerzési**.
7. Válassza ki **foglalás megtekintése** vásárlását állapotát tekintheti meg.

    ![Képernyőfelvétel a fenntartott példány beszerzési elküldése után](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>További lépések 
A fenntartott példány kedvezményeket automatikusan alkalmazzák a futó virtuális gépek, amelyek megfelelnek a fenntartott példány hatókör és attribútumok számát. Frissítheti a hatókör keresztül fenntartott példány [Azure-portálon](https://portal.azure.com), PowerShell, a parancssori felületen vagy az API-n keresztül. 

A fenntartott példánya kezelése című témakörben talál [Azure fenntartott példányok kezelése](../articles/billing/billing-manage-reserved-vm-instance.md).

Azure fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Kevesebbet költeni a virtuális gépek fenntartott osztályt](../articles/billing/billing-save-compute-costs-reservations.md)
- [Az Azure fenntartott példányok kezelése](../articles/billing/billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](../articles/billing/billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](../articles/billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Fenntartott Partner Center Cloud Solution Provider (CSP) program-példány](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.