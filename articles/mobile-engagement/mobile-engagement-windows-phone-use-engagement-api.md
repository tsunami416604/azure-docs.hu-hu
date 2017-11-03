---
title: "A bevonási API használata a Windows Phone Silverlight"
description: "A bevonási API használata a Windows Phone Silverlight"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ec8b6c13ea052c8063dfde4321cdd286ab6cb817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>A bevonási API használata a Windows Phone Silverlight
Ez a dokumentum az bővítménye a dokumentum [a Windows Phone Silverlight-alkalmazást a Mobile Engagement integrálása](mobile-engagement-windows-phone-integrate-engagement.md). A mélység részletei jelentés az alkalmazás statisztikái az Engagement API használatával biztosít.

Ha csak a bevonási jelenti a munkamenetek, a tevékenységek, a összeomlásokat és a technikai információkat az alkalmazás szeretné, akkor a legegyszerűbb módja, hogy minden a `PhoneApplicationPage` alosztályokat örökli a `EngagementPage` osztály.

Ha szeretne többet, például ha adott eseményeket, hibákat és feladatok, jelentenie, vagy ha a jelentés az alkalmazás tevékenységei eltérő módon, mint az egyik valósult meg, hogy a `EngagementPage` osztályokat, akkor használja az Engagement API szükséges.

A bevonási API által biztosított a `EngagementAgent` osztály. Azokat a módszereket használatával tudja elérni `EngagementAgent.Instance`.

Akkor is, ha az ügynök modul inicializálása nem történt meg, az API-t minden egyes hívásakor késleltetve, és végrehajtja újra, amikor az ügynök nem érhető el.

## <a name="engagement-concepts"></a>Engagement – fogalmak
A következő részek a Mobile Engagement fogalmait a Windows Phone platform szűkítéséhez.

### <a name="session-and-activity"></a>`Session` és `Activity`
Egy *tevékenység* tartozik általában az alkalmazás egy oldalnyi, azaz a *tevékenység* kezdődik, amikor az oldal jelenik meg, és leállítja a lap bezárásakor: Ez a helyzet, amikor az Engagement SDK használatával integrálva van a `EngagementPage` osztály.

De *tevékenységek* is szabályozhatja manuálisan az Engagement API használatával. Ez lehetővé teszi több sub részből további részletes információkat az ezen a lapon (például hogy milyen gyakran ismert, és mennyi ideig párbeszédpanelek ezen a lapon belül használt) használatát egy adott lap felosztása.

## <a name="reporting-activities"></a>Jelentéskészítési tevékenység
### <a name="user-starts-a-new-activity"></a>Felhasználó elindítja az új tevékenység
#### <a name="reference"></a>Referencia
            void StartActivity(string name, Dictionary<object, object> extras = null)

Meg kell hívnia `StartActivity()` minden egyes alkalommal, amikor a felhasználói tevékenység módosul. Ez a függvény az első hívás új felhasználói munkamenet indítása.

> [!IMPORTANT]
> Az SDK automatikusan a EndActivity metódust hívja, ha az alkalmazás le van zárva. Ebből kifolyólag javasoljuk a StartActivity metódus meghívására, amikor a tevékenység, a felhasználó módosítása, és soha EndActivity metódus hívható, mert a metódus hívása az aktuális munkamenet be kell fejezni kényszeríti.
> 
> 

#### <a name="example"></a>Példa
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Felhasználói karakterlánccal végződik-e aktuális tevékenysége
#### <a name="reference"></a>Referencia
            void EndActivity()

Meg kell hívnia `EndActivity()` legalább egyszer amikor a felhasználó befejezi az utolsó tevékenység. Ebben értesíti az Engagement SDK-t, hogy a felhasználó jelenleg inaktív, valamint, hogy a felhasználói munkamenet kell egyszer lezárni a munkamenet időkorlátja lejár (ha meghívja a `StartActivity()` előtt a munkamenet időkorlátja lejár, a munkamenet egyszerűen továbbra is).

#### <a name="example"></a>Példa
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Feladatok jelentése
### <a name="start-a-job"></a>Feladat indítása
#### <a name="reference"></a>Referencia
            void StartJob(string name, Dictionary<object, object> extras = null)

A feladatot használhatja egy meghatározott időtartamra vonatkozóan: bizonyos feladatok követése céljából.

#### <a name="example"></a>Példa
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Egy feladat vége
#### <a name="reference"></a>Referencia
            void EndJob(string name)

Amint egy tevékenység követi nyomon a feladat le lett állítva, meg kell a EndJob metódus hívható meg a feladat úgy, hogy megadja a feladat neve.

#### <a name="example"></a>Példa
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Jelentési eseményeket
A következő három típusú eseményeket:

* Önálló események
* Munkamenet-események
* Feladat-események

### <a name="standalone-events"></a>Önálló események
#### <a name="reference"></a>Referencia
            void SendEvent(string name, Dictionary<object, object> extras = null)

Önálló események is előfordulhatnak kívül egy munkamenet környezetében.

#### <a name="example"></a>Példa
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Munkamenet-események
#### <a name="reference"></a>Referencia
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

A munkamenet során a felhasználó által végrehajtott műveletek jelentésére általában használhatók munkamenet események.

#### <a name="example"></a>Példa
**Adatok: nélkül**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Adatokkal:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Feladat-események
#### <a name="reference"></a>Referencia
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Feladat események általában a felhasználó által a feldolgozás során végrehajtott műveletek jelentésére használt.

