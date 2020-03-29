---
title: Hibaelhárítási útmutató – Azure DNS
description: Ebben a tanulási útvonalon kezdje el az Azure DNS-sel kapcsolatos gyakori problémák elhárítását
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: b5e1624bf852256f6e8fb0b616258f932c5a8998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939034"
---
# <a name="azure-dns-troubleshooting-guide"></a>Az Azure DNS hibaelhárítási útmutatója

Ez a cikk hibaelhárítási információkat tartalmaz az Azure DNS-sel kapcsolatos gyakori kérdésekhez.

Ha ezek a lépések nem oldják meg a problémát, a problémát az [MSDN közösségi támogatási fórumán](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)is megkeresheti vagy közzéteheti. Vagy megnyithat egy Azure-támogatási kérelmet.


## <a name="i-cant-create-a-dns-zone"></a>Nem tudok DNS-zónát létrehozni

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Tekintse át az Azure DNS-naplózási naplók a hiba okának meghatározásához.
2.  Minden DNS-zónanévnek egyedinek kell lennie a saját erőforráscsoportján belül. Ez azt, hogy két azonos nevű DNS-zóna nem tud megosztani egy erőforráscsoportot. Próbáljon meg eltérő zónanevet vagy erőforráscsoportot használni.
3.  Előfordulhat, hogy megjelenik az „Elérte vagy túllépte a zónák maximális számát az előfizetésben ({előfizetés azonosítója})” hibaüzenet. Használjon másik Azure-előfizetést, töröljön néhány zónát, vagy vegye fel a kapcsolatot az Azure ügyfélszolgálatával az előfizetésre vonatkozó korlát megemeléséhez.
4.  „A(z) {zónanév} zóna nem érhető el” hibaüzenet is megjelenhet. Ez a hiba azt jelzi, hogy az Azure DNS nem tudott névkiszolgálót lefoglalni ehhez a DNS-zónához. Próbáljon meg egy másik zónanevet használni. Vagy ha Ön a tartománynév tulajdonosa, forduljon az Azure támogatási szolgálatához, hogy névkiszolgálókat foglaljon le Önnek.


### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nem tudok létrehozni egy DNS-rekordot

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Tekintse át az Azure DNS-naplózási naplók a hiba okának meghatározásához.
2.  A rekordhalmaz már létezik?  Az Azure DNS a rekordokat *rekordhalmazok* használatával kezeli, amelyek ugyanazzal a névvel és típussal rendelkező rekordok gyűjteményei. Ha egy ugyanolyan nevű és típusú rekord már létezik, akkor egy másik ugyanilyen rekord hozzáadásához szerkesztenie kell a meglévő rekordhalmazt.
3.  A DNS-zóna legfelső pontján (a zóna „gyökerén”) próbál rekordot létrehozni? Ha igen, az általános egyezmény a DNS rendszerben a „@” karakter használata a rekord neveként. Azt is vegye figyelembe, hogy a DNS-szabványok nem engedélyezik a CNAME rekordokat a zóna csúcsán.
4.  CNAME-ütközést tapasztal?  A DNS-szabványok nem engedélyezik a CNAME rekordot, amelynek neve megegyezik bármely más típusú rekord nevével. Ha rendelkezik egy meglévő CNAME-mel, nem tud egy másik típusú, de ugyanolyan nevű rekordot létrehozni.  A CNAME létrehozása szintén sikertelen lesz, ha a neve megegyezik egy más típusú, már létező rekord nevével. Szüntesse meg az ütközést a másik rekord eltávolításával vagy egy eltérő rekordnév beállításával.
5.  Elérte a DNS-zónában engedélyezett rekordhalmazok számának korlátját? A rekordhalmazok aktuális száma és legfelső korlátja az Azure Portalon látható, a zóna „Tulajdonságainál”. Ha elérte ezt a korlátot, majd töröljön néhány rekordkészletet, vagy lépjen kapcsolatba az Azure-támogatással a zóna rekordkészlet-korlátjának növeléséhez, majd próbálkozzon újra. 


### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nem tudom feloldani a DNS-rekordomat

A DNS-névfeloldás egy többlépéses folyamat, amely több okból is meghiúsulhat. Az alábbi lépések segítenek megvizsgálni, hogy a DNS-feloldás miért sikertelen egy DNS-rekordnál az Azure DNS-ben üzemeltetett zónában.

1.  Győződjön meg róla, hogy a DNS-rekordok helyesen lettek beállítva az Azure DNS-ben. Tekintse át a DNS-rekordokat az Azure Portalon, és győződjön meg róla, hogy a zónanév, a rekordnév és a rekordtípus helyes.
2.  Győződjön meg róla, hogy a DNS-rekordok feloldhatók az Azure DNS névkiszolgálóin.
    - Ha DNS-lekérdezéseket hajt végre a helyi számítógépről, gyorsítótárazott eredményeket láthat, amelyek nem tükrözik a névkiszolgálók aktuális állapotát.  Továbbá a vállalati hálózatok gyakran olyan DNS-proxykiszolgálókat használnak, amelyek megakadályozzák a DNS-lekérdezések adott névkiszolgálókhoz történő továbbítását.  Ha el szeretné kerülni ezeket a problémákat, használjon webes névfeloldási szolgáltatást (ilyen például a [digwebinterface](https://digwebinterface.com)).
    - Mindenképpen adja meg a DNS-zónához tartozó helyes névkiszolgálót, ahogy az Azure Portalon látható.
    - Ellenőrizze a DNS-név (meg kell adnia a teljes nevet, a zónanevet is beleértve) és a rekordtípus helyességét
3.  Győződjön meg róla, hogy a DNS-tartománynév helyesen lett [delegálva az Azure DNS-névkiszolgálókra](dns-domain-delegation.md). [Számos, harmadik féltől származó webhely létezik, amely lehetővé teszi a DNS-delegálás ellenőrzését](https://www.bing.com/search?q=dns+check+tool). Ez egy *zónadelegálási* teszt, így csak a DNS-zónanevét, és nem a teljes rekordnevet kell megadnia.
4.  A fentiek elvégzése után meg kell történnie a DNS-rekord feloldásának. Az ellenőrzéshez ismét használható a [digwebinterface](https://digwebinterface.com), ezúttal az alapértelmezett névkiszolgáló-beállításokkal.


### <a name="recommended-articles"></a>Ajánlott cikkek

* [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hogyan adható meg a „service” és a „protocol” egy SRV-rekordnál?

Az Azure DNS DNS-rekordokat és -rekordhalmazokat (amely az ugyanazzal a névvel és típussal rendelkező rekordok gyűjteménye) kezel. Egy SRV-rekordhalmaznál a „service” és a „protocol” a rekordhalmaz nevének részeként adható meg. Az egyéb SRV-paraméterek („priority”, „weight”, „port” és „target”) külön vannak megadva, a rekordhalmaz egyes rekordjainál.

Példák az SRV-rekordnevekre („sip” szolgáltatásnév, „tcp” protokoll):

- \_sip.\_tcp (egy rekordhalmazt hoz létre a zóna legfelső pontján)
- \_sip.\_tcp.sipservice (egy „sipservice” nevű rekordhalmazt hoz létre)

### <a name="recommended-articles"></a>Ajánlott cikkek

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-rekordhalmazok és -rekordok létrehozása az Azure Portal használatával](dns-getstarted-create-recordset-portal.md)
* [SRV rekordtípus (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>További lépések

* További információ az [Azure DNS-zónákról és -rekordokról](dns-zones-records.md)
* Az Azure DNS használatának megkezdéséhez olvassa el, hogyan [hozhat létre DNS-zónát](dns-getstarted-create-dnszone-portal.md) és [hozhat létre DNS-rekordokat.](dns-getstarted-create-recordset-portal.md)
* Meglévő DNS-zóna áttelepítéséhez olvassa el a [DNS-zónafájl importálásának és exportálásának](dns-import-export.md)módját.

