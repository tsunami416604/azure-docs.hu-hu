---
title: Oktatóanyag – Saját tartomány és altartomány üzemeltetése az Azure DNS-ben
description: Ez az oktatóanyag bemutatja, hogyan konfigurálható az Azure DNS saját DNS-zónák üzemeltetéséhez.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: c0c5c5fe899c9b9b898973a88c7dac4256959ee4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779776"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Üzemeltessen saját tartományt az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Tegyük fel, hogy megvette a „contoso.net” tartományt egy tartományregisztrálótól, majd létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói rekordjait. A regisztráló ezeket a névkiszolgálói rekordokat a „.net” szülőzónában tárolja. A világ különböző pontjain internetes felhasználók majd irányítja az az Azure DNS-zónabeli tartományához, amikor megpróbálják feloldani a "contoso.NET" DNS-rekordjait.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * DNS-zóna létrehozása.
> * Névkiszolgálók listájának lekéréséhez.
> * A tartomány delegálása.
> * Ellenőrizze, működik-e a delegálást.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Teszteli, hogy is üzemeltethet az Azure DNS-ben elérhető tartomány névvel kell rendelkeznie. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Ebben az oktatóanyagban használt példa tartományt contoso.net, de a saját tartománynevét használja.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure portálra.
1. A bal felső sarokban kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **DNS-zóna** lehetőségre a **DNS-zóna létrehozása** lap megnyitásához.

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. A **DNS-zóna létrehozása** lapon adja meg a következő értékeket, majd kattintson a **Létrehozás** parancsra:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|[az Ön tartományneve] |Az Ön által megvásárolt tartománynév. Ez az oktatóanyag a „contoso.net” tartománynevet használja példaként.|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül.<br>Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig "globális", és nem jelenik meg.|
   |**Hely**|USA keleti régiója||

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha az előfizetést, amely a kijelölt már több erőforrás szerepel, a tartománynevet is megadhatja a **Szűrés név alapján** az Alkalmazásátjáró egyszerű eléréséhez mezőbe. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* és *ns4-01.azure-dns.info*:

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.

## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. 

1. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

1. Az Azure DNS-tartomány delegálása, amikor az Azure DNS által nyújtott névkiszolgálókat kell használnia. Használja az összes a négy névkiszolgálói nevet, a tartomány nevétől függetlenül. A tartománydelegáláshoz nem szükséges a névkiszolgáló ugyanazt a legfelső szintű tartományt használja, a tartomány.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Néhány regisztráló szervezetek fűzze hozzá az időszak, ha a végén a Névkiszolgálói név nem rendelkezik. Meg kell felelnie a DNS-RFC az, hogy a záró pontot tartalmaznak.

Más néven a saját zónájában, névkiszolgálókat használó delegálásokat *személyes névkiszolgálókat*, az Azure DNS jelenleg nem támogatottak.

## <a name="verify-the-delegation"></a>A delegálás ellenőrzése

A delegálás befejezése után ellenőrizheti, mint például egy olyan eszközzel működés *nslookup* a Start-szolgáltató (SOA) rekord a zóna lekérdezése. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy Várjon 10 percet kell, vagy további előtt sikeresen is a delegálás befejezése után ellenőrizze, hogy működik. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Adja meg az Azure DNS névkiszolgálóit nem kell. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

1. Egy parancssorból írja be az nslookup parancs az alábbi példához hasonló:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Győződjön meg arról, hogy a válasz a következő nslookup kimenet hasonlít:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja a **contosoRG** erőforráscsoportot. Ellenkező esetben törölje a **contosoRG** erőforráscsoportot és az oktatóanyag során létrehozott erőforrásokat.

- Válassza ki a **contosoRG** erőforrás csoportot, és adja meg **erőforráscsoport törlése**. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a tartományához, és az Azure DNS-delegálás azt. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)