---
title: Felhasználói adatok beszélgetés tanuló - kognitív Microsoft-szolgáltatások kezelése |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan kezelheti a felhasználói adatok beszélgetés tanuló.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348602"
---
# <a name="managing-user-data"></a>Felhasználói adatok kezelése

Ez a lap ismerteti, hogy mi a beszélgetés tanuló felhőszolgáltatás naplózza az során párbeszédpanelek a végfelhasználókkal.  Azt is ismerteti, hogyan beszélgetés tanuló naplók társítandó felhasználói azonosítók, így beolvasása, vagy egy adott felhasználóhoz társított összes naplófájlt törlése.

## <a name="overview-of-end-user-data-logging"></a>Végfelhasználói adatok naplózásának áttekintése

Alapértelmezés szerint a beszélgetés tanuló felhőszolgáltatás naplózza a végfelhasználók és a botot közötti kapcsolat.  Ezek a naplók fontosak a botot engedélyezése azonosíthatja az esetekben, amikor a botot kinyerni a megfelelő entitás vagy a hibás műveletet kijelölt javítására.  Az "Napló párbeszédpanel" weblapra a felhasználói felület, korrigálás, és a javított párbeszédpanel tárolása egy új vonat párbeszédpanelt, majd javítható, ezeket a hibákat. További információkért tekintse meg az a "Napló párbeszédpanelek."

## <a name="how-to-disable-logging"></a>Naplózás letiltása

Azt is szabályozhatja, hogy a végfelhasználók beszélgetést vannak-e a beszélgetés tanuló alkalmazás "Beállítások" lapon.  Nincs olyan be a "Napló beszélgetéseket."  Ez a jelölőnégyzet jelölésének törlése után a végfelhasználók beszélgetést nem naplózza a rendszer.

## <a name="what-is-logged"></a>Mi a naplózására akkor kerül sor 

A napló párbeszédpanelek beszélgetés tanuló minden kapcsolja a felhasználói bevitel, entitás értékek, kijelölt műveletek és időbélyegeket tárolja.  Ezek a naplók tárolása egy ideig, és elveti a (lásd a súgó lapjára "határokat és alapértelmezett értéke" részletei).  

Beszélgetés tanuló minden naplózott párbeszédpanel egyedi Azonosítót hoz létre.  Beszélgetés tanuló does *nem* egy felhasználói azonosítót, a naplózott párbeszédek tárolja.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Párbeszédpanelek társítása naplózza a felhasználói azonosítóval

Gyakran fontos, hogy rendelje hozzá a naplózott párbeszédpanelek, azonosító: a felhasználó – például, hogy tudják lekérni, illetve törölhet naplózott párbeszédpanelek egy adott felhasználó.  Beszélgetés tanuló nem tárolja a felhasználói azonosítót, mivel ezt a társítást kell megőrizni a fejlesztői kóddal.  

Ez a leképezés létrehozásához az azonosító, a naplózott párbeszédpanel lekéréséhez `EntityDetectionCallback`; majd a botot tárolóban, a felhasználói Azonosítót és az a naplózott párbeszédpanel társítását tárolja.  

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

## <a name="headers-for-http-calls"></a>Fejlécek HTTP-hívások

Minden egyes a HTTP-hívásokat az alábbi adja hozzá a következő fejléc:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

Ha `<LUIS_AUTHORING_KEY>` a szerzői műveletekhez a beszélgetés tanuló alkalmazások elérésére használt kulcs LUIS.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Egy naplózott párbeszédpanel nyers adatok beszerzése

A napló párbeszédpanel a nyers adatok beszerzéséhez a HTTP-hívást használhatja:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ha `<appId>` a GUID beszélgetés tanuló alkalmazás, és `<logDialgoId>` szeretné beolvasni a naplófájl-párbeszédpanel azonosítója.  

Vegye figyelembe, hogy napló párbeszédpanelek előfordulhat, hogy lehet a fejlesztőnek szerkeszthetik, párbeszédpanelek betanítása majd tárolva.  Ebben az esetben, ha a beszélgetés tanuló a "forrás" napló párbeszédpanel megnyitása a vonat párbeszédpanel Azonosítóját tárolja.  További egy tanítási párbeszédpanelt is lehet "elágazó" a felhasználói felületen; Ha a vonat párbeszédpanelnek egy forrásoldali napló párbeszédpanel azonosítója, majd a vonat párbeszédpanelről ágak lesz megjelölve napló párbeszédpanel azonos azonosítóval.

Szerezze be az összes vonat párbeszédpanel, amely napló párbeszédpanel származik, kövesse az alábbi lépéseket.

Első lépésként lekéri az összes vonat párbeszédpanel:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Ha `<appId>` beszélgetés tanuló alkalmazás GUID azonosítója.  

Ez visszaad minden vonat párbeszédpanelek.  Keresés a listában, a társított `sourceLogDialogId`, és jegyezze fel a társított `trainDialogId`. 

Egyetlen train-azonosító szerint párbeszédpanel:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ha `<appId>` a GUID beszélgetés tanuló alkalmazás, és `<trainDialogId>` szeretné beolvasni vonat párbeszédpanel azonosítója.  

## <a name="how-to-delete-a-logged-dialog"></a>Egy naplózott párbeszédpanel törlése

Szeretné törölni a megadott Azonosítóval napló párbeszédpanel, ha a HTTP-hívást használhatja:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ha `<appId>` a GUID beszélgetés tanuló alkalmazás, és `<logDialogId>` a törölni kívánt napló párbeszédpanel azonosítója. 

Ha törli a megadott Azonosítóval vonat párbeszédablak, használhatja a HTTP-hívás:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ha `<appId>` a GUID beszélgetés tanuló alkalmazás, és `<trainDialogId>` a törölni kívánt vonat párbeszédpanel azonosítója. 
