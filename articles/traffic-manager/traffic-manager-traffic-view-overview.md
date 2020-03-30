---
title: Forgalomnézet az Azure Traffic Managerben
description: Ebben a bevezetőben megtudhatja, hogyan működik a Traffic manager Traffic view.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938360"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager forgalom nézet

A Traffic Manager dns-szint-útválasztást biztosít, így a végfelhasználók a profil létrehozásakor megadott útválasztási módszer alapján kifogástalan végpontokba lesznek irányítva. A Traffic View lehetővé teszi a Traffic Manager számára a felhasználói bázisok (DNS-feloldó részletességi szintjén) és a forgalmi minta megtekintését. Ha engedélyezi a Forgalomnézetet, a folyamat ezt az információt feldolgozza, hogy használható elemzéseket nyújthasson. 

A Traffic View használatával a következőket teheti:
- a felhasználói bázisok tartózkodási helye (a helyi DNS-feloldó szintű részletességig).
- megtekintheti az ezekből a régiókból származó forgalmat (az Azure Traffic Manager által kezelt DNS-lekérdezésekként megfigyelt).
- betekintést nyerhet abba, hogy mi a felhasználók által tapasztalt reprezentatív késés.
- részletesen belekell vetnie magát az egyes felhasználói bázisokról az Azure-régiókba, ahol végpontokkal rendelkezik. 

A Traffic View használatával például megismerheti, hogy mely régiók rendelkeznek nagy számú forgalommal, de nagyobb késések kelnek el. Ezután használhatja ezt az információt az új Azure-régiókba való bővítés megtervezéséhez, hogy ezek a felhasználók alacsonyabb késési élményben legyenek.

## <a name="how-traffic-view-works"></a>A Traffic View működése

A Traffic View úgy működik, hogy a Traffic Manager megvizsgálja az elmúlt hét napban fogadott bejövő lekérdezéseket egy olyan profillal szemben, amelyen engedélyezve van ez a funkció. A bejövő lekérdezések adataiból a Traffic View kinyeri a DNS-feloldó forrásIP-címét, amely a felhasználók helyének ábrázolásaként használatos. Ezek ezután csoportosítva egy DNS-feloldó szintű részletességgel hozzanak létre felhasználói bázisrégiókat a Traffic Manager által kezelt IP-címek földrajzi adatainak használatával. A Traffic Manager ezután megvizsgálja azOkat az Azure-régiókat, amelyekre a lekérdezést irányították, és forgalmi folyamattérképet hoz létre az adott régiókból származó felhasználók számára.  
A következő lépésben a Traffic Manager korrelálja a felhasználói alaprégiót az Azure-régió leképezésével a hálózati intelligencia késési tábláival, amelyeket a különböző végfelhasználói hálózatok számára tart fenn az adott régiók felhasználói által tapasztalt átlagos késés megértéséhez, amikor az Azure-régiókhoz való csatlakozás. Ezeket a számításokat ezután a helyi DNS-feloldó IP-szintjén kombinálja, mielőtt megjelenik Önnek. Az információkat különböző módokon használhatja fel.

A Traffic view adatfrissítésének gyakorisága több belső szolgáltatásváltozótól függ. Az adatok at általában 24 óránként frissítik.

>[!NOTE]
>A Traffic View-ban leírt késés egy reprezentatív késés a végfelhasználó és az Azure-régiók között, amelyekhez kapcsolódtak, és nem a DNS-keresési késés. A Traffic View a helyi DNS-feloldó és a lekérdezés által irányított Azure-régió közötti késés becslését teszi lehetővé, ha nincs elegendő adat, akkor a visszaadott késés null értékű lesz. 

## <a name="visual-overview"></a>Vizuális áttekintés

Amikor a Traffic Manager-lap **Forgalomnézet** szakaszára lép, megjelenik egy földrajzi térkép, amely a Forgalomnézet elemzési adatait fedelével látja el. A térkép a Traffic Manager-profil felhasználói bázisával és végpontjaival kapcsolatos információkat tartalmaz.

![Traffic Manager Traffic View földrajzi nézet][1]

### <a name="user-base-information"></a>Felhasználói alapinformáció

Azoknak a helyi DNS-feloldóknak, amelyekhely-adatai rendelkezésre állnak, azok megjelennek a térképen. A DNS-feloldó színe azt az átlagos késést jelöli, amelyet azok a végfelhasználók tapasztaltak, akik a DNS-feloldót használták a Traffic Manager-lekérdezéseikhez.

Ha az egérmutatót egy DNS-feloldó hely fölé viszi a térképen, az a következőket jeleníti meg:
- a DNS-feloldó IP-címe
- a Traffic Manager által belőle látott DNS-lekérdezési forgalom mennyisége
- a végpont és a DNS-feloldó közötti vonalként a DNS-feloldóból érkező forgalom továbbítására irányuló végpontok 
- az adott helytől a végpontig történő átlagos késés, amely az őket összekötő vonal színeként jelenik meg

### <a name="endpoint-information"></a>Végpontadatai

Az Azure-régiók, ahol a végpontok találhatók, kék pontokként jelennek meg a térképen. Ha a végpont külső, és nem rendelkezik egy Azure-régió leképezve, akkor a térkép tetején jelenik meg. Kattintson bármelyik végpontra a különböző helyek megtekintéséhez (a használt DNS-feloldó alapján), ahonnan a forgalmat az adott végpontra irányították. A kapcsolatok a végpont és a DNS-feloldó helye közötti vonalként jelennek meg, és a pár közötti reprezentatív késés nek megfelelően vannak színezve. Emellett láthatja a végpont nevét, az Azure-régiót, amelyben fut, és a Traffic Manager-profil által hozzá irányított kérelmek teljes mennyiségét.


## <a name="tabular-listing-and-raw-data-download"></a>Táblázatos lista és nyers adatok letöltése

A Traffic View-adatokat táblázatos formátumban tekintheti meg az Azure Portalon. Minden DNS-feloldó IP/ végpontpárhoz tartozik egy bejegyzés, amely a DNS-feloldó IP-címét, annak az Azure-régiónak a nevét és földrajzi helyét mutatja, amelyben a végpont található (ha rendelkezésre áll), az adott DNS-feloldóhoz társított kérelmek mennyiségét az adott végponthoz, valamint az adott DNS-t használó végfelhasználókhoz társított reprezentatív késést (ahol elérhető). A Traffic View adatokat CSV-fájlként is letöltheti, amely az Ön által választott elemzési munkafolyamat részeként használható.

## <a name="billing"></a>Számlázás

A Forgalomnézet használatakor a rendszer a bemutatott elemzési adatok létrehozásához használt adatpontok száma alapján számláz. Jelenleg az egyetlen használt adatpont-típus a Traffic Manager-profilhoz kapott lekérdezések. Az árképzésről további információt a [Traffic Manager díjszabási oldalán talál.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>Gyakori kérdések

* [Mire történik a Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Hogyan profitálhatok a Traffic View használatából?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Miben különbözik a Traffic View a Traffic Manager metrikáitól az Azure-figyelőn keresztül?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A Traffic View használja az EDNS-ügyfél alhálózati adatait?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Hány nap adatot használ a Traffic View?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Hogyan kezeli a Traffic View a külső végpontokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Engedélyeznem kell a Traffic View nézetet az előfizetésem minden egyes profiljához?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hogyan tudom kikapcsolni a Forgalomnézetet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Hogyan működik a Traffic View számlázása?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>További lépések

- A [Traffic Manager működésének megismerése](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png