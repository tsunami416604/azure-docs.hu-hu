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
ms.openlocfilehash: 59a9011edef49494288716ab16f30e28e440293b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195182"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Tartomány és az Azure web apps SSL tanúsítvánnyal kapcsolatos problémák elhárítása

Ez a cikk az Azure web Apps tartomány- vagy SSL-tanúsítvány konfigurálásakor előforduló gyakori problémák sorolja fel. Lehetséges okait és megoldásait ezeket a problémákat is ismerteti.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**.

## <a name="certificate-problems"></a>Tanúsítvánnyal kapcsolatos problémák

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>A webes alkalmazás nem tudja felvenni egy SSL-tanúsítvány kötése 

#### <a name="symptom"></a>Jelenség

Ha hozzáad egy SSL-kötést, a következő hibaüzenet jelenhet meg:

"Nem sikerült hozzáadni az SSL-kötést. Nem állítható be tanúsítvány meglévő VIP mert egy másik VIP már azt a tanúsítványt használja."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a ugyanazon IP-cím több IP-alapú SSL-kötések még több webes alkalmazások között. A webalkalmazás például egy IP-alapú SSL-Titkosítást az egy régi tanúsítvány van. A webes alkalmazás B rendelkezik egy IP-alapú SSL-Titkosítást az új tanúsítványt az azonos IP-címhez. Ha a webes alkalmazás SSL-kötést frissíti az új tanúsítvánnyal, azt sikertelen lesz. Ez a hiba, mert egy másik alkalmazás használja az ugyanazon IP-cím. 

#### <a name="solution"></a>Megoldás 

A probléma megoldásához használja a következő módszerek egyikét:

- Az IP-alapú SSL-kötést a webalkalmazásban a régi tanúsítványt használó törlése. 
- Hozzon létre egy új IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="you-cant-delete-a-certificate"></a>A tanúsítvány nem törölhető 

#### <a name="symptom"></a>Jelenség

Amikor megpróbálja törölni a tanúsítványt, a következő hibaüzenet jelenhet meg:

"Nem sikerült törölni a tanúsítványt, mert jelenleg használatban van az SSL-kötést. Az SSL-kötés el kell távolítani a tanúsítvány törlése előtt."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a tanúsítványt használja egy másik webes alkalmazást.

#### <a name="solution"></a>Megoldás

Az SSL-kötés az adott tanúsítvány eltávolítása a webalkalmazásokat. Próbálja meg törölni a tanúsítványt. Ha még mindig nem tudja törölni a tanúsítványt, törölje az internetes gyorsítótárban, és nyissa meg újra az Azure portál egy új böngészőablakban. Próbálja meg törölni a tanúsítványt.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nem vásárolhat egy tanúsítványt az alkalmazás szolgáltatáshoz 

#### <a name="symptom"></a>Jelenség
Nem vásárolhat egy [Azure App Service tanúsítvány](./web-sites-purchase-ssl-web-site.md) Azure-portálról.

#### <a name="cause-and-solution"></a>OK és megoldás
Ez a probléma a következő okok miatt:

- Az App Service-csomag ingyenes vagy közös. Ezek a tarifacsomagok nem támogatja az SSL. 

    **Megoldás**: Standard webalkalmazás az App Service-csomag frissítése.

- Az előfizetés nem rendelkezik érvényes hitelkártyát.

    **Megoldás**: egy érvényes hitelkártya hozzáadásához az előfizetéshez. 

- Az előfizetés ajánlat nem támogatja a beszerzési egy App Service-tanúsítvány, például a Microsoft Student.  

    **Megoldás**: az előfizetés frissítése. 

