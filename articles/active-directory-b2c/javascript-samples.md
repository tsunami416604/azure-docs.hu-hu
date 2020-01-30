---
title: JavaScript-minták – Azure Active Directory B2C | Microsoft Docs
description: A JavaScript használatának megismerése Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42dc09ef4518bfda8c63ee183499b1b2e8c22991
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841931"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-minták a Azure Active Directory B2C való használatra

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure Active Directory B2C (Azure AD B2C) alkalmazásaihoz saját JavaScript-ügyféloldali kódot adhat hozzá. Az alkalmazásokhoz való JavaScript engedélyezéséhez hozzá kell adnia egy elemet az [Egyéni szabályzathoz](custom-policy-overview.md), ki kell választania egy [lapelrendezést](page-layout.md), és a [b2clogin.com](b2clogin.md) kell használnia a kérésekben. Ez a cikk azt ismerteti, hogyan módosíthatja az egyéni házirendet a parancsfájlok végrehajtásának engedélyezéséhez.

> [!NOTE]
> Ha engedélyezni szeretné a JavaScriptet a felhasználói folyamatokhoz, tekintse [meg a JavaScript-és lapelrendezés-verziókat a Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="select-a-page-layout"></a>Lapelrendezés kiválasztása

* [Válassza ki](page-layout.md) az alkalmazás felhasználói felületi elemeinek lapelrendezés elemet.

    Ha a JavaScriptet szeretné használni, meg kell [adnia egy](page-layout.md#replace-datauri-values) lapelrendezés-verziót az egyéni szabályzat *összes* tartalmi definíciójában.

## <a name="add-the-scriptexecution-element"></a>A ScriptExecution elem hozzáadása

A szkriptek végrehajtásának engedélyezéséhez adja hozzá a **ScriptExecution** elemet a [RelyingParty](relyingparty.md) elemhez.

1. Nyissa meg az egyéni házirend-fájlt. Például: *SignUpOrSignin. XML*.
2. Adja hozzá a **ScriptExecution** elemet a **RelyingParty** **UserJourneyBehaviors** eleméhez:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Mentse és töltse fel a fájlt.

## <a name="guidelines-for-using-javascript"></a>Útmutató a JavaScript használatához

Kövesse az alábbi irányelveket, amikor testreszabja az alkalmazás felületét a JavaScript használatával:

- `<a>` HTML-elemeken nem köthető kattintási esemény.
- Ne vegyen fel függőséget Azure AD B2C kód vagy Megjegyzés alapján.
- Ne módosítsa Azure AD B2C HTML-elemek sorrendjét vagy hierarchiáját. A felhasználói felületi elemek sorrendjének szabályozásához használjon Azure AD B2C szabályzatot.
- A következő megfontolásokkal hívhat meg bármilyen REST-szolgáltatást:
    - Előfordulhat, hogy az ügyféloldali HTTP-hívások engedélyezéséhez be kell állítania a REST szolgáltatás CORS.
    - Győződjön meg arról, hogy a REST szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja közvetlenül a JavaScriptet Azure AD B2C végpontok meghívásához.
- Beágyazhatja a JavaScriptet, vagy külső JavaScript-fájlokra mutató hivatkozásokat is használhat. Külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-címet használja, nem pedig relatív URL-címet.
- JavaScript-keretrendszerek:
    - Azure AD B2C a jQuery egy adott verzióját használja. Ne adja meg a jQuery egy másik verzióját. Ha egynél több verziót használ ugyanazon a lapon, problémákat okoz.
    - A RequireJS használata nem támogatott.
    - Azure AD B2C nem támogatja a legtöbb JavaScript-keretrendszert.
- Azure AD B2C beállítások a `window.SETTINGS`, `window.CONTENT` objektumok, például az aktuális felhasználói felület nyelvének meghívásával olvashatók. Ne módosítsa az objektumok értékét.
- A Azure AD B2C-hibaüzenet testreszabásához használjon honosítást egy házirendben.
- Ha bármilyen adat egy szabályzat használatával érhető el, általában az ajánlott módszer.

## <a name="javascript-samples"></a>JavaScript-minták

### <a name="show-or-hide-a-password"></a>Jelszó megjelenítése vagy elrejtése

Az ügyfelek számára a regisztráció sikerességének egyik gyakori módja, hogy meglássuk, mit adtak meg a jelszavuk. Ezzel a beállítással a felhasználók regisztrálhatnak, így egyszerűen megtekinthetik és módosíthatják a jelszavukat, ha szükséges. A jelszó típusú mezőknél megjelenik egy jelölőnégyzet a **jelszó megjelenítése** címkével.  Ez lehetővé teszi, hogy a felhasználó egyszerű szövegként tekintse meg a jelszót. Adja meg ezt a kódrészletet a regisztrációs vagy bejelentkezési sablonban egy önérvényesített oldal számára:

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

Adja meg a következő kódot az oldalra, ahol használni szeretné a használati **feltételeket** jelölőnégyzetet. Ezt a jelölőnégyzetet általában a helyi fiók regisztrálásához és a közösségi fiók regisztrációs oldalain kell megadni.

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

A kódban cserélje le a `termsOfUseUrl` kifejezést a használati feltételekre mutató hivatkozással. A címtárhoz hozzon létre egy **termsOfUse** nevű új felhasználói attribútumot, majd adja hozzá a **termsOfUse** felhasználói attribútumként.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával az Azure Active Directory B2C egyéni házirendjének használatával](custom-policy-ui-customization.md).
