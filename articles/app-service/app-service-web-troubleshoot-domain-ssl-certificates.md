---
title: Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása |} Microsoft Docs
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
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása

Ez a cikk az Azure web Apps tartományi vagy SSL-tanúsítvány konfigurálásakor előforduló gyakori problémák sorolja fel. Lehetséges okok és a problémák megoldásához is ismerteti.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás**.

## <a name="certificate-problems"></a>Tanúsítvánnyal kapcsolatos problémák

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Nem sikerült a kötés SSL-tanúsítvány hozzáadása a webalkalmazáshoz 

### <a name="symptom"></a>Jelenség

Ha hozzáad egy SSL-kötést, a következő hibaüzenet jelenhet meg:

**Nem sikerült hozzáadni az SSL-kötést. Tanúsítvány nem állítható a meglévő Fürtben, mert egy másik VIP már használ, hogy a tanúsítványok.**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a ugyanazon IP-cím több IP-alapú SSL-kötések még több webes alkalmazások között. A webalkalmazás például IP-alapú SSL régi tanúsítvánnyal rendelkezik. Webalkalmazás B IP-alapú SSL ugyanazt a címet az új tanúsítvánnyal. Ha web app SSL-kötést frissíti az új tanúsítvánnyal, akkor sikertelen lesz, és ez a hiba, mert ugyanazon IP-cím egy másik alkalmazás éppen használja. 

### <a name="solution"></a>Megoldás 

A probléma megoldásához használja a következő módszerek egyikét:

- Törölje a webalkalmazásban a régi tanúsítványt használó IP-alapú SSL-kötést. 
- Hozzon létre egy új IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="unable-to-delete-a-certificate"></a>Nem sikerült a tanúsítvány törlése 

### <a name="symptom"></a>Jelenség

Amikor megpróbálja törölni a tanúsítványt, a következő hibaüzenet jelenhet meg:

**Nem sikerült törölni a tanúsítványt, mert jelenleg használatban van az SSL-kötést. Az SSL-kötést a tanúsítvány törlése előtt el kell távolítani.**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a tanúsítványt használja egy másik webes alkalmazást.

### <a name="solution"></a>Megoldás

SSL-kötés az adott tanúsítvány eltávolítása a webalkalmazásokat. Próbálja meg törölni a tanúsítványt. Ha még mindig nem tudja törölni a tanúsítványt, törölje az internetes gyorsítótárban, nyissa meg újra az Azure portál egy új böngészőablakban. És próbálja meg törölni a tanúsítványt.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Nem sikerült egy App Service-tanúsítvány vásárlása 

### <a name="symptom"></a>Jelenség
Nem vásárolhat egy [App Service tanúsítvány](./web-sites-purchase-ssl-web-site.md) Azure-portálon.

### <a name="cause-and-solution"></a>OK és megoldás
Ez a probléma a következő okok miatt:

- Az App Service-csomag az "Ingyenes" vagy "Shared". Nem támogatjuk SSL ezen tarifacsomagokhoz. 

    **Megoldás**: a "Standard" webalkalmazás az App Service-csomag frissítése.

- Az előfizetés nem rendelkezik érvényes hitelkártyát.

    **Megoldás**: egy érvényes hitelkártya hozzáadásához az előfizetéshez. 

- Az előfizetés ajánlat nem támogatja egy App Service-tanúsítvány, például a Microsoft Student megvásárlását.  

    **Megoldás**: az előfizetés frissítése. 

