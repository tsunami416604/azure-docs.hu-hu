---
title: Azure Reservations önkiszolgáló csereprogramok és visszatérítések
description: Ismerje meg, hogyan lehet Exchange-vagy visszatérítési Azure Reservations.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 5e38684500520d4565835456b94200aea399c938
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814110"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure Reservations önkiszolgáló csereprogramok és visszatérítések

Azure Reservations rugalmasságot biztosít a változó igények kielégítése érdekében. A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD/év összegig, ha már nincs rá szüksége.

Az önkiszolgáló csere- és lemondási lehetőség nem érhető el az Egyesült Államok kormányának nagyvállalati szerződésével rendelkező ügyfelei számára. Az USA egyéb kormányzati előfizetési típusai, beleértve az utólagos elszámolású és a CSP-ket is, támogatottak.

Meglévő foglalások cseréjéhez vagy visszatérítéséhez tulajdonosi hozzáféréssel kell rendelkeznie a foglalási rendeléshez.

## <a name="exchange-an-existing-reserved-instance"></a>Meglévő fenntartott példány cseréje

A foglalást három gyors lépéssel cserélheti a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Válassza ki a visszatéríteni kívánt foglalásokat, és kattintson az **Exchange**elemre.  
    ![Példa a visszaadott foglalásokat ábrázoló képre](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Válassza ki a megvásárolni kívánt virtuálisgép-terméket, és adja meg a mennyiséget. Győződjön meg arról, hogy az új beszerzés összege meghaladja a teljes visszaadott értéket. [A vásárlás előtt határozza meg a megfelelő méretet](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Példa az Exchange-ben vásárolni kívánt virtuálisgép-terméket ábrázoló képre](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Tekintse át és fejezze be a tranzakciót.  
    ![Példa az Exchange-ben vásárolni kívánt virtuálisgép-termékre, a visszatérés befejezése](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

A foglalások visszatérítéséhez nyissa meg a **foglalás részleteit** , és kattintson a **visszatérítés**lehetőségre.

## <a name="how-transactions-are-processed"></a>A tranzakciók feldolgozásának módja

Először a Microsoft megszakítja a meglévő foglalást, és az adott foglaláshoz tartozó, a szolgáltatásra vonatkozó összegű összeget téríti vissza. Ha van Exchange, az új vásárlás feldolgozása történik. A Microsoft az alábbi módszerek egyikével dolgozza fel a visszatérítéseket a fiók típusától és a fizetési módtól függően:

### <a name="enterprise-agreement-customers"></a>Nagyvállalati szerződéssel rendelkező ügyfelek

A pénz bekerül a csereprogramok és a visszatérítések pénzügyi kötelezettségvállalására, ha az eredeti vásárlást egy használatával végezték el. Az eredeti vásárlások újbóli megnyitása és újraszámítása, valamint a pénzügyi kötelezettségvállalás használatának biztosítása érdekében a rendszer újra megnyitja a túlhasználati számlákat. Ha a foglalást használó pénzügyi kötelezettségvállalás időtartama már nem aktív, akkor a kredit bekerül az aktuális nagyvállalati szerződéshez tartozó pénzügyi kötelezettségvállalási időszakba.

Ha az eredeti vásárlás a túlhasználattal történt, a Microsoft jóváírást küld.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Utólagos elszámolású számla – fizetési és CSP-program

Az eredeti foglalási vásárlási számla megszakítva, majd új számla jön létre a visszatérítéshez. Cserék esetén az új számla a visszatérítést és az új vásárlást jeleníti meg. A visszatérítés összegét a vásárlással korrigáljuk. Ha csak a foglalást téríti vissza, akkor az elszámolási összeg a Microsoftnál marad, és egy későbbi foglalási vásárlásra van beállítva.

### <a name="pay-as-you-go-credit-card-customers"></a>Utólagos elszámolású bankkártyás ügyfelek

Az eredeti számla meg lett szakítva, és létrejön egy új számla. A pénz visszafizetése az eredeti vásárláshoz használt bankkártyára történik. Ha módosította a kártyát, [forduljon](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)az ügyfélszolgálathoz.

## <a name="cancel-exchange-and-refund-policies"></a>Megszakítási, Exchange-és visszatérítési szabályzatok

Az Azure-ban a következő szabályzatok állnak rendelkezésre: lemondás, cserék és visszatérítések.

**Lemondási szabályzatok**

- Ha megszakít egy foglalást, akkor a rendszer 12%-os korai lemondási díjat is igénybe vehet.
- A lemondáshoz kapott visszatérítés a fennmaradó, a 12%-os korai lemondási díj mínusz a megmaradt Pro-rated egyenleg. A megszakításhoz nyissa meg a Azure Portal foglalását, és válassza a **visszatérítés**lehetőséget.

**Exchange-házirendek**

- Több meglévő foglalást is visszaadhat, hogy az azonos típusú új foglalást vásárolja meg. Egy típusú foglalást nem lehet másikra cserélni. Például nem lehet visszaadni egy virtuális gép foglalását egy SQL-foglalás megvásárlásához.
- Csak a foglalási tulajdonosok dolgozhatnak fel Exchange-t. [Megtudhatja, hogyan adhat hozzá vagy módosíthat egy foglalást kezelő felhasználókat](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Az Exchange-t visszatérítésként és újravásárlásként dolgozza fel – a törléshez és az új vásárláshoz különböző tranzakciókat hoznak létre. A lefoglalt foglalás összegét a rendszer az Ön által forgalmazott foglalások után téríti vissza. Az új vásárlásért teljes díjat számítunk fel. Az előre megadott foglalási összeg a visszaadott foglalás napi kiértékelési értéke.
- Ha a foglalás megvásárlásához használt nagyvállalati szerződés lejárt, és új szerződésként megújult, megújíthatja a foglalások cseréjét vagy visszatérítését.
- A foglalási tulajdonságokat, például a méretet, a régiót, a mennyiséget és a kifejezéseket Exchange-re módosíthatja.
- Az új vásárlás végösszegének egyenlőnek vagy annál nagyobbnak kell lennie, mint a visszaadott mennyiség.
- Az Exchange részeként megvásárolt új foglalás új kifejezéssel kezdődik az Exchange-től kezdődően.
- A cserékhez nem jár büntetés vagy éves korlátozás.

**Visszatérítési szabályzatok**
- A teljes visszatérítés összege nem haladhatja meg a 12 hónapos időszak $50 000 USD-t.
- A visszatérítések kiszámítása a vételár vagy a foglalás aktuális díja alapján történik.
- Csak a foglalási tulajdonosok dolgozhatnak fel visszatérítést. [Megtudhatja, hogyan adhat hozzá vagy módosíthat egy foglalást kezelő felhasználókat](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A Microsoft fenntartja a jogot arra, hogy bármely megtérülés esetén 12%-os szankciót számoljon fel, bár a büntetés jelenleg nem számít fel díjat.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>A Premium Storage szolgáltatáshoz nem prémium szintű Exchange-tárterület

Egy olyan virtuális gép méretéhez megvásárolt foglalást cserélhet, amely nem támogatja a Premium Storage-t a megfelelő virtuálisgép-méretre. Például egy _F1_ egy _F1s_. Az Exchange létrehozásához lépjen a foglalás részleteibe, és kattintson az **Exchange**elemre. Az Exchange nem állítja vissza a fenntartott példány időtartamát, vagy új tranzakciót hoz létre.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezeléséről a [Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
    - [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
    - [Fenntartások kezelése az Azure-ban](billing-manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazási módjának megismerése](billing-understand-vm-reservation-charges.md)
    - [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
    - [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a partner Center Cloud Solution Provider (CSP) programban](/partner-center/azure-reservations)
