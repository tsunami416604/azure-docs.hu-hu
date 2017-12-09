
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>A Microsoft Graph API meghívása iOS-alkalmazásból

Ez az útmutató bemutatja, hogyan olyan natív iOS-alkalmazás (Swift) API-k, a Microsoft Azure Active Directory (Azure AD) v2.0-végpontra a hozzáférési jogkivonatok igénylő tudják hívni. Az útmutató ismerteti, hogyan hozzáférési tokenek beszerzése érdekében, és a Microsoft Graph API és a többi API-hívások azok használatát.

Az útmutatóban szereplő gyakorlatok befejezése után az alkalmazás a vállalat vagy szervezet, amely az Azure AD egy védett API hívhatják meg. Az alkalmazás használatával, mint az Outlook.com-os, live.com és más személyes fiókokat, valamint a munkahelyi vagy iskolai fiókok tehet védett API-hívásokat.

## <a name="prerequisites"></a>Előfeltételek
- XCode verziójú 8.x szükség a mintát, amely a jelen útmutató jön létre. Az XCode letöltheti a [iTunes webhely](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-cím").
- A [Carthage](https://github.com/Carthage/Carthage) függőségi kezelő szükség a felügyeleti csomag.

## <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

Ebben az útmutatóban a mintaalkalmazás lehetővé teszi az iOS alkalmazás a Microsoft Graph API-val vagy a webes API-k, amelyek az Azure AD v2.0-végpontra a jogkivonatokat fogad el. Ebben a forgatókönyvben egy token adni a HTTP-kérelmekre használatával a **engedélyezési** fejléc. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Kezeli a védett webes API-k elérésére token beszerzése

Miután a felhasználó hitelesíti magát, a mintaalkalmazást kap jogkivonat. A jogkivonatot a Microsoft Graph API vagy egy webes API-t, amelyet az Azure AD v2.0-végponttól lekérdezésére szolgál.

API-k, például a Microsoft Graph olyan hozzáférési jogkivonatot, hogy egy adott erőforráshoz való hozzáférést igényel. Jogkivonatok szükség a felhasználói profil olvasása, hozzáférés egy felhasználó naptár, küldjön egy e-mailt, és így tovább. Az alkalmazás olyan hozzáférési jogkivonatot kérhetnek MSAL használatával és API-hatókörök megadásával. A hozzáférési jogkivonat hozzáadódik a HTTP **engedélyezési** minden hívás ellen védett erőforrás végrehajtott fejléc.

MSAL kezeli, és a hozzáférési jogkivonatok frissítése, így nem kell az alkalmazást.


## <a name="libraries"></a>Szalagtárak

Ez az útmutató használja a következő könyvtárban:

|Részletes ismertetés|Leírás|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|IOS rendszerhez készült Microsoft hitelesítési könyvtár előzetes verzió|

