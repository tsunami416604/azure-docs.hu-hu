---
title: Beszélgetéstanuló – a Microsoft Cognitive Services a felhasználói adatok kezelése |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan kezelheti a felhasználói adatok Beszélgetéstanuló.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f9de4377857188a8cf483321654fb857e428c7f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171639"
---
# <a name="managing-user-data"></a>Felhasználói adatok kezelése

Ezen a lapon azt ismerteti, mi a Beszélgetéstanuló felhőszolgáltatás naplózzák végző párbeszédpaneleket a végfelhasználók számára.  Emellett bemutatja, hogyan lehet Beszélgetéstanuló naplók felhasználói azonosítóval, társítaná, beolvasása, vagy egy adott felhasználóhoz társított összes napló törlése.

## <a name="overview-of-end-user-data-logging"></a>Végfelhasználói adatok naplózásának áttekintése

Beszélgetéstanuló felhőszolgáltatás alapértelmezés szerint naplózza a végfelhasználók és a robot közötti interakciókat.  Ezek a naplók fontosak a robot, lehetővé téve a esetekben, amikor a robot ki kell olvasni az helytelen entitás, vagy a helytelen művelet kiválasztott azonosítása javítására.  Ezek a hibák majd az "Log-párbeszédpanelekhez" weblapra a felhasználói felület, korrigálás és a javított párbeszédpanel tárolásához, egy tanítási párbeszédpanel való importálásával javítható. További információkért lásd az oktatóanyag a "Log párbeszédpanelek."

## <a name="how-to-disable-logging"></a>Naplózás letiltása

Szabályozhatja, hogy vannak-e a végfelhasználókkal beszélgetések a "Beállítások" lapon a Beszélgetéstanuló modell.  Van egy jelölőnégyzetet a "Beszélgetés Log."  A jelölőnégyzet jelölésének törlése, a végfelhasználóknak beszélgetéseinek a rendszer nem naplózza.

## <a name="what-is-logged"></a>Mi a naplózására akkor kerül sor 

A napló párbeszédpanelek Beszélgetéstanuló egyes kapcsolja be a felhasználói bevitel, entitásértékek, kijelölt műveletek és időbélyegek tárolja.  Ezek a naplók tárolása egy ideig, és utána eldobja (lásd a következő súgóoldalt az "alapértelmezett értéket, és határokat" részletei).  

Beszélgetéstanuló minden naplózott párbeszédpanel egyedi Azonosítót hoz létre.  Beszélgetéstanuló does *nem* egy felhasználói azonosítót, a naplózott párbeszédek tárolja.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Párbeszédpanelek társítása naplózza a felhasználói azonosító

Gyakran fontos lehet például naplózott párbeszédpanelek társítása a felhasználó azonosítója, a--, beolvasását és törlését a naplózott párbeszédpanelek egy adott felhasználó lehessen.  Beszélgetéstanuló nem tárolja a felhasználói azonosító, mivel ezt a társítást kell a fejlesztői kóddal kell tartani.  

Ezen leképezés létrehozásához, szerezze be a naplózott párbeszédpanel a azonosítója `EntityDetectionCallback`; majd a bot Storage, a felhasználói Azonosítót és a naplózott párbeszédpanel közötti társítás tárolja.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>HTTP-hívásokhoz fejlécek

Az egyes alábbi HTTP-hívások a következő fejléc hozzáadása:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

ahol `<LUIS_AUTHORING_KEY>` a LUIS a beszélgetés Learner alkalmazások eléréséhez használt kulcs létrehozási.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Egy naplózott párbeszédpanel szükséges nyers adatok beszerzése

A napló párbeszédpanel a nyers adatok megszerzéséhez használhatja a HTTP-hívás:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ahol `<appId>` ez Beszélgetéstanuló modell GUID azonosítója és `<logDialgoId>` a lekérni kívánt napló párbeszédpanel azonosítója.  

> [!NOTE]
> Napló párbeszédpanelek előfordulhat, hogy a fejlesztő által szerkesztett, és tárolja, a párbeszédpanelek betanításához.  Ha ezzel végzett, Beszélgetéstanuló tárolja a "forrás" log párbeszédpanel a vonat párbeszédpanel azonosítója.  További egy tanítási párbeszédpanel "elágaztathatók" a felhasználói felületen; Ha egy train párbeszédpanel egy forrásoldali Naplóazonosító párbeszédpanelen, majd train párbeszédablakból ágak lesznek megjelölve log párbeszédpanel azonos azonosítóval.

Összes train párbeszédpanel, amely egy naplót párbeszédpanel származik beszerzéséhez kövesse az alábbi lépéseket.

Először kérje le az összes train párbeszédpanel:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Ahol `<appId>` a Beszélgetéstanuló modell GUID azonosítója.  

Ez visszaadja az összes train párbeszédpanel.  Keresés a listában, a társított `sourceLogDialogId`, és jegyezze fel a társított `trainDialogId`. 

Egyetlen vonat azonosító alapján párbeszédpanelen:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ahol `<appId>` ez Beszélgetéstanuló modell GUID azonosítója és `<trainDialogId>` a lekérni kívánt train párbeszédpanel azonosítója.  

## <a name="how-to-delete-a-logged-dialog"></a>Egy naplózott párbeszédpanel törlése

Ha egy napló párbeszédpanelen megadott Azonosítóval törölni kívánt, használhatja a HTTP-hívás:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ahol `<appId>` ez Beszélgetéstanuló modell GUID azonosítója és `<logDialogId>` a törölni kívánt napló párbeszédpanel azonosítója. 

Ha szeretné törölni a megadott Azonosítóval train párbeszédpanel, használhatja a HTTP-hívás:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ahol `<appId>` ez Beszélgetéstanuló modell GUID azonosítója és `<trainDialogId>` a törölni kívánt train párbeszédpanel azonosítója. 
