---
title: JavaScript-minták az Azure Active Directory B2C használata az |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C JavaScript használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4a8d036ff2f36b75df17029c8f00edce25c49e65
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994317"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-minták az Azure Active Directory B2C használata

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C-alkalmazásokat a saját ügyféloldali JavaScript-kódot adhat hozzá. Ez a cikk bemutatja, hogyan lehet módosítani a [egyéni házirendet](active-directory-b2c-overview-custom.md) parancsfájl végrehajtásának engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

Válassza ki a [lap szerződés](page-contract.md) a felhasználói felületi elemek az alkalmazás számára. Ha szeretne használni a JavaScript, egy lap szerződés verziót a tartalom-definíciók minden definiálása az egyéni házirendek szeretné.

## <a name="add-the-scriptexecution-element"></a>Adja hozzá a ScriptExecution elemet

Engedélyezi a parancsfájl végrehajtása hozzáadásával a **ScriptExecution** elem a [RelyingParty](relyingparty.md) elemet.

1. Nyissa meg az egyéni házirend-fájlt. Ha például *SignUpOrSignin.xml*.
2. Adja hozzá a **ScriptExecution** elem a **UserJourneyBehaviors** eleme **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Mentse, és töltse fel a fájlt.

## <a name="guidelines-for-using-javascript"></a>Útmutató a JavaScript használatával

Ha testre szabja a felületén az alkalmazás JavaScript használatával, kövesse az alábbi irányelveket:

- Egy kattintással esemény nem kötési `<a>` HTML-elemek. 
- Nem kell egy függőség az Azure AD B2C-kód vagy a megjegyzéseket.
- Ne módosítsa, sorrend, illetve az Azure AD B2C-vel HTML-elemek hierarchiájának. Egy Azure AD B2C-szabályzat segítségével szabályozhatja a felhasználói felületi elemek sorrendjét.
- E szempontok bármely RESTful szolgáltatás hívása:
    - Szükség lehet a CORS-t lehetővé teszi a HTTP-hívások ügyféloldali RESTful szolgáltatás beállítása.
    - Ellenőrizze, hogy a RESTful szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja a JavaScript közvetlenül az Azure AD B2C-végpontokat.
- Ágyazható be a JavaScript, vagy JavaScript-fájlok külső kapcsolat. Egy külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-címet, és nem egy relatív URL-címet használja.
- JavaScript-keretrendszer:
    - Az Azure AD B2C jQuery egy adott verzióját használja. Nem tartalmazza a jQuery egy másik verziója. Egynél több verziója ugyanazon az oldalon rendelkezik, problémákat.
    - RequireJS használata nem támogatott.
    - A legtöbb JavaScript-keretrendszerek nem támogatottak az Azure AD B2C-t.
- Az Azure AD B2C-beállítások meghívásával olvasható `window.SETTINGS`, `window.CONTENT` objektumok, például a jelenlegi felhasználói felületi nyelven. Ezek az objektumok értékét ne módosítsa.
- Az Azure AD B2C-vel hibaüzenet egyéni beállításához használja a honosítás egy házirendben.
- Ha bármi házirend segítségével biztosíthatja, általában, az ajánlott módszer a.

## <a name="javascript-samples"></a>JavaScript-minták

### <a name="show-or-hide-a-password"></a>Megjelenítése vagy elrejtése a jelszóval

Egy közös segít az ügyfeleknek a regisztráció sikeres módja, hogy mi ezek adta a jelszavát, megtekintheti őket. A funkció lehetővé teszi a felhasználók regisztráljanak engedélyezésével, egyszerűen és a jelszavát, ha a szükséges javításokat. Bármely mező, írja be a jelszó tartozik egy jelölőnégyzet, az egy **Show jelszó** címkét.  Ez lehetővé teszi a felhasználó számára a jelszót egyszerű szövegként. Ez a kódrészlet a regisztrálási vagy bejelentkezési sablonhoz önellenőrzött laphoz a következők:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Használati feltételek hozzáadása 

A következő kódot tartalmazza a lapra, ahol fel szeretne venni egy **használati** jelölőnégyzetet. Ezt a jelölőnégyzetet az helyi fiók regisztrációs és a közösségi fiók regisztrációs lapok általában szükség lesz.  

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }
      
    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;
      
    // create a new <a> element with the same inner text 
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element 
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Cserélje le a kód `terms-of-use-url` a használati feltételek a hivatkozást tartalmazó. A címtárban, hozzon létre egy új felhasználói attribútum nevű **termsOfUse** , majd belefoglalhat **termsOfUse** a felhasználói folyamat felhasználói attribútumaként.

## <a name="next-steps"></a>További lépések

További információ az alkalmazások felhasználói felületének testreszabását [egyéni szabályzat használata az Azure Active Directory B2C az alkalmazás a felhasználói felület testreszabása](active-directory-b2c-ui-customization-custom.md).

