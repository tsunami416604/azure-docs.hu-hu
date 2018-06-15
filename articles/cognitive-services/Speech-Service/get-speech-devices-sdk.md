---
title: A beszédfelismerés eszközök SDK |} Microsoft Docs
description: Megtudhatja, hogyan férhetnek hozzá a beszédfelismerés eszközök SDK-val.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 4706ea623dccd2dbb4164bd9cccf22cff121884a
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35350011"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A kognitív szolgáltatások beszéd eszközök SDK

## <a name="requesting-access"></a>Hozzáférés kérése

A beszédfelismerés eszközök SDK jelenleg korlátozott előzetes verzióban érhető, és meg kell regisztrálni a programba. Jelenleg Microsoft adatbáziskészletbe való hozzáférés nagy méretű vállalatoknál inkább ezt a terméket.

A beszédfelismerés eszközök SDK eléréséhez, kövesse az alábbi lépéseket:

1. Keresse fel a Microsoft beszéd eszközök SDK [bejelentkezési űrlap kitöltése](https://aka.ms/sdsdk-signup).
1. Olvassa el a [licencszerződésének](speech-devices-sdk-license.md).
1. Ha elfogadja a licencszerződés feltételeit, válassza az "Elfogadom."
1. A képernyőn a kérdések megválaszolása.
1. Az űrlap mentése. 
1. Ha az e-mail cím már nem része az Azure Active Directory, a jóváhagyást követően alatt hasonló meghívó e-mail jelenik meg. Ha az e-mail cím már az Azure Active Directoryban, e-mailt kap a jóváhagyást követően a Microsoft Speech csoport, és kihagyhatja azokat, amelyek a [a beszédfelismerés eszközök SDK letöltése](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Jóváhagyási e-mail

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![E-mailt](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Fogadja el a hozzáférést
Csatlakozás az Azure Active Directory regisztráció során megadott e-mail címet az alábbi lépésekkel. Ez a folyamat hozzáférést biztosít a beszédfelismerés eszközök SDK [töltse le a hely](https://shares.datatransfer.microsoft.com/).

1. Kattintson a **Ismerkedés** kapott e-mailben található. Ha a szervezet már Office 365-felhasználó, akkor kérni fogja a bejelentkezéshez, és kihagyhatja azokat, amelyek a 8. lépés.

2. Kattintson a **tovább** a elindított böngészőablakban.

    ![hitelesítési ablak](media/speech-devices-sdk/get-sdk-2.png)

3. Ha még nem rendelkezik egy Microsoft-fiók létrehozása Adja meg ugyanazt az e-mail címet, amelyen megkapta a meghívó e-mail a fenti 6.

    ![Microsoft-fiók létrehozása](media/speech-devices-sdk/get-sdk-3.png)

4. Kattintson a **következő** jelszó létrehozása.

5. Ha kér az e-mailek, térjen vissza a lekérni az Önnek küldött ellenőrző kódot e-mailben kapják.
 
7. Illessze be, vagy a párbeszédpanelen írja be a biztonsági kódot az e-mail-üzenethez. Az ebben a példában is "8406." Ezután kattintson a **Next** (Tovább) gombra.

    ![e-mail cím ellenőrzése](media/speech-devices-sdk/get-sdk-6.png)
 
8. Amikor megjelenik a hozzáférési Panel alkalmazás a böngészőablakban, meggyőződött róla, hogy az e-mail címet (a 6. lépés) jelenleg az Azure Active Directory részét. Most már rendelkezik hozzáféréssel a beszédfelismerés eszközök SDK letöltési helyre.

## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK letöltése

Lépjen a [beszéd eszközök SDK letöltési hely](https://shares.datatransfer.microsoft.com/) és jelentkezzen be a korábban létrehozott Microsoft Account. A beszédfelismerés eszközök SDK társított mintakód és referenciaanyag most töltheti le az alábbiak szerint.

![SDK letöltési hely](media/speech-devices-sdk/get-sdk-7.png)

1. Töltse le és telepítse az Aspera Connect eszközt, ha az alkalmazás kéri, a böngésző.

    ![Aspera Connect letöltése](media/speech-devices-sdk/get-sdk-8.png)
 
1. Kattintson a **Igen** váltás Aspera csatlakozni.

    ![Váltás Aspera kapcsolódni](media/speech-devices-sdk/get-sdk-9.png)
 
1. Kattintson a **engedélyezése** Aspera csatlakozzon a fájlok letöltését.

    ![az Aspera Connect letöltése](media/speech-devices-sdk/get-sdk-10.png)
 
1. Zárja be a Aspera csatlakozás átvitelek ablakot, után a fájlok le vannak töltve.

    ![Átvitelek ablak Aspera csatlakozás](media/speech-devices-sdk/get-sdk-11.png)
 
Alapértelmezés szerint a fájlokat tölti be a **letölti** mappát. Akkor is jelentkezzen ki a hely most. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a beszédfelismerés eszközök SDK-val](speech-devices-sdk-qsg.md)