- Az előfizetés elérte a vásárlás egy előfizetésben megengedett számát.

    **Megoldás**: App Service-tanúsítványok maximális száma 10 tanúsítvány beszerzése, hogy a használatalapú fizetés és EA előfizetéstípusok rendelkezik. A más előfizetés típusú határérték 3. A korlát növeléséhez forduljon [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az App Service tanúsítvány csalás lett megjelölve. A következő hibaüzenet: "a tanúsítvány rendelkezik már meg van jelölve a lehetséges csalásról. A kérelem jelenleg felülvizsgálat alatt. Ha a tanúsítvány nem lesz használható 24 órán belül, forduljon Azure támogatási szolgálatához."

    **Megoldás**: Ha a tanúsítvány csalás van megjelölve, és nincs feloldva 24 óra múlva, kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Ugrás a **App Service-tanúsítványokkal**, és válassza ki azt a tanúsítványt.
    3. Válassza ki **Tanúsítványkonfiguráció** > **2. lépés: Ellenőrizze** > **tartományok ellenőrzésének**. Ebben a lépésben egy e-mail értesítést küld az Azure tanúsítványszolgáltató a probléma megoldásához.

## <a name="domain-problems"></a>Tartomány-problémák

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>A megfelelő tartományhoz tartozó SSL-tanúsítvány megvásárlása

#### <a name="symptom"></a>Jelenség

Egy App Service-tanúsítvány a megfelelő tartomány vásárolta. Nem frissíthető a megfelelő tartományhoz használni kívánt tanúsítványt.

#### <a name="solution"></a>Megoldás

Törölje a tanúsítványt, és megvásárolni az új tanúsítványt.

Ha az aktuális tanúsítvány, amely a megfelelő tartomány használja a "feltüntetett tulajdonos" állapotban van, a fogjuk is számlázni tanúsítvány. App Service-tanúsítványok nem téríthető, de felveheti a kapcsolatot [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hogy van-e egyéb beállításokat. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Egy App Service-tanúsítvány megújított, de a webes alkalmazás látható a régi tanúsítvány 

#### <a name="symptom"></a>Jelenség

Az App Service-tanúsítvány megújított, de a webalkalmazást az App Service-tanúsítványt használó továbbra is a régi tanúsítványt használ. Is hogy figyelmeztető üzenet, hogy a HTTPS protokoll megadása kötelező.

#### <a name="cause"></a>Ok 
Az Azure App Service Web Apps szolgáltatása fut a háttérben történő feldolgozás 8 óránként, és szinkronizálja a tanúsítvány erőforrás, ha a módosul. Forgassa el, vagy a tanúsítvány frissítésekor, egyes esetekben az alkalmazás továbbra is beolvassa a régi tanúsítvány és nem az újonnan frissített tanúsítvány. A hiba oka, hogy a tanúsítvány erőforrás szinkronizálása feladat még nem futott. 
 
#### <a name="solution"></a>Megoldás

Beállíthatja, hogy a tanúsítvány szinkronizálási:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **App Service-tanúsítványokkal**, majd válassza ki a tanúsítványt.
2. Válassza ki **kulcsismétlés és tényleges szinkronizálási**, majd válassza ki **szinkronizálási**. A szinkronizálás befejezése némi időt vesz igénybe. 
3. A szinkronizálás befejezése után megjelenik a következő üzenet: "Sikeresen frissített összes erőforrást a legújabb tanúsítvány."

### <a name="domain-verification-is-not-working"></a>Nem működik a tartomány ellenőrzése 

#### <a name="symptom"></a>Jelenség 
Az App Service tanúsítvány tartományok ellenőrzésének igényel, mielőtt használatra készen áll a tanúsítvány. Ha bejelöli **ellenőrizze**, a folyamat sikertelen lesz.

#### <a name="solution"></a>Megoldás
Manuálisan ellenőrizze a tartományt a TXT-rekord hozzáadásával:
 
1.  Nyissa meg a tartománynév-szolgáltatás (DNS) szolgáltató, amelyen a tartomány nevét.
2.  Adja hozzá a TXT-rekord a tartományhoz, amely a tartomány token az Azure-portálon megjelenő értékét használja. 

Várjon néhány percet a DNS-propagálás futtatásához, és válassza ki a **frissítése** indul el, az ellenőrzés gombra. 

Alternatív megoldásként a HTML-weblap módszer segítségével manuálisan ellenőrizze a tartományt. Ez a módszer lehetővé teszi, hogy a tulajdonjogát, a tartományban, amelyhez a tanúsítványt a hitelesítésszolgáltatótól.

1.  Hozzon létre {tartomány érvényesítési jogkivonat} .html nevű HTML-fájlba. Ez a fájl tartalma tartomány érvényesítési jogkivonat értékének kell lennie.
3.  A feltöltés a webalkalmazás-kiszolgáló, amelyen a tartomány gyökerében.
4.  Válassza ki **frissítése** tanúsítvány állapotának ellenőrzéséhez. Az ellenőrzés befejezéséhez néhány percig is eltarthat.

Például, ha a tartomány ellenőrző jogkivonat 1234abcd az Azure.com webhelyre szabványos tanúsítványt vásárol, webes kérelem végzett http://azure.com/1234abcd.html 1234abcd kell visszaadnia. 

> [!IMPORTANT]
> A tanúsítvány sorrendet rendelkezik csak 15 nappal a tartomány ellenőrzési művelet befejezéséhez. 15 nap elteltével a hitelesítésszolgáltató a tanúsítvány megtagadja, és nem kell fizetnie a tanúsítványt. Ebben a helyzetben törölje ezt a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nem vásárolhat egy tartományhoz

#### <a name="symptom"></a>Jelenség
A Web Apps vagy az App Service-tartományból az Azure portálon tartomány nem vásárlása.

#### <a name="cause-and-solution"></a>OK és megoldás

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Az Azure-előfizetés bankkártyaadatok van, vagy a hitelkártya érvénytelen.

    **Megoldás**: egy érvényes hitelkártya hozzáadásához az előfizetéshez.

- Ön nem az előfizetés tulajdonosa, ezért nem rendelkezik engedéllyel a tartomány vásárlásához.

    **Megoldás**: [hozzáadása a tulajdonosi szerepkört](../billing/billing-add-change-azure-subscription-administrator.md) fiókjába. Vagy az előfizetés-rendszergazdától engedélyt tartomány vásárlásához.
- Elérte a korlátot, az előfizetés tartományokhoz megvásárlása. A jelenlegi maximum 20.

    **Megoldás**: a korlát növelését kérelmezéséhez forduljon [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az Azure-előfizetés típusa nem támogatja a beszerzési az App Service-tartományhoz.

    **Megoldás**: az Azure-előfizetéshez váltson egy másik előfizetés-típus, például az előfizetést használatalapú fizetésre.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Nem adható hozzá egy állomásnevet a webalkalmazáshoz 

#### <a name="symptom"></a>Jelenség

Ha hozzáad egy állomásnevet, ellenőrizze a tartományt, és ellenőrizheti a folyamat sikertelen lesz.

#### <a name="cause"></a>Ok 

Ez a probléma akkor fordul elő, a következő okok valamelyike:

- Nincs engedélye hozzáadása egy állomásnevet.

    **Megoldás**: kérje meg az előfizetés rendszergazdájától állomásnév-hozzáadási jogosultsággal.
- Nem sikerült ellenőrizni a tartomány tulajdonosa.

    **Megoldás**: Győződjön meg arról, hogy a CNAME vagy egy olyan rekordot megfelelően van konfigurálva. Egyéni tartomány leképezése a webes alkalmazás, hozzon létre vagy egy olyan CNAME rekordot, vagy egy A rekordot. Ha azt szeretné, a legfelső szintű tartományt használ, és TXT-rekordok kell használnia:

    |Rekordtípus|Gazdagép|Mutasson a|
    |------|------|-----|
    |A|@|A webalkalmazáshoz tartozó IP-cím|
    |TXT|@|< alkalmazásnév >. azurewebsites.net|
    |CNAME|www|< alkalmazásnév >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS nem oldható fel

#### <a name="symptom"></a>Jelenség

A következő hibaüzenetet kapott:

"A DNS-rekord nem található."

#### <a name="cause"></a>Ok
Ez a probléma akkor fordul elő, a következő okok valamelyike:

- A rendszer mennyi ideig live (TTL) időszak még nem járt le. Ellenőrizze a tartomány az élettartam értéke határozza meg, és várjon, amíg az időszak lejár a DNS-beállításait.
- A DNS-konfiguráció nem megfelelő.

#### <a name="solution"></a>Megoldás
- Várjon, amíg ez a probléma megoldódik 48 órán belül.
- Ha a DNS-konfiguráció a TTL-beállítást módosíthatja, módosítsa az értéket 5 perc – tekintse meg, hogy ez megoldja-e a problémát.
- Használjon [WhatsmyDNS.net](https://www.whatsmydns.net/) való győződjön meg arról, hogy a tartomány a webes alkalmazás IP-címre mutat. Ha nem, az A rekord, a megfelelő IP-címre a webalkalmazás konfigurálása.

### <a name="you-need-to-restore-a-deleted-domain"></a>Vissza kell állítania egy törölt tartományhoz 

#### <a name="symptom"></a>Jelenség
A tartomány már nem jelenik meg az Azure-portálon.

#### <a name="cause"></a>Ok 
Az előfizetés tulajdonosa elképzelhető, hogy véletlenül törölte a tartományhoz.

#### <a name="solution"></a>Megoldás
Ha a tartomány törlése kevesebb mint hét napja, a tartomány még nem kezdődött a törlési folyamat. Ebben az esetben vásárolhatja meg ugyanabban a tartományban, az Azure portál ugyanahhoz az előfizetéshez meg újra. (Ügyeljen arra, hogy a keresési mezőbe írja be a teljes tartománynevet.) Ön nem számlázni újra ezt a tartományt. Ha a tartomány törlése több mint hét napja, forduljon a [az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) visszaállítása a tartomány segítségét.

### <a name="a-custom-domain-returns-a-404-error"></a>Az egyéni tartománynév a 404-es hibaüzenetet adja vissza. 

#### <a name="symptom"></a>Jelenség

Ha a felhasználó a helyhez az egyéni tartománynév használatával, a következő hibaüzenet jelenhet meg:

"Hiba 404-webalkalmazás nem található."


#### <a name="cause-and-solution"></a>OK és megoldás

**OK: 1** 

Az egyéni tartomány konfigurált hiányzik, vagy A CNAME rekord. 

**Megoldás ok 1**

- Ha hozzáadott egy A rekordot, győződjön meg arról, hogy egy TXT rekordot is megjelenik. További információkért lásd: [hozza létre az A rekordot](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- A legfelső szintű tartomány használata a webalkalmazás nem szükséges, ha azt javasoljuk, hogy használja-e egy olyan CNAME rekordot helyett egy A rekordot.
- Ne használjon egy olyan CNAME rekordot, és egy A rekordot is ugyanabban a tartományban. Ez ütközést okozhat, és a tartomány megakadályozása feloldva. 

**OK 2** 

Előfordulhat, hogy továbbra is gyorsítótárazás a régi IP-cím a tartomány az a böngészőben. 

**Megoldás ok 2**

Törölje a böngészőben. Windows-eszközök esetén a parancs futtatható `ipconfig /flushdns`. Használjon [WhatsmyDNS.net](https://www.whatsmydns.net/) való győződjön meg arról, hogy a tartomány a webes alkalmazás IP-címre mutat. 

### <a name="you-cant-add-a-subdomain"></a>Nem adható hozzá egy altartomány 

#### <a name="symptom"></a>Jelenség

A webes alkalmazás hozzárendelése egy altartomány nem adható hozzá egy új nevet.

#### <a name="solution"></a>Megoldás

- Ellenőrizze az előfizetés rendszergazdájához, és győződjön meg arról, hogy Ön jogosult állomásnév hozzáadása a webalkalmazáshoz.
- Ha további altartományok van szüksége, azt javasoljuk, hogy módosítsa az Azure DNS-tartomány üzemeltető. Azure DNS használatával 500 állomásnevek adhat hozzá a webes alkalmazást. További információkért lásd: [hozzáadása egy altartomány](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















