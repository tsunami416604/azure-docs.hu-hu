---
title: SMS-bejelentkezési felhasználói élmény telefonszámhoz (előzetes verzió) – Azure AD
description: További információ az új vagy meglévő telefonszámok SMS-bejelentkezési felhasználói élményéről
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378833"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>A telefonszám használata felhasználónévként (előzetes verzió)

Az eszköz regisztrálása hozzáférést biztosít a telefon számára a szervezet szolgáltatásaihoz, és nem teszi lehetővé a szervezet számára a hozzáférést a telefonhoz. Ha Ön rendszergazda, további információt az [SMS-alapú hitelesítés konfigurálása és engedélyezése](../authentication/howto-authentication-sms-signin.md)című részben talál.

Ha a szervezet nem tette elérhetővé az SMS-bejelentkezést, akkor nem jelenik meg a lehetőség, amikor telefont regisztrál a fiókjával.  

## <a name="when-you-have-a-new-phone-number"></a>Ha új telefonszámmal rendelkezik

Ha új telefont vagy új számot kap, és regisztrál egy olyan szervezetnél, amelyhez elérhető az SMS-bejelentkezés, a szokásos telefonregisztrációs folyamatot tapasztalhatja:

1. Válassza **a Metódus hozzáadása lehetőséget.**
1. Válassza **a Telefon**lehetőséget.
1. Írja be a telefonszámot, és válassza **a Szöveg nekem egy kódot**.
1. A kód megadása után válassza a **Tovább**gombot.
1. Megjelenik egy üzenet, amely azt mondja: "SMS ellenőrizve. A telefon regisztrálása sikeresen megtörtént."

> [!Important]
> Az előzetes verzió ban egy ismert probléma miatt a telefonszám hozzáadása rövid ideig nem regisztrálja az SMS-bejelentkezés számát. A hozzáadott számmal kell bejelentkeznie, majd az SMS-bejelentkezéshez a következő utasításokat követve kell regisztrálnia.

### <a name="when-the-phone-number-is-in-use"></a>Ha a telefonszám használatban van

Ha olyan telefonszámot próbál használni, amelyet a szervezet en valaki más használ, a következő üzenet jelenik meg:

![Hibaüzenet, ha a telefonszám már használatban van](media/sms-sign-in-explainer/sms-sign-in-error.png)

A probléma megoldása érdekében forduljon a rendszergazdához.

## <a name="when-you-have-an-existing-number"></a>Ha van egy meglévő száma

Ha már használ egy telefonszámot egy szervezetnél, és a telefonszámát használja felhasználónévként, a következő lépések segíthetnek a bejelentkezésben.

1. Ha elérhető az SMS-bejelentkezés, megjelenik egy szalagcím, amely megkérdezi, hogy engedélyezi-e az SMS-bejelentkezés telefonszámát:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Az **Engedélyezés** gomb akkor is megjelenik, ha a telefon metódusának csempéjén kiválasztja a kiápolót:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. A metódus engedélyezéséhez válassza az **Engedélyezés**lehetőséget. A program felszólítja a művelet megerősítésére:

    ![Megerősítési párbeszédpanel a telefonszámhoz való SMS-bejelentkezés engedélyezéséhez](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Válassza az **Engedélyezés** lehetőséget.

## <a name="when-you-remove-your-phone-number"></a>A telefonszám eltávolításakor

1. A telefonszám törléséhez kattintson a törlés gombra az SMS bejelentkezési telefon módszerének csempéjén.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Amikor a program a művelet megerősítésére kéri, kattintson az **OK gombra.**

Az alapértelmezett bejelentkezési módszerként használt telefonszámot nem távolíthatja el. A szám eltávolításához módosítania kell az alapértelmezett bejelentkezési módszert, majd újra el kell távolítania a telefonszámot.
