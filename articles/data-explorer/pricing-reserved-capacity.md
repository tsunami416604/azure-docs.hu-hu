---
title: Előre fizet az Azure Data Explorer jelölésért, hogy pénzt takarítson meg
description: Ismerje meg, hogyan vásárolhat az Azure Data Explorer számára fenntartott kapacitást az Azure Data Explorer költségeinek megtakarításához.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969266"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Az Azure Data Explorer csomagegységeinek előre fizetett szolgáltatása az Azure Data Explorer számára fenntartott kapacitással

Az Azure Data Explorer segítségével előre kifizetheti a felosztó-kiosztó díjakat. Az Azure Data Explorer számára fenntartott kapacitással egy vagy három évig kötelezettséget vállal az Azure Data Explorer használatára, hogy jelentős kedvezményt kapjon az Azure Data Explorer jelölési költségeiből. Az Azure Data Explorer fenntartott kapacitásának megvásárlásához csak meg kell adnia a kifejezést, akkor az azure data explorer összes központi telepítésére vonatkozik az összes régióban.

A foglalás megvásárlásával ön egy vagy három évig előre megfizeti a haszonkulcs költségeit. Amint megvásárol egy foglalást, az Azure Data Explorer a foglalási attribútumokkal megegyező díjszabást már nem számítjuk fel a díjkiosztó díjszabásszerint. A már futó vagy újonnan üzembe helyezett Azure Data Explorer-fürtök automatikusan megkapják az előnyt. Ez a foglalás nem terjed ki a fürtökhöz kapcsolódó számítási, hálózati vagy tárolási díjakra. Ezeknek az erőforrásoknak a fenntartott kapacitását külön kell megvásárolni. A foglalási időszak végén a számlázási juttatás lejár, és az Azure Data Explorer jelölési egységek számlázása a felosztó-kirovó áron. A foglalások nem újulnak meg automatikusan. A díjszabási információkért tekintse meg az [Azure Data Explorer díjszabási lapját.](https://azure.microsoft.com/pricing/details/data-explorer/)

Az Azure Data Explorer számára fenntartott kapacitást vásárolhat az [Azure Portalon.](https://portal.azure.com) Az Azure Data Explorer számára fenntartott kapacitás vásárlása:

* Legalább egy vállalati vagy használatra szóló fizetéses előfizetés tulajdonosának kell lennie.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
* A felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak azure Data Explorer fenntartott kapacitást.

Avállalati ügyfelek és az első osztályú fizetéssel kapcsolatos ügyfelek foglalási vásárlásaiért a következő témakörökben talál részleteket:
* [Az Azure-foglalás használatának ismertetése a vállalati regisztrációhoz](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Ismerje meg az Azure-foglaláshasználatát az használatalapú fizetéses előfizetéshez.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-markup-usage-before-purchase"></a>A megfelelő korrektúrahasználat meghatározása vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett Azure Data Explorer-fürtök által használt Azure Data Explorer-jelölőegységek teljes számán kell alapulnia. A jelölőegységek száma megegyezik az Azure Data Explorer motorfürtmagok száma éles környezetben (a fejlesztési és tesztelési termékváltozat nem tartalmazza). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Az Azure Data Explorer lefoglalt kapacitásának vásárlása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Összes szolgáltatás** > **foglalása** > **most**lehetőséget. Válassza a **Hozzáadás lehetőséget**
1. A **Terméktípus kiválasztása** ablaktáblában válassza az **Azure Data Explorer** lehetőséget az Azure Data Explorer-jelölésegységek új foglalásának megvásárlásához. 
1. Válassza a **Vásárlás** lehetőséget
1. Töltse ki a szükséges mezőket. 

    ![Beszerzési oldal](media/pricing-reserved-capacity/purchase-page.png)

1. Tekintse át az Azure Data Explorer-jelölés fenntartott kapacitásfoglalás ának költségét a **Költségek** szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza **a Foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancellations-and-exchanges"></a>Lemondások és cserék

Ha le kell mondania az Azure Data Explorer lefoglalt kapacitásfoglalását, előfordulhat, hogy 12%-os korai megszüntetési díjat kell fizetnie. A visszatérítések a vételár legalacsonyabb árán vagy a foglalás aktuális árán alapulnak. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. Lemondás igényléséhez nyissa meg a foglalást az Azure Portalon, és válassza **a Visszatérítés** lehetőséget egy támogatási kérelem létrehozásához.

Ha módosítania kell az Azure Data Explorer fenntartott kapacitásfoglalását egy másik kifejezésre, kicserélheti azt egy másik, azonos vagy nagyobb értékű foglalásra. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves időszak az új foglalás létrehozásától kezdődik. Cserékéréshez nyissa meg a foglalást az Azure Portalon, és válassza az **Exchange** lehetőséget támogatási kérelem létrehozásához.

A foglalások cseréjével és visszatérítésével kapcsolatos további információkért lásd: [Foglalások cseréje és visszatérítése.](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

## <a name="manage-your-reserved-capacity-reservation"></a>A lefoglalt kapacitásfoglalás kezelése

Az Azure Data Explorer markolati egységek foglalási kedvezmény automatikusan alkalmazza a jelölési egységek száma, amelyek megfelelnek az Azure Data Explorer fenntartott kapacitásfoglalási hatókör és attribútumok. 


> [!NOTE]
> * Frissítheti az Azure Data Explorer fenntartott kapacitásfoglalás hatókörét az [Azure Portalon](https://portal.azure.com), a PowerShellen, a CLI-n vagy az API-n keresztül.
> * Az Azure Data Explorer fenntartott kapacitásfoglalásának kezeléséről az [Azure Data Explorer fenntartott kapacitásának kezelése](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)című témakörben olvashat.

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mik azok az Azure-foglalások?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure-foglalások kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
