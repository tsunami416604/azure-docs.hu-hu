---
title: Felhasználói adatkezelés Conversation Learner-Microsoft Cognitive Servicessal | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan kezelheti a felhasználói információkat Conversation Learner használatával.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705199"
---
# <a name="managing-user-data"></a>Felhasználói adatkezelés

Ez a lap leírja, hogy a Conversation Learner Cloud Service hogyan naplózza a felhasználókat a párbeszédek végrehajtásakor.  Azt is leírja, hogyan társíthatja Conversation Learner naplókat felhasználói azonosítókkal, így lekérheti vagy törölheti az adott felhasználóhoz társított összes naplót.

## <a name="overview-of-end-user-data-logging"></a>A végfelhasználói adatnaplózás áttekintése

Alapértelmezés szerint a Conversation Learner Cloud Service naplózza a végfelhasználók és a robot közötti interakciókat.  Ezek a naplók fontosak a robot javítása érdekében, így azonosíthatja azokat az eseteket, ahol a robot a helytelen entitást kibontotta, vagy a helytelen műveletet választotta.  Ezek a hibák azután kijavíthatók, ha a felhasználói felület "naplózási párbeszédpanelek" lapjára kattint, és kijavítja a javításokat, és a javított párbeszédpanelt új betanítási párbeszédpanelként tárolja. További információkért tekintse meg a "napló párbeszédpanelek" című oktatóanyagot.

## <a name="how-to-disable-logging"></a>A naplózás letiltása

Megadhatja, hogy a végfelhasználók beszélgetések a Conversation Learner modell beállítások lapján vannak-e.  A "beszélgetések naplózása" jelölőnégyzet be van jelölve.  Ha törli a jelölőnégyzet jelölését, a végfelhasználókkal folytatott beszélgetések nem lesznek naplózva.

## <a name="what-is-logged"></a>A naplózott 

A naplózási párbeszédpanelekben Conversation Learner tárolja a felhasználói bevitelt, az entitás értékeit, a kijelölt műveleteket és az időbélyegeket az egyes körökhöz.  Ezeket a naplókat a rendszer egy ideig tárolja, majd elveti (a részletekért tekintse meg a Súgó oldalt az "alapértelmezett érték és határok" oldalon).  

Conversation Learner egyedi azonosítót hoz létre az egyes naplózott párbeszédpanelekhez.  Conversation Learner nem *tárol a* naplózott párbeszédpanelekkel rendelkező felhasználói azonosítót.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Naplózott párbeszédpanelek társítása felhasználói AZONOSÍTÓval

Gyakran fontos, hogy a naplózott párbeszédpaneleket társítsa a felhasználó azonosítójával, például hogy egy adott felhasználó beolvassa vagy törölje a naplózott párbeszédpaneleket.  Mivel Conversation Learner nem tárol felhasználói azonosítót, ezt a társítást a fejlesztői kódnak kell megtartania.  

A leképezés létrehozásához szerezze be a naplózott párbeszédpanel `EntityDetectionCallback`azonosítóját, majd a robot Storage tárolójában tárolja a felhasználói azonosító és a naplózott párbeszédpanel közötti társítást.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
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

## <a name="headers-for-http-calls"></a>HTTP-hívások fejlécei

Az alábbi HTTP-hívások mindegyikében adja hozzá a következő fejlécet:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

hol `<LUIS_AUTHORING_KEY>` található a Conversation Learner alkalmazásokhoz való hozzáféréshez használt Luis authoring-kulcs.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>A naplózott párbeszédpanel nyers információinak beszerzése

A naplózási párbeszédpanel nyers értékének beszerzéséhez használhatja ezt a HTTP-hívást:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ahol `<appId>` a Conversation Learner modell GUID azonosítója, és `<logDialgoId>` a lekérdezni kívánt napló párbeszédpanel azonosítója.  

> [!NOTE]
> A naplózási párbeszédpaneleket a fejlesztő szerkesztheti, majd a rendszer a betanítási párbeszédpanelként tárolja őket.  Ha ez történik, Conversation Learner a "forrás" napló párbeszédpanel AZONOSÍTÓját tárolja a vonat párbeszédpanellel.  Továbbá a betanítási párbeszédpanel "elágazó" lehet a felhasználói felületen; Ha a vonat párbeszédpanelhez tartozik egy társított forrásoldali párbeszédpanel-azonosító, akkor a betanítási párbeszédpanelen lévő ágak ugyanazzal a napló-párbeszédpanellel lesznek megjelölve.

A log (napló) párbeszédpanelből származtatott összes betanítási párbeszédpanel beszerzéséhez kövesse az alábbi lépéseket.

Először is beolvassa az összes vonat párbeszédpanelt:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Hol `<appId>` található a Conversation Learner modell GUID azonosítója.  

Ez az összes vonat párbeszédpanelt adja vissza.  Keresse meg a társított `sourceLogDialogId`listát, és jegyezze fel a társított. `trainDialogId` 

Egyetlen betanítási párbeszédablakban azonosító szerint:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ahol `<appId>` a Conversation Learner modell GUID azonosítója, és a lekérdezni kívánt vonat- `<trainDialogId>` párbeszédpanel azonosítója.  

## <a name="how-to-delete-a-logged-dialog"></a>Naplózott párbeszédpanel törlése

Ha törölni kívánja a log-párbeszédpanelt az azonosítója alapján, akkor ezt a HTTP-hívást használhatja:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Ahol `<appId>` a a Conversation Learner modell GUID azonosítója, és `<logDialogId>` a törölni kívánt napló párbeszédpanel azonosítója. 

Ha törölni szeretne egy vonat-párbeszédpanelt az azonosítója alapján, akkor ezt a HTTP-hívást használhatja:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Ahol `<appId>` a a Conversation Learner modell GUID azonosítója, amely a `<trainDialogId>` törölni kívánt vonat-párbeszédpanel azonosítóját jelöli. 
