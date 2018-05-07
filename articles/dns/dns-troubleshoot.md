---
title: Az Azure DNS-hibaelhárítási útmutatója |} Microsoft Docs
description: Az Azure DNS kapcsolatos gyakori hibák elhárítása
services: dns
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: genli
ms.openlocfilehash: 55db36f1312030cde8e820d9eca9b699ec8cdd4c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-dns-troubleshooting-guide"></a>Az Azure DNS-hibaelhárítási útmutatója

Ezen a lapon hibaelhárításával kapcsolatos kérdésekre Azure DNS-ben.

Ha ezeket a lépéseket nem sikerül megoldani a problémát, keresse meg vagy is a probléma írjon a [közösségi támogatási fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Azt is megteheti nyissa meg az Azure támogatási kérelmet.


## <a name="i-cant-create-a-dns-zone"></a>Nem lehet létrehozni egy DNS-zóna

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Tekintse át az Azure DNS naplókat a probléma okának megállapításához.
2.  Minden DNS-zónanévnek egyedinek kell lennie a saját erőforráscsoportján belül. Ugyanazzal a névvel rendelkező két DNS-zóna nem használhatja közösen az erőforráscsoportot. Próbáljon meg eltérő zónanevet vagy erőforráscsoportot használni.
3.  Előfordulhat, hogy megjelenik az „Elérte vagy túllépte a zónák maximális számát az előfizetésben ({előfizetés azonosítója})” hibaüzenet. Használjon másik Azure-előfizetést, töröljön néhány zónát, vagy vegye fel a kapcsolatot az Azure ügyfélszolgálatával az előfizetésre vonatkozó korlát megemeléséhez.
4.  „A(z) {zónanév} zóna nem érhető el” hibaüzenet is megjelenhet. Ez a hiba azt jelzi, hogy az Azure DNS nem tudott névkiszolgálót lefoglalni ehhez a DNS-zónához. Próbáljon meg egy másik zónanevet használni. Ha Ön a tartománynév tulajdonosa, felveheti a kapcsolatot az Azure ügyfélszolgálatával, ahol le tudnak foglalni egy névkiszolgálót az Ön számára.


### <a name="recommended-documents"></a>**Ajánlott dokumentumok**

[DNS-zónák és -rekordok](dns-zones-records.md)
<br>
[DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nem tudok létrehozni egy DNS-rekordot

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Tekintse át az Azure DNS naplókat a probléma okának megállapításához.
2.  A rekordhalmaz már létezik?  Az Azure DNS a rekordokat *rekordhalmazok* használatával kezeli, amelyek ugyanazzal a névvel és típussal rendelkező rekordok gyűjteményei. Ha egy ugyanolyan nevű és típusú rekord már létezik, akkor egy másik ugyanilyen rekord hozzáadásához szerkesztenie kell a meglévő rekordhalmazt.
3.  A DNS-zóna legfelső pontján (a zóna „gyökerén”) próbál rekordot létrehozni? Ha igen, az általános egyezmény a DNS rendszerben a „@” karakter használata a rekord neveként. Vegye figyelembe, hogy a DNS-szabványok nem engedélyeznek CNAME-rekordokat a zóna legfelső pontján.
4.  CNAME-ütközést tapasztal?  A DNS-szabványok nem engedélyeznek bármilyen más típusú rekorddal megegyező nevű CNAME-rekordokat. Ha rendelkezik egy meglévő CNAME-mel, nem tud egy másik típusú, de ugyanolyan nevű rekordot létrehozni.  A CNAME létrehozása szintén sikertelen lesz, ha a neve megegyezik egy más típusú, már létező rekord nevével. Szüntesse meg az ütközést a másik rekord eltávolításával vagy egy eltérő rekordnév beállításával.
5.  Elérte a DNS-zónában engedélyezett rekordhalmazok számának korlátját? A rekordhalmazok aktuális száma és legfelső korlátja az Azure Portalon látható, a zóna „Tulajdonságainál”. Ha elérte ezt a korlátot, töröljön néhány rekordhalmazt, vagy vegye fel a kapcsolatot az Azure ügyfélszolgálatával a zónához tartozó korlát emeléséhez, majd próbálkozzon újra. 


### <a name="recommended-documents"></a>**Ajánlott dokumentumok**

[DNS-zónák és -rekordok](dns-zones-records.md)
<br>
[DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Nem tudom feloldani a DNS-rekordomat

A DNS-névfeloldás egy többlépéses folyamat, amely több okból is meghiúsulhat. Az alábbi lépések segítenek megvizsgálni, hogy a DNS-feloldás miért sikertelen egy DNS-rekordnál az Azure DNS-ben üzemeltetett zónában.

1.  Győződjön meg róla, hogy a DNS-rekordok helyesen lettek beállítva az Azure DNS-ben. Tekintse át a DNS-rekordokat az Azure Portalon, és győződjön meg róla, hogy a zónanév, a rekordnév és a rekordtípus helyes.
2.  Győződjön meg róla, hogy a DNS-rekordok feloldhatók az Azure DNS névkiszolgálóin.
    - Ha DNS-lekérdezéseket hajt végre a helyi számítógépről, gyorsítótárazott eredményeket láthat, amelyek nem tükrözik a névkiszolgálók aktuális állapotát.  Továbbá a vállalati hálózatok gyakran olyan DNS-proxykiszolgálókat használnak, amelyek megakadályozzák a DNS-lekérdezések adott névkiszolgálókhoz történő továbbítását.  Ha el szeretné kerülni ezeket a problémákat, használjon webes névfeloldási szolgáltatást (ilyen például a [digwebinterface](http://digwebinterface.com)).
    - Mindenképpen adja meg a DNS-zónához tartozó helyes névkiszolgálót, ahogy az Azure Portalon látható.
    - Ellenőrizze a DNS-név (meg kell adnia a teljes nevet, a zónanevet is beleértve) és a rekordtípus helyességét
3.  Győződjön meg róla, hogy a DNS-tartománynév helyesen lett [delegálva az Azure DNS-névkiszolgálókra](dns-domain-delegation.md). [Számos, harmadik féltől származó webhely létezik, amely lehetővé teszi a DNS-delegálás ellenőrzését](https://www.bing.com/search?q=dns+check+tool). Ez egy *zónadelegálási* teszt, így csak a DNS-zónanevét, és nem a teljes rekordnevet kell megadnia.
4.  A fentiek elvégzése után meg kell történnie a DNS-rekord feloldásának. Az ellenőrzéshez ismét használható a [digwebinterface](http://digwebinterface.com), ezúttal az alapértelmezett névkiszolgáló-beállításokkal.


### <a name="recommended-documents"></a>**Ajánlott dokumentumok**

[Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hogyan adható meg a „service” és a „protocol” egy SRV-rekordnál?

Az Azure DNS DNS-rekordokat és -rekordhalmazokat (amely az ugyanazzal a névvel és típussal rendelkező rekordok gyűjteménye) kezel. Egy SRV-rekordhalmaznál a „service” és a „protocol” a rekordhalmaz nevének részeként adható meg. Az egyéb SRV-paraméterek („priority”, „weight”, „port” és „target”) külön vannak megadva, a rekordhalmaz egyes rekordjainál.

Példák az SRV-rekordnevekre („sip” szolgáltatásnév, „tcp” protokoll):

- \_sip.\_tcp (egy rekordhalmazt hoz létre a zóna legfelső pontján)
- \_sip.\_tcp.sipservice (egy „sipservice” nevű rekordhalmazt hoz létre)

### <a name="recommended-documents"></a>**Ajánlott dokumentumok**

[DNS-zónák és -rekordok](dns-zones-records.md)
<br>
[DNS-rekordhalmazok és rekordok létrehozása az Azure-portál használatával](dns-getstarted-create-recordset-portal.md)
<br>
[SRV rekord típusa (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>További lépések

* További tudnivalók [Azure DNS-zónák és rekordok](dns-zones-records.md)
* Azure DNS használatának megkezdéséhez megtudhatja, hogyan [hozzon létre egy DNS-zóna](dns-getstarted-create-dnszone-portal.md) és [DNS-rekordok létrehozása](dns-getstarted-create-recordset-portal.md).
* Egy meglévő DNS-zóna áttelepítéséhez megtudhatja, hogyan [importálni és exportálni egy DNS-zónafájlját](dns-import-export.md).

