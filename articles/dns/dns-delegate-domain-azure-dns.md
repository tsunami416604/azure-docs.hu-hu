---
title: Oktatóanyag – Saját tartomány és altartomány üzemeltetése az Azure DNS-ben
description: Ez az oktatóanyag bemutatja, hogyan konfigurálható az Azure DNS saját DNS-zónák üzemeltetéséhez.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 99a3ca0115611f45ed080c39767d13e087b8efb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464222"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben

Az Azure DNS használatával saját DNS-tartományt üzemeltethet, és kezelheti a tartomány DNS-rekordjait. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Tegyük fel, hogy megvette a „contoso.net” tartományt egy tartományregisztrálótól, majd létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. Mivel Ön a tartomány tulajdonosa, a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálói rekordjait. A regisztráló ezeket a névkiszolgálói rekordokat a „.net” szülőzónában tárolja. Az internetes felhasználók ezután a Azure DNS zónában lesznek átirányítva a tartományba, amikor megpróbálják feloldani a DNS-rekordokat a contoso.net-ben.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy DNS-zónát.
> * A névkiszolgálók listájának beolvasása.
> * A tartomány delegálása.
> * Ellenőrizze, hogy a delegálás működik-e.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Meg kell adnia egy tartománynevet, hogy tesztelni tudja, hogy Azure DNS üzemeltethető-e. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Az oktatóanyaghoz használt contoso.net, de a saját tartománynevét használja.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. DNS-zóna létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **DNS-zónákat**.

   ![DNS-zóna](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Válassza a **DNS-zóna létrehozása**lehetőséget.
1. A **DNS-zóna létrehozása** lapon adja meg a következő értékeket, majd kattintson a **Létrehozás** parancsra:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|[az Ön tartományneve] |Az Ön által megvásárolt tartománynév. Ez az oktatóanyag a „contoso.net” tartománynevet használja példaként.|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül.<br>Az erőforráscsoport helye nincs hatással a DNS-zónára. A DNS-zóna helye mindig "globális", és nem jelenik meg.|
   |**Hely**|USA keleti régiója||

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak nevét. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** ablaktábláján válassza az **Összes erőforrás** lehetőséget. Az **Összes erőforrás** lapon válassza ki a saját DNS-zónáját. Ha a kiválasztott előfizetés már rendelkezik több erőforrással is, megadhatja a tartománynevet a **szűrés név alapján** mezőben az Application Gateway egyszerű eléréséhez. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna lapon. Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* és *ns4-01.azure-dns.info*:

   ![Névkiszolgálók listája](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.

## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. 

1. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS névkiszolgálóira.

1. Amikor Azure DNSre delegál egy tartományt, a Azure DNS által biztosított névkiszolgálók használatát kell használnia. Használja mind a négy névszervert, a tartomány nevétől függetlenül. A tartomány delegálásához nem szükséges, hogy a névkiszolgáló ugyanazt a legfelső szintű tartományt használja, mint a tartomány.

> [!NOTE]
> Amikor átmásolja a névkiszolgálók címeit, mindenképpen másolja át a cím végén szereplő pontot is. A záró karakterként szolgáló pont jelzi egy teljes tartománynév végét. Egyes regisztrátorok hozzáfűzik az időszakot, ha az NS neve nem a végén van. Ahhoz, hogy meg lehessen felelni a DNS RFC-nek, tartalmaznia kell a záró pontot.

Azok a delegálások, amelyek a saját zónában lévő névkiszolgálók használatát használják, esetenként a *hiúság névkiszolgálók*néven is ismertek, a Azure DNS jelenleg nem támogatottak.

## <a name="verify-the-delegation"></a>A delegálás ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy működik-e egy eszköz, például az *nslookup* használatával a zóna SOA-rekordjának lekérdezéséhez. A SOA típusú rekord automatikusan létrejön a zóna létrehozásakor. Előfordulhat, hogy a delegálás befejezése után legalább 10 percet várnia kell, mielőtt sikeresen ellenőrizni tudja, hogy működik-e. Időbe telik a módosítások propagálása a DNS-rendszeren keresztül.

Nem kell megadnia a Azure DNS névkiszolgálók nevét. Ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

1. A parancssorban adjon meg egy, az alábbi példához hasonló nslookup-parancsot:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ellenőrizze, hogy a válasz a következő nslookup-kimenethez hasonlóan néz ki:

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

- Válassza ki a **contosoRG** erőforráscsoportot, majd válassza az **erőforráscsoport törlése**lehetőséget. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy DNS-zónát a tartományhoz, és delegálta azt Azure DNSra. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)