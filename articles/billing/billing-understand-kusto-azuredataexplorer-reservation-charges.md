---
title: A foglalási kedvezmény alkalmazása az Azure Data Explorerre
description: Megtudhatja, hogyan alkalmazható a foglalási kedvezmény az Azure Data Explorer árrésmérőjére.
services: data-explorer
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: eba8a9b2a2774ba721a8442ca03035bd3801dc53
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585515"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>A foglalási kedvezmény alkalmazása az Azure Data Explorerre

A fenntartott Azure Data Explorer-kapacitás megvásárlása után a foglalási kedvezmény automatikusan alkalmazva lesz a foglalás attribútumaival és mennyiségével egyező Azure Data Explorer-erőforrásokra. A foglalás magában foglalja az Azure Data Explorer-árrés díját. Nem tartalmazza az Azure Data Explorer-fürt üzemeltetéséhez szükséges számítási, hálózatkezelési, tárolási és egyéb Azure-erőforrások díját. Az ilyen erőforrások foglalásait külön kell megvásárolni.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Az Azure Data Explorer-fürtökre alkalmazott foglalási kedvezmény

A foglalási kedvezmény az Azure Data Explorer árréshasználatára való alkalmazása óránkénti alapon történik. Azon Azure Data Explorer-erőforrások esetében, amelyek nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező egyéb Data Explorer-erőforrásokra alkalmazza a foglalási kedvezményt. A kedvezmény párhuzamosan futó Azure Data Explorer-erőforrásokra is alkalmazható. Ha nem rendelkezik olyan, a foglalási attribútumokkal egyező Azure Data Explorer-erőforrásokkal, amelyek egy teljes órán át futnak, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

> [!NOTE]
> * A fenntartott kapacitással elérhető megtakarítás maximalizálása érdekében **határozottan ajánlott** [foglalási kapacitást](../virtual-machines/windows/prepay-reserved-vm-instances.md) vásárolni az Azure Data Explorer-fürthöz használt virtuális géphez.
> * A foglalás megvásárlásakor a rendszer alkalmazza a kedvezményeket az összes régióra.

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott Azure Data Explorer-kapacitásra érvényes kedvezmény a megvásárolt árrésegységek száma alapján, és az alapján, mikor futnak.
Például a **2 D11_v2 virtuális gép esetén** motorfürtméret esetében a teljes igény szerinti díj óránként négy Azure Data Explorer-árrésmérő egységre vonatkozik. 

**1. forgatókönyv** 

8 Azure Data Explorer-árrésegységnek megfelelő Azure Data Explorer fenntartott kapacitást vásárol. Két D13_v2 virtuális gépből álló motorfürtöt futtat összesen 16 maggal, amely után 16 egységnyi Azure Data Explorer-árrésegységet kell fizetnie óránként, és amely megegyezik a foglalás többi attribútumával. A nyolcmagos Azure Data Explorer számításierőforrás-használata után használatalapú díjat kell fizetnie, a foglalási kedvezményt pedig a nyolcmagos Azure Data Explorer-árrésegység egy órányi használata után kapja meg.

A többi példa esetében azt feltételezzük, hogy a fenntartott Azure Data Explorer-kapacitást egy 16 magos Azure Data Explorer-fürthöz vásárolta, és a többi foglalási attribútum megegyezik a futó Azure Data Explorer-fürttel.

**2. forgatókönyv** 

Két Azure Data Explorer-motorfürtöt futtat nyolc-nyolc maggal egy órán keresztül két különböző régióban. A 16 magos foglalási kedvezményt a rendszer a fürtökre és a felhasznált 16 egységnyi Azure Data Explorer-árrésegységre is alkalmazza.

**3. forgatókönyv** 

Egy 16 magos Azure Data Explorer-motorfürtöt futtat 13:00 és 13:30 között. Egy másik 16 magos Azure Data Explorer-motorfürtöt pedig 13:30 és 14:00 között futtat. A foglalási kedvezmény mindkettőt fedezi.

**4. forgatókönyv** 

Egy 16 magos Azure Data Explorer-motorfürtöt futtat 13:00 és 13:45 között. Egy másik 16 magos Azure Data Explorer-motorfürtöt pedig 13:30 és 14:00 között futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő Azure Data Explorer-árréshasználatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](billing-understand-reserved-instance-usage-ea.md) cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

* [Előre fizetés fenntartott Azure Data Explorer-kapacitással rendelkező Azure Data Explorer számítási erőforrásokért](../data-explorer/pricing-reserved-capacity.md)  
* [Mik azok az Azure-beli foglalások?](billing-save-compute-costs-reservations.md)  
* [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)  
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](billing-understand-reserved-instance-usage.md)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)
* [A foglalási kihasználtság ismertetése CSP-előfizetésnél](https://docs.microsoft.com/partner-center/azure-reservations)
