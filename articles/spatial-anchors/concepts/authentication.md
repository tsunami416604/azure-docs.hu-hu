---
title: Hitelesítés és engedélyezés az Azure térbeli horgonyokra mutató |} A Microsoft Docs
description: Ismerje meg a különböző módszereket hitelesítheti az alkalmazás vagy szolgáltatás Azure térbeli horgonyok, és a szintű Azure térbeli horgonyok kapu hozzáféréssel rendelkező vezérlését.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: e641025d49dd42125aa692925c0697235489b1db
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307152"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Hitelesítés és engedélyezés az Azure térbeli horgonyokra mutató

Ebben a szakaszban ismertetjük a különböző módon hitelesítheti magát Azure térbeli horgonyok az alkalmazáshoz vagy webszolgáltatáshoz, és a lehetőségek, amelyben használatára szerepköralapú hozzáférés-vezérlés az Azure Directory (Azure AD) a térbeli horgonyok fiókok elérésének szabályozására.  

## <a name="overview"></a>Áttekintés

![Azure térbeli horgonyokra mutató hitelesítés áttekintése](./media/spatial-anchors-authentication-overview.png)

Egy adott térbeli horgonyok Azure-fiók eléréséhez, az ügyfelek kell először szerezze be egy hozzáférési jogkivonatot az Azure a vegyes valóság biztonsági jogkivonat szolgáltatás (STS). STS származó jogkivonatokat élő 24 órán keresztül, és tartalmaz adatokat a térbeli horgonyok szolgáltatások engedélyezési döntéseket a fiók, és győződjön meg arról, hogy csak engedélyezett rendszerbiztonsági tagok is hozzáférni ehhez a fiókhoz. 

Hozzáférési jogkivonatok az exchange szerezhető be vagy kulcsait, vagy az Azure AD által kiállított jogkivonatokat. 

Fiókkulcsok lehetővé teszik a gyors kezdéshez az Azure térbeli horgonyok szolgáltatás; Azonban mielőtt telepítené az alkalmazást az éles környezetben, javasolt frissíteni az alkalmazást az Azure AD-alapú hitelesítés használatához. 

Az Azure AD hitelesítési tokenek kétféleképpen szerezhető be:

- Ha egy vállalati alkalmazást fejleszt, és a cége a Azure ad-ben az identitás rendszerként, használhatja a felhasználó-alapú Azure AD-hitelesítés az alkalmazást, és hozzáférést biztosít a térbeli horgonyok fiókokat a meglévő Azure AD biztonsági csoportok használata vagy közvetlenül a felhasználók számára a szervezetben. 
- Ellenkező esetben javasoljuk, az Azure AD-jogkivonatok lekérését az alkalmazás egy webes szolgáltatás. Éles alkalmazások esetén javasolt hitelesítési módszer támogató webszolgáltatáson keresztül, mivel ezzel elkerülheti az ügyfélalkalmazásban található Azure térbeli horgonyok eléréséhez a hitelesítő adatok beágyazása. 

## <a name="account-keys"></a>Fiókkulcsok

Első lépésként a legegyszerűbb fiók-kulcsokat használnak a hozzáférést a térbeli horgonyok Azure-fiókjához. A fiókkulcsok megtalálja az Azure Portalon. Keresse meg a fiókjához, és válassza a "Kulcsok" lapot.

