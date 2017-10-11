---
title: "A bevonási API használatával Android rendszeren"
description: "Legújabb Android SDK - az Engagement API használatával Android rendszeren"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: d353cd2fe47c54a0282cc5bb1b22b4a56e0cd82c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-android"></a>A bevonási API használatával Android rendszeren
Ez a dokumentum az bővítménye a dokumentum [speciális jelentéskészítési lehetőségek a Mobile Engagement Android SDK](mobile-engagement-android-advanced-reporting.md). A mélység részletei jelentés az alkalmazás statisztikái az Engagement API használatával biztosít.

Ne feledje, hogy ha Engagement jelenti a munkamenetek, a tevékenységek, a összeomlásokat és a technikai információkat az alkalmazás csak szeretne, majd a legegyszerűbb módja a rendszer minden ahhoz a `Activity` alosztályokat öröklik a megfelelő `EngagementActivity` osztály.

Ha szeretne többet, például ha adott eseményeket, hibákat és feladatok, jelentenie, vagy ha a jelentés az alkalmazás tevékenységei eltérő módon, mint az egyik valósult meg, hogy a `EngagementActivity` osztályokat, akkor használja az Engagement API szükséges.

A bevonási API által biztosított a `EngagementAgent` osztály. Ez az osztály példánya meghívásával lehet beolvasni a `EngagementAgent.getInstance(Context)` statikus metódus (vegye figyelembe, hogy a `EngagementAgent` objektumot adott vissza az Egypéldányos).

## <a name="engagement-concepts"></a>Engagement – fogalmak
A következő részekből finomíthatja a közös [Mobile Engagement fogalmait](mobile-engagement-concepts.md), az Android platformhoz.

### <a name="session-and-activity"></a>`Session` és `Activity`
Ha a felhasználó több mint néhány másodpercen belül két között tétlen marad *tevékenységek*, majd a sorozatát *tevékenységek* van szétosztva két különböző *munkamenetek*. Ezek néhány másodpercet a "munkamenet időkorlátja" nevezzük.

Egy *tevékenység* tartozik általában egy képernyő az alkalmazás, azaz a *tevékenység* kezdődik, amikor a képernyő jelenik meg, és leállítja a képernyő bezárásakor: Ez a helyzet, amikor az Engagement SDK használatával integrálva van a `EngagementActivity` osztályok.

De *tevékenységek* is szabályozhatja manuálisan az Engagement API használatával. Ez lehetővé teszi több sub pontján további részletes információkat az ezen a képernyőn (például hogy milyen gyakran ismert, és mennyi ideig párbeszédpanelek ezen a képernyőn belül használt) használatát egy adott képernyő felosztása.

## <a name="reporting-activities"></a>Jelentéskészítési tevékenység
> [!IMPORTANT]
> Ebben a szakaszban ismertetett használata tevékenységek, például jelentés nem kell a `EngagementActivity` osztály és annak változataihoz Android dokumentumon integrálni engagement hogyan leírtak szerint.
> 
> 

### <a name="user-starts-a-new-activity"></a>Felhasználó elindítja az új tevékenység
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Meg kell hívnia `startActivity()` minden egyes alkalommal, amikor a felhasználói tevékenység módosul. Ez a függvény az első hívás új felhasználói munkamenet indítása.

Ez a függvény hívása a legjobb hely van, minden tevékenység `onResume` visszahívás.

### <a name="user-ends-his-current-activity"></a>Felhasználói karakterlánccal végződik-e aktuális tevékenysége
            EngagementAgent.getInstance(this).endActivity();

Meg kell hívnia `endActivity()` legalább egyszer amikor a felhasználó befejezi az utolsó tevékenység. Ebben értesíti az Engagement SDK-t, hogy a felhasználó jelenleg inaktív, valamint, hogy a felhasználói munkamenet kell egyszer lezárni a munkamenet időkorlátja lejár (ha meghívja a `startActivity()` előtt a munkamenet időkorlátja lejár, egyszerűen folytatni a munkamenetet).

Ez a függvény hívása a legjobb hely van, minden tevékenység `onPause` visszahívás.

## <a name="reporting-events"></a>Jelentési eseményeket
### <a name="session-events"></a>Munkamenet-események
A munkamenet során a felhasználó által végrehajtott műveletek jelentésére általában használhatók munkamenet események.