- Az előfizetés elérte a maximális száma, amelyek számára engedélyezett az előfizetés beszerzési.

    **Megoldás**: App Service-tanúsítványok maximális száma 10 tanúsítvány vásárlás fizetési, Go és EA előfizetések esetében rendelkezik. A más előfizetés típusú határérték 3. A korlát növeléséhez forduljon [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az App Service tanúsítvány csalás lett megjelölve. A következő hibaüzenetet kapja: "a tanúsítvány rendelkezik már meg van jelölve a lehetséges csalásról. A kérelem jelenleg felülvizsgálat alatt. Ha a tanúsítvány nem lesz használható 24 órán belül".

    **Megoldás**: Ha a tanúsítvány csalás van megjelölve, és 24 óra múlva nem lett feloldva, majd kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
    2. Ugrás a **App Service-tanúsítványokkal**, válassza ki azt a tanúsítványt.
    3. Válassza ki **Tanúsítványkonfiguráció** > **2. lépés: Ellenőrizze** > **tartományok ellenőrzésének**. Ez egy e-mail értesítést küld az Azure tanúsítványszolgáltató a probléma megoldásához.

## <a name="domain-problems"></a>Tartomány-problémák

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>SSL-tanúsítvány helytelen tartomány vásárolt

### <a name="symptom"></a>Jelenség

Egy App Service-tanúsítvány a megfelelő tartomány vásárolta, és nem lehet frissíteni a megfelelő tartományhoz használni kívánt tanúsítványt.

### <a name="solution"></a>Megoldás

- Törölje a tanúsítványt, és megvásárolni az új tanúsítványt.
- Ha az aktuális tanúsítvány, amely a megfelelő tartomány használja a "feltüntetett tulajdonos" állapotban van, majd is a számlázás történik az adott tanúsítvány. App Service-tanúsítványok nem téríthető, de felveheti a kapcsolatot [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hogy van-e egyéb beállításokat. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>App Service-tanúsítvány megújított, de továbbra is mutatja a régi tanúsítvány 

### <a name="symptom"></a>Jelenség

Az App Service-tanúsítvány megújított, de a webalkalmazást az App Service-tanúsítványt használó továbbra is a régi tanúsítványt használ. Is hogy figyelmeztető üzenet, hogy a HTTPS protokoll megadása kötelező.

### <a name="cause"></a>Ok 
A Web app service egy háttérben történő feldolgozás 8 óránként fut, és a tanúsítvány erőforrás szinkronizálásának, ha a módosul. Ezért elforgatása vagy a tanúsítvány frissítésekor, időnként az alkalmazás továbbra is beolvassa a régi tanúsítvány és nem az újonnan frissített tanúsítvány. Ennek oka az, a tanúsítvány erőforrás szinkronizálása feladat futtatása még nem. 
 
### <a name="solution"></a>Megoldás

Beállíthatja, hogy a tanúsítvány szinkronizálási:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Válassza ki a **App service-tanúsítványokkal**, majd válassza ki a tanúsítványt.
2. Kattintson a **kulcsismétlés és tényleges szinkronizálási**, és kattintson a **szinkronizálási**. Bizonyos idő befejezéséhez. 
3. A szinkronizálás befejezése után megjelenik a következő üzenet: "Frissítése sikeresen megtörtént az erőforrásokat a legújabb tanúsítvánnyal".

### <a name="domain-verification-is-not-working"></a>Nem működik a tartomány ellenőrzése 

### <a name="symptom"></a>Jelenség 
Az App Service tanúsítvány tartományok ellenőrzésének igényel, mielőtt használatra készen áll a tanúsítvány. Amikor rákattint **ellenőrizze**, a folyamat sikertelen lesz.

### <a name="solution"></a>Megoldás
Manuálisan ellenőrizze a tartományt a TXT-rekord hozzáadásával:
 
1.  Nyissa meg a tartománynév-szolgáltatás (DNS) szolgáltató, amelyen a tartomány nevét.
2.  Adja hozzá a TXT-rekord a tartományhoz, amely a tartomány token az Azure-portálon megjelenő értékét használja. 

Várjon néhány percet a DNS-propagálás futtatásához, és majd **frissítése** indul el, az ellenőrzés gombra. 

Alternatív módszert manuálisan ellenőrizheti a "HTML-weblap mód", amely lehetővé teszi a hitelesítésszolgáltatótól, a tulajdonjogát a tanúsítványt a tartomány használható.

1.  Hozzon létre {tartomány ellenőrző jogkivonat} .html nevű HTML-fájlba. 
2.  Tartalom fájl kell tartomány ellenőrző jogkivonat értékét.
3.  Ez a webalkalmazás-kiszolgáló, amelyen a tartomány gyökerében-fájl feltöltése
4.  Kattintson a **frissítése** tanúsítvány állapotának ellenőrzéséhez. Az ellenőrzés befejezéséhez néhány percig is eltarthat.

Például ha egy szabványos tanúsítványt az Azure.com webhelyre, a tartomány ellenőrzése Token "1234abcd" vásárol, majd egy webes kéréssel végzett http://azure.com/1234abcd.html 1234abcd kell visszaadnia. 

> [!IMPORTANT]
> A tanúsítvány sorrendet rendelkezik csak 15 nappal a tartomány ellenőrzési művelet befejezéséhez. 15 nap múlva megtagadta a a tanúsítványt a hitelesítésszolgáltatótól, és nem kell fizetnie a tanúsítványt. Ebben a helyzetben törölje ezt a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Nem lehet egy tartomány vásárlása

### <a name="symptom"></a>Jelenség
A webalkalmazás vagy szolgáltatás alkalmazástartományban tartomány nem vásárlása az Azure portálon.

### <a name="cause-and-solution"></a>OK és megoldás

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Bankkártyaadatok az Azure-előfizetés vagy hitelkártya érvénytelen.

    **Megoldás**: egy érvényes hitelkártya hozzáadásához az előfizetéshez, ha még nem rendelkezik ilyennel.

- Ha nem az előfizetés tulajdonosa, akkor előfordulhat, hogy nincs engedélye tartomány vásárlásához.

    **Megoldás**: [hozzáadása a tulajdonosi szerepkört](../billing/billing-add-change-azure-subscription-administrator.md) a fiók vagy az előfizetési rendszergazda, és kérjen engedélyt azokhoz a tartomány vásárlásához kapcsolatot.
- Elérte a korlátot, az előfizetés tartományokhoz megvásárlása. A jelenlegi maximum 20.

    **Megoldás**: A kérés növelje a korlátot, a kapcsolattartási [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az Azure-előfizetés típusa nem támogatja a beszerzési App Service-tartománynak.

    **Megoldás**: az Azure-előfizetéshez frissítsen más előfizetéstípusok, például az előfizetést használatalapú fizetésre.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Nem sikerült az állomásnév felvétele webalkalmazásokba 

### <a name="symptom"></a>Jelenség

Ha hozzáad egy állomásnevet, ellenőrizze a tartományt, és ellenőrizheti a folyamat sikertelen lesz.

### <a name="cause"></a>Ok 

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Nincs engedélye az állomásnév hozzáadni.

    **Megoldás**: előfizetés rendszergazda segítségével ellenőrizze, hogy rendelkezik-e jogosultsága az állomásnév ellenőrzése.
- Nem sikerült ellenőrizni a tartomány tulajdonosa.

    **Megoldás**: Győződjön meg arról, ha a CNAME vagy a bejegyzés helyességét. Az egyéni tartomány leképezése a webes alkalmazás, hozzon létre egy CNAME vagy egy A rekordot. Ha a legfelső szintű tartomány használni kívánt, és TXT-rekordok kell használnia:

    |Rekordtípus|Gazdagép|Mutasson a|
    |------|------|-----|
    |A|@|Webalkalmazás IP-cím|
    |TXT|@|< alkalmazásnév >. azurewebsites.net|
    |CNAME|www|< alkalmazásnév >. azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>Nem oldható fel DNS

### <a name="symptom"></a>Jelenség

Egy "a DNS-rekord nem található" hibaüzenet jelenhet meg.

### <a name="cause"></a>Ok
Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Az élettartam (TTL) időszak ideje nem járt. Ellenőrizze a tartomány az élettartam értéke határozza meg, és várjon, amíg az időszak lejár a DNS-beállításait.
- A DNS-konfiguráció nem megfelelő.

### <a name="solution"></a>Megoldás
- Várjon, amíg ez a probléma megoldódik 48 órán belül.
- Ha a DNS-konfiguráció a TTL-beállítást módosíthatja, módosítsa az értéket 5 perc – tekintse meg, hogy ez megoldja-e a problémát.
- Használjon [WhatsmyDNS.net](https://www.whatsmydns.net/) való győződjön meg arról, hogy a tartomány a webes alkalmazás IP-címre mutat. Ha nem létezik, az A rekord, a megfelelő IP-címre a webalkalmazás konfigurálása.

### <a name="restore-a-deleted-domain"></a>A törölt tartományi visszaállítása 

### <a name="symptom"></a>Jelenség
A tartomány már nem jelenik meg az Azure-portálon.

### <a name="cause"></a>Ok 
A tartomány véletlenül törölték az előfizetés tulajdonosa.

### <a name="solution"></a>Megoldás
A tartomány törlése kisebb, mint hét napja, ha a tartomány még nem kezdődött a törlési folyamat. Ebben az esetben vásárolhatja meg ugyanabban a tartományban, az Azure portál ugyanahhoz az előfizetéshez (Ügyeljen rá, hogy a Keresés mezőbe írja be a teljes tartománynév) meg újra. Nem kell fizetnie újra ehhez a tartományhoz. Ha a tartomány törlése több mint hét napja, lépjen kapcsolatba [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) visszaállítása a tartomány segítségét.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>Az egyéni tartomány adja vissza 404-es vagy a hely nem érhető el 

### <a name="symptom"></a>Jelenség

Ha a felhasználó a helyhez az egyéni tartománynév használatával, a következő hibaüzenet jelenhet meg:

**Hiba történt a 404-webes alkalmazás nem található.**


### <a name="cause-and-solution"></a>OK és megoldás

**OK: 1** 

Az egyéni tartomány konfigurált hiányzik, vagy A CNAME rekord. 

**Megoldás ok 1**

- Ha hozzáadott egy A rekordot, győződjön meg arról a TXT-rekord is megjelenik. További információkért lásd: [létrehozás-az-a-rekordot](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Nem kell a webalkalmazás gyökértartomány használni, ha egy olyan CNAME rekordot használata helyett A rekord ajánlott.
- Ne használjon egy CNAME és az A rekord ugyanabban a tartományban. Ez ütközést okozhat, és a tartomány megakadályozása megoldása. 

**OK 2** 

Előfordulhat, hogy továbbra is gyorsítótárazás a régi IP-cím a tartomány az a böngészőben. 

**Megoldás ok 2**

Törölje a böngészőben. Windows-eszközök esetén a parancs futtatható `ipconfig /flushdns`. Használjon [WhatsmyDNS.net](https://www.whatsmydns.net/) való győződjön meg arról, hogy a tartomány a webes alkalmazás IP-címre mutat. 

### <a name="unable-to-add-subdomain"></a>Nem adható hozzá altartomány 

### <a name="symptom"></a>Jelenség

A webes alkalmazás altartomány hozzárendelni egy új állomásnév nem lehet hozzáadni.

### <a name="solution"></a>Megoldás

- Ellenőrizze az előfizetés rendszergazdájához, és győződjön meg arról, hogy Ön jogosult az állomásnév hozzáadása a webalkalmazáshoz.
- Ha további altartományok van szüksége, azt javasoljuk, hogy módosítsa az Azure DNS-tartomány üzemeltető. Azure DNS használatával 500 állomásnevek adhat hozzá a webes alkalmazást. További információkért lásd: [sub tartomány hozzáadása](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















