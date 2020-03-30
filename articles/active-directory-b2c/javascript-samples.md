---
title: JavaScript-minták
titleSuffix: Azure AD B2C
description: Ismerje meg a JavaScript használatát az Azure Active Directory B2C-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187661"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-minták az Azure Active Directory B2C-ben való használatra

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Saját JavaScript ügyféloldali kódot adhat hozzá az Azure Active Directory B2C (Azure AD B2C) alkalmazásokhoz.

A JavaScript engedélyezése az alkalmazásokhoz:

* Elem hozzáadása az [egyéni házirendhez](custom-policy-overview.md)
* [Oldalelrendezés kijelölése](page-layout.md)
* [A b2clogin.com](b2clogin.md) használata a kérésekben

Ez a cikk azt ismerteti, hogyan módosíthatja az egyéni házirendet a parancsfájlok végrehajtásának engedélyezéséhez.

> [!NOTE]
> Ha engedélyezni szeretné a JavaScriptet a felhasználói folyamatokhoz, olvassa el [a JavaScript és a lapelrendezés verziói című témakört az Azure Active Directory B2C-ben.](user-flow-javascript-overview.md)

## <a name="prerequisites"></a>Előfeltételek

### <a name="select-a-page-layout"></a>Oldalelrendezés kijelölése

* Válassza ki az alkalmazás felhasználói felületének elemeinek [lapelrendezését.](contentdefinitions.md#select-a-page-layout)

    Ha JavaScriptet kíván használni, meg kell [határoznia egy lapelrendezési verziót](contentdefinitions.md#migrating-to-page-layout) az egyéni házirend `contract` *összes* tartalomdefiníciójának verzióverziójával.

## <a name="add-the-scriptexecution-element"></a>A ScriptExecution elem hozzáadása

A parancsfájl-végrehajtásengedélyezéséhez adja hozzá a **ScriptExecution** elemet a [RelyingParty](relyingparty.md) elemhez.

1. Nyissa meg az egyéni házirendfájlt. Például *SignUpOrSignin.xml*.
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

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript-minták

### <a name="show-or-hide-a-password"></a>Jelszó megjelenítése vagy elrejtése

Az ügyfelek nek a regisztráció sikerességében való segítésének gyakori módja, ha lehetővé teszi számukra, hogy megtekinthessék, mit adtak meg jelszóként. Ez a beállítás segít a felhasználóknak a regisztrációban azáltal, hogy lehetővé teszi számukra, hogy szükség esetén könnyen láthassák és javíthassák a jelszavukat. Minden jelszótípusú mezőnek van egy **jelölőnégyzetje** a Jelszó megjelenítése címkével.  Ez lehetővé teszi, hogy a felhasználó egyszerű szövegként lássa a jelszót. A kódrészlet felvétele a regisztrációs vagy bejelentkezési sablonba egy önérvényesítő laphoz:

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

Adja meg a következő kódot az oldalába, ahol a **Használati feltételek** jelölőnégyzetet szeretné megadni. Erre a jelölőnégyzetre általában a helyi fiók regisztrációs és közösségi fiókregisztrációs oldalain van szükség.

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

A kódban `termsOfUseUrl` cserélje le a használati feltételekre mutató hivatkozást. A címtárhoz hozzon létre egy új felhasználói attribútumot, a **"termsOfUse"-t,** majd adja meg **a termsOfUse-t** felhasználói attribútumként.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan szabhatja testre az alkalmazások felhasználói felületét [az alkalmazás felhasználói felületének testreszabása az Azure Active Directory B2C egyéni házirendjével című részében.](custom-policy-ui-customization.md)
