---
title: 'Oktatóanyag: A tartomány és az altartomány üzemeltetése – Azure DNS'
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure DNS-t a DNS-zónák üzemeltetésére.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 8f29a2bbe0eb392927dd111b13e2260111ddd18e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238971"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Tegyük fel, hogy megvette a „contoso.net” tartományt egy tartományregisztrálótól, majd létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói rekordjait. A regisztráló ezeket a névkiszolgálói rekordokat a „.net” szülőzónában tárolja. Az internetfelhasználók a világ minden táján az Azure DNS-zónában lévő tartományba irányítják őket, amikor contoso.net dns-rekordokat próbálnak feloldani.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy DNS-zónát.
> * Névkiszolgálók listájának beolvasása.
> * Delegáld a tartományt.
> * Ellenőrizze, hogy működik-e a delegálás.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS-ben üzemeltethető tartománynévvel kell rendelkeznie. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Az oktatóanyaghoz használt példatartomány contoso.net, de használja a saját tartománynevét.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Dns-zóna létrehozásához lépjen az [Azure Portalra.](https://portal.azure.com/) Dns-zónák keresése és **kijelölése**.

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Válassza **a DNS-zóna létrehozása**lehetőséget.
1. A **DNS-zóna létrehozása** lapon adja meg a következő értékeket, majd kattintson a **Létrehozás** parancsra:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Név**|[az Ön tartományneve] |Az Ön által megvásárolt tartománynév. Ez az oktatóanyag a „contoso.net” tartománynevet használja példaként.|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül.<br>Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig "globális", és nem jelenik meg.|
   |**Helyen**|USA keleti régiója||

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha a kiválasztott előfizetésben már több erőforrás is szerepel, a **Tartománynevet** a Szűrő név szerint mezőbe írhatja be, hogy könnyen hozzáférhessen az alkalmazásátjáróhoz. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a zónához contoso.net van rendelve névkiszolgálókhoz *ns1-01.azure-dns.com,* *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org*és *ns4-01.azure-dns.info:*

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.

## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. 

1. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

1. Amikor delegál egy tartományt az Azure DNS-nek, az Azure DNS által biztosított névkiszolgálókat kell használnia. A tartomány nevétől függetlenül használja mind a négy névkiszolgálót. A tartománydelegáláshoz nincs szükség névkiszolgálóra, hogy ugyanazt a legfelső szintű tartományt használja, mint a tartomány.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Egyes regisztrátorok hozzáfűzik az időszakot, ha az NS neve nem rendelkezik a végén. A DNS RFC-nek való megfeleléshez adja meg a záró időszakot.

Az Azure DNS jelenleg nem támogatja a saját zónájában névkiszolgálókat használó delegálásokat, más néven *hiúsági névkiszolgálókat.*

## <a name="verify-the-delegation"></a>A delegálás ellenőrzése

Miután befejezte a delegálást, ellenőrizheti, hogy működik-e egy eszközzel, például *az nslookup-pal* a zóna SOA-rekordjának lekérdezéséhez. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy a delegálás befejezése után 10 percet vagy többet kell várnia, mielőtt sikeresen ellenőrizhetné, hogy működik-e. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Nem kell megadnia az Azure DNS-névkiszolgálókat. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

1. A parancssorból írjon be egy nslookup parancsot az alábbi példához hasonló módon:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ellenőrizze, hogy a válasz a következő nslookup kimenethez hasonlóan néz-e ki:

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

- Jelölje ki a **contosoRG** erőforráscsoportot, majd válassza **az Erőforráscsoport törlése lehetőséget.** 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a tartományhoz, és delegálta az Azure DNS-nek. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)