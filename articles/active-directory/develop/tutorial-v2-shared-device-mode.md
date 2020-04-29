---
title: Megosztott eszköz mód használata a MSAL Android használatával | Azure
description: Megtudhatja, hogyan készítse elő az Android-eszközök megosztott módban történő futtatását, és futtasson egy firstline Worker alkalmazást.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b2f74d2d441007f195abd38ca26ca7fa73605318
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886432"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Oktatóanyag: megosztott eszköz üzemmód használata Android-alkalmazásokban

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Fejlesztői útmutató

Ez az útmutató fejlesztői útmutatást nyújt a megosztott eszköz üzemmódjának megvalósításához egy Android-alkalmazásban a Microsoft Authentication Library (MSAL) használatával. Tekintse meg a [MSAL Android-oktatóanyagot](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) , amelyből megtudhatja, hogyan integrálhatja a MSAL az Android-alkalmazással, hogyan jelentkezhet be egy felhasználó, meghívhatja a Microsoft Graphot, és kijelentkezhet

### <a name="download-the-sample"></a>A minta letöltése

A [minta alkalmazás](https://github.com/Azure-Samples/ms-identity-android-java/) klónozása a githubról. A minta [egy vagy több fiókos módban](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)is működhet.

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>A MSAL SDK hozzáadása a helyi Maven-tárházhoz

Ha nem használja a minta alkalmazást, adja hozzá a MSAL-függvénytárat a Build. gradle fájl függőségéhez, például:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Az alkalmazás konfigurálása megosztott eszköz üzemmód használatára

A konfigurációs fájl beállításával kapcsolatos további információkért tekintse meg a [konfigurációs dokumentációt](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) .

`true` Állítsa be a beállítást `"shared_device_mode_supported"` a MSAL konfigurációs fájljába.

Előfordulhat, hogy nem tervezi több fiókos üzemmód használatát. Ez akkor lehet lehetséges, ha nem megosztott eszközt használ, és a felhasználó egyszerre több fiókkal is bejelentkezhet az alkalmazásba. Ha igen, állítsa `"account_mode"` a `"SINGLE"`következőre:. Ez garantálja, hogy az alkalmazás mindig megkapja `ISingleAccountPublicClientApplication`az alkalmazást, és jelentősen leegyszerűsíti a MSAL-integrációt. Az alapértelmezett értéke `"account_mode"` `"MULTIPLE"`, ezért fontos, hogy módosítsa ezt az értéket a konfigurációs fájlban, ha a módot használja `"single account"` .

Íme egy példa arra a auth_config. JSON fájlra, amely szerepel **app**>a minta alkalmazás**fő**>**res**>**RAW** könyvtárában:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Megosztott eszköz üzemmódjának észlelése

A megosztott eszköz mód lehetővé teszi, hogy az Android-eszközöket több alkalmazott számára is meg lehessen osztani, miközben biztosítja az eszköz Microsoft Identity-alapú felügyeletét. Az alkalmazottak bejelentkezhetnek az eszközökre, és gyorsan hozzáférhetnek az ügyfelek adataihoz. Ha végzett a váltással vagy a feladattal, egyetlen kattintással kijelentkezhetnek a megosztott eszközön lévő összes alkalmazásból, és az eszköz azonnal készen áll a következő alkalmazott használatára.

Ezzel `isSharedDevice()` a paranccsal megállapíthatja, hogy egy alkalmazás egy megosztott eszköz módban lévő eszközön fut-e. Az alkalmazás ennek a jelzőnek a használatával állapíthatja meg, hogy ennek megfelelően kell-e módosítania az UX-t.

Az alábbi kódrészletből megtudhatja, hogyan használhatja `isSharedDevice()`.  Ez a minta alkalmazás `SingleAccountModeFragment` osztálya:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>A PublicClientApplication objektum inicializálása

Ha a MSAL `"account_mode":"SINGLE"` konfigurációs fájlban be van állítva, akkor a visszaadott alkalmazás objektumát biztonságosan elvégezheti `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Egyetlen vagy több fiók üzemmódjának észlelése

Ha olyan alkalmazást ír, amely csak egy megosztott eszközön lévő firstline-feldolgozók számára lesz felhasználva, javasoljuk, hogy az alkalmazás csak az egyfiókos üzemmódot támogassa. Ez magában foglalja a legtöbb olyan alkalmazást, amely a feladathoz tartozik, például a Medical Records-alkalmazások, a számlafogadó-alkalmazások és a legtöbb üzletági alkalmazás. Ez leegyszerűsíti a fejlesztést, mivel az SDK számos funkcióját nem kell elfogadnia.

Ha az alkalmazás több fiókot is támogat, és a megosztott eszköz módot is használja, akkor az alább látható módon be kell jelölnie a megfelelő felületet.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>A bejelentkezett felhasználó beolvasása, és annak megállapítása, hogy a felhasználó módosult-e az eszközön

A `loadAccount` metódus lekéri a bejelentkezett felhasználó fiókját. A `onAccountChanged` metódus meghatározza, hogy a bejelentkezett felhasználó módosult-e, és ha igen, törölje a következőt:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Globális bejelentkezés felhasználóként

Az alábbi jelek az eszközön lévő felhasználókon keresztül más, a hitelesítő alkalmazással MSAL használó alkalmazásokhoz használhatók:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Felhasználó globális kijelentkezése

A következő eltávolítja a bejelentkezett fiókot, és törli a gyorsítótárazott jogkivonatokat nem csak az alkalmazásból, hanem a megosztott eszköz módban lévő eszközről is:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Rendszergazdai útmutató

A következő lépések leírják, hogyan állíthatja be az alkalmazást a Azure Portalban, és hogyan helyezheti üzembe az eszközt a megosztott eszközökön.

### <a name="register-your-application-in-azure-active-directory"></a>Az alkalmazás regisztrálása a Azure Active Directoryban

Először regisztráljon az alkalmazást a szervezeti bérlőn belül. Ezután adja meg ezeket az értékeket a auth_config. JSON fájlban, hogy az alkalmazás megfelelően fusson.

Ennek módjáról az [alkalmazás regisztrálása](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application)című témakörben olvashat bővebben.

> [!NOTE]
> Az alkalmazás regisztrálása után a bal oldalon a gyors üzembe helyezési útmutatóban válassza az **Android**lehetőséget. Ez egy olyan oldalra mutat, amelyben meg kell adnia az alkalmazáshoz tartozó **csomag nevét** és **aláírási kivonatát** . Ezek nagyon fontosak annak biztosításához, hogy az alkalmazás konfigurációja működni fog. Ekkor megjelenik egy olyan konfigurációs objektum, amelyet az alkalmazáshoz használhat, amelyet a auth_config. JSON fájlba kivágja és beilleszt.

![Az alkalmazás regisztrációs](media/tutorial-v2-shared-device-mode/register-app.png) képernyőjén válassza a **módosítás elvégzése nekem** lehetőséget, majd adja meg, hogy a rövid útmutató milyen értékeket kér a Azure Portal. Ha elkészült, a rendszer létrehozza az összes szükséges konfigurációs fájlt.

![Az alkalmazás konfigurációs adatai képernyő](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Bérlő beállítása

Tesztelési célból állítsa be a következőt a bérlőben: legalább két alkalmazott, egy felhőalapú eszköz rendszergazdája és egy globális rendszergazda. A Azure Portalban állítsa be a felhőalapú eszköz rendszergazdáját a szervezeti szerepkörök módosításával. A Azure Portal a szervezeti szerepköröket a **Azure Active Directory** > **szerepkörök és rendszergazdák** > **Felhőbeli eszköz rendszergazdája**lehetőség kiválasztásával érheti el. Adja hozzá azokat a felhasználókat, akik számára az eszköz megosztott módba helyezhető.

## <a name="set-up-an-android-device-in-shared-mode"></a>Android-eszköz beállítása megosztott módban

### <a name="download-the-authenticator-app"></a>A hitelesítő alkalmazás letöltése

Töltse le a Microsoft Authenticator alkalmazást a Google Play áruházból. Ha már letöltötte az alkalmazást, győződjön meg arról, hogy a legújabb verzió.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Hitelesítő alkalmazás beállításai & a felhőben regisztrálja az eszközt

Indítsa el a hitelesítő alkalmazást, és navigáljon a főfiók lapra. Ha megjelenik a **fiók hozzáadása** lap, készen áll az eszköz megosztására.

![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Nyissa meg a **Beállítások** ablaktáblát a jobb oldali menüsor használatával. Válassza az **eszköz regisztrálása** **munkahelyi & iskolai fiókok**alatt lehetőséget.

 ![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Ha erre a gombra kattint, a rendszer arra kéri, hogy engedélyezze az eszköz névjegyeinek elérését. Ennek oka az, hogy az Android-fiók integrálva van az eszközön. Válassza az **Engedélyezés**lehetőséget.

 ![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

A Felhőbeli eszköz rendszergazdájának meg kell adnia a szervezeti e-mail-címét **, vagy regisztrálnia kell megosztott eszközként**. Ezután kattintson a **regisztrálás megosztott eszközként** gombra, és adja meg a hitelesítő adataikat.

![eszköz regisztrálása képernyő](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

Az eszköz már megosztott módban van.

![eszköz regisztrálása képernyő](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Az eszközön a bejelentkezések és a kijelentkezések globálisak lesznek, ami azt jelenti, hogy minden olyan alkalmazásra érvényesek, amely integrálva van a MSAL és a Microsoft Authenticator az eszközön. Mostantól telepítheti az alkalmazásokat az eszközre, amely megosztott eszköz üzemmódú szolgáltatásokat használ.

## <a name="view-the-shared-device-in-the-azure-portal"></a>A Azure Portal megosztott eszközének megtekintése

Az eszköz megosztott módban való üzembe helyezése után a rendszer a szervezete számára ismertté válik, és nyomon követheti a szervezeti bérlőn. A megosztott eszközök megtekintéséhez tekintse meg a Azure Portal Azure Active Directory paneljén található **JOIN (illesztés) típust** .

![A Azure Portal minden eszköz panelje](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>A minta alkalmazás futtatása

A minta alkalmazás egy egyszerű alkalmazás, amely meghívja a szervezet Graph APIét. Az első futtatáskor a rendszer megkéri, hogy az alkalmazás új legyen az alkalmazott fiókjában.

![Az alkalmazás konfigurációs adatai képernyő](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>További lépések

További információ a megosztott üzemmódról az [Android-eszközök megosztott eszközének módjáról](msal-android-shared-devices.md)