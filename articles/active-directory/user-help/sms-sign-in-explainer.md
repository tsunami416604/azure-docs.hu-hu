---
title: SMS bejelentkezési felhasználói élmény telefonszámhoz (előzetes verzió) – Azure AD
description: További információ az SMS bejelentkezési felhasználói élményéről új vagy meglévő telefonszámok esetén
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378833"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Telefonszám használata felhasználónévként (előzetes verzió)

Az eszköz regisztrálása lehetővé teszi a telefon hozzáférését a szervezet szolgáltatásaihoz, és nem engedélyezi a szervezete számára a telefon elérését. Ha Ön rendszergazda, további információt a [felhasználók konfigurálása és engedélyezése SMS-alapú hitelesítéshez](../authentication/howto-authentication-sms-signin.md)című témakörben talál.

Ha a szervezet még nem tette elérhetővé az SMS-alapú bejelentkezést, akkor nem fog megjelenni, amikor regisztrál egy telefont a fiókjával.  

## <a name="when-you-have-a-new-phone-number"></a>Ha van új telefonszáma

Ha új telefonszámot vagy új számot kap, és regisztrálja azt egy olyan szervezettel, amely számára elérhető az SMS-bejelentkezés, akkor a telefon normál regisztrációs folyamatát tapasztalja:

1. Válassza a **hozzáadási módszer**lehetőséget.
1. Válassza a **telefon**lehetőséget.
1. Adja meg a telefonszámot, és válassza ki **a kód szövegét**.
1. A kód beírása után válassza a **tovább**lehetőséget.
1. Megjelenik egy üzenet, amely szerint a rendszer SMS-ben ellenőrizte a következőt:. A telefon regisztrálása sikeresen megtörtént. "

> [!Important]
> Az előzetes verzió ismert hibája miatt rövid idő alatt a telefonszám hozzáadása nem regisztrálja az SMS-bejelentkezés számát. Be kell jelentkeznie a hozzáadott számmal, majd az utasításokat követve regisztrálnia kell a számot az SMS-bejelentkezéshez.

### <a name="when-the-phone-number-is-in-use"></a>Ha a telefonszám használatban van

Ha olyan telefonszámot próbál használni, amelyet a szervezetben valaki más használ, a következő üzenet jelenik meg:

![Hibaüzenet, ha a telefonszáma már használatban van](media/sms-sign-in-explainer/sms-sign-in-error.png)

A probléma megoldásához forduljon a rendszergazdához.

## <a name="when-you-have-an-existing-number"></a>Ha van meglévő száma

Ha már használ telefonszámot egy szervezettel, és a telefonszámát használja a Felhasználónév elérhetővé válik, a következő lépések segíthetnek a bejelentkezésben.

1. Ha az SMS-bejelentkezés elérhető, megjelenik egy szalagcím, amely arra kéri, hogy engedélyezze a telefonszámot az SMS-bejelentkezéshez:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Emellett az **enable (Engedélyezés** ) gomb is megjelenik, ha a kurzort a telefonos módszer csempén választja:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. A metódus engedélyezéséhez válassza az **Engedélyezés**lehetőséget. A rendszer felszólítja, hogy erősítse meg a műveletet:

    ![Megerősítő párbeszédpanel, amely lehetővé teszi az SMS-bejelentkezést a telefonszámhoz](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Válassza az **Engedélyezés** lehetőséget.

## <a name="when-you-remove-your-phone-number"></a>A telefonszám eltávolításakor

1. A telefonszám törléséhez kattintson a Delete (Törlés) gombra az SMS bejelentkezési telefon módszer csempén.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Amikor a rendszer kéri, hogy erősítse meg a műveletet, kattintson **az OK gombra**.

Az alapértelmezett bejelentkezési módszerként használt telefonszám nem távolítható el. A szám eltávolításához módosítania kell az alapértelmezett bejelentkezési módszert, majd újra el kell távolítania a telefonszámot.
