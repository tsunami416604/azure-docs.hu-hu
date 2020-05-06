---
title: Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai
description: Ismerje meg, hogyan cserélheti be vagy térítheti vissza az Azure Reservations foglalásait.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 174ed17056bf49b541d55719f4058141e88e7ea5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192110"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai

Az Azure Reservations biztosítja a rugalmasságot a változó igények kielégítéséhez. A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD/év összegig, ha már nincs rá szüksége. A visszatérítések felső korlátja a Microsofttal kötött szerződése hatálya alá tartozó összes foglalásra vonatkozik.

Az önkiszolgáló csere- és lemondási lehetőség nem érhető el az Egyesült Államok kormányának nagyvállalati szerződésével rendelkező ügyfelei számára. Az Egyesült Államok kormányának (US Government) egyéb előfizetési típusai támogatottak, például a használatalapú fizetés és a CSP.

Meglévő foglalások cseréjéhez vagy visszatérítéséhez tulajdonosi hozzáféréssel kell rendelkeznie a foglalási rendeléshez.

## <a name="exchange-an-existing-reserved-instance"></a>Meglévő fenntartott példány cseréje

A foglalás cseréjét három gyors lépésbe végezheti el az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Válassza ki a visszatéríteni kívánt foglalásokat, majd válassza az **Átváltás** lehetőséget.  
    ![Példa kép a visszaadni kívánt foglalásokról](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Válassza ki a megvásárolni kívánt virtuálisgép-terméket, és adja meg a mennyiséget. Győződjön meg arról, hogy az új vásárlás végösszege nagyobb, mint a visszatérítés végösszege. [A vásárlás előtt határozza meg a megfelelő méretet.](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)  
    ![Példa kép a cserével vásárolni kívánt virtuálisgép-termékről](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Tekintse át és fejezze be a tranzakciót.  
    ![Példa kép a cserével vásárolni kívánt virtuálisgép-termékről, a csere teljesítésével](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Egy foglalás visszatérítéséhez lépjen a **Foglalás részleteire**, és válassza a **Visszatérítés** lehetőséget.

## <a name="how-transactions-are-processed"></a>A tranzakciók feldolgozása

Először a Microsoft megszakítja a meglévő foglalást, és visszatéríti az adott foglalásra vonatkozó arányosított összeget. Ha van csere, akkor a rendszer feldolgozza az új vásárlást. A Microsoft a visszatérítések feldolgozását az alábbi módszerek valamelyike szerint végzi, a fióktípustól és a fizetés módjától függően:

### <a name="enterprise-agreement-customers"></a>Nagyvállalati Szerződéssel rendelkező ügyfelek

A pénz hozzáadódik a csereprogramok és visszatérítések pénzügyi keretéhez, ha az eredeti vásárlás is ilyen módon történt. Az eredeti vásárlások óta készült túlhasználati számlákat a rendszer újra megnyitja és újraszámolja, ezzel biztosítva a pénzügyi keret felhasználását. Ha a foglalást használó pénzügyi keret időtartama már nem aktív, akkor a kredit hozzáadódik a jelenlegi Nagyvállalati Szerződéshez tartozó pénzügyi keret időtartamához. A jóváírás a visszatérítés napjától kezdve 90 napig érvényes. A fel nem használt jóváírás 90 nap után lejár.

Ha az eredeti vásárlás túlhasználatként történt, a Microsoft kiállít egy jóváírási emlékeztetőt.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Használatalapú fizetéses számlás fizetések és a CSP-program

A rendszer az eredeti foglalás vásárlási számláját érvényteleníti, és létrehoz egy új számlát a visszatérítésről. Csere esetén az új számla tartalmazza a visszatérítést és az új vásárlást. A visszatérítés összegét a rendszer a vásárláshoz igazítja. Ha csak a foglalás lett visszatérítve, akkor az arányosított összeg a Microsoftnál marad, és egy későbbi foglalás vásárlásakor lesz beszámítva.

### <a name="pay-as-you-go-credit-card-customers"></a>Használatalapú fizetéses hitelkártyás ügyfelek

A rendszer az eredeti számlát érvényteleníti, és létrehoz egy új számlát. A pénz az eredeti vásárláshoz használt hitelkártyára lesz visszatérítve. Ha azóta módosította a kártyát, [forduljon az ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="cancel-exchange-and-refund-policies"></a>Lemondással, cserével és visszatérítéssel kapcsolatos szabályzatok

Az Azure-ban a következő szabályzatok érvényesek a lemondásokra, cserékre és visszatérítésekre.

**Csereszabályzatok**

- Egyszerre több létező foglalást is becserélhet egy ugyanolyan típusú új foglalás vásárlásakor. Az adott típusú foglalásokat nem lehet másik típusúra cserélni. Például nem lehet egy virtuális gép foglalását egy SQL-foglalás megvásárlására becserélni.
- A cseréket csak foglalások tulajdonosai dolgozhatják fel. További tudnivalók: [A foglalást kezelő felhasználók hozzáadása vagy módosítása](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A cserét a rendszer visszatérítésként és újravásárlásként dolgozza fel – vagyis külön tranzakciókat hoz létre a lemondáshoz és az új vásárláshoz. A becserélt foglalások arányosított foglalási összegét visszatéríti a rendszer. Az új vásárlásért pedig teljes díjat kell fizetni. Az arányosított foglalási összeg a visszaadott foglalás nap szerint kiszámított maradványértéke.
- A foglalások akkor is becserélhetők vagy visszatéríthetők, ha a megvásárlásukhoz használt Nagyvállalati Szerződés lejárt, és új szerződésként lett megújítva.
- A cserék során a foglalások bármely tulajdonsága módosítható, így a család, a sorozat, a verzió, a termékváltozat, a régió, a mennyiség és az időtartam is.
- Az új vásárlásnak a visszatérített összeggel egyenlőnek vagy annál nagyobb összegűnek kell lennie.
- A csere keretében megvásárolt új foglalás új időtartamot kap, amelynek a csere időpontja lesz a kezdete.
- A cserékre semmilyen pótdíj vagy éves korlát nem vonatkozik.

**Visszatérítési szabályzatok**
- A foglalás jövőbeli lemondásáért 12%-os korai felmondási díj számítható fel. Ezt jelenleg nem alkalmazzuk.
- A teljes visszatérítési összeg nem haladhatja meg az 50 000 USD-t az elmúlt 12 hónapra vonatkozóan.
- A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számítja ki.
- A visszatérítéseket csak a foglalásrendelések tulajdonosai dolgozhatják fel. További tudnivalók: [A foglalást kezelő felhasználók hozzáadása vagy módosítása](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Nem prémium szintű tároló cseréje prémium tárolóra

Ha egy olyan virtuálisgép-méretre rendelkezik foglalással, amely nem támogatja a Premium Storage-ot, becserélheti egy olyan egyenértékű VM-méretre, amely támogatja azt. Például egy _F1_ méretűt egy _F1s_ méretűre. A csere végrehajtásához lépjen a Foglalás részleteire, és válassza a **Csere** lehetőséget. A csere nem állítja vissza a fenntartott példány időtartamát, és nem hoz létre új tranzakciót.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
    - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
    - [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)
