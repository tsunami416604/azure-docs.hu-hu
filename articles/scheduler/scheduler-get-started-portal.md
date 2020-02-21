---
title: Ütemezett feladatok létrehozása – Azure Portal
description: Az első automatizált feladatot az Azure Scheduler használatával hozhatja létre, ütemezheti és futtathatja az Azure Portal
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: estfan
ms.reviewer: klam, estfan, logicappspm
ms.topic: conceptual
ms.date: 02/29/2020
ms.openlocfilehash: a9f7169f4b54dfc08612b1d53bfde48154ee2d1d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524801"
---
# <a name="create-and-schedule-your-first-job-by-using-azure-scheduler---azure-portal"></a>Az első feladatainak létrehozása és beütemezés az Azure Scheduler használatával – Azure Portal

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

Ebből az oktatóanyagból megtudhatja, milyen egyszerűen hozhat létre és ütemezhet feladatot, majd monitorozhatja és kezelheti a feladatot.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

## <a name="create-job"></a>Feladat létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Az Azure Search mezőbe írja be a `scheduler` szűrőt. Az eredmények listából válassza ki a **Scheduler-feladatok gyűjteményeit**, és válassza a **Létrehozás**lehetőséget.

   ![A Scheduler-erőforrás létrehozása](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Most hozzon létre egy feladatot, amely egy GET kérést küld az URL-címre: `https://www.microsoft.com/` 

1. A **Scheduler-feladat** alatt adja meg ezt az adatot:

   | Tulajdonság | Példaérték | Leírás |
   |----------|---------------|-------------| 
   | **Name (Név)** | getMicrosoft | A feladat neve | 
   | **Feladatgyűjtemény** | <*feladatgyűjtemény-neve*> | Hozzon létre egy feladatgyűjteményt, vagy válasszon ki egy meglévő gyűjteményt. | 
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az Azure-előfizetés neve | 
   |||| 

1. Válassza a **Műveletbeállítások – konfigurálás** lehetőséget, adja meg ezt az adatot, majd, ha elkészült, kattintson az **OK** gombra:

   | Tulajdonság | Példaérték | Leírás |
   |----------|---------------|-------------| 
   | **Művelet** | **Http** | A futtatandó művelet típusa | 
   | **Metódus** | **Get** | A meghívott metódus | 
   | **URL-cím** | **https://www.microsoft.com** | A cél URL-címe | 
   |||| 
   
   ![Feladat meghatározása](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Válassza az **Ütemezés – konfigurálás** lehetőséget, határozza meg az ütemezést, majd, ha elkészült kattintson az **OK** gombra:

   Bár létrehozhat egy egyszeri feladatot, ebben a példában egy ismétlődő ütemezést állítunk be.

   | Tulajdonság | Példaérték | Leírás |
   |----------|---------------|-------------| 
   | **Ismétlődés** | **Ismétlődő** | Egy egyszeri vagy ismétlődő feladat | 
   | **Kezdés** | <*a mai dátum*> | A feladat kezdő dátuma | 
   | **Ismétlődés** | **1 óra** | Az ismétlés időköze és gyakorisága | 
   | **Befejezés** | **Befejeződés**: a mai naptól számított két nap múlva | A feladat befejezési dátuma | 
   | **UTC-eltérés** | **UTC +08:00** | Az egyezményes világidő (UTC) és a hely megfigyelt ideje közötti különbség | 
   |||| 

   ![Ütemezés megadása](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Ha elkészült, válassza a **Létrehozás** elemet.

   Miután létrehozta a feladatot, az Azure bevezeti azt, és megjelenik az Azure-irányítópulton. 

1. Amikor az Azure megjeleníti a sikeres bevezetésről szóló üzenetet, válassza a **Rögzítés az irányítópulton** lehetőséget. Ellenkező esetben válassza az **Értesítések** ikont (harang) az Azure eszköztárán, majd a **Rögzítés az irányítópulton** lehetőséget.

## <a name="monitor-and-manage-jobs"></a>Feladatok monitorozása és kezelése

A feladat áttekintéséhez, monitorozásához és kezeléséhez válassza ki az Azure Portalon a feladatot. A **Beállítások** alatt a következő területeket tekintheti át és kezelheti a feladathoz:

![Feladatbeállítások](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Ezekről a területekről további információért jelöljön ki egy területet:

* [**Tulajdonságok**](#properties)
* [**Művelet beállításai**](#action-settings)
* [**Ütemezés**](#schedule)
* [**Előzmények**](#history)
* [**Felhasználók**](#users)

<a name="properties"></a>

### <a name="properties"></a>Tulajdonságok

A kezelési metaadatokat leíró, csak olvasható tulajdonságok megtekintéséhez a feladatnál válassza a **Tulajdonságok** lehetőséget.

![Feladat tulajdonságainak megtekintése](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Művelet beállításai

A feladat speciális beállításainak módosításához válassza a **Művelet beállításai** lehetőséget. 

![Művelet beállításainak áttekintése](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Művelettípus | Leírás | 
|-------------|-------------| 
| Minden típus | Módosíthatja az **Újrapróbálkozási szabályzat** és a **Hibakezelési művelet** beállításait. | 
| HTTP és HTTPS | A **Metódus** beállítását módosíthatja bármelyik engedélyezett metódusra. Lehetőség van továbbá a fejlécek és az egyszerű hitelesítési adatok hozzáadására, törlésére vagy módosítására is. | 
| Tárolási üzenetsor| Módosíthatja a tárfiókot, az üzenetsornevet, a SAS-jogkivonatot és törzset. | 
| Service Bus | Módosíthatja a névteret, a témakör/üzenetsor elérési útját, a hitelesítési beállításokat, az átvitel típusát, az üzenettulajdonságokat és az üzenettörzset. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Ütemezés

Ha a feladatvarázslóval állít be ütemezést, ezt az ütemezést módosíthatja, például a kezdő és befejező dátumát, az ismétlődést és az ismétlődő feladatok befejező napját és idejét.
Felépíthet továbbá [komplex ütemezéseket és speciális ismétlődéseket](scheduler-advanced-complexity.md).

A nézet vagy a feladatütemezés módosításához válassza az **Ütemezés** lehetőséget:

![Feladatütemezés megtekintése](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Előzmények

Ha szeretné megtekinteni egy kijelölt feladat minden futtatásának metrikáját, válassza az **Előzmények** lehetőséget. Ezek az adatok valós idejű értékeket biztosítanak a feladat állapotára, például az újrapróbálkozások számára, az előfordulások számára, a kezdési időre és a befejezési időre vonatkozóan.

![Feladatelőzmények és metrikák megtekintése](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Az egyes futtatásokhoz tartozó előzmények részleteinek, például az egyes futtatásokra adott teljes válasznak a megtekintéséhez jelölje ki az **Előzmények** alatt az egyes futtatásokat. 

![Feladatelőzmények részleteinek megtekintése](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Felhasználók

Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával részletes beállításokat adhat meg az egyes felhasználók számára az Azure Scheduler eléréséhez. Ha szeretné megtudni, hogyan adhat meg szerepköralapú hozzáférés-beállítást, tekintse át a [Hozzáférés kezelése RBAC-vel](../role-based-access-control/role-assignments-portal.md) című szakaszt

## <a name="next-steps"></a>Következő lépések

* Az [alapfogalmak, terminológia és entitáshierarchia](scheduler-concepts-terms.md) megismerése
* [Komplex ütemezések és speciális ismétlődések létrehozása](scheduler-advanced-complexity.md)
* A [Schedulerrel kapcsolatos magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md) megismerése
* [Korlátok, kvóták, alapértékek és hibakódok](scheduler-limits-defaults-errors.md) megismerése