#### <a name="example"></a>Példa
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Hibát jelentett
A következő három típusú hibák:

* Önálló hibák
* Munkamenet-hibák
* Hibák: feladathiba

### <a name="standalone-errors"></a>Önálló hibák
#### <a name="reference"></a>Referencia
            void SendError(string name, Dictionary<object, object> extras = null)

Munkamenet hibák ellentétesen önálló felléphetnek a kívül egy munkamenet környezetében.

#### <a name="example"></a>Példa
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Munkamenet-hibák
#### <a name="reference"></a>Referencia
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Munkamenet a hibák általában használhatók a a munkamenet során a felhasználót érintő hibák jelentését.

#### <a name="example"></a>Példa
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Hibák: feladathiba
#### <a name="reference"></a>Referencia
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Hibák a jelenlegi felhasználói munkamenet való helyett egy futó feladat kapcsolódhat.

#### <a name="example"></a>Példa
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Jelentéskészítési összeomlik
Az ügynök az összeomlások kezelésére két módszert biztosít.

### <a name="send-an-exception"></a>Kivétel küldése
#### <a name="reference"></a>Referencia
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Példa
Bármikor kivétel meghívásával küldheti el:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Egy nem kötelező paraméter segítségével állítsa le a engagement munkamenet, mint a összeomlási egyszerre. Ehhez hívja meg:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Ha így tesz, a munkamenet és feladatok csak az összeomlás elküldése után megszűnik.

### <a name="send-an-unhandled-exception"></a>Nem kezelt kivétel küldése
#### <a name="reference"></a>Referencia
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement küldése nem kezelt kivételek módszert is biztosít. Ez különösen akkor hasznos, ha a silverlight UnhandledException eseménykezelő használni.

Ezt a módszert fog **mindig** a bevonási munkamenet és feladatok meghívott után leáll.

#### <a name="example"></a>Példa
Használhatja a saját UnhandledException eseménykezelő megvalósítása (különösen akkor, ha le van tiltva az automatikus összeomlási jelentéskészítési funkció bekapcsolása). Például a `Application_UnhandledException` metódusában a `App.xaml.cs` fájlt:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Referencia
            void OnActivated(ActivatedEventArgs e)

Amikor a felhasználók megnyitják előre, egy alkalmazást, másik lapra után az inaktív egy esemény jelenik meg, az operációs rendszer megkísérli az alkalmazás alvó állapotba helyezni. Az alkalmazás akkor törlésre. A folyamat a rendszer megszakítja a kérelmet, de az alkalmazás és az alkalmazáson belül az egyes lapokon állapotára vonatkozó néhány adat megőrződik.

Helyezze be, hogy `EngagementAgent.Instance.OnActivated(e)` a a `Application_Activated` az App.xaml.cs fájlt, az Engagement ügynök alaphelyzetbe állítani, ha az alkalmazás már a törlésre kijelölt metódust.

### <a name="example"></a>Példa
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Eszközazonosító
            String GetDeviceId()

A metódus hívása kaphat az engagement-eszközazonosító.

## <a name="extras-parameters"></a>Kiegészítő funkciók paraméterek
Egy esemény, a hiba, a tevékenység vagy a feladat tetszőleges adatok csatolható. Ezen adatok segítségével dictionary szervezhetők. Kulcsok és értékek bármilyen típusú lehet.

Kiegészítő funkciók adatokat, ha be szeretné szúrni a saját típusát a kiegészítő funkciók fel kell vennie egy típus adategyezmény szerializálva vannak.

### <a name="example"></a>Példa
Azt a hozzon létre egy új osztályt "Személy".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Ezt követően adunk hozzá egy `Person` egy további példányt.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Ha más típusú objektumokat, ellenőrizze, hogy azok ToString() metódus megvalósítása emberi olvasható karakterláncot.
> 
> 

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az objektum minden egyes kulcsot meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Kiegészítő funkciók korlátozva **1024** hívás karakteres.

## <a name="reporting-application-information"></a>Jelentéskészítési alkalmazással kapcsolatos adatok
### <a name="reference"></a>Referencia
            void SendAppInfo(Dictionary<object, object> appInfos)

A SendAppInfo() használatával információkat (vagy más alkalmazás egyedi információt) követési függvény manuálisan jelentést.

Vegye figyelembe, hogy ezek az információt elküldi Növekményesen: a megadott kulcs csak a legutóbbi értékét az adott eszköz megmarad. Esemény kiegészítő funkciók, például egy szótár használata\<objektum, objektum\> informations csatlakoztatni.

### <a name="example"></a>Példa
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Korlátok
#### <a name="keys"></a>Kulcsok
Az objektum minden egyes kulcsot meg kell egyeznie a következő reguláris kifejezésnek:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Ez azt jelenti, hogy kulcsok betűk, számok és aláhúzásjelek követ legalább egy betűvel kell kezdődnie (\_).

#### <a name="size"></a>Méret
Alkalmazásadatok esetén egyre korlátozódik **1024** hívás karakteres.

Az előző példában a a kiszolgálónak küldött JSON-ja 44 karakter:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Naplózás
### <a name="enable-logging"></a>Naplózás engedélyezése
Az SDK beállítható úgy, hogy az IDE-konzolon a vizsgálati naplók eredményez.
Ezek a naplók alapértelmezés szerint nincsenek aktiválva. Ez testreszabásához frissítse a tulajdonságát `EngagementAgent.Instance.TestLogEnabled` egy érhetők el az érték a `EngagementTestLogLevel` számbavételi, például:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
