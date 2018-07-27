---
title: Beszédeszközök SDK letöltése
description: Ismerje meg, hogyan érheti el a Speech eszköz SDK-val.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: f70b41cd7e3a7a6eddf32ae6ad024fa9ac040f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281782"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédfelismerő eszközök SDK beszerzése

## <a name="requesting-access"></a>Hozzáférés kérése

A beszédfelismerés Devices SDK-val a korlátozott előzetes verzióban érhető el, és megköveteli, hogy a program regisztrálhatja. Jelenleg a Microsoft a termékre hozzáférés csatolásával nagy vállalatok részesíti előnyben.

A beszédfelismerés Devices SDK-val eléréséhez, kövesse az alábbi lépéseket:

1. Nyissa meg a Microsoft beszédfelismerési Devices SDK-val [bejelentkezési űrlap kitöltése](https://aka.ms/sdsdk-signup).
1. Olvassa el a [licencszerződésének](speech-devices-sdk-license.md).
1. Ha elfogadja a licencszerződés feltételeit, válassza az "Elfogadom".
1. Válaszoljon a kérdésekre, a képernyőn.
1. A kérdőív beküldése. 
1. Ha az e-mail-címét még nem része az Azure Active Directory, a jóváhagyást követően alább egy hasonló meghívó e-mail érkezik. Ha az Azure Active Directoryban már van az e-mail-címét, egy e-mailt kap a jóváhagyást követően a Microsoft Speech-csapattól, és, előfordulhat, hogy folytassa a [töltse le a Speech Devices SDK-val](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Jóváhagyási e-mail

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![e-mail-üzenet](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Fogadja el a hozzáférést
Hajtsa végre az alábbi lépéseket az Azure Active Directory, a regisztráció során megadott e-mail-címmel csatlakozni. Ez a folyamat a Speech eszközök SDK hozzáférést biztosít [letöltőwebhelyéről](https://shares.datatransfer.microsoft.com/).

1. Kattintson a **Ismerkedés** kapott e-mail-üzenetben. Ha a szervezet már Office 365-felhasználó, kéri a bejelentkezéshez, és előfordulhat, hogy folytassa a 8. lépés.

2. Kattintson a **tovább** az elindított böngészőablakban.

    ![hitelesítési ablak](media/speech-devices-sdk/get-sdk-2.png)

3. Ha még nem rendelkezik, hozzon létre egy Microsoft-fiók. Adja meg ugyanazt az e-mail címet, amelyen kapta a meghívást tartalmazó e-mail fenti 6. lépés.

    ![Microsoft-fiók létrehozása](media/speech-devices-sdk/get-sdk-3.png)

4. Kattintson a **tovább** jelszót.

5. Amikor a rendszer kéri, az e-mailek ellenőrzése, térjen vissza az e-mailben kapják az Önnek küldött ellenőrző kód beolvasásához.
 
7. Illessze be, vagy írja be a biztonsági kódot az e-mail-üzenet a párbeszédpanelen. Ebben a példában "8406." Ezután kattintson a **Next** (Tovább) gombra.

    ![e-mail cím ellenőrzése](media/speech-devices-sdk/get-sdk-6.png)
 
8. Amikor megjelenik a hozzáférési Panel alkalmazás a böngészőablakban, meggyőződött róla, hogy az e-mail-címét (a 6. lépés) már az Azure Active Directory részét. Most már elérhetők a Speech eszközök SDK letöltési helyre.

## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK letöltése

Nyissa meg a [Speech eszközök SDK letöltési hely](https://shares.datatransfer.microsoft.com/) és jelentkezzen be a korábban létrehozott Microsoft Account. Az alábbi lépéseket a Speech Devices SDK-val, kapcsolódó mintakód és referenciaanyag mostantól letölthető.

![SDK-letöltési helye](media/speech-devices-sdk/get-sdk-7.png)

1. Töltse le és telepítse az Aspera Connect eszközzel, amikor a böngésző kéri.

    ![Töltse le az Aspera-csatlakozás](media/speech-devices-sdk/get-sdk-8.png)
 
1. Kattintson a **Igen** Aspera csatlakozás váltani.

    ![Váltson át az Aspera-csatlakozás](media/speech-devices-sdk/get-sdk-9.png)
 
1. Kattintson a **engedélyezése** Aspera csatlakozzon a fájlok letöltését.

    ![az Aspera Connect letöltése](media/speech-devices-sdk/get-sdk-10.png)
 
1. Zárja be Aspera csatlakozás adatátvitel után a fájlok le vannak töltve.

    ![Adatátvitel ablak Aspera csatlakoztatása](media/speech-devices-sdk/get-sdk-11.png)
 
Alapértelmezés szerint a fájlokat tölti be a **letölti** mappát. Akkor is jelentkezzen ki a hely most. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszédfelismerés eszközök SDK használatának első lépései](speech-devices-sdk-qsg.md)
