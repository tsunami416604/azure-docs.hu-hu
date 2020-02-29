---
title: JavaScript-minták
titleSuffix: Azure AD B2C
description: Ismerje meg az Azure Active Directory B2C JavaScript használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187661"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-minták az Azure Active Directory B2C használata

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure Active Directory B2C (Azure AD B2C) alkalmazásaihoz saját JavaScript-ügyféloldali kódot adhat hozzá.

JavaScript engedélyezése az alkalmazásokhoz:

* Elem hozzáadása az [Egyéni szabályzathoz](custom-policy-overview.md)
* [Lapelrendezés kiválasztása](page-layout.md)
* [B2clogin.com](b2clogin.md) használata a kérésekben

Ez a cikk azt ismerteti, hogyan módosíthatja az egyéni házirendet a parancsfájlok végrehajtásának engedélyezéséhez.

> [!NOTE]
> Ha engedélyezni szeretné a JavaScriptet a felhasználói folyamatokhoz, tekintse [meg a JavaScript-és lapelrendezés-verziókat a Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="select-a-page-layout"></a>Lapelrendezés kiválasztása

* Válassza ki az alkalmazás felhasználói felületi elemeinek [lapelrendezés](contentdefinitions.md#select-a-page-layout) elemet.

    Ha a JavaScriptet szeretné használni, meg kell [adnia egy](contentdefinitions.md#migrating-to-page-layout) lapelrendezés-verziót, amely az egyéni szabályzat *összes* tartalmi definíciójának `contract` verzióját használja.

## <a name="add-the-scriptexecution-element"></a>Adja hozzá a ScriptExecution elemet

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
3. Mentse, és töltse fel a fájlt.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript-minták

### <a name="show-or-hide-a-password"></a>Megjelenítése vagy elrejtése a jelszóval

Egy közös segít az ügyfeleknek a regisztráció sikeres módja, hogy mi ezek adta a jelszavát, megtekintheti őket. A funkció lehetővé teszi a felhasználók regisztráljanak engedélyezésével, egyszerűen és a jelszavát, ha a szükséges javításokat. A jelszó típusú mezőknél megjelenik egy jelölőnégyzet a **jelszó megjelenítése** címkével.  Ez lehetővé teszi a felhasználó számára a jelszót egyszerű szövegként. Ez a kódrészlet a regisztrálási vagy bejelentkezési sablonhoz önellenőrzött laphoz a következők:

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

Adja meg a következő kódot az oldalra, ahol használni szeretné a használati **feltételeket** jelölőnégyzetet. Ezt a jelölőnégyzetet az helyi fiók regisztrációs és a közösségi fiók regisztrációs lapok általában szükség lesz.

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

## <a name="next-steps"></a>További lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával az Azure Active Directory B2C egyéni házirendjének használatával](custom-policy-ui-customization.md).
