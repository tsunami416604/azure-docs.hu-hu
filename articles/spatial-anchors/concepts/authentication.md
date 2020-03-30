---
title: Hitelesítés és engedélyezés
description: Ismerje meg, hogy egy alkalmazás vagy szolgáltatás milyen különböző módokon hitelesítheti magát az Azure Spatial Anchors számára, valamint az Azure Spatial Anchors-hoz való hozzáférés hez való hozzáférés hez való hozzáférés hez szükséges vezérlőszintek.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656977"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Hitelesítés és engedélyezés az Azure Spatial Anchors számára

Ebben a szakaszban bemutatjuk, hogy milyen különböző módokon hitelesítheti magát az Azure Spatial Anchors az alkalmazásból vagy a webszolgáltatásból, valamint azt, hogy milyen módon használhatja a szerepköralapú hozzáférés-vezérlést az Azure Directoryban (Azure AD) a térbeli horgonyok fiókjaihoz való hozzáférés szabályozására.

## <a name="overview"></a>Áttekintés

![Az Azure Spatial Anchors hitelesítésének áttekintése](./media/spatial-anchors-authentication-overview.png)

Egy adott Azure Spatial Anchors-fiók eléréséhez az ügyfeleknek először be kell szerezniük egy hozzáférési jogkivonatot az Azure Mixed Reality Security Token Service (STS) szolgáltatásból. Az STS-ből 24 órán keresztül beszerzett jogkivonatok, amelyek információkat tartalmaznak a Spatial Anchors szolgáltatások számára a fiók engedélyezési döntéseihez, és biztosítják, hogy csak az engedélyezett rendszertagok férhessenek hozzá a fiókhoz.

Access-jogkivonatok beszerezhető cserébe vagy fiókkulcsok, vagy az Azure AD által kibocsátott jogkivonatok.

A fiókkulcsok lehetővé teszik az Azure Spatial Anchors szolgáltatás használatának gyors megkezdését; azonban az alkalmazás éles környezetben való üzembe helyezése előtt azt javasoljuk, hogy frissítse az alkalmazást az Azure AD-alapú hitelesítés használatához.

Az Azure AD hitelesítési jogkivonatok kétféleképpen szerezhetők be:

- Ha nagyvállalati alkalmazást hoz létre, és vállalata az Azure AD-t használja identitásrendszerként, használhatja a felhasználóalapú Azure AD-hitelesítést az alkalmazásban, és hozzáférést adhat a térbeli horgonyok fiókjaihoz a meglévő Azure AD biztonsági csoportok használatával, vagy közvetlenül a szervezet felhasználóinak.
- Ellenkező esetben azt javasoljuk, hogy az Azure AD-jogkivonatok beszerzése egy webszolgáltatás támogatja az alkalmazást. A támogató webszolgáltatás használata az éles alkalmazások ajánlott hitelesítési módja, mivel elkerüli az Azure Spatial Anchors eléréséhez szükséges hitelesítő adatok beágyazását az ügyfélalkalmazásban.

## <a name="account-keys"></a>Fiókkulcsok

A fiókkulcsok használata az Azure Spatial Anchors-fiók eléréséhez a legegyszerűbb módja az első lépéseknek. A fiókkulcsokat az Azure Portalon találja. Nyissa meg a fiókját, és válassza a "Kulcsok" lapot.

