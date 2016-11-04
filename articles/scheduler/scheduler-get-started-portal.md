---
title: Ismerkedés az Azure portál Azure Scheduler szolgáltatásával | Microsoft Docs
description: Ismerkedés az Azure portál Azure Scheduler szolgáltatásával
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''

ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli

---
# Ismerkedés az Azure portál Azure Scheduler szolgáltatásával
Az Azure Scheduler szolgáltatásban egyszerűen hozhat létre ütemezett feladatokat. Ezen oktatóanyag segítségével elsajátíthatja egy feladat létrehozásának műveletét. Megismerkedhet a Scheduler megfigyelési és felügyeleti képességeivel is.

## Feladat létrehozása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).  
2. Kattintson az **+Új** lehetőségre, írja be a *Scheduler* kifejezést a keresőmezőbe, majd válassza ki a **Scheduler** elemet az eredmények között, végül pedig kattintson a **Létrehozás** gombra.
   
    ![][marketplace-create]
3. Hozzunk létre egy olyan feladatot, amely egy GET kéréssel egyszerűen rákeres a http://www.microsoft.com/ webhelyre. A **Scheduler-feladat** képernyőn adja meg a következő információkat:
   
   1. **Név:** `getmicrosoft`  
   2. **Előfizetés:** Az Ön Azure-előfizetése   
   3. **Feladatgyűjtemény:** Válasszon ki egy létező feladatgyűjteményt, vagy kattintson az **Új létrehozása** lehetőségre, és adjon meg egy nevet.
4. Ezt követően határozza meg a következő értékeket a **Művelet beállításai** területen:
   
   1. **Művelettípus:** ` HTTP`  
   2. **Módszer:** `GET`  
   3. **URL-cím:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. Végül határozzon meg egy ütemezést. A feladat egyszeriként is megadható, most azonban vegyünk fel egy ismétlődési ütemezést:
   
   1. **Ismétlődés**: `Recurring`
   2. **Indítás**: A mai dátum
   3. **Ismétlődés**: `12 Hours`
   4. **Befejeződés**: A mai naptól számított két nap múlva  
      
      ![][recurrence-schedule]
6. Kattintson a ** Create** (Létrehozás) gombra

## Feladatok kezelése és figyelése
A létrehozott feladatok megjelennek az Azure fő irányítópultján. A feladatra kattintva egy új ablak nyílik meg a következő lapokkal:

1. Tulajdonságok  
2. Művelet beállításai  
3. Ütemezés  
4. Előzmények
5. Felhasználók
   
   ![][job-overview]

### Tulajdonságok
A Scheduler-feladathoz tartozó metaadatok kezelését ezen írásvédett tulajdonságok írják le.

   ![][job-properties]

### Művelet beállításai
Az adott feladat konfigurálásához kattintson rá a **Feladatok** képernyőn. Ha a gyorslétrehozási varázslóban ezt nem tette meg, itt lehetősége van speciális beállítások konfigurálására.

Az újrapróbálkozási házirend és a hibakezelési művelet módosítására az összes művelettípus esetében lehetőség van.

A HTTP- és a HTTPS-feladatok művelettípusai esetében a módszer bármely engedélyezett HTTP-parancsra módosítható. Lehetőség van továbbá a fejlécek és az egyszerű hitelesítési adatok hozzáadására, törlésére vagy módosítására is.

Tárolásisor-művelettípusok esetében módosítható a tárfiók, az üzenetsor neve, az SAS-token és a törzs.

Service Bus művelettípusok esetében módosítható a névtér, a témakör/üzenetsor elérési útvonala, a hitelesítési beállítások, az átvitel típusa, az üzenettulajdonságok és az üzenettörzs.

   ![][job-action-settings]

### Ütemezés
Ha módosítani kívánja a gyorslétrehozási varázslóban létrehozott ütemezést, itt újrakonfigurálhatja azt.

Ez lehetőséget ad [komplex és speciális, ismétlődő ütemezések létrehozására a feladathoz](scheduler-advanced-complexity.md).

Módosítható a kezdési dátum és időpont, az ismétlődési ütemezés, valamint a befejezési dátum és időpont (amennyiben a feladat ismétlődő jellegű).

   ![][job-schedule]

### Előzmények
Az adott feladathoz a rendszerben elérhető valamennyi feladat-végrehajtási mód kiválasztott adatai megjelennek az **Előzmények** lapon. Ezen adatok segítségével valós időben értékelhető a Scheduler szolgáltatás állapota:

1. status  
2. Részletek  
3. Újrapróbálkozási kísérletek
4. Előfordulás: 1., 2., 3. stb.
5. A futtatás kezdési időpontja  
6. A futtatás befejezési időpontja
   
   ![][job-history]

Az adott futtatásra kattintva megtekintheti annak **Előzményadatait**, beleértve a teljes választ minden végrehajtás esetében. Ezen párbeszédpanelről lehetőség van a válasz vágólapra másolására is.

   ![][job-history-details]

### Felhasználók
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletesen beállítható hozzáférés-vezérlést biztosít az Azure Scheduler szolgáltatáshoz. A Felhasználók lap használatának elsajátításához lásd: [Azure szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md)

## Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [A Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)

 [A Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [A Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés a Schedulerben](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png



<!--HONumber=Sep16_HO5-->


