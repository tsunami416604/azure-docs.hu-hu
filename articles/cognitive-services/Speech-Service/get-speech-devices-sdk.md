---
title: Beszédeszközök SDK letöltése
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatások együttműködve számos hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. Ebben a cikkben fogja elsajátíthatja a beszédfelismerési Devices SDK-val való hozzáférést, és kezdje el a fejlesztést.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3c5874625ee9d1932c401238c1586ad89d5d206d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856722"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédfelismerő eszközök SDK beszerzése

A beszédfelismerés Devices SDK-val a korlátozott előzetes verzióban érhető el, és megköveteli, hogy a programba. Jelenleg a Microsoft a termékre hozzáférés csatolásával nagy vállalatok részesíti előnyben.

## <a name="request-access"></a>Hozzáférés kérése

A beszédfelismerés Devices SDK-val eléréséhez:

1. Nyissa meg a Microsoft beszédfelismerési Devices SDK-val [bejelentkezési űrlap kitöltése](https://aka.ms/sdsdk-signup).
1. Olvassa el a [licencszerződésének](speech-devices-sdk-license.md).
1. Ha elfogadja a licencszerződés feltételeit, jelölje be **elfogadom**.
1. Válaszoljon a kérdésekre, a képernyőn.
1. A kérdőív beküldése.
1. Ha az e-mail-címét még nem része az Azure Active Directory (Azure AD), az alábbi példához hasonlóan meghívó e-mail kap hozzáférés jóváhagyásakor van. Ha az e-mail cím már az Azure ad-ben, kap egy e-mailt a Microsoft Speech-csapattól, még a hozzáféréshez jóváhagyott, és áttérhet a [töltse le a Speech Devices SDK-val](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Jóváhagyási e-mail

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![e-mail-üzenet](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Fogadja el a hozzáférést

A következő lépéseket az Azure AD a regisztráció során megadott e-mail-címmel csatlakozzon. Ez a folyamat a Speech Devices SDK-val hozzáférést biztosít [letöltőwebhelyéről](https://shares.datatransfer.microsoft.com/).

1. Válassza ki az e-mail-üzenetet kapott **Ismerkedés**. Ha a szervezet már Office 365-felhasználó, jelentkezzen be a rendszer kéri, és áttérhet a 7. lépés.

2. A böngészőben megjelenő ablakban válassza **tovább**.

    ![hitelesítési ablak](media/speech-devices-sdk/get-sdk-2.png)

3. Ha még nem rendelkezik egy Microsoft-fiók létrehozásához. Adja meg ugyanazt az e-mail címet, amelyen a meghívó e-mailben kapott.

    ![Microsoft-fiók regisztrálása](media/speech-devices-sdk/get-sdk-3.png)

4. Válassza ki **tovább** jelszót.

5. Amikor a rendszer kéri, ellenőrizze az e-mailek, az ellenőrző kód le a meghívó e-mailben kapott.

7. Illessze be, vagy írja be a biztonsági kódot az e-mail-üzenet, a párbeszédpanel bezárásához. Ebben a példában a biztonsági kódot az **8406**. Kattintson a **Tovább** gombra.

    ![E-mail cím visszaigazolása](media/speech-devices-sdk/get-sdk-6.png)

8. A hozzáférési Panel alkalmazás a böngészőben jelenik meg, ha meggyőződött róla, hogy az e-mail-címét az Azure AD részét képezi. Most már elérhetők a Speech eszközök SDK letöltési helyre.

## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK letöltése

Nyissa meg a [Speech eszközök SDK letöltési hely](https://shares.datatransfer.microsoft.com/). Jelentkezzen be a korábban létrehozott Microsoft-fiókjával.

![SDK-letöltési helye](media/speech-devices-sdk/get-sdk-7.png)

Töltse le a Speech tartozó SDK-t és eszközöket mintakód és referenciaanyagok:

1. Töltse le és telepítse az Aspera Connect eszközzel, amikor a rendszer kéri a böngészőben.

    ![Töltse le az Aspera-csatlakozás](media/speech-devices-sdk/get-sdk-8.png)

1. Válassza ki **Igen** Aspera Connect alkalmazások váltani.

    ![Váltson át az Aspera-csatlakozás](media/speech-devices-sdk/get-sdk-9.png)

1. Válassza ki **engedélyezése** Aspera Connect használatával a fájlok letöltését.

    ![Töltse le az Aspera-csatlakozás](media/speech-devices-sdk/get-sdk-10.png)

1. Az Aspera csatlakozás adatátvitel ablak bezárásához, a fájlok letöltése után.

    ![Aspera csatlakozás adatátvitel ablak](media/speech-devices-sdk/get-sdk-11.png)

Alapértelmezés szerint a fájlokat a rendszer letölti a **letölti** mappát. Akkor is jelentkezzen ki a hely most.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszédfelismerés eszközök SDK használatának első lépései](speech-devices-sdk-qsg.md)