![Azure térbeli horgonyokra mutató hitelesítés áttekintése](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Két kulcs érhetik el, melyek mind egyszerre érvényes hozzáférés a térbeli horgonyok fiókot. Javasoljuk, hogy rendszeresen frissíti a fiók; eléréséhez használt kulcs nehogy két külön például frissítése nélkül; érvényes kulcsok engedélyezése csak akkor frissítse azt is megteheti az elsődleges és a másodlagos kulcs. 

Az SDK-t a fiókkulcsok; hitelesítésről beépített támogatással rendelkezik. egyszerűen szeretne a AccountKey tulajdonsága a cloudSession objektumon. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

Miután ez megtörtént, az SDK-t az exchange, a hozzáférési token-fiók kulcsát, és a jogkivonatok az alkalmazáshoz szükséges gyorsítótár fogja kezelni. 

> [!WARNING] 
> Fiókkulcsok használata javasolt a gyors az előkészítési, de fejlesztés/prototípus-készítés csak során. Erősen ajánlott, nem az alkalmazás egy beágyazott fiókkulcs használatával, az éles környezetbe szállításra, és inkább használja a felhasználó- vagy service-alapú Azure AD-hitelesítés megközelíti a listán szereplő következő.

## <a name="azure-ad-user-authentication"></a>Az Azure AD-felhasználói hitelesítés

Alkalmazások Azure Active Directory-felhasználók, az ajánlott módszer az Azure AD-token használatára a felhasználó számára, amely szerezheti be az ADAL-kódtár használatával, a következő dokumentációban ismertetett módon: https://docs.microsoft.com/azure/active-directory/develop/v1-overview; kövesse a felsorolt lépéseket a "Gyors kezdődik" többek között:

1. Konfiguráció az Azure Portalon
    1.  Az alkalmazás regisztrálása az Azure AD szolgáltatásba **natív alkalmazás**. Regisztrálás részeként kell meghatározni e az alkalmazás több-bérlős kell-e, és adja meg az átirányítási URL-címek engedélyezett az alkalmazás.  
    2.  Hozzáférést biztosít az alkalmazás vagy a felhasználók az erőforrásra: 
        1.  Keresse meg a térbeli horgonyok erőforrást az Azure Portalon
        2.  Váltson a **hozzáférés-vezérlés (IAM)** lap
        3.  Találati **szerepkör-hozzárendelés hozzáadása**
            1.  [Szerepkör kiválasztása](#role-based-access-control)
            2.  Az a **kiválasztása** mezőben adja meg a felhasználó, csoport és/vagy, amelyhez hozzáférést hozzárendelni kívánt alkalmazás(ok) nevére. 
            3.  Találati **mentése**.
2. A kódban:
    1.  Győződjön meg arról, hogy használja a **Alkalmazásazonosító** és **átirányítási Uri** , mint a saját Azure AD-alkalmazást a **ügyfél-azonosító** és **RedirectUri** az adal-t paraméterek
    2.  Állítsa be a bérlő információkat:
        1.  Ha az alkalmazás támogatja a **csak saját szervezet**, cserélje le ezt az értéket a **Bérlőazonosító** vagy **bérlőnevet** (például contoso.microsoft.com)
        2.  Ha az alkalmazás támogatja a **bármely szervezeti directory fiókok**, cserélje le ezt az értéket **szervezetek**
        3.  Ha az alkalmazás támogatja a **minden Microsoft-fiókok felhasználóinak**, cserélje le ezt az értéket **közös**
    3.  A jogkivonat kérése állítsa be a **erőforrás** a "https://sts.mixedreality.azure.com". Az "erőforrás" jelzi az Azure ad-hez, hogy az alkalmazás egy tokent az Azure térbeli horgonyok szolgáltatás.  

Az adott az alkalmazás beszerzése az adal-t egy Azure AD-jogkivonattal; képesnek kell lennie Beállíthatja, hogy az Azure AD-token, a **auhenticationtoken mezőbe helyezi** a felhő munkamenet konfigurációs objektum. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Azure AD-hitelesítés

Azure térbeli horgonyok kihasználva éles alkalmazások központi telepítése az ajánlott, hogy kihasználja a háttérszolgáltatás közvetíteni fogja a hitelesítési kérések. Az általános rendszer legyen ez az ábra leírtak szerint:

![Azure térbeli horgonyokra mutató hitelesítés áttekintése](./media/spatial-anchors-aad-authentication.png)

Itt azt feltételezzük, hogy az alkalmazás a saját mechanizmust használ (például: Microsoft-fiókkal, a PlayFab, a Facebook, Google-azonosító, egyéni felhasználónév/jelszó stb.) a háttérszolgáltatás hitelesítésre. Miután a felhasználók hitelesítése a háttérszolgáltatásban szolgáltatás lekérő Azure AD-token Azure térbeli horgonyok a hozzáférési jogkivonatot az exchange, és térjen vissza az ügyfélalkalmazásban.

Az Azure AD hozzáférési jogkivonatot lekéri az ADAL-kódtár használatával, a következő dokumentációban ismertetett módon: https://docs.microsoft.com/azure/active-directory/develop/v1-overview; meg kell a következő lépésekkel a "Gyors kezdődik", többek között:

1.  Az Azure portal-konfiguráció:
    1.  Az alkalmazás regisztrálása az Azure ad-ben:
        1.  Az Azure Portalon lépjen **Azure Active Directory**, és válassza ki **alkalmazásregisztrációk**
        2.  Válassza ki **új alkalmazás regisztrálása**
        3.  Adja meg az alkalmazást, válassza ki a nevét **webalkalmazás / API** az alkalmazás típusát, és adja meg a hitelesítési URL-cím a szolgáltatáshoz. Kattintson a gombra **létrehozás**.
        4.  Nyomja le az alkalmazást a **beállítások**, majd válassza ki a **kulcsok** fülre. Adja meg annak a kulcsnak a nevét, válassza ki az időtartamot, és nyomja le **mentése**. Ellenőrizze, hogy elmentse a kulcs értékét, ugyanakkor a megjelenített, mert szüksége lesz, adja meg a webszolgáltatás kódjával.
    2.  Hozzáférést az alkalmazás és/vagy a felhasználók az erőforráshoz:
        1.  Keresse meg a térbeli horgonyok erőforrást az Azure Portalon
        2.  Váltson a **hozzáférés-vezérlés (IAM)** lap
        3.  Találati **szerepkör-hozzárendelés hozzáadása**
        1.  [Szerepkör kiválasztása](#role-based-access-control)
        2.  Az a **kiválasztása** mezőben adja meg a létrehozott alkalmazás nevét, és amely hozzárendelni kívánt nyissa meg. Ha azt szeretné, hogy az alkalmazás-felhasználók számára a térbeli horgonyok fiók ellen eltérő szerepkörökkel, kell több alkalmazás regisztrálása az Azure ad-ben, és minden egyes külön szerepkör hozzárendelése. A megfelelő szerepkör használata a felhasználók az engedélyezési logikát megvalósításához.  
    3.  Találati **mentése**.
2.  A kódban (Megjegyzés: a szolgáltatás-minta a Githubon található is használhat):
    1.  Az Alkalmazásazonosító, az alkalmazás titkos használja, és átirányítási Uri-ját, az ügyfél-Azonosítót a saját Azure AD-alkalmazás titkos kulcsot, valamint a RedirectUri paramétereket az adal-t
    2.  Állítsa be a bérlő Azonosítóját az adal-t a szolgáltató paramétert, a saját AAAzure hozzáadása bérlő azonosítója
    3.  A jogkivonat kérése állítsa be a **erőforrás** a "https://sts.mixedreality.azure.com" 

Az adott a háttérszolgáltatás lekérheti az Azure AD-token. Azt is majd exchange-, MR tokenre, a rendszer visszaadja az ügyfélnek. Azure AD-token használatával egy MR jogkivonatot beolvasni történik egy REST-hívást keresztül. Itt látható egy minta-hívás:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Ha az engedélyezési fejléc módon formázott: `Bearer <accoundId>:<accountKey>`

És a válasz tartalmazza a MR tokent, az egyszerű szöveg.
 
MR tokenre majd küld vissza az ügyfélnek. Az ügyfélalkalmazás ezután állíthatja, a hozzáférési jogkivonatot a felhő munkamenet-konfigurációt.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Alkalmazások, szolgáltatások vagy a szolgáltatás az Azure AD-felhasználók kapnak a hozzáférési szintet, segítségével szabályozhatja, a következő szerepkörök lett létrehozva a térbeli horgonyok Azure-fiókok ellen igény szerint rendelhet hozzá:

- **Térbeli horgonyok fióktulajdonos**: vagy a szerepkörrel rendelkező felhasználók képesek arra, hogy a térbeli horgonyokat létrehozni, a lekérdezések a számukra, és törölje őket. Fiókkulcsok, a fiókjába hitelesítésekor a **térbeli horgonyok fióktulajdonos** szerepkör hozzá van rendelve a hitelesített egyszerű. 
- **Térbeli horgonyok közreműködő**: vagy a szerepkörrel rendelkező felhasználók létre tudja hozni a térbeli horgonyok, lekérdezés, de nem lehet törölni. 
- **Térbeli horgonyok fiók olvasói**: vagy a szerepkörrel rendelkező felhasználók képesek csak lekérdezni a térbeli horgonyok, de nem újakat hoz létre, törölje a meglévő vagy frissíteni a metaadatokat a térbeli horgonyok. Ez általában alkalmazásokhoz használható ahol néhány felhasználó összeválogatásában-e a környezetet, míg mások is csak visszahívása horgonyok korábban már elhelyezett az adott környezetben.

## <a name="next-steps"></a>További lépések

Az első alkalmazás létrehozása az Azure térbeli horgonyok.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
