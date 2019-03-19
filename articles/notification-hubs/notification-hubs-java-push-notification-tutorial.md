---
title: A Notification Hubs használata javával
description: Útmutató az Azure Notification hubs szolgáltatás használata egy Java-háttérrendszert.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 993eafd5a0b74be706d13fe8e06483c223f81eb6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901185"
---
# <a name="how-to-use-notification-hubs-from-java"></a>A Notification Hubs javából használata

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ez a témakör ismerteti az új teljes mértékben támogatott hivatalos Azure Notification Hub Java SDK fő funkcióira.
Ez a projekt egy nyílt forráskódú projekt, és megtekintheti a teljes SDK kódot [Java SDK].

Általánosságban elmondható, elérheti az összes Notification Hubs szolgáltatás háttérrendszerből egy Java/PHP vagy Python vagy Ruby használatával a Notification Hub REST-felület, az MSDN-témakörben leírtak szerint [Notification Hubs – REST API-k](https://msdn.microsoft.com/library/dn223264.aspx). A Java SDK-val dinamikusan burkolót biztosít a REST-felületeihez Java-környezetben keresztül.

Az SDK jelenleg támogatja:

* CRUD-MŰVELETEKKEL, a Notification hubs szolgáltatásban
* Regisztrációs CRUD-MŰVELETEKKEL
* Felügyeleti telepítés
* Importálási/exportálási regisztrációk
* Rendszeresen küld
* Ütemezett küldése
* Az aszinkron műveletek Java NIO keresztül
* Támogatott platformok: Az APNS (iOS), FCM (Android), a WNS (Windows Store apps), az MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android Google services nélkül)

## <a name="sdk-usage"></a>SDK-használata

### <a name="compile-and-build"></a>Fordítás és felépítés

Használat [Maven 3]

Hozhat létre:

    mvn package

## <a name="code"></a>Kód

### <a name="notification-hub-cruds"></a>Notification Hub CRUDs

**Hozzon létre egy NamespaceManager:**

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

**Értesítési központ beolvasása:**

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

**Hozzon létre ügyfél-értesítési központ:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Windows regisztrációs létrehozása:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**IOS-es regisztrációs létrehozása:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Hasonlóképpen létrehozhat regisztrációk Androidra (FCM), a Windows Phone (MPNS) és a Kindle Fire (ADM).

**Sablonregisztrációk létrehozása:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Hozzon létre regisztrációk használatával hozzon létre regisztrációs Azonosítót + upsert minta:**

Eltávolítja az ismétlődéseket miatt elveszett válaszokat, ha az eszköz regisztrációs azonosítókkal tárolására:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Regisztrációk frissítése:**

    ```java
    hub.updateRegistration(reg);
    ```

**Odstranit registrace:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Lekérdezés regisztrációk:**

* **Egyetlen regisztrációs lekérése:**

    ```java
    hub.getRegistration(regid);
    ```

* **Kérje le az összes regisztrációk hub:**

    ```java
    hub.getRegistrations();
    ```

* **Kérje le a regisztrációk címkével:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Csatorna regisztrációk lekéréséhez:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Minden gyűjtemény lekérdezések $top és a folytatási token támogatja.

### <a name="installation-api-usage"></a>Telepítés API-használat

Telepítés API regisztrációkezelés alternatív módszereket is. Több regisztrációk, amely nem triviális, és előfordulhat, hogy könnyedén elvégezheti, vagy helytelenül töredezetté, karbantartása helyett, most már lehetőség egyetlen telepítés objektummal.

Telepítés tartalmaz minden szükséges: channel (eszköztoken), a címkéket, a sablonok, a másodlagos csempék leküldéses (a WNS és APNS). Nem kell többé Azonosítójának lekéréséhez – csak hoznak létre GUID Azonosítót vagy bármely más azonosító, legyen az eszközön és küldeni a háttérrendszerhez leküldéses channel (eszköztoken) együtt a szolgáltatás hívásához.

A háttérrendszer csak akkor egyetlen meghívása `CreateOrUpdateInstallation`; teljesen idempotens legyen, így nyugodtan szükség esetén ismételje meg.

