---
title: A megosztott eszköz mód használata az MSAL Android segítségével | Azure
description: Ismerje meg, hogyan készítheti elő az Android-eszközöket a megosztott módban való futtatásra és az első vonalbeli munkavégző alkalmazás futtatására.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: bf7e6bb22ce89d6be3f79efad1f1a3679e8780e7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086065"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Oktatóanyag: Megosztott eszköz mód használata az Android-alkalmazásban

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="developer-guide"></a>Fejlesztői útmutató

Ez az útmutató fejlesztői útmutatást nyújt a megosztott eszköz mód androidos alkalmazásokban való megvalósításához a Microsoft hitelesítési könyvtár (MSAL) használatával. Tekintse meg az [MSAL Android oktatóanyagát,](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) amelyből megtudhatja, hogyan integrálhatja az MSAL-t az Android-alkalmazással, hogyan jelentkezik be egy felhasználó, hívhatja a Microsoft graphot, és hogyan jelentkeztetheti ki a felhasználót.

### <a name="download-the-sample"></a>A minta letöltése

Klónozza a [mintaalkalmazást](https://github.com/Azure-Samples/ms-identity-android-java/) a GitHubról. A minta képes [egy- vagy többfiókos módban](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)dolgozni.

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Az MSAL SDK hozzáadása a helyi Maven-tárházhoz

Ha nem használja a mintaalkalmazást, adja hozzá az MSAL-könyvtárat függőségként a build.gradle fájlhoz, így:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Az alkalmazás konfigurálása megosztott eszköz mód használatára

A konfigurációs fájl beállításával kapcsolatos további információkért tekintse meg a [konfigurációs](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) dokumentációt.

Az `"shared_device_mode_supported"` `true` MSAL konfigurációs fájlban van beállítva.

Lehet, hogy nem tervezi a többfiókos mód támogatását. Ez akkor lehet, ha nem használ megosztott eszközt, és a felhasználó egyszerre több fiókkal is bejelentkezhet az alkalmazásba. Ha igen, `"account_mode"` `"SINGLE"`állítsa a beállítását. Ez garantálja, hogy az `ISingleAccountPublicClientApplication`alkalmazás mindig megkapja , és jelentősen leegyszerűsíti az MSAL-integrációt. Az alapértelmezett `"account_mode"` érték `"MULTIPLE"`a , ezért a mód használata `"single account"` esetén fontos, hogy ezt az értéket módosítsa a konfigurációs fájlban.

Íme egy példa a auth_config.json fájlra, amely a mintaalkalmazás **fő**>**main**>**res**>**nyers** könyvtárában található:

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

### <a name="detect-shared-device-mode"></a>Megosztott eszköz mód észlelése

A megosztott eszköz mód lehetővé teszi, hogy az Android-eszközöket több alkalmazott is megoszthassa, miközben a Microsoft Identity által támogatott eszközfelügyeletet biztosít. Az alkalmazottak bejelentkezhetnek az eszközeikre, és gyorsan hozzáférhetnek az ügyfelek adataihoz. Amikor befejezték a műszakot vagy a feladatot, egyetlen kattintással kijelentkezhetnek a megosztott eszközön lévő összes alkalmazásból, és az eszköz azonnal készen áll a következő alkalmazott használatára.

Ezzel `isSharedDevice()` meghatározhatja, hogy egy alkalmazás megosztott eszköz módban lévő eszközön fut-e. Az alkalmazás ezzel a jelzővel határozhatja meg, hogy ennek megfelelően módosítsa-e a felhasználói felületet.

Itt van egy kódrészlet, amely bemutatja, hogyan használhatja a . `isSharedDevice()`  A mintaalkalmazás `SingleAccountModeFragment` osztályából származik:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>A PublicClientApplication objektum inicializálása

Ha az `"account_mode":"SINGLE"` MSAL konfigurációs fájlban van beállítva, biztonságosan `ISingleAccountPublicCLientApplication`eljátszhatja a visszaadott alkalmazásobjektumot .

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

### <a name="detect-single-vs-multiple-account-mode"></a>Egy és több fiók mód észlelése

Ha olyan alkalmazást ír, amelyet csak az első vonalbeli dolgozók számára használ egy megosztott eszközön, javasoljuk, hogy úgy írja meg az alkalmazást, hogy csak az egyfiókos módot támogassa. Ez magában foglalja a legtöbb olyan alkalmazást, amely feladatközpontú, például az orvosi feljegyzésekre, a számlaalkalmazásokra és a legtöbb üzletági alkalmazásra. Ez egyszerűsíti a fejlesztést, mivel az SDK számos funkciójának nem kell elhelyeznie.

Ha az alkalmazás több fiókot és megosztott eszköz módot is támogat, el kell végeznie egy típusellenőrzést, és a megfelelő felületre kell vetnie az alábbi módon.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>A bejelentkezett felhasználó beszereznie, és annak megállapítása, hogy a felhasználó megváltozott-e az eszközön

A `loadAccount` metódus beolvassa a bejelentkezett felhasználó fiókját. A `onAccountChanged` módszer azt határozza meg, hogy a bejelentkezett felhasználó megváltozott-e, és ha igen, törölje a következőket:

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

### <a name="globally-sign-in-a-user"></a>Globális bejelentkezés egy felhasználóba

A következő jelek a felhasználó az eszközön keresztül más alkalmazások, amelyek az MSAL a Hitelesítő app:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Felhasználó globális kijelentkezése

A következőkben eltávolítja a bejelentkezett fiókot, és törli a gyorsítótárazott jogkivonatokat nemcsak az alkalmazásból, hanem a megosztott eszköz módban lévő eszközről is:

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

A következő lépések ismertetik az alkalmazás beállítása az Azure Portalon, és az eszköz elhelyezése a megosztott eszköz módban.

### <a name="register-your-application-in-azure-active-directory"></a>Az alkalmazás regisztrálása az Azure Active Directoryban

Először regisztrálja az alkalmazást a szervezeti bérlőn belül. Ezután adja meg ezeket az értékeket az auth_config.json ban, hogy az alkalmazás megfelelően fusson.

Ennek módjáról a [Jelentkezés regisztrálása](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application)című lapban talál további információt.

> [!NOTE]
> Amikor regisztrálja az alkalmazást, használja a bal oldali rövid útmutatót, majd válassza az **Android**lehetőséget. Ez egy olyan oldalra vezet, ahol meg kell adnia az alkalmazás **csomagnevét** és **aláíráskivonatát.** Ezek nagyon fontosak annak érdekében, hogy az alkalmazás konfigurációja működni fog. Ezután kap egy konfigurációs objektumot, amelyet az alkalmazáshoz használhat, amelyet kivág és beilleszt a auth_config.json fájlba.

![Alkalmazásregisztrációs](media/tutorial-v2-shared-device-mode/register-app.png) képernyő Válassza **ezt a módosítást nekem,** és majd adja meg az értékeket a gyorsindítás kéri az Azure Portalon. Ha ez megtörtént, létrehozunk minden szükséges konfigurációs fájlt.

![Alkalmazáskonfigurációs információs képernyő](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Bérlő beállítása

Tesztelési célokra állítsa be a következőket a bérlőben: legalább két alkalmazott, egy felhőeszköz-rendszergazda és egy globális rendszergazda. Az Azure Portalon állítsa be a felhőbeli eszközrendszergazdát a szervezeti szerepkörök módosításával. Az Azure Portalon az **Azure Active Directory szerepkörök** > és**rendszergazdák** > **felhőalapú eszközfelügyelő**jének kiválasztásával érheti el a szervezeti szerepköröket. Adja hozzá azeszközöket megosztott módba helyezhető felhasználókat.

## <a name="set-up-an-android-device-in-shared-mode"></a>Android-eszköz beállítása megosztott módban

### <a name="download-the-authenticator-app"></a>A Hitelesítő alkalmazás letöltése

Töltse le a Microsoft Authenticator alkalmazást a Google Play áruházból. Ha már letöltötte az alkalmazást, győződjön meg arról, hogy az a legújabb verzió.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>A hitelesítő alkalmazás beállításai & az eszköz felhőben történő regisztrálása

Indítsa el a Hitelesítő alkalmazást, és keresse meg a főfiók oldalát. Miután megjelenik a **Fiók hozzáadása** lap, készen áll arra, hogy az eszközt megossza.

![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Nyissa meg a **Beállítások** ablaktáblát a jobb oldali menüsor használatával. Válassza az **Eszközregisztráció** lehetőséget **a Munkahelyi & Iskolai fiókok csoportban.**
 
 ![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Ha erre a gombra kattint, a rendszer megkéri, hogy engedélyezze az eszköznévjegyekhez való hozzáférést. Ez annak köszönhető, hogy az Android fiókintegrációja az eszközön. Válassza az **Allow lehetőséget.**

 ![Hitelesítő fiók hozzáadása képernyő](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

A felhőeszköz-rendszergazdának meg kell adnia a szervezeti e-mail-címét a **Vagy regisztráljon megosztott eszközként**területen. Ezután kattintson a **regiszter megosztott eszközként** gombra, és adja meg a hitelesítő adatait.

![regisztráló készülék képernyője](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

Az eszköz most megosztott módban van.

![regisztráló készülék képernyője](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Az eszközön lévő bejelentkezések és kijelentkezések globálisak lesznek, ami azt jelenti, hogy az eszközön az MSAL-szal és a Microsoft Authenticator-ral integrált összes alkalmazásra vonatkoznak. Most már telepítheti az alkalmazásokat az eszközre, amelyek megosztott eszköz módú funkciókat használnak.

## <a name="view-the-shared-device-in-the-azure-portal"></a>A megosztott eszköz megtekintése az Azure Portalon

Miután megosztott módban helyezte el az eszközt, ismertté válik a szervezet számára, és a szervezeti bérlő nyomon követi. A megosztott eszközöket az Azure-portál Azure Active Directory paneljén található **Join Type** megtekintésével tekintheti meg.

![Az Azure Portal összes eszközpanelje](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>A mintaalkalmazás futtatása

A mintaalkalmazás egy egyszerű alkalmazás, amely meghívja a szervezet Graph API-ját. n első futtatáskor a rendszer kérni fogja a beleegyezést, mivel az alkalmazás új az alkalmazotti fiókjában.

![Alkalmazáskonfigurációs információs képernyő](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>További lépések

További információ a megosztott módról az [Android-eszközök megosztott eszköz módban](shared-device-mode.md)