![Az Azure Spatial Anchors hitelesítésének áttekintése](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Két kulcs érhető el, amelyek egyszerre érvényesek a Térbeli horgonyok fiókhoz való hozzáféréshez. Javasoljuk, hogy rendszeresen frissítse a fiók eléréséhez használt kulcsot; a két különálló érvényes kulcs lehetővé teszi az ilyen frissítéseket állásidő nélkül; csak frissítenie kell az elsődleges és a másodlagos kulcsot.

Az SDK beépített támogatást nyújt a fiókkulcsok hitelesítéséhez; egyszerűen be kell állítania a AccountKey tulajdonságot a cloudSession objektumon.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Ha ez megtörtént, az SDK kezeli a fiókkulcs cseréjét egy hozzáférési jogkivonathoz, és az alkalmazás jogkivonatainak szükséges gyorsítótárazását.

> [!WARNING]
> A fiókkulcsok használata a gyors beszálláshoz ajánlott, de csak fejlesztés/prototípus készítés során. Erősen ajánlott, hogy ne szállítsa az alkalmazást éles környezetbe egy beágyazott fiókkulcs használatával, és ehelyett a következőként felsorolt felhasználó- vagy szolgáltatásalapú Azure AD-hitelesítési megközelítéseket használja.

## <a name="azure-ad-user-authentication"></a>Azure AD-felhasználó hitelesítése

Az Azure Active Directory-felhasználókat célzó alkalmazások esetében az ajánlott megközelítés egy Azure AD-token használata a felhasználó számára, amely az [MSAL-kódtár](../../active-directory/develop/msal-overview.md)használatával szerezhető be. Kövesse a felsorolt lépéseket a [regiszter egy alkalmazás rövid útmutató](../../active-directory/develop/quickstart-register-app.md), amely tartalmazza:

1. Konfiguráció az Azure Portalon
    1.  Regisztrálja az alkalmazást az Azure **AD-ben natív alkalmazásként.** A regisztráció részeként meg kell határoznia, hogy az alkalmazás több-bérlős legyen-e vagy sem, és meg kell adnia az alkalmazás számára engedélyezett átirányítási URL-eket.
        1.  Váltás az **API-engedélyek** lapra
        2.  Engedély **hozzáadása** lehetőséget
            1.  Válassza a **vegyes valóságerőforrás-szolgáltatót** a **szervezetáltal használt API-k** lapon
            2.  **Delegált engedélyek** kiválasztása
            3.  Jelölje be a **mixedreality.signin** jelölőnégyzetet **a mixedreality** alatt
            4.  **Engedélyek hozzáadása** kiválasztása
        3.  Válassza **a Rendszergazdai hozzájárulás megadása lehetőséget.**
    2.  Az alkalmazás vagy a felhasználók hozzáférésének megadása az erőforráshoz:
        1.  Keresse meg a Spatial Anchors erőforrást az Azure Portalon
        2.  Váltás a **Hozzáférés-vezérlés (IAM)** lapra
        3.  Hit **Szerepkör-hozzárendelés hozzáadása**
            1.  [Szerepkör kiválasztása](#role-based-access-control)
            2.  A **Kijelölés** mezőbe írja be annak a felhasználónak, csoportnak és/vagy alkalmazásnak a nevét, amelyhez hozzáférést kíván rendelni.
            3.  Kattintson a **Mentés** gombra.
2. A kódban:
    1.  Győződjön meg arról, hogy az **alkalmazásazonosítót** használja, és **átirányítja** a saját Azure AD-alkalmazás Uri-ját **ügyfélazonosítóként** és **RedirectUri-paraméterekként** az ADAL-ban
    2.  Állítsa be a bérlői adatokat:
        1.  Ha az alkalmazás csak a **Saját szervezet**lehetőséget támogatja, cserélje le ezt az értéket a **bérlői azonosítóra** vagy **a bérlő nevére** (például contoso.microsoft.com)
        2.  Ha az alkalmazás támogatja a **fiókok at bármely szervezeti címtárban,** cserélje le ezt az értéket **szervezetek**
        3.  Ha az alkalmazás támogatja **az összes Microsoft-fiókfelhasználót,** cserélje le ezt az értéket **a Common**
    3.  A jogkivonat-kérelemben állítsahttps://sts.mixedreality.azure.comaz **erőforrást** " ".On your token request, set the resource to " ". Ez az "erőforrás" jelzi az Azure AD-nek, hogy az alkalmazás egy jogkivonatot kér az Azure Spatial Anchors szolgáltatáshoz.

Ezzel az alkalmazás nak képesnek kell lennie arra, hogy beszerezze az MSAL egy Azure AD-jogkivonatot; beállíthatja, hogy az Azure AD token **a hitelesítéstoken** a felhőbeli munkamenet konfigurációs objektum.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD szolgáltatás hitelesítése

Az ajánlott lehetőség az Azure Spatial Anchors éles környezetben az Azure Spatial Anchors éles környezetben történő üzembe helyezéséhez, hogy kihasználja a háttérszolgáltatás, amely a hitelesítési kérelmek közvetítése. Az általános rendszernek az ábrán leírt módon kell lennie:

![Az Azure Spatial Anchors hitelesítésének áttekintése](./media/spatial-anchors-aad-authentication.png)

Itt feltételezzük, hogy az alkalmazás saját mechanizmust használ (például Microsoft-fiók, PlayFab, Facebook, Google ID, egyéni felhasználónév/jelszó stb.) a háttérszolgáltatás hitelesítéséhez. Miután a felhasználók hitelesítve vannak a háttérszolgáltatásban, hogy a szolgáltatás lekérheti az Azure AD-jogkivonatot, cserélje ki egy hozzáférési jogkivonatot az Azure Spatial Anchors számára, és visszaküldheti azt az ügyfélalkalmazásnak.

Az Azure AD-hozzáférési jogkivonat beolvasása az [MSAL-kódtár](../../active-directory/develop/msal-overview.md)használatával történik. Kövesse a felsorolt lépéseket a [regiszter egy alkalmazás rövid útmutató](../../active-directory/develop/quickstart-register-app.md), amely tartalmazza:

1.  Konfiguráció az Azure Portalon:
    1.  Regisztrálja alkalmazását az Azure AD-ben:
        1.  Az Azure Portalon keresse meg az **Azure Active Directoryt,** és válassza ki **az alkalmazásregisztrációkat**
        2.  **Új alkalmazásregisztráció** kiválasztása
        3.  Adja meg az alkalmazás nevét, válassza a **Web app / API** alkalmazástípusként, és adja meg a szolgáltatás hitelesítési URL-címét. Ezután nyomja **le a Create (Létrehozás) lenyomul.**
        4.  Az adott alkalmazásban nyomja le a **Beállítások gombot,** majd a **Billentyűk** lapot. **Save** Győződjön meg arról, hogy mentse a kulcs érték jelenik meg abban az időben, ahogy meg kell, hogy tartalmazza azt a webszolgáltatás kódját.
    2.  Az alkalmazás és/vagy a felhasználók hozzáférésének megadása az erőforráshoz:
        1.  Keresse meg a Spatial Anchors erőforrást az Azure Portalon
        2.  Váltás a **Hozzáférés-vezérlés (IAM)** lapra
        3.  Hit **Szerepkör-hozzárendelés hozzáadása**
        1.  [Szerepkör kiválasztása](#role-based-access-control)
        2.  A **kijelölendő** mezőben adja meg a létrehozott alkalmazás(ok) nevét, amelyhez hozzáférést kíván rendelni. Ha azt szeretné, hogy az alkalmazás felhasználói különböző szerepköröket a Spatial Anchors-fiókkal szemben, regisztráljon több alkalmazást az Azure AD-ben, és rendeljen mindegyikhez külön szerepkört. Ezután valósítsa meg az engedélyezési logikát a megfelelő szerepkör használatához a felhasználók számára.
    3.  Kattintson a **Mentés** gombra.
2.  A kódban (megjegyzés: használhatja a GitHubon található szolgáltatásmintát):
    1.  Győződjön meg arról, hogy az alkalmazásazonosítót, az alkalmazástitkos kulcsot és a saját Azure AD-alkalmazás Uri-ját használja ügyfélazonosítóként, titkos és RedirectUri-paraméterekként az ADAL-ban
    2.  Állítsa be a bérlőazonosítót a saját AAAzure ADD-bérlőazonosítójára az ADAL jogosultsági paraméterében
    3.  A jogkivonat-kérelemben állítsahttps://sts.mixedreality.azure.comaz **erőforrást** " "

Ezzel a háttérszolgáltatás egy Azure AD-jogkivonat ot is lekérhet. Ezután kicserélheti egy MR-tokenre, amely visszafog térni az ügyfélnek. Egy Mr-jogkivonat lekérése egy Azure AD-jogkivonat használatával rest-hívás történik. Itt van egy minta hívás:

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

Ha az engedélyezési fejléc formázása a következő:`Bearer <accoundId>:<accountKey>`

És a válasz tartalmazza az MR token egyszerű szövegben.

Ezt az MR-jogkivonatot ezután visszaadja az ügyfélnek. Az ügyfélalkalmazás ezután beállíthatja, mint a hozzáférési jogkivonatot a felhőbeli munkamenet konfigurációjában.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szolgáltatás alkalmazások, szolgáltatások vagy Azure AD-felhasználók számára biztosított hozzáférési szint szabályozása érdekében a következő szerepkörök et hozták létre, amelyeket szükség szerint hozzárendelhet az Azure Spatial Anchors-fiókokhoz:

- **Térbeli horgonyok fióktulajdonosa:** az ilyen szerepkört bekezelő alkalmazások vagy felhasználók képesek térbeli horgonyokat létrehozni, lekérdezni és törölni őket. Amikor fiókkulcsokkal hitelesíti a fiókját, a **Spatial Anchors fióktulajdonosszerepkör** a hitelesített egyszerű felhasználóhoz lesz rendelve.
- **Térbeli horgonyok fiók közreműködője:** alkalmazások vagy felhasználók, amelyek ezt a szerepkört képesek létrehozni térbeli horgonyok, lekérdezés számukra, de nem tudja törölni őket.
- **Térbeli horgonyok fiókolvasó:** az ilyen szerepkörrel rendelkező alkalmazások vagy felhasználók csak térbeli horgonyokat kérdezhetnek le, de nem hozhatnak létre újakat, nem törölhetik a meglévőket, és nem frissíthetik a térbeli horgonyok metaadatait. Ezt általában olyan alkalmazásokhoz használják, ahol egyes felhasználók a környezetet gondozzák, míg mások csak a korábban az adott környezetben elhelyezett horgonyokat tudják visszahívni.

## <a name="next-steps"></a>További lépések

Hozza létre első alkalmazását az Azure Spatial Anchors segítségével.

> [!div class="nextstepaction"]
> [Egység (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Egység (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Egység (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
