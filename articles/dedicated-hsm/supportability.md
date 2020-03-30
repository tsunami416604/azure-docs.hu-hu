---
title: Támogathatóság - Azure dedikált HSM | Microsoft dokumentumok
description: Támogatási lehetőségek és felelősségi területek az Azure dedikált HSM-hez különböző forgatókönyvekben
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881008"
---
# <a name="azure-dedicated-hsm-supportability"></a>Az Azure dedikált HSM-támogatási lehetősége

Az Azure dedikált HSM-szolgáltatás egy fizikai eszköz egyetlen ügyfél általi használatra, teljes körű felügyeleti felügyelet és felügyeleti felelősség. A rendelkezésre bocsátott eszköz egy [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft nem rendelkezik rendszergazdai hozzáféréssel, ha egy ügyfél kiépíti, a fizikai soros portmellékleten kívül, mint figyelési szerepkört.  Hozzáférés nélkül a Microsoft nem rendelkezhet folyamatos szoftverszintű karbantartással vagy rendszerfelügyeleti feladatokkal. Ennek eredményeképpen az ügyfelek felelősek a tipikus működési tevékenységekért.
Az ügyfelek teljes mértékben felelősek a HSM-eket használó alkalmazásokért, és a Gemalto-val együttműködve támogatást vagy tanácsadáson alapuló segítséget nyújtanak. Az üzemeltetési higiénia identikai ügyféláltali tulajdonának mértéke miatt a Microsoft nem tud semmilyen magas rendelkezésre állási garanciát nyújtani erre a szolgáltatásra. Az ügyfél felelőssége annak biztosítása, hogy alkalmazásaik megfelelően legyenek konfigurálva a magas rendelkezésre állás elérése érdekében. A Microsoft figyeli és fenntartja az eszköz állapotát és a hálózati kapcsolatot.

## <a name="getting-support"></a>Támogatás igénylése

A Dedikált HSM ügyfélszolgálata a Microsoft és a Gemalto közös erőfeszítése. A Microsoft minden hardverrel vagy hálózati útvonallal kapcsolatos problémát megold, és a Tényleges HSM-mel kapcsolatos minden problémát, például a konfigurációt, a szoftvert, a firmware-t és az alkalmazásfejlesztést a Gemalto kezeli. Ez a támogatási modell biztosítja a leggyorsabb útvonalat a leghatékonyabb támogatáshoz. Ha egy adott kérdéssel kapcsolatban kétségei vannak, nyújtson be támogatási kérelmet a Microsofthoz, és mi biztosítjuk, hogy ön megfelelő en legyen. A Microsoft továbbra is részt vesz minden támogatási forgatókönyvben, és a legjobb támogatási élményre törekszik ügyfeleink számára.

## <a name="gemalto-support"></a>Gemalto támogatás

A dedikált HSM szolgáltatást használó ügyfelek a Plus támogatási csomagjuknak megfelelően jogosultak a Gemalto támogatására. Ehhez csak egy regisztrációs folyamatra van szükség a Gemalto támogatási portál használatával. Ehhez a Microsofttal a dedikált HSM-szolgáltatáshoz való hozzáférésre vonatkozó kezdeti kötelezettségvállalás részeként ügyfélazonosítót és utasításokat adunk. A Gemalto támogatásának mechanizmusa az [ügyfélszolgálati portáljukon](https://supportportal.gemalto.com/csm/)keresztül történik.
Fontos megjegyezni, hogy a Gemalto biztosítja a HSM használatához szükséges összes szoftvert és dokumentációt (például ügyfél-hozzáférési szoftvert és SDK-t) az ügyfélszolgálati portálon történő letöltésen keresztül.

### <a name="software-components"></a>Szoftverösszetevők

A HSM-eszközök konfigurációjában különböző szoftverösszetevőket használnak:

* Ügyfélszoftver
* SDK
* Eszközök

### <a name="guidance"></a>Útmutatás

A Gemalto az [ügyfélszolgálati portálon](https://supportportal.gemalto.com/csm/)keresztül teszi elérhetővé az adminisztrációs és konfigurációs útmutatást. Ha érvényes ügyfélazonosítóval jelentkezett be, ezek a dokumentumok letölthetők. A Gemalto integrációs útmutatók sorozatát is biztosítja, hogy segítse az ügyfeleket a különböző forgatókönyvekkel és szoftverintegrációkkal. További információt a [Microsoft Gemalto partnerwebhelyén talál.](https://safenet.gemalto.com/partners/microsoft/)

### <a name="support"></a>Támogatás

A HSM-ek dedikált HSM-szolgáltatás részeként történő használatával kapcsolatos bármely szoftverszintű problémát vagy kérdést közvetlenül a Gemalto-támogatásnak kell címezni. A fent felsorolt összes szoftverösszetevővel és a kiépítés utáni egyéni HSM-konfigurációkkal a Gemalto foglalkozik. További információt a [Gemalto ügyfélszolgálati portálján talál.](https://supportportal.gemalto.com/csm/)

### <a name="consulting-services"></a>Tanácsadói szolgáltatások

A HSM-et használó egyéni alkalmazások tervezésében, fejlesztésében és telepítésében a Gemalto-fiók képviselőjével kapcsolatos segítségért forduljon.

## <a name="microsoft-support"></a>Microsoft ügyfélszolgálata

A Microsoft gondoskodik arról, hogy a fizikai HSM-eszközök hálózaton keresztül hozzáférhetők legyenek, és működési állapotban legyenek egyetlen ügyfél kizárólagos használatára. Az ügyfelek felelősek az eszköz konfigurálásáért, felügyeletéért és kezeléséért. A Microsoft feladatai a következők:

* Annak biztosítása, hogy a készülék áramellátással és hűtési
* A HSM működési állapotának fenntartása (például törési/javítási forgatókönyvek)
* Az eszköz a hálózaton keresztül érhető el.

Az alábbiakhoz hasonló problémákat jelenteni kell a Microsoftnak:

* Összetevő-hibák
* Teljes eszközhiba
* Hálózati hozzáféréssel kapcsolatos problémák
* Problémák kiépítése és a megszüntetés.

A Microsoft fizikai soros porthozzáférést biztosít az eszközhöz egy figyelési szerepkör (azaz nem felügyeleti szerepkör) segítségével, amely lehetővé teszi az alapvető állapottelemetriai adatokat.  Ez lehetővé teszi a Microsoft számára, hogy proaktív értesítést küldjön az ügyfélnek a problémákról, kivéve, ha az ügyfél úgy dönt, hogy korlátozza ezt az engedélyt. 

### <a name="provisioning-and-decommissioning"></a>Kiépítés és leszerelés

Miután egy ügyfél rendelkezik egy jóváhagyott regisztrációval a dedikált HSM szolgáltatáshoz, képesek lesznek HSM-erőforrásokat létrehozni (jelenleg a PowerShellen vagy a parancssori felületen keresztül, és nem az Azure Portalon). Az erőforrás egy olyan foglalási folyamaton megy keresztül, amely egy adott régióban lévő fizikai eszközt az ügyfél előre definiált virtuális hálózatához (VNet) rendel. Miután látható a virtuális hálózaton, az ügyfél hozzáférhet az eszközhöz, és konfigurálja azt tovább, mint egy követelmény. Az ügyfelek a Gemalto ügyfélszoftverekkel és -eszközökkel férhetnek hozzá a dedikált HSM-ekhez. Az erőforrás-létrehozási folyamatot a Microsoft támogatja. Az egyéni konfigurációs folyamatot és azon túl a Gemalto támogatja. (lásd a fenti Gemalto-támogatást). Ha egy ügyfél befejezte a HSM használatát, alaphelyzetbe kell állítani (vagy nullázni) az adatok megőrzésének biztosítása érdekében. Az eszköz alaphelyzetbe állításának folyamata eltávolítja az összes egyéni konfigurációt és adatot. A Microsoft felszabadítja az eszközt, és eredeti állapotban visszaállítja azt a készletbe. Ez azt jelenti, hogy amikor az eszköz visszakerül a készletbe, nincs bizonyíték a korábbi ügyféltevékenységre. 

### <a name="hardware-issues"></a>Hardverproblémák

A HSM eszköz redundáns és cserélhető tápegységekkel és ventilátoregységekkel rendelkezik.  A ventilátoregység eltávolítása azonban továbbra is szabotázseseményt okoz. Összetevő-hiba esetén a Microsoft a legmegfelelőbb eljárást használja az összetevőszintű probléma olyan módon történő kezelésére, amely minimális megszakítást és a legalacsonyabb kockázatot okozza ügyfeleink szolgáltatásának elérhetőségére nézve.
Az eszköz további súlyos meghibásodása azt eredményezi, hogy az eszközt egy friss eszköz váltja fel az ingyenes készletből. Az ügyfél egyszerűen tartalmazza az új eszközt a meglévő HA-párban, hogy szinkronizálja és visszatérjen a teljes működési állapotba. A meghibásodott eszköz adatcsapágyi eszközeit eltávolítják és megsemmisítik az adatközpont ban. Csak az alvázat juttatják vissza a Gemalto-ba újrahasznosításra.


### <a name="networking-issues"></a>Hálózati problémák

Ha az ügyfelek hálózati hozzáférési problémákat tapasztalnak a HSM-eszközhöz, forduljon a Microsoft támogatási szolgálatához. A hálózati hozzáférés egyszerű tesztje az SSH használata a HSM-eszközhöz való csatlakozáshoz. Ha ez nem sikerül, forduljon a Microsoft támogatási szolgálatához.

## <a name="service-level-expectations-for-support"></a>Szolgáltatási szintre vonatkozó elvárások a támogatáshoz

A Microsoft támogatási szolgáltatási szintjeiről az [Azure támogatási csomagja](https://azure.microsoft.com/support/plans/)nyújt például .
A Gemalto támogatási szolgáltatási szintjeit a [Gemalto Support Essentials című dokumentumban tájékán írjuk](https://azure.microsoft.com/support/plans/)be.

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy az eszközkiépítés és az alkalmazástervezés vagy -telepítés előtt megfelelően ismert kulcsfogalmakat, például a magas rendelkezésre állást és a biztonságot.

* [Telepítési architektúra](deployment-architecture.md)
* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)

