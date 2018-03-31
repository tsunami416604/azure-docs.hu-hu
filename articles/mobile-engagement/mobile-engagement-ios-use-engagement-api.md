---
title: A bevonási API használata IOS rendszerű eszközökön
description: Legújabb iOS SDK - IOS rendszerű eszközökön a bevonási API használatával
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 189a3029449a3161da2a20f940b77a5bb63bd1ef
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>A bevonási API használata IOS rendszerű eszközökön
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a dokumentum az bővítménye a dokumentum hogyan integrálhatja Engagement IOS: a mélység részletei jelentés az alkalmazás statisztikái az Engagement API használatával biztosít.

Ne feledje, hogy ha Engagement jelenti a munkamenetek, a tevékenységek, a összeomlásokat és a technikai információkat az alkalmazás csak szeretne, majd a legegyszerűbb módja annak, hogy az egyéni `UIViewController` objektumok öröklik a megfelelő `EngagementViewController` osztály.

Ha szeretne többet, például ha adott eseményeket, hibákat és feladatok, jelentenie, vagy ha a jelentés az alkalmazás tevékenységei eltérő módon, mint az egyik valósult meg, hogy a `EngagementViewController` osztályokat, akkor használja az Engagement API szükséges.

A bevonási API által biztosított a `EngagementAgent` osztály. Ez az osztály példánya meghívásával lehet beolvasni a `[EngagementAgent shared]` statikus metódus (vegye figyelembe, hogy a `EngagementAgent` objektumot adott vissza az Egypéldányos).

Minden API-hívások előtt az `EngagementAgent` objektumot inicializálni kell a metódus meghívásával. `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Engagement – fogalmak
A következő részekből finomíthatja a közös [Mobile Engagement fogalmait](mobile-engagement-concepts.md) az iOS platformra.

### <a name="session-and-activity"></a>`Session` és `Activity`
Egy *tevékenység* tartozik általában egy képernyő az alkalmazás, azaz a *tevékenység* kezdődik, amikor a képernyő jelenik meg, és leállítja a képernyő bezárásakor: Ez a helyzet, amikor az Engagement SDK használatával integrálva van a `EngagementViewController` osztályok.

De *tevékenységek* is szabályozhatja manuálisan az Engagement API használatával. Ez lehetővé teszi több sub pontján további részletes információkat az ezen a képernyőn (például hogy milyen gyakran ismert, és mennyi ideig párbeszédpanelek ezen a képernyőn belül használt) használatát egy adott képernyő felosztása.

## <a name="reporting-activities"></a>Jelentéskészítési tevékenység
### <a name="user-starts-a-new-activity"></a>Felhasználó elindítja az új tevékenység
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Meg kell hívnia `startActivity()` minden egyes alkalommal, amikor a felhasználói tevékenység módosul. Ez a függvény az első hívás új felhasználói munkamenet indítása.

### <a name="user-ends-his-current-activity"></a>Felhasználói karakterlánccal végződik-e aktuális tevékenysége
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Meg kell **soha** hív, ez a funkció önállóan, kivéve ha szeretné osztani több munkamenet az alkalmazás használatát: a függvény hívása úgy, azonnal, a jelenlegi munkamenet volna end későbbi hívása `startActivity()` volna indítson egy újat. Ez a funkció automatikusan hívja az SDK az alkalmazás bezárása után.
> 
> 

## <a name="reporting-events"></a>Jelentési eseményeket
### <a name="session-events"></a>Munkamenet-események
A munkamenet során a felhasználó által végrehajtott műveletek jelentésére általában használhatók munkamenet események.

**További adatok nélkül. példa:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Példa a további adatokat:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Önálló események
Ellentétesen munkamenet események különálló esemény egy munkamenet környezetében kívül is használható.

**Példa**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Hibát jelentett
### <a name="session-errors"></a>Munkamenet-hibák
Munkamenet a hibák általában használhatók a a munkamenet során a felhasználót érintő hibák jelentését.

**Példa**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Önálló hibák
Munkamenet hibák ellentétesen önálló hibák kívül egy munkamenet környezetében használható.

**Példa**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Feladatok jelentése
**Példa**

Tegyük fel, hogy szeretne jelentést készíteni a bejelentkezési folyamat időtartama:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>A feladat során hibák jelentése
Hibák a jelenlegi felhasználói munkamenet való helyett egy futó feladat kapcsolódhat.

**Példa**

Tegyük fel, hogy a bejelentkezési folyamat során hiba jelentését:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>A feladat során események
Események egy futó feladat helyett a jelenlegi felhasználói munkamenet való kapcsolódhat.

**Példa**

Tegyük fel, közösségi hálózata, és a teljes idő, ameddig a felhasználó csatlakozik-e a kiszolgáló egy feladatot, amely a jelentés használjuk. A felhasználó fogadhat üzeneteket az ismerősök, ez az esemény feladat.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>További paraméterek
Tetszőleges adatok csatolható események, hibákat, tevékenységeket és feladatokat.

Ezek az adatok szervezhetők, iOS tartozó NSDictionary osztályt használja.

Vegye figyelembe, hogy a kiegészítő funkciók tartalmazhatnak `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` vagy más `NSDictionary` példányok.

> [!NOTE]
> A kiegészítő paraméterrel szerializált a JSON-ban. Ha a különböző objektumok, mint a fent leírt átadni kívánt, meg kell valósítani a következő metódust az osztályban:
> 
> -(NSString*)JSONRepresentation;
> 
> A módszer az objektum JSON-ábrázolását kell visszaadnia.
> 
> 

### <a name="example"></a>Példa
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az egyes kulcsok a `NSDictionary` meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Kiegészítő funkciók korlátozva **1024** karakter / hívás (egyszer kódolású a JSON-ban az Engagement ügynök).

Az előző példában a a kiszolgálónak küldött JSON-ja 58 karakter:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Jelentéskészítési alkalmazással kapcsolatos adatok
Manuálisan jelentheti a nyomkövetési adatokat (vagy más alkalmazás egyedi információt) használatával a `sendAppInfo:` függvény.

Vegye figyelembe, hogy ezek az információt elküldi Növekményesen: a megadott kulcs csak a legutóbbi értékét az adott eszköz megmarad.

Esemény kiegészítő funkciók, például a `NSDictionary` osztály absztrakt alkalmazással kapcsolatos információk, vegye figyelembe, hogy tömbök vagy alárendelt szótárak minősül, egyszerű karakterlánc (JSON-szerializálás) szolgál.

**Példa**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az egyes kulcsok a `NSDictionary` meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Alkalmazásadatok esetén egyre korlátozódik **1024** karakter / hívás (egyszer kódolású a JSON-ban az Engagement ügynök).

Az előző példában a a kiszolgálónak küldött JSON-ja 44 karakter:

    {"birthdate":"1983-12-07","gender":"female"}