**További adatok nélkül. példa:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Példa a további adatokat:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Önálló események
Munkamenet események ellentétesen önálló események egy munkamenet környezetében kívül is előfordulhatnak.

**Példa**

Tegyük fel, a szórásos receiver kiváltásakor bekövetkező jelentés események:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Hibát jelentett
### <a name="session-errors"></a>Munkamenet-hibák
Munkamenet a hibák általában használhatók a a munkamenet során a felhasználót érintő hibák jelentését.

**Példa**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Önálló hibák
Munkamenet hibák ellentétesen önálló felléphetnek a kívül egy munkamenet környezetében.

**Példa**

A következő példa bemutatja, hogyan hiba jelentését, amikor a memória válik alacsony a telefonján, az alkalmazás folyamat futása közben.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Feladatok jelentése
### <a name="example"></a>Példa
Tegyük fel, hogy szeretne jelentést készíteni a bejelentkezési folyamat időtartama:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>A feladat során hibák jelentése
Hibák a jelenlegi felhasználói munkamenet való helyett egy futó feladat kapcsolódhat.

**Példa**

Tegyük fel, hogy során hiba bejelentkezési folyamat jelentést szeretne:

[...] nyilvános "void" signIn (helyi környezet,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>A feladat során jelentési eseményeket
Események egy futó feladat helyett a jelenlegi felhasználói munkamenet való kapcsolódhat.

**Példa**

Tegyük fel, közösségi hálózata, és a teljes idő, ameddig a felhasználó csatlakozik-e a kiszolgáló egy feladatot, amely a jelentés használjuk. A felhasználó is maradhat háttérben még akkor is, ha azt egy másik alkalmazás használja, vagy ha a telefonszám alvó állapotban van, így nincs munkamenet sincs.

A felhasználó fogadhat üzeneteket az ismerősök, ez az esemény feladat.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>További paraméterek
Tetszőleges adatok csatolható események, hibákat, tevékenységeket és feladatokat.

Ezek az adatok szervezhetők, Android tartozó csomagot osztály használ (ténylegesen, akkor működik, mint az Android leképezések kiegészítő paraméterekkel). Vegye figyelembe, hogy a csomag egyik Gyermekszoftver-tömb, vagy egy másik köteg példányokat tartalmazhat.

> [!IMPORTANT]
> Ha parcelable vagy szerializálható paraméterek be, ellenőrizze, hogy azok `toString()` metódus megvalósítása emberek számára olvasható karakterláncot. Szerializálható nem átmeneti, amelyek nem szerializálható mezőket tartalmazó osztályokat megkönnyítő Android összeomlási hívásakor lesz`bundle.putSerializable("key",value);`
> 
> [!WARNING]
> A további paraméterek ritka tömbök használata nem támogatott, ez azt jelenti, hogy nem szerializálható tömbként. Meg kell alakíthatja át őket a szabványos tömbök további paraméterek használatához.
> 
> 

### <a name="example"></a>Példa
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az egyes kulcsok a `Bundle` meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Kiegészítő funkciók korlátozva **1024** karakter / hívás (egyszer kódolású a JSON-ban az Engagement szolgáltatás).

Az előző példában a a kiszolgálónak küldött JSON-ja 58 karakter:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Jelentéskészítési alkalmazással kapcsolatos adatok
Manuálisan jelentheti a nyomkövetési adatokat (vagy más alkalmazás egyedi információt) használatával a `sendAppInfo()` függvény.

Vegye figyelembe, hogy ezek az információt elküldi Növekményesen: a megadott kulcs csak a legutóbbi értékét az adott eszköz megmarad.

Esemény kiegészítő funkciók, például a csomag osztály szolgál az alkalmazással kapcsolatos adatok absztrakt, vegye figyelembe, hogy tömbök vagy alárendelt kötegek minősül, egyszerű karakterlánc (JSON-szerializálás).

### <a name="example"></a>Példa
Ez a kódminta, a felhasználó nemét és születési dátumot küldését:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az egyes kulcsok a `Bundle` meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Alkalmazásadatok esetén egyre korlátozódik **1024** karakter / hívás (egyszer kódolású a JSON-ban az Engagement szolgáltatás).

Az előző példában a a kiszolgálónak küldött JSON-ja 44 karakter:

            {"expiration":"2016-12-07","status":"premium"}
