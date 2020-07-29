---
title: forgalomáttekintő az Azure-ban Traffic Manager
description: Ebből a bevezetőből megtudhatja, hogyan működik a Traffic Manager forgalom nézete.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76938360"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager forgalomáttekintő

A Traffic Manager DNS-szintű útválasztást biztosít a végfelhasználók számára, hogy a profil létrehozásakor megadott útválasztási módszer alapján a felhasználók egészséges végpontokra legyenek irányítva. A forgalomáttekintő Traffic Managert biztosít a felhasználói bázisok (DNS-feloldó részletességi szint) és a forgalmi minta megtekintésével. A forgalomáttekintő engedélyezésekor a rendszer feldolgozza ezeket az információkat, hogy a gyakorlatban hasznosítható elemzéseket biztosítson. 

A forgalomáttekintő használatával a következőket teheti:
- Ismerje meg, hogy a felhasználói bázisok hol találhatók (legfeljebb egy helyi DNS-feloldó szintű részletességgel).
- megtekintheti az ezekből a régiókból származó, az Azure Traffic Manager által kezelt DNS-lekérdezésekben megfigyelt forgalom mennyiségét.
- betekintést nyerhet a felhasználók által tapasztalt jellemző késésre.
- részletesen tekintse át az egyes felhasználói bázisok konkrét forgalmi mintáit azon Azure-régiókba, ahol végpontokkal rendelkezik. 

A forgalomáttekintő segítségével például megtudhatja, hogy mely régiókban van nagy forgalmú forgalom, de nagyobb késések esetén. Ezt az információt használhatja arra, hogy megtervezze a lábnyom terjeszkedését az új Azure-régiókba, hogy ezek a felhasználók alacsonyabb késési élményt tudjanak használni.

## <a name="how-traffic-view-works"></a>A forgalomáttekintő működése

Forgalomáttekintő úgy működik, hogy Traffic Manager megtekinti az elmúlt hét napban fogadott bejövő lekérdezéseket egy olyan profillal szemben, amelyen engedélyezve van ez a funkció. A bejövő lekérdezések adatainál forgalomáttekintő kibontja a DNS-feloldó forrás IP-címét, amelyet a felhasználók helyének ábrázolására használ a rendszer. Ezeket a rendszer egy DNS-feloldó szintű részletességgel csoportosítja a felhasználói alaprégiók létrehozásához a Traffic Manager által fenntartott IP-címek földrajzi információi alapján. Traffic Manager ezután megtekinti azokat az Azure-régiókat, amelyekhez a lekérdezés át lett irányítva, és létrehoz egy adatforgalmi térképet az adott régiókban lévő felhasználók számára.  
A következő lépésben a Traffic Manager összekapcsolja a felhasználói alaprégiót az Azure region leképezésével a hálózati intelligencia késési tábláival, amelyeket a különböző végfelhasználói hálózatok számára tart fenn, hogy megértse az adott régiókban lévő felhasználók által az Azure-régiókhoz való csatlakozás során tapasztalt átlagos késést. Ezeket a számításokat ezután a rendszer a helyi DNS-feloldó IP-szinten kombinálja, mielőtt azok bemutatják Önt. Az információkat többféleképpen is felhasználhatja.

A forgalmi nézet adatfrissítésének gyakorisága több belső szolgáltatási változótól függ. Az adatgyűjtést azonban általában 24 óránként egyszer kell frissíteni.

>[!NOTE]
>Az forgalomáttekintőban leírt késés a végfelhasználó és az Azure-régiók között jellemző késés, amelyhez csatlakoztak, és nem a DNS keresési késése. Forgalomáttekintő a helyi DNS-feloldó és az Azure-régió közötti késés legjobb becslését biztosítja, ha nincs elegendő adat, a visszaadott késés pedig NULL lesz. 

## <a name="visual-overview"></a>Vizuális áttekintés

