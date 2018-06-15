---
title: A Notification Hubs használata Java
description: Megtudhatja, hogyan használja a Azure Notification Hubs from Java háttér.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 88e3ab3cc03cc1e760672120bc5c484af1ba4722
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778375"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Notification Hubs Java használatával
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ez a témakör ismerteti a kulcsfontosságú szolgáltatásokat az új teljes mértékben támogatott hivatalos Azure Notification Hub Java SDK. Ebben a projektben egy nyílt forráskódú projektet, és megtekintheti a teljes SDK kódot [Java SDK]. 

Általában elérhető összes értesítési központok szolgáltatás egy Java/PHP/Python vagy Ruby-háttér használata a Notification Hub REST interfész, az MSDN-témakörben leírtak szerint [Notification hub REST API-k](http://msdn.microsoft.com/library/dn223264.aspx). A Java SDK kínál egy vékony Java nyelven, a többi kapcsolatokon. 

Az SDK jelenleg támogatja:

* A Notification hubs használatával CRUD 
* A regisztráció CRUD
* Telepítési felügyeleti
* Importálási/exportálási regisztrációk
* Rendszeresen küld
* Ütemezett küldése
* Az aszinkron műveletek Java NIO keresztül
* Támogatott platformok: APNS (iOS), a GCM (Android), a WNS (a Windows Áruházbeli alkalmazások), a MPNS (Windows Phone), a ADM (Amazon Kindle tűz), a Baidu (Android Google szolgáltatások nélkül) 

## <a name="sdk-usage"></a>SDK használata
### <a name="compile-and-build"></a>Fordítási és -buildek
Használjon [Maven]

Hozhat létre:

    mvn package

## <a name="code"></a>Kód
### <a name="notification-hub-cruds"></a>Értesítési központ CRUDs
**Hozzon létre egy NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Értesítési központ létrehozása:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 VAGY

    hub = new NotificationHub("connection string", "hubname");

**Töltse le az értesítési központ:**

    hub = namespaceManager.getNotificationHub("hubname");

**Értesítési központ frissítése:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Értesítési központ törlése:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>Regisztrációs CRUDs
**Hozzon létre ügyfél-értesítési központ:**

    hub = new NotificationHub("connection string", "hubname");

**Windows regisztrációs létrehozása:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**IOS-regisztráció létrehozása:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Hasonlóképpen regisztrációk hozhat létre Android (GCM), a Windows Phone (MPNS) és a Kindle tűz (ADM).

**Sablon regisztrációk létrehozása:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**A regisztrálás létrehozása regisztrációs Azonosítót + upsert létre minta**

Eltávolítja az ismétlődő elveszett válaszokat miatt, ha az eszközön lévő regisztrációs azonosítók tárolására:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Regisztráció frissítése:**

    hub.updateRegistration(reg);

**Regisztráció törlése:**

    hub.deleteRegistration(regid);

**Lekérdezés regisztrációk:**

* **Egyetlen regisztrációs beolvasása:**
  
        hub.getRegistration(regid);

* **Minden regisztrációk központban érhető el:**
  
        hub.getRegistrations();

* **Töltse le a regisztrációk címkével:**
  
        hub.getRegistrationsByTag("myTag");

* **Töltse le a regisztrációk csatornánként:**
  
        hub.getRegistrationsByChannel("devicetoken");


Az összes lekérdezés $top és folytatási jogkivonatokat támogatja.

### <a name="installation-api-usage"></a>Telepítési API használata
Telepítés API alternatív módszereket a regisztrációs felügyeleti. Helyett karbantartása több regisztrációk, amelyeket a rendszer nem triviális könnyen tehetik tévesen vagy töredezetté, akkor most lehet egyetlen telepítés objektummal. Telepítés tartalmaz mindent, amire szüksége: csatorna (eszköz jogkivonatát), a címkéket, a sablonok, a másodlagos csempék leküldéses (a wns-ből és APNS). Meghívja a szolgáltatást azonosító már - csak GUID vagy bármely más azonosító létrehozása, eszközön legyen, és küldhet leküldéses csatorna (eszköz jogkivonatát) és a háttérkiszolgáló nem kell. A háttérkiszolgálón csak akkor tegye a hívást: CreateOrUpdateInstallation, teljes mértékben idempotent, ezért úgy, hogy szükség esetén próbálja meg újra.

Az Amazon Kindle tűz példaként:

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Ha szeretné a frissítést: 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Speciális forgatókönyvek esetén használja a részleges frissítési funkció, amely lehetővé teszi, hogy a telepítési objektum csak adott tulajdonságainak módosítása. Részleges update egy részhalmazát JSON javítási műveletek telepítési objektum is futtathatók.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Telepítés törléséhez:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, a javítás és a Delete nincs idővel konzisztenssé váljanak a Get. A kért művelet csak a hívás során a rendszer várólistára kerül, és a háttérben végrehajtása. Get nem arra terveztük fő futásidejű forgatókönyvhöz, de csak a Hibakeresés és hibaelhárítás céljából az szorosan folyamatban van a szolgáltatás.

Küldési telepítés folyamatábrája ugyanaz, mint a regisztráció. Értesítést az adott telepítési cél-címke használja "végrehajtott: {szükségeskonfiguráció-azonosító}". Ebben az esetben a kód a következő:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

A több sablonok valamelyikét:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>(STANDARD csomagban érhető el) értesítések ütemezése
Azonos rendszeres küldési megegyezik, azonban egy további paraméter - scheduledTime, mely szerint, amikor értesítési kézbesítési. A szolgáltatás bármely pontján most + 5 perc között eltelt idő elfogadja, most már a + 7 nap.

**A natív Windows értesítési ütemezése:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Importálási/exportálási (STANDARD csomagban érhető el)
Néha regisztrációk tömeges művelet végrehajtásához van szükség. Általában egy másik rendszeren való integrációra van, vagy csak egy nagy mondja ki a javítást a címkék frissítése. Nem ajánlott a Get-/ frissítési folyamat használatára, ha a regisztrációk ezer van szó. Importálási/exportálási képesség célja, hogy a forgatókönyv foglalkozik. Alapvetően egy hozzáférést szeretne biztosítani néhány blob tároló a tárfiók a bejövő adatok és a kimeneti helyet forrásként.

**Exportálási feladat elküldése:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Importálási feladat elküldése:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Várjon, amíg nem történik:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**Töltse le az összes feladat:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**SAS-aláírással rendelkező URI:** Ez az URL néhány blob fájl vagy a blob tároló URL-CÍMÉT, valamint paraméterkészletet például engedélyek és a lejárati idő és a fiókhoz tartozó SAS-kulcs használatával létrehozott összes lépést aláírás. Az Azure Storage Java SDK rendelkezik sokoldalú képességeit, beleértve az ilyen típusú URI-azonosítók létrehozását. Egyszerű alternatív ImportExportE2E teszt osztály (helyről a githubon) amely aláírási algoritmus alapvető és kompakt végrehajtásának egy pillantást is igénybe vehet.

### <a name="send-notifications"></a>Értesítések küldése
Az értesítési objektum nagyon egyszerűen a fejlécek törzs, néhány segédprogram-metódusokat a natív és a sablon értesítések objektumok létrehozásakor.

* **Windows áruház és Windows Phone 8.1 (nem Silverlight)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0-s és 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Tűz kindle**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Címkék elküldése**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Címke kifejezés küldése**       
  
        hub.sendNotification(n, "foo && ! bar");
* **Sablon értesítés küldése**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

A Java kódja most kell előállítania egy értesítés jelenik meg a céleszközön.

## <a name="next-steps"></a>További lépések
Ez a témakör egy egyszerű Java REST ügyfélbeállítások a Notification Hubs bemutatta. Itt a következőket teheti:

* Töltse le a teljes [Java SDK], amely tartalmazza a teljes SDK kódot. 
* A mintákkal lejátszása:
  * [Ismerkedés a Notification hubs használatával]
  * [Legfrissebb hírek elküldése]
  * [Honosított legfrissebb hírek elküldése]
  * [Értesítések küldése a hitelesített felhasználók]
  * [Platformok közötti értesítések küldése a hitelesített felhasználók]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Ismerkedés a Notification hubs használatával]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Legfrissebb hírek elküldése]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Honosított legfrissebb hírek elküldése]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Értesítések küldése a hitelesített felhasználók]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Platformok közötti értesítések küldése a hitelesített felhasználók]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

