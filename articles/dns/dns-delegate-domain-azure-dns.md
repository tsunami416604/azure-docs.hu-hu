---
title: Oktatóanyag – Saját tartomány és altartomány üzemeltetése az Azure DNS-ben
description: Ez az oktatóanyag bemutatja, hogyan konfigurálható az Azure DNS saját DNS-zónák üzemeltetéséhez.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: ea0dc257d691326bc073b4cbff37e847a6990f02
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452298"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. 

Tegyük fel, hogy megvette a „contoso.net” tartományt egy tartományregisztrálótól, majd létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói rekordjait. A regisztráló ezeket a névkiszolgálói rekordokat a „.net” szülőzónában tárolja. A világ különböző pontjain tartózkodó internetfelhasználók ekkor az Azure DNS-zónabeli tartományához lesznek átirányítva, amikor megpróbálják feloldani a „contoso.net” DNS-rekordjait.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * DNS-zóna létrehozása
> * Névkiszolgálók listájának lekérése
> * A tartomány delegálása
> * A delegálás működésének ellenőrzése


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure portálra.
1. A bal felső sarokban kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **DNS-zóna** lehetőségre a **DNS-zóna létrehozása** lap megnyitásához.

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. A **DNS-zóna létrehozása** lapon adja meg a következő értékeket, majd kattintson a **Létrehozás** parancsra:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|[az Ön tartományneve] |Az Ön által megvásárolt tartománynév. Ez az oktatóanyag a „contoso.net” tartománynevet használja példaként.|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. |
   |**Hely**|USA keleti régiója||

> [!NOTE]
> Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha a kiválasztott előfizetésben már több erőforrás szerepel, az alkalmazásátjáró egyszerű eléréséhez beírhatja a tartománynevet a **Szűrés név alapján** mezőbe. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* és *ns4-01.azure-dns.info*:

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.


## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

Amikor egy tartományt az Azure DNS-be delegál, az Azure DNS által nyújtott névkiszolgálókat kell használnia. Javasoljuk, hogy használja mind a négy névkiszolgálói nevet, a tartomány nevétől függetlenül. A tartománydelegáláshoz nem szükséges, hogy a névkiszolgáló ugyanazt a legfelső szintű tartományt használja, mint az Ön tartománya.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Előfordulhat, hogy egyes regisztrálók hozzáfűzik a pontot, ha nincsen ott a névkiszolgáló nevének végén. Azonban a DNS RFC-vel való megfelelés érdekében mindenképpen tegye a cím végére a záró pontot, mert nem számolhat azzal, hogy minden regisztráló hozzáfűzi a címhez, ha Ön lehagyja.

A saját zónájában történő, névkiszolgálókat használó delegálásokat – más néven *személyes névkiszolgálókat* – az Azure DNS jelenleg nem támogatja.

## <a name="verify-that-the-delegation-is-working"></a>A delegálás működésének ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy működik-e. Ezt például az *nslookup* vagy egy hasonló eszköz segítségével teheti meg, amely lekérdezi a zónája SOA típusú rekordját. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy a delegálás befejezését követően akár 10 percet vagy többet is várni kell, mielőtt a delegálás működését sikeresen ellenőrizni lehetne. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Az Azure DNS névkiszolgálóit nem kell megadnia. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

A parancssorba írjon be egy, a következőhöz hasonló nslookup parancsot:

```
nslookup -type=SOA contoso.net
```

Az alábbiakban egy példát láthat az előző parancsra adott válaszra:

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

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja a **contosoRG** erőforráscsoportot. Ellenkező esetben törölje a **contosoRG** erőforráscsoportot és az oktatóanyag során létrehozott erőforrásokat. Ehhez kattintson a **contosoRG** erőforráscsoportra, majd az **Erőforráscsoport törlése** elemre. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a saját tartománynevéhez, és delegálta azt az Azure DNS-be. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
