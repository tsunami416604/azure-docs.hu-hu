---
title: Troubleshooting guide - Azure DNS
description: In this learning path, get started troubleshooting common issues with Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: b5fedba7b739c07a37f3aabf75ddd8ca465ba73b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210938"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS troubleshooting guide

This article provides troubleshooting information for common Azure DNS questions.

If these steps don't resolve your issue, you can also search for or post your issue on our [community support forum on MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Or, you can open an Azure support request.


## <a name="i-cant-create-a-dns-zone"></a>I can't create a DNS zone

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Minden DNS-zónanévnek egyedinek kell lennie a saját erőforráscsoportján belül. That is, two DNS zones with the same name can't share a resource group. Próbáljon meg eltérő zónanevet vagy erőforráscsoportot használni.
3.  Előfordulhat, hogy megjelenik az „Elérte vagy túllépte a zónák maximális számát az előfizetésben ({előfizetés azonosítója})” hibaüzenet. Használjon másik Azure-előfizetést, töröljön néhány zónát, vagy vegye fel a kapcsolatot az Azure ügyfélszolgálatával az előfizetésre vonatkozó korlát megemeléséhez.
4.  „A(z) {zónanév} zóna nem érhető el” hibaüzenet is megjelenhet. Ez a hiba azt jelzi, hogy az Azure DNS nem tudott névkiszolgálót lefoglalni ehhez a DNS-zónához. Próbáljon meg egy másik zónanevet használni. Or, if you are the domain name owner you can contact Azure support to allocate name servers for you.


### <a name="recommended-articles"></a>Recommended articles

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Nem tudok létrehozni egy DNS-rekordot

A leggyakoribb hibák elhárításához próbálja ki az alábbi lépéseket:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  A rekordhalmaz már létezik?  Az Azure DNS a rekordokat *rekordhalmazok* használatával kezeli, amelyek ugyanazzal a névvel és típussal rendelkező rekordok gyűjteményei. Ha egy ugyanolyan nevű és típusú rekord már létezik, akkor egy másik ugyanilyen rekord hozzáadásához szerkesztenie kell a meglévő rekordhalmazt.
3.  A DNS-zóna legfelső pontján (a zóna „gyökerén”) próbál rekordot létrehozni? Ha igen, az általános egyezmény a DNS rendszerben a „@” karakter használata a rekord neveként. Also note that the DNS standards don't permit CNAME records at the zone apex.
4.  CNAME-ütközést tapasztal?  The DNS standards don't allow a CNAME record with the same name as a record of any other type. Ha rendelkezik egy meglévő CNAME-mel, nem tud egy másik típusú, de ugyanolyan nevű rekordot létrehozni.  A CNAME létrehozása szintén sikertelen lesz, ha a neve megegyezik egy más típusú, már létező rekord nevével. Szüntesse meg az ütközést a másik rekord eltávolításával vagy egy eltérő rekordnév beállításával.
5.  Elérte a DNS-zónában engedélyezett rekordhalmazok számának korlátját? A rekordhalmazok aktuális száma és legfelső korlátja az Azure Portalon látható, a zóna „Tulajdonságainál”. If you've reached this limit, then either delete some record sets or contact Azure Support to raise your record set limit for this zone, then try again. 


### <a name="recommended-articles"></a>Recommended articles

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


### <a name="recommended-articles"></a>Recommended articles

* [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hogyan adható meg a „service” és a „protocol” egy SRV-rekordnál?

Az Azure DNS DNS-rekordokat és -rekordhalmazokat (amely az ugyanazzal a névvel és típussal rendelkező rekordok gyűjteménye) kezel. Egy SRV-rekordhalmaznál a „service” és a „protocol” a rekordhalmaz nevének részeként adható meg. Az egyéb SRV-paraméterek („priority”, „weight”, „port” és „target”) külön vannak megadva, a rekordhalmaz egyes rekordjainál.

Példák az SRV-rekordnevekre („sip” szolgáltatásnév, „tcp” protokoll):

- \_sip.\_tcp (egy rekordhalmazt hoz létre a zóna legfelső pontján)
- \_sip.\_tcp.sipservice (egy „sipservice” nevű rekordhalmazt hoz létre)

### <a name="recommended-articles"></a>Recommended articles

* [DNS-zónák és -rekordok](dns-zones-records.md)
* [DNS-rekordhalmazok és -rekordok létrehozása az Azure Portal használatával](dns-getstarted-create-recordset-portal.md)
* [SRV rekordtípus (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Következő lépések

* Learn about [Azure DNS zones and records](dns-zones-records.md)
* To start using Azure DNS, learn how to [create a DNS zone](dns-getstarted-create-dnszone-portal.md) and [create DNS records](dns-getstarted-create-recordset-portal.md).
* To migrate an existing DNS zone, learn how to [import and export a DNS zone file](dns-import-export.md).

