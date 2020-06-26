---
title: JavaScript-minták
titleSuffix: Azure AD B2C
description: A JavaScript használatának megismerése Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a92810841ee5d656b23105d6f40c763847971c4f
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85387796"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-minták a Azure Active Directory B2C való használatra

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

    Ha a JavaScriptet kívánja használni, meg kell [adnia egy](contentdefinitions.md#migrating-to-page-layout) lapelrendezés-verziót, amely az `contract` Egyéni szabályzat *összes* tartalmi definíciójának megfelelő verziójú.

## <a name="add-the-scriptexecution-element"></a>A ScriptExecution elem hozzáadása

A szkriptek végrehajtásának engedélyezéséhez adja hozzá a **ScriptExecution** elemet a [RelyingParty](relyingparty.md) elemhez.

1. Nyissa meg az egyéni házirend-fájlt. Például *SignUpOrSignin.xml*.
2. Adja hozzá a **ScriptExecution** elemet a **RelyingParty** **UserJourneyBehaviors** eleméhez:

    ```xml
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

A kódban cserélje le a `termsOfUseUrl` kifejezést a használati feltételekre vonatkozó szerződésre mutató hivatkozásra. A címtárhoz hozzon létre egy **termsOfUse** nevű új felhasználói attribútumot, majd adja hozzá a **termsOfUse** felhasználói attribútumként.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával az Azure Active Directory B2C egyéni házirendjének használatával](custom-policy-ui-customization.md).
