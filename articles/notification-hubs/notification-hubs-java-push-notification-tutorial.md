---
title: Az Azure Értesítési központok használata a Java-val
description: Ismerje meg, hogyan használhatja az Azure Értesítési központokat java háttérrendszerből.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263863"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Az értesítési központok használata java-ból

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ez a témakör ismerteti az új, teljes mértékben támogatott hivatalos Azure Notification Hub Java SDK főbb funkcióit.
Ez a projekt egy nyílt forráskódú projekt, és megtekintheti a teljes SDK-kódot a [Java SDK.]

Általánosságban elmondható, hogy az Értesítési központok összes szolgáltatását java/PHP/Python/Ruby háttérrendszerből érheti el az Értesítési központ REST-felületén az MSDN [értesítési központok REST API-jai](https://msdn.microsoft.com/library/dn223264.aspx)című témakörben leírtak szerint. Ez a Java SDK egy vékony wrapper át ezeket a REST-felületek Java.

Az SDK jelenleg a következőket támogatja:

* CRUD az értesítési központokon
* CRUD a regisztrációk
* Telepítéskezelés
* Regisztrációk importálása/exportálása
* Rendszeres küldés
* Ütemezett küldés
* Async műveletek Java NIO-n keresztül
* Támogatott platformok: APNS (iOS), FCM (Android), WNS (Windows Áruházbeli alkalmazások), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android Google-szolgáltatások nélkül)

## <a name="sdk-usage"></a>SDK-használat

### <a name="compile-and-build"></a>Fordítás és összeállítás

[Maven] használata

Építeni:

    mvn package

## <a name="code"></a>Kód

### <a name="notification-hub-cruds"></a>Értesítési központ CRUD-k

**NamespaceManager létrehozása:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Értesítési központ létrehozása:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 VAGY

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Értesítési központ beszerezése:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Értesítési központ frissítése:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Értesítési központ törlése:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Regisztrációs CRUDs

**Értesítési központ-ügyfél létrehozása:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Windows-regisztráció létrehozása:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**IOS-regisztráció létrehozása:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Hasonlóképpen létrehozhat regisztrációkat Android (FCM), Windows Phone (MPNS) és Kindle Fire (ADM) rendszerre.

**Sablonregisztrációk létrehozása:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Regisztrációk létrehozása regisztrációs azonosító létrehozása + upsert minta használatával:**

Eltávolítja az elveszett válaszok miatti ismétlődéseket, ha regisztrációs azonosítókat tárol az eszközön:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Regisztrációk frissítése:**

    ```java
    hub.updateRegistration(reg);
    ```

**Regisztrációk törlése:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Lekérdezési regisztrációk:**

* **Egyszeri regisztráció:**

    ```java
    hub.getRegistration(regid);
    ```

* **Az összes regisztráció beszereznie a hubon:**

    ```java
    hub.getRegistrations();
    ```

* **Regisztrációk beszerezhetők címkével:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Regisztrációk beszerezhetése csatorna szerint:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Minden gyűjteménylekérdezés támogatja $top és a folytatási jogkivonatokat.

### <a name="installation-api-usage"></a>Telepítési API-használat

A telepítési API egy alternatív mechanizmus a regisztráció kezelésére. Ahelyett, hogy több regisztrációt tartana fenn, amelyek nem triviálisak, és könnyen elvégezhetők helytelenül vagy nem hatékonyan, most már egyetlen telepítési objektum is használható.

A telepítés mindent tartalmaz, amire szüksége van: push csatorna (eszköz token), címkék, sablonok, másodlagos csempék (WNS és APNS). Nem kell hívni a szolgáltatást, hogy id többé - csak generál GUID vagy bármely más azonosítót, tartsa meg az eszközön, és küldje el a háttér-együtt push channel (eszköz token).

A háttérben csak egyetlen hívást kell `CreateOrUpdateInstallation`végeznie a ; ez teljesen idempotent, így nyugodtan újra, ha szükséges.

