---
title: "Ismerkedés az Azure AD Cordova |} Microsoft Docs"
description: "Megtudhatja, hogyan hozható létre a Cordova-alkalmazás, amely integrálható az Azure ad-val bejelentkezhet, és meghívja az Azure AD-védelemmel ellátott API-OAuth használatával."
services: active-directory
documentationcenter: 
author: vibronet
manager: mtillman
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: eceeccc0d6225613fdd75a92b894290665168fc6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Azure AD integrálása az Apache Cordova-alkalmazás
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Apache Cordova segítségével futtatható, mint a teljes körű natív alkalmazások mobileszközökön HTML5 vagy JavaScript-alkalmazások fejlesztéséhez. Az Azure Active Directoryval (Azure AD) vállalati szintű hitelesítési képességeket adhat a Cordova-alkalmazást.

A Cordova beépülő modul becsomagolja az Azure AD natív SDK-k iOS, Android, Windows áruház és Windows Phone. Amely a beépülő modul, növelheti az alkalmazás számára jelentkezzen be a felhasználók a Windows Server Active Directory-fiókokat támogatja, Office 365 és Azure API-k eléréséhez, és még a hívások a saját egyéni webes API védelme érdekében.

Ebben az oktatóanyagban egy egyszerű alkalmazást javíthatja a következő funkciók felvételének fogjuk felhasználni az Apache Cordova beépülő modul az Active Directory Authentication Library (ADAL):

* A pár sornyi kód hitelesíteni a felhasználót, és jogkivonat beszerzése.
* A jogkivonat segítségével meghívni a Graph API, amellyel könyvtárhoz, és az eredmények megjelenítéséhez.  
* Az ADAL jogkivonat gyorsítótára minimalizálása érdekében kell használni a felhasználói hitelesítést kér.

Ahhoz, hogy ezek a fejlesztések, kell:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Kód hozzáadása az alkalmazáshoz szükséges jogkivonatok.
3. Adja hozzá a jogkivonatot a Graph API lekérdezéshez használt kódot, és megjeleníti az eredményeket.
4. A Cordova-telepítési projekt létrehozása szeretne megcélozni, adja hozzá a Cordova ADAL beépülő modul és a megoldás tesztelése az emulátorok minden platform.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Az Azure AD-bérlő app development jogosultságokkal rendelkező fiók esetében.
* A fejlesztési környezet, amely az Apache Cordova használatára van konfigurálva.  

Ha mindkét már van beállítva, hogy továbblépjen közvetlenül 1. lépés.

Ha az Azure AD-bérlő nem rendelkezik, használja a [utasításokat az beszerzése](active-directory-howto-tenant.md).

Apache Cordova állítsa be a számítógépre nincs, telepítse a következő:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (Csomagkezelő NPM segítségével könnyedén telepíthető: `npm install -g cordova`)

Az előző telepítések a számítógép és a Mac kell működnie.

Minden érintett rendszerek különböző előfeltételei:

