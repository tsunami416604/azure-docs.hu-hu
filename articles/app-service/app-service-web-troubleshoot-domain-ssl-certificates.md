---
title: Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása |} A Microsoft Docs
description: Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e10c6580c29784f8d56a31e267c47ec08894113b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418364"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása

Ez a cikk felsorolja az Azure web Apps egy tartományhoz vagy az SSL-tanúsítvány konfigurálásakor előforduló gyakori problémák. Lehetséges okait és megoldásait ezeket a problémákat is ismerteti.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN és Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás kérése**.

## <a name="certificate-problems"></a>Tanúsítvánnyal kapcsolatos problémák

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Nem adhat hozzá egy SSL-tanúsítvány kötése egy webalkalmazáshoz 

#### <a name="symptom"></a>Jelenség

SSL-kötést ad hozzá, amikor a következő hibaüzenet jelenhet meg:

"Nem sikerült hozzáadni az SSL-kötés létrehozásához. Tanúsítvány nem állítható be a meglévő virtuális IP-cím, mert egy másik virtuális IP-cím már használja a tanúsítványt."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha több IP-alapú SSL-kötéseit az azonos IP-cím több web apps között. Például egy webalkalmazás, egy IP-alapú SSL-régi tanúsítványt. Webes alkalmazás B az IP-alapú SSL a azonos IP-cím egy új tanúsítvánnyal rendelkezik. Amikor frissíti a webes alkalmazás SSL-kötést az új tanúsítvánnyal, az nem hiba, mert egy másik alkalmazás használja az ugyanazon IP-cím. 

#### <a name="solution"></a>Megoldás 

A probléma megoldásához használja a következő módszerek egyikét:

- Az IP-alapú SSL-kötés van a webalkalmazást, amely a régi tanúsítvány törlése. 
- Hozzon létre egy új IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="you-cant-delete-a-certificate"></a>A tanúsítvány nem törölhető 

#### <a name="symptom"></a>Jelenség

Amikor próbál meg törölni a tanúsítványt, a következő hibaüzenet jelenhet meg:

"Nem lehet törölni a tanúsítványt, mert azt jelenleg használatban van az SSL-kötést. Az SSL-kötés létrehozásához meg kell szüntetni a tanúsítvány törlése előtt."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha egy másik webes alkalmazást a tanúsítványt használ.

#### <a name="solution"></a>Megoldás

Távolítsa el a web apps az SSL-kötés az adott tanúsítvány. Próbálja meg törölni a tanúsítványt. Ha még mindig nem tudja törölni a tanúsítványt, törölje az internetes gyorsítótárban, és nyissa meg újra az Azure Portalon, egy új böngészőablakban. Próbálja meg törölni a tanúsítványt.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nem vásárolhat egy App Service-tanúsítvány 

#### <a name="symptom"></a>Jelenség
Nem vásárolhat egy [Azure App Service-tanúsítvány](./web-sites-purchase-ssl-web-site.md) az Azure Portalról.

#### <a name="cause-and-solution"></a>OK és megoldás
Ez a probléma a következő okokból fordulhat elő:

- Az App Service-csomag az ingyenes és közös. Ezek a tarifacsomag nem támogatják az SSL. 

    **Megoldás**: webalkalmazás az App Service-csomag frissítsen a standard előfizetésre.

- Az előfizetés nem rendelkezik érvényes bankkártyaadatok.

    **Megoldás**: érvényes bankkártyaadatok hozzáadása az előfizetéshez. 

- Az előfizetési ajánlatok között nem támogatja az App Service-tanúsítvány, például a Microsoft Student megvásárlásával.  

    **Megoldás**: frissíti az előfizetését. 

