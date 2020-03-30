---
title: Tartomány- és SSL-tanúsítványok – problémamegoldás
description: Megoldásokat találhat azokra a gyakori problémákra, amelyekkel egy tartomány vagy SSL-tanúsítvány konfigurálásakor találkozhat az Azure App Service-ben.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: e299821b54692327cbb7d497af0295e3b93658cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966978"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Tartomány- és SSL-tanúsítványokkal kapcsolatos problémák elhárítása az Azure App Service szolgáltatásban

Ez a cikk azokat a gyakori problémákat sorolja fel, amelyekkel a webalkalmazások hoz az Azure App Service-ben konfigurálásakor találkozhat. Ismerteti továbbá a lehetséges okokat és megoldásokat ezekre a problémákra.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure [szakértőivel az MSDN és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Tanúsítványproblémák

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Nem adhat hozzá SSL-tanúsítványkötést egy alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

SSL-kötés hozzáadásakor a következő hibaüzenet jelenik meg:

"Nem sikerült hozzáadni az SSL-kötést. A meglévő VIP-tanúsítvány nem állítható be, mert egy másik VIP már használja ezt a tanúsítványt."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha több IP-alapú SSL-kötéssel rendelkezik ugyanahhoz az IP-címhez több alkalmazásban. Az A alkalmazás például egy régi tanúsítvánnyal rendelkező IP-alapú SSL-lel rendelkezik. A B alkalmazás IP-alapú SSL-lel rendelkezik, és ugyanahhoz az IP-címhez új tanúsítványt kell tanúsítania. Amikor frissíti az alkalmazás SSL-kötését az új tanúsítvánnyal, ez a hiba sikertelen lesz, mert ugyanazt az IP-címet használja egy másik alkalmazás. 

#### <a name="solution"></a>Megoldás 

A probléma megoldásához használja az alábbi módszerek egyikét:

- Törölje az IP-alapú SSL-kötést a régi tanúsítványt használó alkalmazáson. 
- Hozzon létre egy új, IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="you-cant-delete-a-certificate"></a>Tanúsítvány nem törölhető 

#### <a name="symptom"></a>Hibajelenség

Amikor megpróbál törölni egy tanúsítványt, a következő hibaüzenet jelenik meg:

"Nem lehet törölni a tanúsítványt, mert jelenleg SSL-kötésben használatos. A tanúsítvány törlése előtt el kell távolítani az SSL-kötést."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha egy másik alkalmazás használja a tanúsítványt.

#### <a name="solution"></a>Megoldás

Távolítsa el az adott tanúsítvány SSL-kötését az alkalmazásokból. Ezután próbálja meg törölni a tanúsítványt. Ha továbbra sem tudja törölni a tanúsítványt, törölje az internetböngésző gyorsítótárát, és nyissa meg újra az Azure Portalt egy új böngészőablakban. Ezután próbálja meg törölni a tanúsítványt.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nem vásárolhat App Service-tanúsítványt 

#### <a name="symptom"></a>Hibajelenség
Nem vásárolhat Azure [App Service-tanúsítványt](./configure-ssl-certificate.md#import-an-app-service-certificate) az Azure Portalon.

#### <a name="cause-and-solution"></a>Ok és megoldás
A probléma a következő okok valamelyike miatt fordulhat elő:

- Az App Service-csomag ingyenes vagy megosztott. Ezek a tarifacsomagok nem támogatják az SSL-t. 

    **Megoldás:** Frissítse az App Service-csomagot az alkalmazás standardra.

- Az előfizetés nem rendelkezik érvényes hitelkártyával.

    **Megoldás:** Adjon hozzá érvényes hitelkártyát az előfizetéséhez. 

- Az előfizetési ajánlat nem támogatja az App Service-tanúsítvány, például a Microsoft Student megvásárlását.  

    **Megoldás:** Frissítse előfizetését. 

- Az előfizetés elérte az előfizetésben engedélyezett vásárlások korlátját.

    **Megoldás:** Az App Service-tanúsítványok legfeljebb 10 tanúsítványvásárlást vásárolhatnak a szolgáltatáskezelő és az EA előfizetési típusokhoz. Más előfizetési típusok esetén a korlát 3. A korlát növeléséhez forduljon az [Azure ügyfélszolgálatához.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Az App Service-tanúsítvány csalásként volt megjelölve. A következő hibaüzenet jelenik meg: "A tanúsítványt csalás miatt megjelölték. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem válik használhatóvá, forduljon az Azure-támogatáshoz."

    **Megoldás:** Ha a tanúsítvány csalásként van megjelölve, és 24 óra elteltével nem oldódik fel, kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
    2. Nyissa meg az **App Service-tanúsítványok**lehetőséget, és jelölje ki a tanúsítványt.
    3. Válassza a Tanúsítvány **konfigurációs** > **lépés 2.** > **Domain Verification** Ez a lépés e-mailértesítést küld az Azure-tanúsítványszolgáltatónak a probléma megoldásához.

## <a name="custom-domain-problems"></a>Egyéni tartományproblémák

### <a name="a-custom-domain-returns-a-404-error"></a>Egy egyéni tartomány 404-es hibát ad vissza 

#### <a name="symptom"></a>Hibajelenség

Amikor az egyéni tartománynév vel lépfel a webhelyre, a következő hibaüzenet jelenik meg:

"Hiba 404-Web app nem található."

#### <a name="cause-and-solution"></a>Ok és megoldás

**1. ok** 

A konfigurált egyéni tartományból hiányzik egy CNAME vagy A rekord. 

**Megoldás az 1. ok esetén**

- Ha A rekordot adott hozzá, győződjön meg arról, hogy txt rekord is hozzá lett adva. További információt az A rekord létrehozása című [témakörben talál.](./app-service-web-tutorial-custom-domain.md#create-the-a-record)
- Ha nem kell használnia a gyökértartományt az alkalmazáshoz, javasoljuk, hogy A rekord helyett CNAME rekordot használjon.
- Ne használjon cname rekordot és A rekordot ugyanarra a tartományra. Ez a probléma ütközést okozhat, és megakadályozhatja a tartomány megoldását. 

**2. ok** 

Lehet, hogy az internetböngésző továbbra is gyorsítótárazása a tartomány régi IP-címét. 

**Megoldás a 2.**

Törölje a böngészőt. Windows-eszközök esetén futtathatja `ipconfig /flushdns`a parancsot. A [WhatsmyDNS.net](https://www.whatsmydns.net/) segítségével ellenőrizze, hogy a tartomány az alkalmazás IP-címére mutat-e. 

### <a name="you-cant-add-a-subdomain"></a>Altartomány nem vehet fel 

#### <a name="symptom"></a>Hibajelenség

Altartomány hozzárendeléséhez nem adhat hozzá új állomásnevet egy alkalmazáshoz.

#### <a name="solution"></a>Megoldás

- Ellenőrizze az előfizetés rendszergazdájától, hogy rendelkezik-e állomásnév hozzáadására vonatkozó engedélyekkel az alkalmazáshoz.
- Ha további altartományokra van szüksége, azt javasoljuk, hogy módosítsa a tartományüzemeltetésit Az Azure Domain Name Service (DNS) szolgáltatásra. Az Azure DNS használatával 500 állomásnevet adhat hozzá az alkalmazáshoz. További információt az [Altartomány hozzáadása](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)című témakörben talál.

### <a name="dns-cant-be-resolved"></a>A DNS nem oldható fel

#### <a name="symptom"></a>Hibajelenség

A következő hibaüzenet jelenik meg:

"A DNS-rekord nem található."

#### <a name="cause"></a>Ok
A probléma a következő okok valamelyike miatt fordul elő:

- Az életidő (TTL) időszaka még nem járt le. Ellenőrizze a tartomány DNS-konfigurációját a TTL érték meghatározásához, majd várja meg, amíg az időszak lejár.
- A DNS-konfiguráció helytelen.

#### <a name="solution"></a>Megoldás
- Várjon 48 órát, amíg a probléma magától megoldódik.
- Ha a DNS-konfigurációban meg tudja változtatni a TTL-beállítást, módosítsa az értéket 5 percre, és nézze meg, hogy ez megoldja-e a problémát.
- A [WhatsmyDNS.net](https://www.whatsmydns.net/) segítségével ellenőrizze, hogy a tartomány az alkalmazás IP-címére mutat-e. Ha nem, konfigurálja az A rekordot az alkalmazás megfelelő IP-címére.

### <a name="you-need-to-restore-a-deleted-domain"></a>Vissza kell állítania egy törölt tartományt 

#### <a name="symptom"></a>Hibajelenség
A tartomány már nem látható az Azure Portalon.

#### <a name="cause"></a>Ok 
Lehet, hogy az előfizetés tulajdonosa véletlenül törölte a tartományt.

#### <a name="solution"></a>Megoldás
Ha a tartományt kevesebb mint hét nappal ezelőtt törölték, a tartomány még nem kezdte el a törlési folyamatot. Ebben az esetben megvásárolhatja ugyanazt a tartományt újra az Azure Portalon ugyanazzal az előfizetéssel. (Ne felejtse el beírni a pontos tartománynevet a keresőmezőbe.) Ehhez a tartományért nem kell újra fizetnie. Ha a tartományt több mint hét nappal ezelőtt törölték, kérjen segítséget az Azure támogatási szolgálatátaaakért a tartomány visszaállításához. [Azure support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="domain-problems"></a>Tartományi problémák

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Nem a megfelelő tartományhoz vásárolt SSL-tanúsítványt

#### <a name="symptom"></a>Hibajelenség

Nem a megfelelő tartományhoz vásárolt App Service-tanúsítványt. A tanúsítvány nem frissíthető a megfelelő tartomány használatához.

#### <a name="solution"></a>Megoldás

Törölje a tanúsítványt, majd vásároljon új tanúsítványt.

Ha a nem megfelelő tartományt használó jelenlegi tanúsítvány "Kiadva" állapotban van, akkor a tanúsítványért is fizetnie kell. Az App Service-tanúsítványok nem téríthetők vissza, de az [Azure-támogatáshoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) fordulhat, hogy megállapítsa, vannak-e más lehetőségek is. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Egy App Service-tanúsítvány megújításra került, de az alkalmazás a régi tanúsítványt jeleníti meg 

#### <a name="symptom"></a>Hibajelenség

Az App Service-tanúsítvány meglett újulva, de az App Service-tanúsítványt használó alkalmazás továbbra is a régi tanúsítványt használja. Emellett figyelmeztetést is kapott, hogy a HTTPS protokoll szükséges.

#### <a name="cause"></a>Ok 
Az App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt. A tanúsítvány elforgatása vagy frissítésekor az alkalmazás néha még mindig a régi tanúsítványt, és nem az újonnan frissített tanúsítványt kéri be. Ennek az az oka, hogy a tanúsítvány-erőforrás szinkronizálásának feladata még nem futott. Kattintson a Szinkronizálás gombra. A szinkronizálási művelet automatikusan frissíti a tanúsítvány állomásnév-kötéseit az App Service-ben anélkül, hogy az alkalmazásoknak állásidőt okozna.
 
#### <a name="solution"></a>Megoldás

Kényszerítheti a tanúsítvány szinkronizálását:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **az App Service-tanúsítványok**lehetőséget, majd válassza ki a tanúsítványt.
2. Válassza **a Rekey and Sync (Újrakulcs és szinkronizálás)** lehetőséget, majd a **Szinkronizálás**lehetőséget. A szinkronizálás befejezése némi időt vesz igénybe. 
3. Amikor a szinkronizálás befejeződött, a következő értesítés jelenik meg: "Sikeresen frissítette az összes erőforrást a legújabb tanúsítvánnyal."

### <a name="domain-verification-is-not-working"></a>A tartomány ellenőrzése nem működik 

#### <a name="symptom"></a>Hibajelenség 
Az App Service-tanúsítvány tartományi ellenőrzést igényel, mielőtt a tanúsítvány készen áll a használatra. Ha az Ellenőrzés lehetőséget **választja,** a folyamat sikertelen lesz.

#### <a name="solution"></a>Megoldás
Manuálisan ellenőrizze a tartományt TXT rekord hozzáadásával:
 
1.  Nyissa meg a tartománynevet tartalmazó DNS-szolgáltatót.
2.  Adjon hozzá egy TXT-rekordot a tartományhoz, amely az Azure Portalon megjelenő tartományi jogkivonat értékét használja. 

Várjon néhány percet, amíg a DNS-propagálás futni fog, majd a **Frissítés** gombra kattintva indítsa el az ellenőrzést. 

Másik lehetőségként a HTML-weblap módszerrel manuálisan ellenőrizheti a tartományt. Ez a módszer lehetővé teszi a hitelesítésszolgáltató számára, hogy ellenőrizze annak a tartománynak a tartomány tulajdonjogát, amelyhez a tanúsítványt kiállították.

1.  {domain ellenőrző token}.html nevű HTML-fájl létrehozása. A fájl tartalmának a tartomány-ellenőrző jogkivonat értékének kell lennie.
3.  Töltse fel ezt a fájlt a tartományt tároló webkiszolgáló gyökerébe.
4.  A tanúsítvány állapotának ellenőrzéséhez válassza a **Frissítés** lehetőséget. Az ellenőrzés befejezéséhez néhány percbe is beletelhet.

Ha például egy 1234abcd tartomány-ellenőrzési jogkivonattal rendelkező azure.com szabványos tanúsítványt https://azure.com/1234abcd.html vásárol, a webes kérelemnek 1234abcd-et kell visszaadnia. 

> [!IMPORTANT]
> A tanúsítványrendelésnek mindössze 15 napja van a tartományellenőrzési művelet befejezésére. 15 nap elteltével a hitelesítésszolgáltató megtagadja a tanúsítványt, és nem számítunk fel díjat a tanúsítványért. Ebben az esetben törölje ezt a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nem vásárolhat tartományt

#### <a name="symptom"></a>Hibajelenség
Az Azure Portalon nem vásárolhat App Service-tartományt.

#### <a name="cause-and-solution"></a>Ok és megoldás

A probléma a következő okok valamelyike miatt fordul elő:

- Nincs hozzáadva hitelkártya az Azure-előfizetéshez, vagy a hitelkártya érvénytelen.

    **Megoldás:** Adjon hozzá érvényes hitelkártyát az előfizetéséhez.

- Nem Ön az előfizetés tulajdonosa, így nem rendelkezik engedéllyel a tartományvásárláshoz.

    **Megoldás:** [Rendelje hozzá a tulajdonosi szerepkört](../role-based-access-control/role-assignments-portal.md) a fiókjához. Vagy forduljon az előfizetés rendszergazdájához, hogy engedélyt kérjen egy tartomány megvásárlására.
- Az előfizetésében elérte a tartományvásárlások maximális számát. Jelenleg legfeljebb 20 tartományt vásárolhat.

    **Megoldás:** Ha a korlátra való emelést szeretné kérni, forduljon az [Azure ügyfélszolgálatához.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Az Azure-előfizetésének típusa nem támogatja App Service-tartományok vásárlását.

    **Megoldás:** Frissítse Azure-előfizetését egy másik előfizetéstípusra, például egy szolgáltatásalapú előfizetésre.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nem adhat állomásnevet egy alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

Állomásnév hozzáadásakor a folyamat nem ellenőrzi és nem ellenőrzi a tartományt.

#### <a name="cause"></a>Ok 

A probléma a következő okok valamelyike miatt fordul elő:

- Nincs engedélye állomásnév hozzáadására.

    **Megoldás:** Kérje meg az előfizetés rendszergazdáját, hogy adjon engedélyt egy állomásnév hozzáadására.
- A tartomány tulajdonjoga nem ellenőrizhető.

    **Megoldás:** Ellenőrizze, hogy a CNAME vagy a A rekord megfelelően van-e konfigurálva. Ha egyéni tartományt szeretne egy alkalmazáshoz hozzárendeli, hozzon létre Egy CNAME vagy Egy rekordot. Ha gyökértartományt szeretne használni, A és TXT rekordokat kell használnia:

    |Rekordtípus|Gazdagép|Mutasson a|
    |------|------|-----|
    |A|@|Alkalmazás IP-címe|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>GYIK

**Be kell állítanom az egyéni tartományomat a webhelyemhez, miután megvásároltam?**

Ha megvásárol egy tartományt az Azure Portalon, az App Service-alkalmazás automatikusan konfigurálva van az adott egyéni tartomány használatára. Nem kell további lépéseket tennie. További információért tekintse meg az [Azure App Service önsegítő szolgálatát: Egyéni tartománynév hozzáadása](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) a Channel9-en.

**Használhatom az Azure Portalon vásárolt tartományt, hogy inkább egy Azure-beli virtuális gépre mutassak?**

Igen, a tartományt egy virtuális gépre irányíthatja. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../dns/dns-custom-domain.md) ismertető cikkben talál.

**A tartományomat a GoDaddy vagy az Azure DNS üzemelteti?**

Az App Service-tartományok a GoDaddy segítségével regisztrálják a tartományokat, az Azure DNS pedig a tartományokat. 

**Engedélyezve van az automatikus megújítás, de továbbra is kapott megújítási értesítést a tartományomról e-mailben. Mit kell tennem?**

Ha engedélyezve van az automatikus megújítás, nem kell semmilyen műveletet végrehajtania. Az értesítési e-mailt adunk, hogy tájékoztassuk, hogy a domain közel van a lejárathoz, és manuálisan megújítható, ha az automatikus megújítás nincs engedélyezve.

**Kell fizetnem a tartományomat tároló Azure DNS-ért?**

A tartományvásárlás kezdeti költsége csak a tartomány regisztrációjára vonatkozik. A regisztrációs költségmellett az Azure DNS-ért a használat alapján is felszámítjuk. További információ: [Azure DNS-díjszabás](https://azure.microsoft.com/pricing/details/dns/) további részletekért.

**A tartományomat korábban vásároltam az Azure Portalon, és át szeretnék helyezni a GoDaddy-tárhelyről az Azure DNS-üzemeltetésre. Hogy tehetném ezt?**

Nem kötelező áttelepíteni az Azure DNS-üzemeltetési. Ha szeretne áttérni az Azure DNS-re, az Azure Portalon található tartománykezelési élmény az Azure DNS-re való áttéréshez szükséges lépésekről nyújt tájékoztatást. Ha a tartományt az App Service-en keresztül vásárolta, a GoDaddy-tárhelyről az Azure DNS-re való áttelepítés viszonylag zökkenőmentes eljárás.

**Szeretném megvásárolni a tartományomat az App Service-tartományból, de az Azure DNS helyett a GoDaddy-n is üzemeltethetem a tartományomat?**

2017. július 24-től a portálon vásárolt App Service-tartományok az Azure DNS-en találhatók. Ha egy másik tárhelyszolgáltatót szeretne használni, akkor a webhelyükön kell megvásárolnia a domain hosting megoldást.

**Fizetnem kell a tartományom adatvédelmi védelméért?**

Ha az Azure Portalon keresztül vásárol egy tartományt, további költségek nélkül hozzáadhat adatvédelmet. Ez az egyik előnye a tartomány megvásárlásának az Azure App Service-en keresztül.

**Ha úgy döntök, hogy már nem szeretném a tartományomat, visszakaphatom a pénzemet?**

Domain vásárlásakor nem kell öt napig fizetnie, amely idő alatt eldöntheti, hogy nem kívánja-e a tartományt. Ha úgy dönt, hogy nem szeretné, hogy a tartomány az adott öt napos időszakon belül, akkor nem kell fizetnie. (.uk domainek kivételt képeznek ez alól. Ha .uk domaint vásárol, azonnal felszámítjuk, és nem térítjük vissza.)

**Használhatom a tartományt egy másik Azure App Service-alkalmazásban az előfizetésemben?**

Igen. Amikor az Azure Portalon az egyéni tartományok és az SSL-panel eléréséhez, láthatja a megvásárolt tartományokat. Beállíthatja, hogy az alkalmazás ezen tartományok bármelyikét használja.

**Átvihetek egy tartományt az egyik előfizetésből egy másik előfizetésbe?**

A tartomány áthelyezheti a tartományt egy másik előfizetés/erőforráscsoport a [Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell-parancsmag használatával.

**Hogyan kezelhetem az egyéni tartományomat, ha jelenleg nem rendelkezem Azure App Service-alkalmazással?**

A tartomány akkor is kezelheti, ha nem rendelkezik App Service Web App alkalmazással. A tartomány azure-szolgáltatásokhoz, például virtuális géphez, tárolóhoz stb. Ha az App Service Web Apps tartományát kívánja használni, akkor olyan webalkalmazást kell megadnia, amely nem szerepel az ingyenes app service-csomagban ahhoz, hogy a tartományt a webalkalmazáshoz kösse.

**Áthelyezhetek egy egyéni tartományt rendelkező webalkalmazást egy másik előfizetésbe vagy az App Service-környezet 1-es verzióból v2-be?**

Igen, áthelyezheti a webalkalmazást az előfizetések között. Kövesse az [Erőforrások áthelyezése az Azure-ban](../azure-resource-manager/management/move-resource-group-and-subscription.md)című útmutatót. A webalkalmazás áthelyezésekének során néhány korlátozás van. További információt az [App Service-erőforrások áthelyezésének korlátozása](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)című témakörben talál.

A webalkalmazás áthelyezése után az egyéni tartományokon belüli tartományok állomásnév-kötéseinek változatlannak kell maradniuk. Az állomásnév-kötések konfigurálásához nincs szükség további lépésekre.
