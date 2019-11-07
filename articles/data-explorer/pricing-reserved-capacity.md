---
title: Előre fizetés az Azure Adatkezelő Markup szolgáltatásban pénz megtakarítása érdekében
description: Megtudhatja, hogyan vásárolhat Azure Adatkezelő fenntartott kapacitást az Azure-Adatkezelő költségeinek mentéséhez.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681626"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Előre fizetés az Azure Adatkezelő Markup egységek számára az Azure Adatkezelő fenntartott kapacitással

Az utólagos elszámolású díjszabáshoz képest előre fizetve megtakaríthatja az Azure Adatkezelőét. Az Azure Adatkezelő fenntartott kapacitással az Azure-Adatkezelő egy vagy három éves időszakra vonatkozó kötelezettségvállalást tesz, hogy jelentős kedvezményt kapjon az Azure Adatkezelő Markup költségeire. Az Azure Adatkezelő fenntartott kapacitás megvásárlásához csak a kifejezést kell megadnia, az összes régióban az összes Azure Adatkezelő üzemelő példányra érvényes lesz.

A foglalás megvásárlásával egy-egy vagy három éves időszakra előre fizet a haszonkulcs költségeiért. A foglalás megvásárlása után az Azure Adatkezelő a foglalási attribútumoknak megfelelő árrések díját az utólagos elszámolású díjszabásban már nem számoljuk el. A már futó vagy újonnan telepített Azure Adatkezelő-fürtök automatikusan megkapják a kedvezményt. Ez a foglalás nem fedi le a fürtökhöz társított számítási, hálózatkezelési és tárolási díjakat. Az ilyen erőforrások számára fenntartott kapacitást külön kell megvásárolni. A foglalási időszak végén a számlázási juttatás lejár, és az Azure Adatkezelő jelölési egységeit az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem állnak automatikusan megújítva. A díjszabással kapcsolatos információkért tekintse meg az [Azure adatkezelő díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/data-explorer/).

Az Azure Adatkezelő fenntartott kapacitást a [Azure Portal](https://portal.azure.com)is megvásárolhatja. Az Azure Adatkezelő fenntartott kapacitásának megvásárlása:

* Legalább egy vállalati vagy utólagos elszámolású előfizetés tulajdonosának kell lennie.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg az Azure Adatkezelő fenntartott kapacitását.

A foglalások vásárlásával kapcsolatos további részletekért tekintse meg a következő témakört:
* [Az Azure foglalás használatának ismertetése a vállalati beléptetéshez](../billing/billing-understand-reserved-instance-usage-ea.md) 
* Az utólagos elszámolású [előfizetések esetében az Azure foglalás használatának ismertetése](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>A megfelelő haszonkulcs-használat meghatározása a vásárlás előtt

A foglalás méretét a meglévő vagy hamarosan üzembe helyezett Azure Adatkezelő-fürtök által használt Azure Adatkezelő jelölő egységek teljes száma alapján kell megadni. A jelölő egységek száma megegyezik az Azure Adatkezelő Engine-fürtök éles üzemben való számával (a fejlesztési/tesztelési SKU-val nem együtt). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Az Azure Adatkezelő fenntartott kapacitásának megvásárlása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza **a minden szolgáltatás** > **foglalás** > **megvásárlás**lehetőséget. Válassza a **Hozzáadás** lehetőséget
1. A **terméktípus kiválasztása** ablaktáblán válassza az **Azure adatkezelő** lehetőséget, ha új foglalást szeretne vásárolni az Azure adatkezelő Markup units szolgáltatásban. 
1. **Vásárlás** kiválasztása
1. Töltse ki a kötelező mezőket. 

    ![Vásárlási oldal](media/pricing-reserved-capacity/purchase-page.png)

1. Tekintse át az Azure Adatkezelő Markup fenntartott kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancellations-and-exchanges"></a>Lemondások és cserék

Ha meg kell szakítania az Azure Adatkezelő fenntartott kapacitás foglalását, akkor 12%-os korai lemondási díjat kell fizetnie. A visszatérítések a vételár legalacsonyabb árával vagy a foglalás aktuális árával függenek. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. A lemondás lekéréséhez lépjen a Azure Portal foglalására, és válassza a **visszatérítés** lehetőséget a támogatási kérelem létrehozásához.

Ha módosítania kell az Azure Adatkezelő fenntartott kapacitás foglalását egy másik kifejezésre, akkor azt egy másik, egyenlő vagy nagyobb értékű foglalásra cserélheti. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves időszak az új foglalás létrehozásakor kezdődik. Az Exchange igényléséhez lépjen a foglalás a Azure Portal, majd válassza az **Exchange** elemet a támogatási kérelem létrehozásához.

A foglalások cseréjével és visszatérítésével kapcsolatos további információkért lásd: a [foglalási cserék és a visszafizetések](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Fenntartott kapacitás foglalásának kezelése

Az Azure Adatkezelő Markup Unit foglalási kedvezmény automatikusan érvényes az Azure Adatkezelő fenntartott kapacitás foglalási hatókörét és attribútumait megegyező haszonkulcs-egységek számára. 


> [!NOTE]
> * Az Azure Adatkezelő fenntartott kapacitás foglalásának hatókörét a [Azure Portal](https://portal.azure.com), a PowerShell, a CLI vagy az API segítségével frissítheti.
> * Az Azure Adatkezelő fenntartott kapacitás foglalásának kezelésével kapcsolatos információkért lásd: az [azure adatkezelő fenntartott kapacitás kezelése](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mi az az Azure Reservations?](../billing/billing-save-compute-costs-reservations.md)
* [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)
* [Az Azure Reservations-kedvezmény ismertetése](../billing/billing-understand-reservation-charges.md)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../billing/billing-understand-reserved-instance-usage.md)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
