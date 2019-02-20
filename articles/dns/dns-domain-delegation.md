---
title: Az Azure DNS-delegálás áttekintése
description: Ismerje meg, hogyan módosíthatja a tartományok delegálását és használhatja tartományszolgáltatóként az Azure DNS-névkiszolgálóit.
services: dns
author: vhorne
ms.service: dns
ms.date: 2/19/2019
ms.author: victorh
ms.openlocfilehash: 85f7318ac584e324f58d2ad470dc896e79e416d5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417888"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>DNS-zónák delegálása az Azure DNS-sel

Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Egy tartomány DNS-lekérdezései csak akkor érik el az Azure DNS-t, ha a tartomány delegálva van az Azure DNS-be a szülőtartományból. Ne feledje: nem az Azure DNS a tartományregisztráló. Ez a cikk ismerteti a tartománydelegálás működését és a tartományok Azure DNS-be való delegálását.

## <a name="how-dns-delegation-works"></a>A DNS-delegálás működése

### <a name="domains-and-zones"></a>Tartományok és zónák

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  A legfelső szintű tartományok alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”.  És így tovább. A DNS-hierarchia tartományait különálló DNS-zónák üzemeltetik. A zónák globálisan fel vannak osztva, és a világ különböző pontjain található DNS-névkiszolgálók üzemeltetik őket.

**DNS-zóna** – A tartományok egyedi nevek a tartománynévrendszerben, például „contoso.com”. Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A „contoso.com” tartomány például számos DNS-rekordot tartalmazhat: „mail.contoso.com” (levelezési kiszolgálóhoz) és „www.contoso.com” (webhelyhez).

**Tartományregisztráló** – A tartományregisztráló egy olyan cég, amely internetes tartományneveket biztosít. Ezek a cégek ellenőrzik, hogy a használni kívánt internetes tartomány elérhető-e, és ők engedélyezik azok megvásárlását. A tartománynév regisztrálása után Ön annak a jogos tulajdonosa. Ha már van internetes tartománya, az aktuális tartományregisztrálóval delegálhat az Azure DNS-be.

Az akkreditált tartomány regisztráló szervezetek kapcsolatos további információkért lásd: [ICANN-Accredited regisztráló szervezetek](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

* A *mérvadó* DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
* A *rekurzív* DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Az Azure DNS mérvadó DNS szolgáltatást nyújt.  Rekurzív DNS szolgáltatást nem biztosít. Az Azure felhőszolgáltatásai és virtuális gépei automatikusan egy rekurzív DNS szolgáltatás használatára vannak konfigurálva, amelyet az Azure-infrastruktúra külön biztosít. Ha további információt szeretne kapni ezen DNS-beállítások módosításáról, olvassa el az [Azure-beli névfeloldást](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ismertető cikket.

A számítógépes vagy mobileszköz-kompatibilis DNS-ügyfelek általában egy rekurzív DNS-kiszolgálóval végeztetik el az ügyfélalkalmazások számára szükséges DNS-lekérdezéseket.

Amikor egy rekurzív DNS-kiszolgáló egy DNS-rekordra (például „www.contoso.com”) vonatkozó lekérdezést kap, először meg kell keresnie a „contoso.com” tartomány zónáját üzemeltető névkiszolgálót. A névkiszolgáló megkereséséhez a gyökér-névkiszolgálótól kiindulva megkeresi a „com” zónát üzemeltető névkiszolgálókat. Ezután lekérdezi a „com” névkiszolgálóktól a „contoso.com” zónát üzemeltető névkiszolgálókat.  Végül lekérdezi ezektől a névkiszolgálóktól a „www.contoso.com” címet.

Ez az eljárás a DNS-név feloldása. Szigorú értelemben véve a DNS-feloldás más lépéseket is tartalmaz, például a CNAME-rekordok követését, de ez nem fontos a DNS-delegálás megértéséhez.

Hogyan „mutat rá” egy szülőzóna a gyermekzóna névkiszolgálóira? Ezt egy speciális DNS-rekorddal, az úgynevezett névkiszolgálói rekorddal hajtja végre. Például a gyökérzóna tartalmazza a „com” névkiszolgálói rekordjait, és megjeleníti a „com” zóna névkiszolgálóit. A „com” zóna pedig tartalmazza a „contoso.com” névkiszolgálói rekordjait, amelyek a „contoso.com” zóna névkiszolgálóit mutatják. Egy szülőzónán belüli gyermekzóna névkiszolgálói rekordjainak beállítását nevezzük tartománydelegálásnak.

Az alábbi képen egy példa DNS-lekérdezés látható. A contoso.net és a partners.contoso.net Azure DNS-zónák.

![DNS-névkiszolgáló](./media/dns-domain-delegation/image1.png)

1. Az ügyfél lekéri a `www.partners.contoso.net` címet a helyi DNS-kiszolgálóról.
2. A helyi DNS-kiszolgálón nem található meg a rekord, így lekéri azt a gyökér-névkiszolgálótól.
3. A gyökér-névkiszolgálón sem található meg a rekord, azonban ismeri a `.net`-névkiszolgáló címét, és megadja azt a DNS-kiszolgálónak.
4. A helyi DNS-kiszolgáló a kérést küld a `.net` kiszolgáló nevét.
5. A `.net` kiszolgáló neve nem található a rekord, de tudja címét a `contoso.net` kiszolgáló nevét. Ebben az esetben reagáljon az Azure DNS Szolgáltatásban üzemeltetett DNS-zóna számára a névkiszolgáló címét.
6. A helyi DNS-kiszolgáló elküldi a kérelmet a névkiszolgálóját a `contoso.net` az Azure DNS-ben üzemeltetett zónában.
7. A zóna `contoso.net` nem található a rekord, de a névkiszolgálója `partners.contoso.net` és fűzi hozzá a címet. Ebben az esetben érdemes az Azure DNS Szolgáltatásban üzemeltetett DNS-zónában.
8. A helyi DNS-kiszolgáló elküldi a kérelmet a névkiszolgálóját a `partners.contoso.net` zóna.
9. A `partners.contoso.net` zóna a-rekordra van, és az IP-cím válaszol.
10. A helyi DNS-kiszolgáló biztosítja az IP-címet az ügyfélnek
11. Az ügyfél csatlakozik a `www.partners.contoso.net` webhelyhez.

A delegálások a névkiszolgálói rekordok két példányával rendelkeznek: egy a gyermekzónára mutató szülőzónában, egy pedig magában a gyermekzónában található. A „contoso.net” zóna a „net” névkiszolgálói rekordjai mellett a „contoso.net” névkiszolgálói rekordjait is tartalmazza. Ezek a rekordok az úgynevezett mérvadó névkiszolgálói rekordok, és a gyermekzóna tetején találhatók.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [delegálhat tartományokat az Azure DNS-be](dns-delegate-domain-azure-dns.md).

