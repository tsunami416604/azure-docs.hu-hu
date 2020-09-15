---
title: Azure foglalási javaslatok
description: További tudnivalók az Azure foglalási javaslatokról.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 869009d7dd26685842da3c948fbdc058a38feb21
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398426"
---
# <a name="reservation-recommendations"></a>Foglalási javaslatok

Az Azure-beli fenntartott példányok (RI) vásárlására vonatkozó javaslatokat az Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations), az [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) és az Azure Portal foglalásvásárlási folyamata hozza létre.

A javaslatkalkulációs módszer magadásának lépései:

1. A javaslati motor az elmúlt 7, 30 és 60 napra vonatkozóan kiértékeli az erőforrások óránkénti használatát az adott hatókörben.
2. A használati adatok alapján a motor szimulálja a költségeket a foglalásokkal és azok nélkül is.
3. A költségek szimulálása különböző mennyiségek esetében történik, és a maximális megtakarítást eredményező mennyiség a javasolt mennyiség.
4. Ha az erőforrásai rendszeresen le vannak állítva, a szimuláció nem fog megtakarítási lehetőséget találni, és nem ad vásárlási javaslatot.
5. A javaslatszámítások figyelembe veszik az igény szerinti felhasználás díjaira vonatkozó esetleges különleges kedvezményeket.

## <a name="recommendations-in-the-azure-portal"></a>Javaslatok az Azure Portalon

Foglalásvásárlási javaslatok a vásárlási folyamat során is megjelennek az Azure Portalon. A javaslatok az **Ajánlott mennyiség** alatt jelennek meg. Az Azure által javasolt mennyiség megvásárlása teszi lehetővé a legnagyobb megtakarítást. Bár tetszőleges mennyiséget vásárolhat, ha eltérő mennyiséget vásárol, a megtakarítás nem lesz optimális.

Lássunk néhány példát, miért.

Az alábbi szemléltető képen a kiválasztott javaslathoz az Azure 6 darab vásárlását javasolja.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="A foglalásvásárlási javaslatot bemutató példa" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

A javaslattal kapcsolatos további információk a **Részletek megjelenítése** hivatkozás kiválasztásakor lesznek láthatók. Az alábbi képen a javaslat részletei láthatók. A javasolt mennyiség kiszámítása a lehető legnagyobb használathoz történik a korábbi használat alapján. Egyenetlen használat esetén előfordulhat, hogy az Önnek szóló javaslat nem 100%-os kihasználtságot céloz meg. A példában megfigyelheti, hogy a kihasználtság az idő során ingadozott. Látható a foglalás költsége, a lehetséges megtakarítási lehetőségek és a százalékos kihasználtság.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="A foglalásvásárlási javaslat részleteit bemutató példa" :::

Ha a javaslaton kívüli értékre növeli vagy csökkenti a foglalás mennyiségét, a diagram és a becsült értékek megváltoznak. A foglalások mennyiség növelésével csökken a megtakarítás, mivel csökken a foglalás kihasználtsága. Más szóval a nem teljes mértékben kihasznált foglalásokért fizet.

Ha csökkenti a foglalások mennyiségét, a megtakarítás szintén csökkenni fog. Bár nagyobb a kihasználtság, valószínűleg lesznek olyan időszakok, amikor a foglalások nem fedezik teljesen a használatot. A foglalási mennyiségen túli használatot drágább használat alapján fizetett erőforrások fogják fedezni. A következő kép szemlélteti a lényeget. A foglalási mennyiséget manuálisan 4-re csökkentettük. A foglalás kihasználtsága növekedett, de a teljes megtakarítás csökkent, mivel használatalapú költségek jelentek meg.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="A módosított foglalásvásárlási javaslat részleteit bemutató példa" :::

A foglalásokon való megtakarítás maximalizálása érdekében a javaslathoz a lehető legközelebb álló foglalási mennyiséget érdemes választani.

## <a name="recommendations-in-azure-advisor"></a>Javaslatok az Azure Advisorban

Az Azure Advisorben is elérhetők foglalásvásárlási javaslatok. Tartsa szem előtt az alábbi szempontokat:

- Az Advisorban csak egyelőfizetéses hatókörre érvényes javaslatok érhetők el. Ha meg szeretné tekinteni a teljes számlázási hatókörre (számlázási fiók vagy számlázási profil) vonatkozó javaslatokat, lépjen az Azure Portal > Foglalások > Hozzáadás területre, és válassza ki azt a típust, amelyre vonatkozóan meg szeretné tekinteni a javaslatokat.
- Az Advisorban elérhető javaslatok az előző 30 napi használatot veszik figyelembe.
- A javasolt mennyiségek és megtakarítások, ahol lehetséges, 3 éves foglalási időszakra vonatkoznak. Ha a szolgáltatáshoz nem vásárolható 3 éves foglalás, akkor a javaslat az 1 éves foglalási áron alapul.
- A javaslatszámítások figyelembe veszik az igény szerinti felhasználás díjaira vonatkozó esetleges különleges kedvezményeket.
- Ha megosztott hatókörrel rendelkező foglalást vásárol, az Advisor foglalásvásárlási javaslatai legfeljebb 5 napig láthatók.

## <a name="other-expected-api-behavior"></a>Egyéb várt API-viselkedés

- Amikor hétnapos visszatekintési időszakot alkalmaz, előfordulhat, hogy nem kap javaslatokat, ha a virtuális gépek egy napnál tovább vannak leállítva.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg [az Azure-foglalási kedvezmény virtuális gépekre történő alkalmazásával](../manage/understand-vm-reservation-charges.md).