Az Amazon Kindle Fire példaként:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Ha frissíteni szeretne:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Speciális forgatókönyvek esetén használja a részleges frissítési funkció, amely lehetővé teszi, hogy a telepítési objektum csak adott tulajdonságainak módosítása. Részleges frissítést futtathatja a telepítési objektum JSON-javítás műveletek részhalmazát.

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

`CreateOrUpdate`, `Patch`, és `Delete` a végül konzisztens `Get`. A kért művelet csak a hívás során a rendszer várólistára kerül, és a háttérben hajtja végre. Get nem alkalmas a futtatókörnyezet fő forgatókönyv, de csak a hibakeresési és hibaelhárítási célokra, szigorúan szabályozott a szolgáltatás által.

Küldési telepítés folyamatábrája Registrace azonos. Értesítés az adott telepítési cél – címke használja "InstallationId: {desired-id}". Ebben az esetben a kódja:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Egy vagy több sablont:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Ütemezett értesítések (STANDARD szinten érhető el)

A ugyanaz, mint a rendszeres küldési, de egy további paraméter - scheduledTime, amikor értesítési kézbesítési alkalmazásszabályzatban. A szolgáltatás bármikor now + 5 perc közötti elfogadja és now + 7 nap.

**A Windows natív értesítési ütemezése:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (STANDARD szinten érhető el)

Szükség lehet a regisztrációk tömeges művelet végrehajtásához. Általában célszerű az integráció az egy másik system és a egy nagy paranccsal frissítheti a címkéket. Nem ajánlott a használata a lekérdezéséhez/frissítéséhez folyamatot, ha ezer van szó. A rendszer importálási/exportálási funkciót azért hoztuk terjed ki a forgatókönyvet. Bejövő adatok és a kimeneti helyet forrásként storage-fiókja alatt fog biztosít hozzáférést a blobtárolóba.

**Exportálási feladat elküldése:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Importálási feladat elküldése:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Várjon, amíg a feladat történik:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Minden feladat beolvasása:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**Az SAS-aláírás URI:**

 Az URL-cím egy blob fájl vagy blob-tároló URL-CÍMÉT és a különböző paraméterek, például engedélyek és a lejárati idő plusz aláírását, ezeket a módosításokat végzett a fiók SAS-kulcs használatával. Az Azure Storage Java SDK-t többek között az URI-k létrehozását, a széles körű lehetőségekkel rendelkezik. Egyszerű helyett, vessen egy pillantást a `ImportExportE2E` tesztelése (a helyről GitHub) osztály, amely rendelkezik aláírási algoritmus alap- és kompakt megvalósítását.

### <a name="send-notifications"></a>Értesítések küldése

Értesítési objektum egyszerűen csak a fejlécek törzséhez, a natív és a sablon értesítések objektumok épületben érdekében bizonyos segédprogram-metódusokat.

* **Windows Store és a Windows Phone 8.1-es (nem Silverlight)**

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

* **Windows Phone 8.0-s és 8.1 Silverlight**

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

* **A kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **A címkék küldése**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Címkét alkotó kifejezés küldése**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Sablon-értesítés küldése**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

A Java-kód futtatása most kell előállítania egy értesítés jelenik meg a céleszközön.

## <a name="next-steps"></a>Következő lépések

Ez a témakör láthatta, hogyan hozhat létre egy egyszerű Java REST-ügyfél a Notification hubs szolgáltatásra. Itt a következőket teheti:

* Töltse le a teljes [Java SDK], amely tartalmazza a teljes SDK kódot.
* Próbálja ki a mintákat:
  * [Notification Hubs használatának első lépései]
  * [Legfrissebb hírek elküldése]
  * [Honosított legfrissebb hírek elküldése]
  * [Értesítések küldése hitelesített felhasználóknak]
  * [Platformfüggetlen értesítések küldése hitelesített felhasználóknak]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Notification Hubs használatának első lépései]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Legfrissebb hírek elküldése]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Honosított legfrissebb hírek elküldése]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Platformfüggetlen értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven 3]: https://maven.apache.org/
