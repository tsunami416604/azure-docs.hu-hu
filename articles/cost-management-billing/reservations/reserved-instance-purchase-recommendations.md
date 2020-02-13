---
title: Az Azure-beli foglalási javaslatok létrehozása
description: Megtudhatja, hogyan jönnek létre az Azure-beli foglalási javaslatok a virtuális gépekhez.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851351"
---
# <a name="how-reservation-recommendations-are-created"></a>Foglalási javaslatok létrehozása

Az Azure-beli fenntartott példányok (RI) vásárlására vonatkozó javaslatokat az Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations) hozza létre. Az API javaslatait az [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) is alkalmazza. Az Advisor javaslatai az Azure Portalon jelennek meg.

Ha rendelkezik Azure-ban futó virtuális gépekkel, kihasználhatja a fenntartott példányok kedvezményes díjszabását, és előre fizethet a virtuális gépekért. A Microsoft Consumption recommendation API kiértékeli 7, 30 és 60 nap használatát, és optimális konfigurációkat javasolt a fenntartott példányokhoz. A megtakarítások optimalizálása érdekében összehasonlítja a fenntartott példányok nélkül és a fenntartott példányok mellett fizetendő költséget.

Az Azure Advisor egy 30 napos időszak alapján jeleníti meg a javaslatokat.

Az egyszerűség kedvéért az alábbi példa egy hétnapos javaslathoz alkalmazott számításokat mutatja be. A 30 és 60 napos javaslatok kiszámítása ugyanezzel a módszerrel történik.

## <a name="calculation-method-example"></a>Számítási módszer – példa

Tegyük fel, hogy egy adott termékváltozat és régió esetén a Windows rendszerű virtuális gép óránkénti használata egy hét napos (168 órán) időszakon belül változó. A hét nap alatt a minimális használat 65 egység, a maximális használat pedig 127 egység. Ebben a példában a 79. órában a rendszer 80 virtuális gépet használt, Ön pedig 75 fenntartott példányt vásárolt.

Ha vásárolt 75 fenntartott példányt, a 79. óráért az alábbi költségeket fizeti:

- 75 fenntartott példány. A költség kifizetése előre megtörténik, amikor fenntartott példányokat vásárol.
- A fenntartott példányok lefedik a virtuális gépek futtatásának hardveres költségét, ezért a 75 óráért csak szoftveres áron kell fizetnie.
- A 79. órára vonatkozó használat 80, így az 5 óra Windows-használatot és a hardverkombináció mérőeszközének árát fizeti ki. A kombinált ár azon alapul, hogy Nagyvállalati Szerződéses (EA-) vagy használatalapú fizetéses díjat fizet.

Ha 75 fenntartott példányt vásárol, a teljes költséget a megelőző óraalapú költségek hozzáadásával számíthatja ki. Az aktuális költséget a díjszabása segítségével is kiszámíthatja. Ebben a példában a két összeg közötti különbség a hét napra vonatkozó megtakarítás.

Az API minden egyes használati ponthoz végez számításokat. Ezután visszaadja azt a javasolt mennyiséget, amelynél maximális a megtakarítás. Az alábbi példában az ábra azt mutatja, hogy a megtakarítások 68-nál érik el a csúcsot. A megtakarítás mértéke utána már csökken, ezért az API a 68-at javasolja.

![Megtakarítási csúcsértéket mutató ábra](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Egyéb várt API-viselkedés

- Az API az [Azure Hybrid Benefittel](https://azure.microsoft.com/pricing/hybrid-benefit/) Windows rendszeren megtakarítható összegeket mutatja meg a kedvezmény alkalmazása esetén. Ha nem használja ki a kedvezményt, akkor az API-javaslatok az alapvető Windows-költségeken alapulnak. Ha elérhető az Ön számára, fontolja meg az Azure Hybrid Benefit használatát a megtakarítások növeléséhez.
- Amikor hétnapos visszatekintési időszakot alkalmaz, előfordulhat, hogy nem kap javaslatokat, ha a virtuális gépek egy napnál tovább vannak leállítva.

## <a name="next-steps"></a>További lépések
- Ismerkedjen meg [az Azure-foglalási kedvezmény virtuális gépekre történő alkalmazásával](../manage/understand-vm-reservation-charges.md).
