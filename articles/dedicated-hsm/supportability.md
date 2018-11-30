---
title: Az Azure dedikált HSM támogatási lehetőségek |} A Microsoft Docs
description: Azure dedikált HSM FIPS szabványnak megfelelő Azure-ban kulcstároló-képességeket biztosít 140-2-3. szint tanúsítvánnyal
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: barclayn
ms.openlocfilehash: 7c7cc38cb3332b153cd2a315d48c69b48a1dc357
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319504"
---
# <a name="azure-dedicated-hsm-supportability"></a>Dedikált HSM Azure támogatási lehetőségek

Az Azure dedikált HSM Service egy fizikai eszköz az egyetlen ügyfél használata révén a teljes körű rendszergazdai vezérlés és felügyeleti felelősséget. Az eszköz elérhetővé tett egy [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft nem jutnak felügyeleti figyelési szerepkörként fizikai soros port melléklet túl az ügyfél egyszer kiépítve.  Hozzáféréssel, nem a Microsoft nincs folyamatban levő szoftverek szintű karbantartás, illetve a rendszer felügyeleti feladatok is rendelkezik. Ennek eredményeképpen ügyfelei felelősek az üzemeltetési tevékenységek jellemző.
Ügyfelek teljes körűen felelős az alkalmazásokat, amelyek használni a HSM-EK és a támogatási vagy consulting-alapú segítséget Gemalto együttműködve. Ügyfél tulajdonosi működési higiéniai mértékben, mert nincs lehetőség a Microsoft a szolgáltatás magas rendelkezésre állási garancia bármilyen ajánlat. Fontos, hogy az ügyfél felelőssége annak biztosítása érdekében az alkalmazások helyesen van konfigurálva a magas rendelkezésre állás elérése érdekében. A Microsoft ellenőrzi és eszközök állapotának és a hálózati kapcsolat fenntartása érdekében.

## <a name="gemalto-support"></a>Gemalto támogatása

A dedikált HSM szolgáltatást használó ügyfelek rendelkeznie kell egy támogatási szerződés Gemalto helyen. A támogatási szerződés részeként az ügyfelek közvetlenül kapják meg útmutatást, a támogatást és a szolgáltatások Gemalto. A mechanizmus kérhet támogatást a Gemalto keresztül van azok [támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/).
Gemalto biztosít bármely szoftver-összetevő a hardveres biztonsági MODULT (például hozzáférés ügyfélszoftvert és SDK-k) használata szükséges. Hogy is támogatja a konfigurációt, és a tervezési, fejlesztési és üzembe helyezést a SafeNet Luna 7 hardveres biztonsági MODULT használó alkalmazások tanácsadási szolgáltatásokat nyújtanak.

### <a name="software-components"></a>Szoftver-összetevő

Többféle szoftver-összetevő használt hardveres biztonsági modulokhoz konfigurációját:

* Ügyfélszoftver
* SDK
* Eszközök

### <a name="guidance"></a>Útmutatás

Gemalto keresztül elérhető felügyeleti és konfigurációs útmutatás lehetővé teszi a [támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/). Ha már bejelentkezett egy érvényes ügyfél-Azonosítót használ, ezeket a dokumentumokat letölthetők. Gemalto integrációs útmutatóiban, hogy az ügyfelek különböző forgatókönyveket és a szoftver Integrációk sorozatát is biztosít. További információkért lásd: a [Gemalto partner webhelyét a Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Támogatás

Bármilyen szintű szoftverproblémára vagy kérdés viszonyítva a hardveres biztonsági modulok használata a dedikált HSM-szolgáltatás részeként foglalkozzon Gemalto közvetlenül támogatja. Gemalto fent felsorolt összes szoftverösszetevőket, és minden olyan egyéni HSM-konfiguráció által utáni kiépítési fogja javítani. További információkért lásd: a [Gemalto támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Tanácsadói szolgáltatások

A tervezési, fejlesztési és a hardveres biztonsági MODULT használó egyéni alkalmazások üzembe helyezését bármely segítségért lépjen kapcsolatba fiókfelelősével Gemalto.

## <a name="microsoft-support"></a>Microsoft-ügyfélszolgálat

A Microsoft feladata a HSM fizikai eszközök érhetők el, és a egy működési állapota a kizárólagos használata egyetlen ügyfél. Ügyfelek felügyelete és kezelése az eszköz felelős. A Microsoft feladatai a következők:

* Arról, hogy az eszköz energiagazdálkodási és hűtési
* A hardveres biztonsági MODULT (például hibajavítási forgatókönyv) a egy működési állapotot karbantartása
* Az eszközt a hálózaton keresztül érhető el.

Például a következő problémákat kell a Microsoftnak:

* Összetevő-hibák
* Hiba a teljes eszköz
* Hálózati hozzáférési problémák
* Problémák kiépítésének és megszüntetésének biztosítása.

A Microsoft egy figyelési szerepkör (azaz nem rendszergazdai szerepkör), amely lehetővé teszi az alapszintű állapot-telemetria-n keresztül az eszköz soros port fizikai hozzáféréssel rendelkezik.  Ez lehetővé teszi a Microsoftnak, hogy adja meg a problémák proaktív értesítést küld az ügyfélnek, kivéve, ha az ügyfél úgy dönt, hogy ez az engedély korlátozása. 

### <a name="provisioning-and-decommissioning"></a>Kiépítés és leszerelése

Miután egy ügyfél rendelkezik egy jóváhagyott regisztrációs a dedikált HSM-szolgáltatás, lesz (jelenleg keresztül PowerShell vagy parancssori felület és nem az Azure portal) HSM-erőforrások létrehozására. Az erőforrás-elosztási folyamat, amely egy fizikai eszköz egy meghatározott régióban, az ügyfél előre meghatározott virtuális hálózat (VNet) végighalad. Látható a virtuális hálózathoz, ha az ügyfél az eszköz eléréséhez, és konfigurálja azt további követelmények. Ügyfelek eléréséhez a dedikált HSM-EK Gemalto ügyfélszoftvert és eszközök használatával. A Microsoft támogatja az erőforrás létrehozásának folyamatát. Egyéni konfiguráció feldolgozása és Gemalto után támogatottak. (lásd a fenti támogatja Gemalto). Amikor egy ügyfél véget ért, hardveres biztonsági MODULT használ, azt kell kéréséhez (vagy zeroized) ahhoz, hogy nincsenek az adatok megőrzését. Alaphelyzetbe állíthatja az eszközt, a folyamat összes egyéni konfigurációs és adatokat távolítja el. A Microsoft felszabadítja az eszközt, és visszaadja a készlet az eredeti állapotba. Ez azt jelenti, hogy amikor az eszközt vissza nem a készlethez nincs bizonyíték előző felhasználói tevékenység. 

### <a name="hardware-issues"></a>Hardverproblémák

A HSM-eszközre van redundáns és cserélhető áramforrások és egységek ventilátor. Ha eltávolítja, ha az eszköz bekapcsolásakor egy illetéktelen eseményt okozhat a ventilátor egység eltávolítása. Összetevő hiba esetén a Microsoft használja a legmegfelelőbb folyamat a összetevő szintű probléma úgy, hogy minimális megszakítással használhatja tovább, és a legalacsonyabb kockázatát, az ügyfelek szolgáltatás rendelkezésre állása miatt.
Az eszköz minden komolyabb hiba az eszköz váltja fel az ingyenes készletből egy friss eredményez. Az ügyfél az új eszköz egyszerűen tartalmazza a meglévő magas rendelkezésre ÁLLÁSÚ párok, szinkronizálását, és térjen vissza a teljes működési állapotot. A sikertelen eszköz kell szem előtt eltávolítja, és a helyen, az Adatközpont aprítva eszközök adatait. Csak a váz visszatér Gemalto újrafelhasználás céljából.


### <a name="networking-issues"></a>Hálózatkezelés problémák

Ha az ügyfelek hálózati hozzáférési problémákat tapasztal a HSM-eszközre, akkor kell forduljon a Microsoft ügyfélszolgálatához. A hálózati hozzáféréshez egy egyszerű tesztet az SSH használatával csatlakozhat a HSM eszközt. Ha ez nem sikerül, forduljon a Microsoft ügyfélszolgálatához.

## <a name="service-level-expectations-for-support"></a>Szolgáltatás szintű elvárásoknak támogatás

A Microsoft támogatási szolgáltatási szintekhez, tekintse meg a [Azure-támogatási csomag](https://azure.microsoft.com/support/plans/).
Gemalto támogatást szolgáltatási szintekhez, tekintse meg a [Gemalto támogatja az Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy legfontosabb fogalmaival, mint például a magas rendelkezésre állással és biztonsággal megértsük eszközkiépítési és az alkalmazás-tervezés vagy a központi telepítés előtt.

* [Üzembe helyezési architektúrája](deployment-architecture.md)
* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Monitorozás](monitoring.md)