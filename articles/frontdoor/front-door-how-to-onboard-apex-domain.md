---
title: Előkészítheti egy legfelső szintű vagy rekordnevet tartomány egy már létező bejárati ajtajának, az Azure portal használatával
description: Ismerje meg, hogyan vezethető egy legfelső szintű vagy rekordnevet tartomány egy már létező bejárati ajtajának, az Azure portal használatával.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605793"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Előkészítheti a bejárati ajtó a legfelső szintű vagy rekordnevet tartományhoz
Azure bejárati ajtajának CNAME-rekordokat az egyéni tartományok bevezetése a tartomány tulajdonjogának ellenőrzéséhez használ. Ezenkívül bejárati ajtajának nem teszi közzé a bejárati ajtajának profilhoz társított előtérbeli IP-cím, és így, nem képezhető le a apex-tartomány IP-címet, ha az a célja a munkába, az Azure bejárati ajtajának.

A DNS protokoll megakadályozza, hogy a CNAME-rekordokat a zóna legfelső pontján hozzárendelését. Például, ha a tartomány `contoso.com`; hozhat létre CNAME-rekordokat a `somelabel.contoso.com`; tartomány CNAME-REKORDJA nem hozható létre, de `contoso.com` magát. Ez a korlátozás az elosztott terhelésű alkalmazások mögötti Azure bejárati ajtajának rendelkező alkalmazástulajdonosok problémát jelent. Egy CNAME rekord létrehozása bejárati ajtajának profillal van szüksége, mivel már nem lehet a zóna felső pontja a bejárati ajtajának profilnál pontot.

Ez a probléma van megoldani az Azure DNS-alias rekordok használatával. Ellentétben a CNAME rekordot a alias rekordok úgy jöjjenek létre a zóna legfelső pontján és alkalmazástulajdonosok szolgál, a zóna felső pontja rekord átirányítása nyilvános végpontokkal rendelkező bejárati ajtajának profil. Alkalmazástulajdonos ugyanazt bejárati ajtó a profilt a DNS-zóna más tartományban használt mutasson. Ha például `contoso.com` és `www.contoso.com` , ha ugyanazt a bejárati ajtajának profilt. 

A rekordnevet vagy a legfelső szintű tartomány leképezése a bejárati ajtajának profil alapvetően szükséges CNAME az egybesimítás vagy DNS-rekord feloldása, amely egy mechanizmust, a DNS-ben szolgáltató rekurzív módon oldja fel a CNAME bejegyzés IP-cím beolvasásig. Ez a funkció az Azure DNS által támogatott bejárati ajtajának végpontok. 

> [!NOTE]
> Más DNS-szolgáltatók is, amelyek támogatják az egybesimítás CNAME vagy DNS-rekord feloldása azonban Azure bejárati ajtajának javasolja ügyfeleinek üzemeltetéséhez a tartományok Azure DNS használata.

A bejárati ajtó a egy apex-tartomány előkészítése az Azure portal használata, és a HTTPS engedélyezése az SSL-lezárást tanúsítvánnyal társításával. Apex-tartományokat is hivatkozunk legfelső szintű vagy csupasz tartományok.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy alias a rekordot, amely a bejárati ajtajának profil
> * Adja hozzá a legfelső szintű tartományt kezdettől fogva
> * A telepítő a legfelső szintű tartomány HTTPS

> [!NOTE]
> Ehhez az oktatóanyaghoz, hogy már rendelkezik egy létrehozott bejárati ajtajának profilt. Tekintse meg hasonló más oktatóanyagok [a rövid útmutató: Hozzon létre egy bejárati ajtajának](./quickstart-create-front-door.md) vagy [bejárati ajtó létrehozása HTTP – HTTPS átirányításról](./front-door-how-to-redirect-https.md) a kezdéshez.

## <a name="create-an-alias-record-for-zone-apex"></a>Hozzon létre egy aliast rekord zóna felső pontja

1. Nyissa meg **Azure DNS** konfigurációs előkészíteni a tartományhoz.
2. Hozzon létre vagy szerkessze a rekord a zóna felső pontja.
3. Válassza ki a rekord **típus** , _A_ jegyezze fel, és válassza ki _Igen_ a **Alias rekordhalmaz**. **Alias típus** értékre kell állítani _Azure-erőforrás_.
4. Válassza ki az Azure-előfizetést a bejárati ajtajának profil üzemeltető, és válassza ki a bejárati ajtajának erőforrásban a **Azure-erőforrás** legördülő listából.
5. Kattintson a **OK** gombra a módosítások küldéséhez.

    ![Alias record for zone apex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A fenti lépés létrehoz egy, a bejárati ajtajának erőforrás, és egy CNAME rekord hozzárendelésének afdverify mutató zóna apex-rekord (például: `afdverify.contosonews.com`) való `afdverify.<name>.azurefd.net` amely használt bevezetése a tartomány a bejárati ajtajának profiljában.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Előkészítheti az egyéni tartományt állít be a bejárati ajtó

1. A Tervező bejárati ajtajának lapon kattintson a "+" ikont a Frontend gazdagépeken szakasz egy új egyéni tartomány hozzáadása.
2. Egyéni állomás név mezőben adja meg a legfelső szintű vagy rekordnevet tartománynév például `contosonews.com`.
3. Ha a CNAME-leképezés az a bejárati ajtó a tartomány érvényesítve van, kattintson a **Hozzáadás** az egyéni tartomány hozzáadása.
4. Kattintson a **mentése** gombra a módosítások küldéséhez.

![Egyéni tartomány menü](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Az egyéni tartomány HTTPS engedélyezése

1. Kattintson az egyéni tartományban vettek fel, és a szakaszában **egyéni tartományi HTTPS**, állítsa a állapotát **engedélyezve**.
2. Válassza ki a **felügyeleti tanúsítványtípus** való _"Saját tanúsítvány használata"_ .

> [!WARNING]
> Első felügyelt ajtó tanúsítványkezelés típusa jelenleg nem támogatott a rekordnevet vagy a legfelső szintű tartományok. Az egyetlen lehetőség érhető el a HTTPS-rekordhalmazokat vagy a legfelső szintű tartomány bejárati ajtajának engedélyezéséhez szükséges saját egyéni SSL-tanúsítvány Azure Key Vaultban lévő üzemeltetett használ.

3. Gondoskodjon arról, hogy a telepítő a megfelelő engedélyekkel a bejárati ajtajának eléréséhez a key Vault, a felhasználói Felületet, mielőtt folytatná a következő lépésben leírtaknak megfelelően.
4. Válasszon egy **Key Vault-fiók** a az aktuális előfizetésben, és válassza ki a megfelelő **titkos** és **titkos kód verziója** leképezése a megfelelő tanúsítványt.
5. Kattintson a **frissítés** menteni a kijelölést, majd **mentése**.
6. Kattintson a **frissítése** után néhány percet, és kattintson újra a Tanúsítványtelepítés előrehaladásának megtekintéséhez az egyéni tartományon. 

> [!WARNING]
> Győződjön meg arról, hogy megfelelő útválasztási szabályok létrehozása az apex-tartomány vagy a tartomány hozzá meglévő útválasztási szabályokat.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.