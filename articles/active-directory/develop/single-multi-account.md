---
title: Egy-és több fiók nyilvános ügyfélszoftvere | Azure
description: Egy és több fiókból álló nyilvános ügyfélalkalmazások áttekintése.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae798c6108ec78b92b1ee6ac167b01c2f72c26d9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679710"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Egy-és több fiók nyilvános ügyfélalkalmazások

Ez a cikk segítséget nyújt az egy-és többfiókos nyilvános ügyfélprogramokban használt típusok megismerésében, és az egyfiókos nyilvános ügyfélalkalmazások használatára koncentrál. 

A Azure Active Directory Authentication Library (ADAL) a kiszolgálót modellezi.  A Microsoft Authentication Library (MSAL) Ehelyett az ügyfélalkalmazás modelljét használja.  Az Android-alkalmazások többsége nyilvános ügyfélnek számít. A nyilvános ügyfél olyan alkalmazás, amely nem tudja biztonságosan megőrizni a titkos kulcsot.  

A MSAL a `PublicClientApplication` API-felületét specializálja az olyan alkalmazások fejlesztési élményének egyszerűsítésére és tisztázására, amelyekben egyszerre csak egy fiókot lehet használni. @no__t – 0 `SingleAccountPublicClientApplication` és `MultipleAccountPublicClientApplication`.  Az alábbi ábrán az osztályok közötti kapcsolat látható.

![SingleAccountPublicClientApplication UML-osztály diagramja](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Egy fiókhoz tartozó nyilvános ügyfélalkalmazás

A @no__t 0 osztály lehetővé teszi, hogy olyan MSAL-alapú alkalmazást hozzon létre, amely egyszerre csak egyetlen fiók bejelentkezni. a `SingleAccountPublicClientApplication` a következő módokon tér el az `PublicClientApplication` értéktől:

- A MSAL nyomon követi a jelenleg bejelentkezett fiókot.
  - Ha az alkalmazás egy közvetítőt használ (az alapértelmezett Azure Portal az alkalmazás regisztrációja során), és olyan eszközre van telepítve, amelyen a közvetítő található, akkor a MSAL ellenőrzi, hogy a fiók továbbra is elérhető-e az eszközön.
- a `signIn` lehetővé teszi, hogy a fiókokat explicit módon, a kérelmeket pedig a kérelmektől függetlenül jelentkezzen be.
- a `acquireTokenSilent` nem igényel fiók paramétert.  Ha megadta a fiókot, és az Ön által megadott fiók nem egyezik meg a MSAL által követett aktuális fiókkal, akkor a rendszer egy `MsalClientException` értéket ad meg.
- @no__t – 0 nem engedélyezi a felhasználó számára a fiókok váltását. Ha a felhasználó egy másik fiókra próbál váltani, kivétel keletkezik.
- a `getCurrentAccount` egy eredmény objektumot ad vissza, amely a következőket biztosítja:
  - Logikai érték, amely azt jelzi, hogy a fiók módosult-e. Előfordulhat például, hogy egy fiók az eszközről való eltávolításának eredményeképpen módosul.
  - A korábbi fiók. Ez akkor lehet hasznos, ha a fiókot eltávolítja az eszközről, vagy amikor új fiók van bejelentkezve.
  - A currentAccount.
- @no__t – 0 – eltávolítja az ügyfélhez társított jogkivonatokat az eszközről.  

Ha egy androidos hitelesítési közvetítő, például Microsoft Authenticator vagy Intune Céges portál telepítve van az eszközön, és az alkalmazás a közvetítő használatára van konfigurálva, `signOut` nem távolítja el a fiókot az eszközről.

## <a name="single-account-scenario"></a>Egyetlen fiókra vonatkozó forgatókönyv

A következő pszeudo-kód a `SingleAccountPublicClientApplication` használatát mutatja be.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Több fiók nyilvános ügyfélalkalmazás

A `MultipleAccountPublicClientApplication` osztály olyan MSAL-alapú alkalmazások létrehozására szolgál, amelyek lehetővé teszik, hogy egyszerre több fiókot lehessen bejelentkezni. Lehetővé teszi a fiókok beszerzését, hozzáadását és eltávolítását a következőképpen:

### <a name="add-an-account"></a>Új fiók felvétele

Használjon egy vagy több fiókot az alkalmazásban a `acquireToken` egy vagy több alkalommal történő meghívásával.  

### <a name="get-accounts"></a>Fiókok beolvasása

- Egy adott fiók beszerzéséhez hívja meg a `getAccount` értéket.
- Hívja meg a `getAccounts`to lekérdezi az alkalmazás által jelenleg ismert fiókok listáját.

Az alkalmazás nem tudja felsorolni az összes Microsoft Identity platform-fiókot az eszközön, amely a közvetítő alkalmazás számára ismert. Csak azokat a fiókokat sorolja fel, amelyeket az alkalmazás használ.  Az eszközről eltávolított fiókokat ezek a függvények nem adják vissza.

### <a name="remove-an-account"></a>Fiók eltávolítása

Fiók azonosítójának meghívásával távolítsa el a fiókot `removeAccount`.

Ha az alkalmazás egy közvetítő használatára van konfigurálva, és egy közvetítő van telepítve az eszközön, akkor a rendszer a fiókot nem távolítja el a közvetítőből a `removeAccount` hívásakor.  A rendszer csak az ügyfélhez társított jogkivonatokat távolítja el.

## <a name="multiple-account-scenario"></a>Több fiók esetén

A következő pszeudo-kód bemutatja, hogyan hozhat létre több fiókból álló alkalmazást, hogyan listázhatja a fiókokat az eszközön, és hogyan vásárolhat jogkivonatokat.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