Például az Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Ha frissíteni szeretné:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Speciális esetekben használja a részleges frissítési képességet, amely lehetővé teszi a telepítési objektum csak bizonyos tulajdonságainak módosítását. A részleges frissítés a Telepítési objektumon futtatható JSON patch műveletek részhalmaza.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Telepítés törlése:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`és `Delete` végül összhangban `Get`vannak a . A kért művelet csak a rendszervárólistába kerül a hívás során, és a háttérben kerül végrehajtásra. A get nem a fő futásidejű forgatókönyv, de csak hibakeresési és hibaelhárítási célokra, a szolgáltatás szorosan szabályozza.

A telepítések küldési folyamata megegyezik a regisztrációk esetében. Az adott telepítés értesítésének megcélzásához egyszerűen használja a "InstallationId:{desired-id}" címkét. Ebben az esetben a kód:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

A több sablon egyikén:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Ütemezési értesítések (a STANDARD szinthez érhető el)

Ugyanaz, mint a rendszeres küldés, de egy további paraméterrel - scheduledTime, amely megmondja, hogy mikor kell értesítést küldeni. Szolgáltatás elfogadja bármely időpontban között most + 5 perc és most + 7 nap.

**Windows natív értesítés ütemezése:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importálás/exportálás (a STANDARD szinthez érhető el)

Előfordulhat, hogy tömeges műveletet kell végrehajtania a regisztrációk ellen. Általában ez az integráció egy másik rendszer, vagy egy hatalmas javítást, hogy frissítse a címkéket. Nem javasoljuk a Get/Update folyamat használatát, ha több ezer regisztrációról van szó. A rendszer importálási/exportálási képessége a forgatókönyv lefedésére szolgál. Hozzáférést biztosít egy blob tároló a tárfiók alatt, mint a bejövő adatok forrása és a kimeneti hely forrása.

**Exportálási feladat beküldése:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Importálási feladat beküldése:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Várjon, amíg a feladat befejeződik:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Szerezd meg az összes munkát:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI SAS-aláírással:**

 Ez az URL-cím egy blobfájl vagy blobtároló URL-címe, valamint olyan paraméterek, mint az engedélyek és a lejárati idő, valamint a fiók SAS-kulcsával létrehozott összes ilyen dolog aláírása. Az Azure Storage Java SDK gazdag képességekkel rendelkezik, beleértve ezeknek az URI-knak a létrehozását. Egyszerű alternatívaként tekintse meg `ImportExportE2E` a tesztosztályt (a GitHub helyéről), amely az aláírási algoritmus alapvető és kompakt implementációját tartalmazza.

### <a name="send-notifications"></a>Értesítések küldése

Az értesítési objektum egyszerűen egy törzs fejlécek, néhány segédprogram módszerek segítségével a natív és sablon értesítések objektumok.

* **Windows Áruház és Windows Phone 8.1 (nem Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 és 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Tűz**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Küldés címkékre**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Küldés címkekifejezésre**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Sablonértesítés küldése**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

A Java-kód futtatásának most egy értesítést kell létrehoznia a céleszközön.

## <a name="next-steps"></a><a name="next-steps"></a>Következő lépések

Ez a témakör bemutatja, hogyan hozhat létre egy egyszerű Java REST-ügyfelet az értesítési központokhoz. Ebből a menüből:

* Töltse le a teljes [Java SDK-t,]amely tartalmazza a teljes SDK-kódot.
* Játssz a mintákkal:
  * [Az értesítési központok – első lépések]
  * [Friss hírek küldése]
  * [Honosított legfrissebb hírek küldése]
  * [Értesítések küldése hitelesített felhasználóknak]
  * [Platformfüggetlen értesítések küldése hitelesített felhasználóknak]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Az értesítési központok – első lépések]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Friss hírek küldése]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Honosított legfrissebb hírek küldése]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Platformfüggetlen értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
