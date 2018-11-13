---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 73e95f6259c916b06fe61cb47fd36beac4c7a427
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572248"
---
A tartománynévrendszer (DNS) segítségével keresse meg a dolgok az interneten. Például amikor adjon meg egy címet a böngészőben, vagy kattintson egy hivatkozásra a weblapon, az DNS segítségével a tartomány lefordítása IP-címet. Az IP-cím rendezését, például egy utca, házszám van, de nem nagyon emberi rövid. Például sokkal egyszerűbb legyen a DNS-név, például ne feledje **contoso.com** ne felejtse el IP-címet, például 192.168.1.88 vagy 2001:0:4137:1f67:24a2:3888:9cce:fea3, mint.

A DNS rendszerben alapján *rekordok*. Rekordok társíthatja egy adott *neve*, mint például **contoso.com**, és az IP-címet vagy egy másik DNS-nevét. Ha az alkalmazás, például egy webes böngésző megkeresi a DNS-nevét, a rekord keresése, és bármilyen mutat címet használja. Ha az érték mutat egy IP-címet, a böngésző ezt az értéket fogja használni. Ha egy másik DNS-név mutat, majd az alkalmazás azt feloldási újra el. Végső soron minden névfeloldás lejár egy IP-címet.

Amikor létrehoz egy Azure-webhelyen, a DNS-név automatikusan a helyhez. Ez a név formájában történik  **&lt;yoursitename&gt;. azurewebsites.net**. A webhely Azure Traffic Manager-végpontként való hozzáadásakor az a webhely majd keresztül érhető el a  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** tartományhoz.

> [!NOTE]
> Ha a webhely van konfigurálva, egy Traffic Manager-végpont, használhatja a **. trafficmanager.net** cím DNS-rekordok létrehozása során.
> 
> Csak használható CNAME-rekordokat a Traffic Managerrel
> 
> 

Is több rekordtípust, mindegyik a saját funkciók és -korlátozások, de a Traffic Manager-végpontok ét webhelyek esetén csak számunkra egy; *CNAME* rögzíti.

### <a name="cname-or-alias-record"></a>CNAME or Alias record
Egy CNAME rekord leképezi egy *adott* DNS-nevet, például **mail.contoso.com** vagy **www.contoso.com**, egy másik (canonical) tartománynévvel. A kanonikus tartománynév van, az Azure Websites Traffic Manager használata esetén a  **&lt;myapp >. trafficmanager.net** tartományneve a Traffic Manager-profil. Létrehozása után a CNAME REKORDOT hoz létre egy alias a  **&lt;myapp >. trafficmanager.net** tartomány nevét. A CNAME bejegyzés feloldja az IP-címet a  **&lt;myapp >. trafficmanager.net** tartománynév automatikusan, így ha a webhely IP-címe megváltozik, akkor nem kell semmit sem kell.

Miután a forgalom érkezik, a Traffic Manager, majd irányítja a forgalmat a webhelyre, és a terheléselosztási mód konfigurálva van a használatával. Ez a teljes mértékben átlátható a webhely látogatói. Az egyéni tartománynév böngészőjükben csak akkor fogja látni.

> [!NOTE]
> Néhány tartomány regisztráló szervezetek leképezheti altartományt, mint például a CNAME rekord, használatakor csak engedélyezése **www.contoso.com**, és nem legfelső szintű hitelesítésszolgáltatója nevét, például **contoso.com**. A CNAME-rekordokat további információkért lásd: a dokumentáció a regisztráló térképein <a href="http://en.wikipedia.org/wiki/CNAME_record">a CNAME-rekordot a Wikipedia bejegyzés</a>, vagy a <a href="http://tools.ietf.org/html/rfc1035">IETF tartománynevek - megvalósítási és meghatározása</a> dokumentumot.
> 
> 