* Létrehozásához és az alkalmazások a Windows/Táblagép vagy a Windows Phone:
  * Telepítés [Visual Studio 2013 Update 2 vagy újabb verziójú Windows](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express vagy egy másik verziója) vagy [Visual Studio 2015-öt](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* IOS-alkalmazások létrehozásához és:

  * Xcode telepítése 6.x vagy újabb. Töltse le a [Apple Developer hely](http://developer.apple.com/downloads) vagy a [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Telepítés [ios-sim](https://www.npmjs.org/package/ios-sim). IOS-alkalmazások indítása a parancssorból iOS-szimulátor használhatja. (A Terminálszolgáltatások segítségével könnyedén telepíthető: `npm install -g ios-sim`.)
* Létrehozásához és az alkalmazások Android:

  * Telepítés [Java fejlesztői készlet (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) vagy újabb. Győződjön meg arról, hogy `JAVA_HOME` (környezeti változó) megfelelően van beállítva a JDK telepítési elérési útját (például, C:\Program Files\Java\jdk1.7.0_75) megfelelően.
  * Telepítés [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) , és adja hozzá a `<android-sdk-location>\tools` helyét (például C:\tools\Android\android-sdk\tools), a `PATH` környezeti változó.
  * Nyissa meg az Android SDK Manager (például a terminál használatával: `android`) és telepítése:
    * *Android 5.0.1-es (API 21)* platform SDK
    * *Android SDK Build Tools* 19.1.0 verzió vagy újabb verzió
    * *Android-támogatás tárház* (kiegészítő funkciók)

  Az Android SDK bármely alapértelmezett emulátor példány nem biztosít. Futtatásával `android avd` a terminálból majd **létrehozása**, ha azt szeretné, hogy az emulátor az Android-alkalmazás futtatásához. Azt javasoljuk, hogy egy API-szintet 19 vagy magasabb. Android emulator és létrehozásának beállításokkal kapcsolatos további információkért lásd: [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) a Android helyen.

## <a name="step-1-register-an-application-with-azure-ad"></a>1. lépés: Egy alkalmazás regisztrálása az Azure ad szolgáltatással
Ez a lépés nem kötelező megadni. Ez az oktatóanyag segítségével tekintse meg a minta működés közben anélkül bármely saját bérlőt kiépítési előre kiosztott értékeket biztosít. Azonban azt javasoljuk, hogy hajtsa végre ezt a lépést, és ismerkedjen meg a folyamat, mert lesz szükség a saját alkalmazások létrehozásakor.

Az Azure AD számára csak ismert alkalmazások jogkivonatokat. Az Azure AD a alkalmazás használata előtt kell bejegyzés létrehozása a bérlő számára. A bérlő új alkalmazás regisztrálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy **natív ügyfélalkalmazás**. (Bár a Cordova-alkalmazásokkal HTML-alapú, most létrehozzuk natív ügyfélalkalmazás itt. A **natív ügyfélalkalmazás** beállítást, vagy az alkalmazás nem fog működni.)
  * **Név** az alkalmazás a felhasználók számára ismerteti.
  * **Átirányítási URI** az URI azonosítót ad vissza a jogkivonatok az alkalmazáshoz. Adja meg **http://MyDirectorySearcherApp**.

Regisztráció befejezése után az Azure AD egy egyedi alkalmazás Azonosítót rendel az alkalmazáshoz. Ez az érték a következő szakaszokban lévő lesz szüksége. Az újonnan létrehozott alkalmazás az alkalmazás lapon találja.

Futtatásához `DirSearchClient Sample`, az újonnan létrehozott alkalmazás engedélyt lekérdezése az Azure AD Graph API:

1. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**.  
2. Az Azure Active Directory-alkalmazást, válassza a **Microsoft Graph** az API-t, és adja hozzá a **hozzáférés a címtárhoz a bejelentkezett felhasználó az** engedélyt a **delegált engedélyek**.  Ez lehetővé teszi az alkalmazás lekérdezése a Graph API-t a felhasználók számára.

## <a name="step-2-clone-the-sample-app-repository"></a>2. lépés: A minta app tárház klónozása
A rendszerhéj vagy a parancssorból írja be a következő parancsot:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>3. lépés: A Cordova-alkalmazás létrehozása
Több módon is Cordova-alkalmazás létrehozása. Ebben az oktatóanyagban a Cordova parancssori felület (CLI) fogjuk használni.

1. A rendszerhéj vagy a parancssorból írja be a következő parancsot:

        cordova create DirSearchClient

   Ez a parancs létrehozza a gyökérmappa-szerkezetében és a Cordova-projekt állványok.

2. Helyezze át az új DirSearchClient mappába:

        cd .\DirSearchClient

3. A www másolja a alapszintű projekt tartalmának másolása a Fájlkezelőben vagy a következő parancsot a rendszerhéj használatával:

  * Windows:`xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac:`cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Adja hozzá az engedélyezési lista beépülő modult. Erre akkor szükség, a Graph API meghívására.

        cordova plugin add cordova-plugin-whitelist

5. Adja hozzá a támogatni kívánt összes platformon. Ahhoz, hogy egy minta, kell végrehajtani a következő parancsok egyikét. Vegye figyelembe, hogy Ön nem tudja emulálni Windows iOS vagy Mac a Windows rendszer

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Az adal-t a Cordova beépülő modul hozzáadása a projekthez:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>4. lépés: Adja hozzá a kódot a hitelesíti a felhasználókat, és az Azure AD tokenek beszerzése
Az alkalmazást, amely kidolgozása ebben az oktatóanyagban egy egyszerű directory keresési funkciót biztosít. A felhasználó ezután írja be annak a felhasználónak az aliasneve a könyvtárban, és néhány alapvető attribútum megjelenítése. A kezdő projektet az alapszintű felhasználói felület (a www/index.html) az alkalmazás definícióját tartalmazza, és a állványok, amely alapszintű alkalmazást eseményt kábeleket ciklusok, a felhasználói felület kötések, és annak az eredménye megjelenítése a logikai (www/js/index.js). A csak akkor maradt feladata a adja hozzá a programot, amely megvalósítja az identitás feladatok.

Először is végre kell hajtani a kódban bevezetni az protokoll értékeket, amelyeket az alkalmazás és az erőforrások azonosítására használ az Azure AD, hogy a célként meghatározott. Ezeket az értékeket a jogkivonat-kérelmeket összeállítani később használható. Szúrja be a következő kódrészletet a index.js fájl elején:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

A `redirectUri` és `clientId` értéket meg kell felelnie az értékeket, amelyeket az alkalmazás leírása az Azure ad-ben. Szerintiek megtalálhatja a **konfigurálása** lap az Azure portálon, ez az oktatóanyag a korábban az 1. lépésben leírtak szerint.

> [!NOTE]
> Ha nem regisztrálja egy új alkalmazást a saját bérlőt választotta, egyszerűen beillesztheti regisztrációja, mivel az előre beállított értékek. A minta fut, majd tekintheti meg, ha mindig hozzon létre saját bejegyzést az alkalmazások, amelyek termelési környezetben.

Ezután adja hozzá a jogkivonatkérelem kódot. Helyezze be a következő kódrészletet között a `search` és `renderData` definíciók:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vizsgáljuk meg, hogy a függvény által bontásához a két fő részből áll.
Ez a minta tervezték semmilyen bérlővel szemben nem a megadott kötődik. Használja a "/ közös" végpontot, amely lehetővé teszi a felhasználó számára adjon meg egy fiókot hitelesítés közben, és a kérelem a bérlő irányítja, ahol tartozik.

Ez a metódus első része megvizsgálja az ADAL gyorsítótár megtekintéséhez, ha egy jogkivonatot már tárolja. Ha igen, a metódusnak a bérlők a token származási helyét az adal-t újrainicializálását. Ez azért kell kerülni a felesleges kér, mert a használja a "/ közös" mindig eredményezi, amely kéri a felhasználót, hogy adjon meg egy új fiókot.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
A metódus második része a megfelelő jogkivonatkérelem hajt végre. A `acquireTokenSilentAsync` metódus kéri ADAL vissza a megadott erőforrás jogkivonat bármely UX megjelenítése nélkül Amely akkor fordulhat elő, ha a gyorsítótár már tartalmaz egy megfelelő hozzáférési jogkivonat tárolja, vagy ha egy frissítési jogkivonat férhetnek hozzá egy új jogkivonatot minden kérdés megjelenítése nélkül is használható. Amely sikertelen lesz, ha azt térhet vissza `acquireTokenAsync`– amely láthatóan fogja kérni a felhasználó hitelesítésére.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Most, hogy a jogkivonat, azt végül a Graph API meghívása és a keresési lekérdezés, amely azt szeretnénk, végezze el. Helyezze be a következő kódrészletet az alábbi a `authenticate` definíciója:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
A kiindulópont-fájlokat egy egyszerű UX megadott beviteli mezőben írja be a felhasználói alias. Ez a módszer olyan lekérdezést, a hozzáférési jogkivonat összevonásához, elküldi a Microsoft Graph és az eredményeket elemezni ezt az értéket használja. A `renderData` metódus már szerepel a kiindulópont-fájlban, az eredmények megjelenítik gondoskodik.

## <a name="step-5-run-the-app"></a>5. lépés: Az alkalmazás futtatása
Az alkalmazás végül készen áll a futtatásra. Egyszerű az operációs rendszer: az alkalmazás indításakor írja be a megkeresni kívánt felhasználóhoz aliasát, és kattintson a gombra. Megkéri, hogy hitelesítésre. Sikeres hitelesítés és a keresés sikeres a keresett felhasználó attribútumainak jelennek meg.

Utólagosan végez a keresési megjelenítése minden kérdés környezetnek köszönhetően a korábban beszerzett jogkivonat a gyorsítótárban jelenléte nélkül.

Az alkalmazás futtatásához konkrét szükséges lépések eltérhetnek a platformon.

### <a name="windows-10"></a>Windows 10
   / Táblagép:`cordova run windows --archs=x64 -- --appx=uap`

   A Mobile (a számítógép csatlakozik a Windows 10 Mobile eszközt igényel):`cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Az első futtatás során előfordulhat, hogy kérni jelentkeznek be egy fejlesztői licencet. További információkért lásd: [fejlesztői licenc](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Windows 8.1 / Táblagép
   `cordova run windows`

   > [!NOTE]
   > Az első futtatás során előfordulhat, hogy kérni jelentkeznek be egy fejlesztői licencet. További információkért lásd: [fejlesztői licenc](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Egy csatlakoztatott eszközön futtatása:`cordova run windows --device -- --phone`

   Az alapértelmezett emulátor futtatásához:`cordova emulate windows -- --phone`

   Használjon `cordova run windows --list -- --phone` összes elérhető tárolók megtekintéséhez és `cordova run windows --target=<target_name> -- --phone` az alkalmazás futtatásához egy adott eszköz vagy az emulátor (például `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Egy csatlakoztatott eszközön futtatása:`cordova run android --device`

   Az alapértelmezett emulátor futtatásához:`cordova emulate android`

   Ellenőrizze, hogy létrehozott egy emulátor példány AVD Manager segítségével az "Előfeltételek" szakaszban leírtak szerint.

   Használjon `cordova run android --list` összes elérhető tárolók megtekintéséhez és `cordova run android --target=<target_name>` az alkalmazás futtatásához egy adott eszköz vagy az emulátor (például `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Egy csatlakoztatott eszközön futtatása:`cordova run ios --device`

   Az alapértelmezett emulátor futtatásához:`cordova emulate ios`

   > [!NOTE]
   > Győződjön meg arról, hogy a `ios-sim` csomag az emulátor futni. További információkért tekintse meg az "Előfeltételek" szakaszban.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Következő lépések
Referenciaként az elkészült mintát (a konfigurációs értékek nélkül) érhető el a [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Most már továbbléphet összetettebb (és érdekesebb) forgatókönyvek. Akkor célszerű: [egy Node.js webes API-t az Azure ad-vel biztonságos](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
