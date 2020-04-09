---
title: Egy és több fiókos nyilvános ügyfélalkalmazások | Azure
description: Egy és több fiókos nyilvános ügyfélalkalmazások áttekintése.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881350"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Egy és több fiókos nyilvános ügyfélalkalmazások

Ez a cikk segít megérteni az egy és több fiókos nyilvános ügyfélalkalmazásokban használt típusokat, amelyek az egyfiókos nyilvános ügyfélalkalmazásokra összpontosítanak. 

Az Azure Active Directory hitelesítési könyvtár (ADAL) modellezi a kiszolgálót.  A Microsoft authentication library (MSAL) ehelyett az ügyfélalkalmazást modellezi.  Az Android-alkalmazások többsége nyilvános ügyfélnek minősül. A nyilvános ügyfél olyan alkalmazás, amely nem tud biztonságosan titkot tartani.  

Az MSAL az API `PublicClientApplication` felületét úgy specializálja, hogy egyszerűsítse és tisztázza az olyan alkalmazások fejlesztési élményét, amelyek egyszerre csak egy fiókot engedélyeznek. `PublicClientApplication`alosztályba sorolta a és `SingleAccountPublicClientApplication` `MultipleAccountPublicClientApplication`a alosztályt.  Az alábbi ábra az osztályok közötti kapcsolatot mutatja be.

![SingleAccountPublicClientApplication UML osztálydiagram](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Egyfiókos nyilvános ügyfélalkalmazás

Az `SingleAccountPublicClientApplication` osztály lehetővé teszi, hogy hozzon létre egy MSAL-alapú alkalmazás, amely csak lehetővé teszi, hogy egy fiókot kell bejelentkezett egy időben. `SingleAccountPublicClientApplication`eltér a `PublicClientApplication` következő módoktól:

- Az MSAL nyomon követi az aktuálisan bejelentkezett fiókot.
  - Ha az alkalmazás egy közvetítőt használ (az alapértelmezett az Azure Portal alkalmazás regisztrációja során), és telepítve van egy olyan eszközön, ahol egy közvetítő van jelen, az MSAL ellenőrzi, hogy a fiók továbbra is elérhető-e az eszközön.
- `signIn`Lehetővé teszi, hogy a fiókba explicit módon és a gyűjtőkörök kérésétől elkülönítve jelentkezzen be.
- `acquireTokenSilent`nem igényel fiókparamétert.  Ha megad egy fiókot, és a megadott fiók nem egyezik `MsalClientException` meg az MSAL által nyomon követett folyószámlával, az an eldobásra kerül.
- `acquireToken`nem teszi lehetővé a felhasználó számára a fiókok közötti váltást. Ha a felhasználó megpróbál másik fiókra váltani, kivétel történik.
- `getCurrentAccount`eredményobjektumot ad vissza, amely a következőket biztosítja:
  - Logikai érték, amely jelzi, hogy a fiók megváltozott-e. Egy fiók megváltozhat például az eszközről való eltávolítás következtében.
  - Az előző számla. Ez akkor hasznos, ha helyi adatok karbantartását kell végeznie, amikor a fiókot eltávolítják az eszközről, vagy amikor új fiók van bejelentkezve.
  - A currentAccount.
- `signOut`eltávolítja az ügyfélhez társított tokeneket az eszközről.  

Ha egy Android-hitelesítési bróker, például a Microsoft Authenticator vagy az Intune Company `signOut` Portal telepítve van az eszközön, és az alkalmazás a közvetítő használatára van konfigurálva, nem távolítja el a fiókot az eszközről.

## <a name="single-account-scenario"></a>Egyfiókos forgatókönyv

A következő pszeudokód a használatát mutatja be. `SingleAccountPublicClientApplication`

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

## <a name="multiple-account-public-client-application"></a>Többfiókos nyilvános ügyfélalkalmazás

Az `MultipleAccountPublicClientApplication` osztály msal-alapú alkalmazások létrehozására szolgál, amelyek lehetővé teszik több fiók egyidejű bejelentkezését. Ez lehetővé teszi, hogy a fiókokat az alábbiak szerint kapja hozzá, adja hozzá és távolítsa el:

### <a name="add-an-account"></a>Fiók hozzáadása

Használjon egy vagy több fiókot az alkalmazásban `acquireToken` egy vagy több alkalommal.  

### <a name="get-accounts"></a>Fiókok beszereznie

- Hívjon, `getAccount` hogy egy adott fiókot.
- Hívás `getAccounts`az alkalmazás által jelenleg ismert fiókok listájának lehívásához.

Az alkalmazás nem lesz képes számba venni az összes Microsoft-identitás platform fiók az eszközön ismert a közvetítő alkalmazás. Csak az alkalmazás által használt fiókokat tudja számba venni.  Az eszközről eltávolított fiókokat ezek a függvények nem adják vissza.

### <a name="remove-an-account"></a>Fiók eltávolítása

Fiók eltávolítása `removeAccount` fiókazonosítóval.

Ha az alkalmazás úgy van beállítva, hogy egy bróker, és egy bróker telepítve van az `removeAccount`eszközön, a számla nem törlődik a bróker, amikor hívja .  Csak az ügyfélhez társított tokenek törlődnek.

## <a name="multiple-account-scenario"></a>Többfiókos forgatókönyv

A következő pszeudokód bemutatja, hogyan hozhat létre többfiókos alkalmazást, hogyan listázza a fiókokat az eszközön, és hogyan szerezhet be jogkivonatokat.

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