- Az előfizetés elérte a korlátot, amelyek egy adott előfizetés engedélyezett vásárolt.

    **Megoldás**: App Service-tanúsítványok legfeljebb 10 tanúsítványa vásárlásokra a használatalapú fizetés és a nagyvállalati szerződéssel rendelkező előfizetés esetében. Minden olyan előfizetés esetében a korlát: 3. A korlát növeléséhez forduljon [az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az App Service-tanúsítvány csalás lett megjelölve. A következő hibaüzenet: "a tanúsítvány meg lett jelölve lehetséges csalás. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány nem válik használhatóvá 24 órán belül, forduljon az Azure ügyfélszolgálatához."

    **Megoldás**: Ha a tanúsítvány csalás van megjelölve, és 24 óra nincs feloldva, kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Lépjen a **App Service-tanúsítványok**, és válassza ki a tanúsítványt.
    3. Válassza ki **Tanúsítványkonfiguráció** > **2. lépés: Ellenőrizze** > **tartomány-ellenőrzés**. Ebben a lépésben egy e-mailben értesítést küld az Azure-tanúsítvány-szolgáltatóhoz a probléma megoldásához.

## <a name="domain-problems"></a>Tartomány-problémák

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Megvásárolta a megfelelő tartomány SSL-tanúsítvány

#### <a name="symptom"></a>Jelenség

App Service-tanúsítvány, a megfelelő tartomány vásárolt. A tanúsítványt használja, a megfelelő tartománynevet nem lehet frissíteni.

#### <a name="solution"></a>Megoldás

Törölje a tanúsítványt, és ezután vásárol egy új tanúsítványt.

Ha az aktuális tanúsítvány, amely a megfelelő tartományt használ a "feltüntetett tulajdonos" állapotban van, a fog is felszámítjuk a tanúsítványt. App Service-tanúsítványok nem téríthető, hanem forduljon [az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) megtekintéséhez, hogy vannak-e más lehetőségek. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>App Service-tanúsítvány megújított, de a webalkalmazás jeleníti meg a régi tanúsítvány 

#### <a name="symptom"></a>Jelenség

Az App Service-tanúsítvány megújított, de a webalkalmazást az App Service-tanúsítványt használó továbbra is a régi tanúsítványt használ. Ezenkívül, figyelmeztető üzenet, hogy a HTTPS protokoll megadása kötelező.

#### <a name="cause"></a>Ok 
Az Azure App Service Web Apps funkcióját háttérfeladatként 8 óránként fut, és szinkronizálja a tanúsítvány erőforrás, ha vannak a módosításokat. Forgatás vagy frissítésekor egy tanúsítványt, néha az az alkalmazás továbbra is beolvasása van, a régi tanúsítványt, és nem az újonnan frissített tanúsítványt. A hiba oka, hogy a feladat szinkronizálja a tanúsítvány erőforrás még futott-e. 
 
#### <a name="solution"></a>Megoldás

Kényszerítheti, hogy a tanúsítvány szinkronizálást:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **App Service-tanúsítványok**, majd válassza ki a tanúsítványt.
2. Válassza ki **Újrakulcsolás és szinkronizálás**, majd válassza ki **szinkronizálási**. A szinkronizálás befejezése hosszabb időt vesz igénybe. 
3. Amikor a szinkronizálás befejeződött, megjelenik a következő értesítés: "Frissítése sikerült összes erőforrást a legújabb tanúsítvánnyal."

### <a name="domain-verification-is-not-working"></a>Tartomány-ellenőrzés nem működik. 

#### <a name="symptom"></a>Jelenség 
Az App Service-tanúsítvány tartomány-ellenőrzés van szükség, mielőtt a tanúsítvány készen áll a használatra. Ha bejelöli **ellenőrizze**, a folyamat sikertelen lesz.

#### <a name="solution"></a>Megoldás
Manuálisan ellenőrizze a tartomány egy txt típusú rekord hozzáadásával:
 
1.  Nyissa meg a tartomány neve szolgáltatás (DNS) szolgáltató, amelyen a tartomány nevét.
2.  Adjon hozzá egy txt típusú rekordot a tartományhoz, amely a tartomány-token, amely az Azure Portalon látható értékét használja. 

Várjon néhány percet a DNS-propagálás futtatásához, és válassza ki a **frissítése** gombra az ellenőrzés indításához. 

Alternatív megoldásként a HTML-weblap módszer segítségével manuálisan ellenőrizze a tartományt. Ez a módszer lehetővé teszi, hogy a hitelesítésszolgáltató ellenőrizze annak a tartománynak, amelyet a tanúsítványt a tartomány tulajdonjogát.

1.  Hozzon létre egy {tartomány-ellenőrzési token} .html nevű HTML-fájlt. Ez a fájl tartalmát a tartomány-ellenőrzési token értékét kell lennie.
3.  Töltse fel ezt a fájlt a webalkalmazás-kiszolgáló, amelyen az a tartomány gyökerében.
4.  Válassza ki **frissítése** tanúsítvány állapotának ellenőrzéséhez. Az ellenőrzés befejezése néhány percet igénybe vehet.

Például, ha a tartomány ellenőrzési token 1234abcd értéket az Azure.com webhelyen egy normál tanúsítványt vásárol, webes kérelem végzett http://azure.com/1234abcd.html 1234abcd értéket kell visszaadnia. 

> [!IMPORTANT]
> A tanúsítványrendelések csak 15 nap áll a tartomány-ellenőrzési művelet befejezéséhez. 15 nap után a hitelesítésszolgáltató a tanúsítványt megtagadja, és nem terheli a tanúsítványt. Ebben a helyzetben törli ezt a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nem vásárolhat egy tartományt

#### <a name="symptom"></a>Jelenség
Nem vásárolhat egy tartományt az Azure Portalon vagy az App Service Web Apps tartományból.

#### <a name="cause-and-solution"></a>OK és megoldás

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Nincs bankkártyára az Azure-előfizetést, vagy a hitelkártya érvénytelen.

    **Megoldás**: érvényes bankkártyaadatok hozzáadása az előfizetéshez.

- Ön nem az előfizetés tulajdonosa, így nem kell tartományt vásárolnia engedéllyel.

    **Megoldás**: [a tulajdonosi szerepkör hozzárendelése](../role-based-access-control/role-assignments-portal.md) a fiókjához. Vagy forduljon az előfizetés adminisztrátorát, hogy kérjen engedélyt vásárolhat egy tartományt.
- Elérte a határértéket tartományok az előfizetés megvásárlásával kapcsolatos. A jelenlegi maximum 20.

    **Megoldás**: Ha szeretné növelni a korlátot, lépjen kapcsolatba [az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az Azure-előfizetés típusa nem támogatja az App Service-tartományok vásárlása.

    **Megoldás**: az Azure-előfizetés váltson egy másik előfizetés-típus, például egy használatalapú fizetéses előfizetésre.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>A webalkalmazás nem adhat hozzá egy nevet 

#### <a name="symptom"></a>Jelenség

Amikor hozzáad egy állomásnevet, a folyamat érvényesítéséhez és a tartomány ellenőrzése sikertelen lesz.

#### <a name="cause"></a>Ok 

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Nincs engedélye a gazdagépnév hozzáadása.

    **Megoldás**: kérje meg az előfizetés adminisztrátorát, hogy adjon engedélyt adjon hozzá egy nevet.
- Nem sikerült ellenőrizni a tartomány tulajdonjogát.

    **Megoldás**: Győződjön meg arról, hogy a CNAME és a egy rekord megfelelően van konfigurálva. Egyéni tartomány leképezése a webalkalmazást, hozzon létre egy CNAME-rekordot vagy egy A rekordot. Ha szeretne egy legfelső szintű tartományt használja, és txt típusú rekordok kell használnia:

    |Rekordtípus|Gazdagép|Mutasson|
    |------|------|-----|
    |A|@|Webes alkalmazás IP-címe|
    |TXT|@|< alkalmazásnév >. azurewebsites.net|
    |CNAME|www|< alkalmazásnév >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS nem oldható fel

#### <a name="symptom"></a>Jelenség

A következő hibaüzenetet kapott:

"A DNS-rekord nem található."

#### <a name="cause"></a>Ok
Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Az élő (TTL) időszak ideje nem járt le. Ellenőrizze az élettartam értéke határozza meg, és ezután Várjon, amíg az időszak lejár, a tartomány DNS-konfigurációját.
- A DNS-konfiguráció helytelen.

#### <a name="solution"></a>Megoldás
- Várjon, amíg ez a probléma megoldódik, hogy 48 órán belül.
- Ha a DNS-konfiguráció is megváltoztatjuk a TTL-értéket, módosítsa az értéket 5 perc megtekintéséhez, hogy ez megoldja a problémát.
- Használat [WhatsmyDNS.net](https://www.whatsmydns.net/) , ellenőrizze, hogy a tartomány a webalkalmazás IP-címre mutat-e. Ha nem, az A rekord helyes IP-címet a webalkalmazás konfigurálása

### <a name="you-need-to-restore-a-deleted-domain"></a>Vissza kell állítania egy tartomány törlése 

#### <a name="symptom"></a>Jelenség
A tartomány már nem jelenik meg az Azure Portalon.

#### <a name="cause"></a>Ok 
Az előfizetés tulajdonosa előfordulhat, hogy véletlenül törölt a tartományhoz.

#### <a name="solution"></a>Megoldás
Ha a tartomány törölték a kevesebb mint hét napja, a tartomány még nem indult a törlési folyamat. Ebben az esetben is vásárolhatnak ugyanahhoz a tartományhoz újra, az Azure Portalon egy előfizetésen belül. (Ügyeljen arra, hogy a Keresés mezőbe írja be a pontos tartománynév.) Nem kell fizetnie az újra ezt a tartományt. Ha a tartományt törölték a több mint hét napja, lépjen kapcsolatba [az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) segítséget visszaállítani a tartományhoz.

### <a name="a-custom-domain-returns-a-404-error"></a>Egyéni tartomány 404-es hibát ad vissza. 

#### <a name="symptom"></a>Jelenség

Ha a felhasználó a helynek az egyéni tartománynév használatával, a következő hibaüzenet jelenhet meg:

"Hiba 404-webalkalmazás nem található."


#### <a name="cause-and-solution"></a>OK és megoldás

**OK: 1** 

Az egyéni tartomány konfigurált hiányzik egy CNAME és a egy rekordot. 

**1 OK megoldás**

- Ha hozzáadott egy A rekordot, győződjön meg arról, hogy egy txt típusú rekordot adott is. További információkért lásd: [a rekord létrehozása](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Ha nem rendelkezik a legfelső szintű tartományt használja, a webalkalmazás, javasoljuk, hogy használjon-e egy CNAME-rekordot helyett egy A rekordot.
- Ne használja egy CNAME-rekordot és a egy A rekordot is ugyanabban a tartományban. Ez egy ütközést okozhat, és megakadályozza, hogy a tartomány feloldva. 

**OK 2** 

Az internetböngészőben továbbra is előfordulhat, hogy gyorsítótárazza a tartomány régi IP-címét. 

**Megoldás ok 2**

Törölje a böngészőben. Windows-eszközök esetén a parancs futtatása `ipconfig /flushdns`. Használat [WhatsmyDNS.net](https://www.whatsmydns.net/) , ellenőrizze, hogy a tartomány a webalkalmazás IP-címre mutat-e. 

### <a name="you-cant-add-a-subdomain"></a>Nem adhat hozzá egy altartományt 

#### <a name="symptom"></a>Jelenség

Altartomány hozzárendelése egy webalkalmazáshoz nem adjon hozzá egy új nevet.

#### <a name="solution"></a>Megoldás

- Ellenőrizze az előfizetés adminisztrátorát, hogy győződjön meg arról, hogy Ön jogosult a gazdagépnév hozzáadása a webalkalmazáshoz.
- Ha több altartománnyal van szüksége, javasoljuk, hogy módosítsa az Azure DNS-tartomány üzemeltetése. Az Azure DNS használatával a webes alkalmazás 500 állomásnevek adhat hozzá. További információkért lásd: [altartomány hozzáadása](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