Ha a Traffic Manager oldal **forgalomáttekintő** szakaszára navigál, egy földrajzi Térkép jelenik meg, amely forgalomáttekintői bepillantást tartalmaz. A Térkép információt nyújt a Traffic Manager profiljához tartozó felhasználói bázisról és végpontokról.

![Traffic Manager forgalomáttekintő földrajzi nézet][1]

### <a name="user-base-information"></a>Felhasználói alapinformációk

Azok a helyi DNS-feloldók, amelyekhez a helyadatok elérhetők, megjelennek a térképen. A DNS-feloldó színe azt jelzi, hogy az adott DNS-feloldót használó végfelhasználók milyen átlagos késéssel rendelkeznek a Traffic Manager lekérdezésekhez.

Ha a térképen a DNS-feloldási hely fölé viszi a mutatót, az a következőket jeleníti meg:
- a DNS-feloldó IP-címe
- a Traffic Manager által látott DNS-lekérdezési forgalom mennyisége
- azok a végpontok, amelyeken a DNS-feloldó felé irányuló forgalmat irányították, a végpont és a DNS-feloldó közötti vonalként 
- az adott hely és a végpont közötti átlagos késés, amely a csatlakozáshoz használt vonal színét jelöli.

### <a name="endpoint-information"></a>Végpont adatai

Az Azure-régiók, amelyekben a végpontok találhatók, kék pontként jelennek meg a térképen. Ha a végpont kívül van, és nem rendelkezik hozzárendelt Azure-régióval, a Térkép tetején látható. Kattintson bármelyik végpontra a különböző helyszínek (a használt DNS-feloldó alapján) megjelenítéséhez, ahol a forgalom a végpontra irányult. A kapcsolatok a végpont és a DNS-feloldó helye közötti vonalként jelennek meg, és az adott párt közötti reprezentatív késésnek megfelelően színezettek. Emellett megtekintheti a végpont nevét, az Azure-régiót, amelyben az fut, valamint a Traffic Manager-profil által a kérelemre irányuló kérelmek teljes mennyiségét.


## <a name="tabular-listing-and-raw-data-download"></a>Táblázatos Listázás és nyers adatok letöltése

Az forgalomáttekintő adatokat táblázatos formátumban tekintheti meg Azure Portalban. Minden olyan DNS-feloldó IP-/végponti pár esetében szerepel egy bejegyzés, amely megjeleníti a DNS-feloldó IP-címét, az Azure-régió nevét és földrajzi helyét (ha elérhető), a DNS-feloldóhoz társított kérelmek mennyiségét az adott végponthoz, valamint az adott DNS-t használó végfelhasználók (ahol elérhető) jellemző késését. A forgalomáttekintő adatait CSV-fájlként is letöltheti, amely a választott elemzési munkafolyamatok részeként is használható.

## <a name="billing"></a>Számlázás

Forgalomáttekintő használatakor a számlázás a bemutatott elemzések létrehozásához használt adatpontok száma alapján történik. Jelenleg az egyetlen adatpont-típust használja a Traffic Manager profiljához kapott lekérdezések. A díjszabással kapcsolatos további információkért látogasson el a [Traffic Manager díjszabási oldalára](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Gyakori kérdések

* [Mit tesz forgalomáttekintő?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Milyen előnyökkel jár a forgalomáttekintő használata?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Miben különbözik forgalomáttekintő az Azure monitoron keresztül elérhető Traffic Manager metrikákkal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A forgalomáttekintő használja az EDNS-ügyfél alhálózati adatait?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Hány napi adatmennyiséget forgalomáttekintő használni?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Hogyan kezeli a forgalomáttekintő a külső végpontokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Engedélyeznie kell a forgalomáttekintő az előfizetésem minden profiljához?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hogyan kapcsolható ki a forgalomáttekintő?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Hogyan működik forgalomáttekintő számlázás?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>További lépések

- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png