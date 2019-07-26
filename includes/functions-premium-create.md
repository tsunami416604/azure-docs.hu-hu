---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443984"
---
1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali oldalon, majd válassza a **Function app**elemet.

1. A **üzemeltetési csomag**területen válassza a **app Service terv**, majd a **app Service terv/hely**lehetőséget.

    ![Függvényalkalmazás létrehozása](./media/functions-premium-create/create-function-app-resource.png)

1. Válassza **az új létrehozása**elemet, írja be a **app Servicei csomag** nevét, válasszon egy **helyet** az Ön közelében lévő [régióban](https://azure.microsoft.com/regions/) vagy a funkciókhoz hozzáférő egyéb szolgáltatások közelében, majd válassza az **árképzési szintet**.

    ![App Service-csomag létrehozása](./media/functions-premium-create/new-app-service-plan.png)

1. Válassza ki a **EP1** (rugalmas prémium) csomagot, majd válassza az **alkalmaz**lehetőséget.

    ![Prémium csomag kiválasztása](./media/functions-premium-create/hosting-plan.png) 

1. A terv létrehozásához kattintson az **OK gombra** , majd használja a fennmaradó Function App-beállításokat a rendszerkép alatti táblázatban megadott módon. 

    ![Elkészült az App Service-csomag](./media/functions-premium-create/create-function-app.png)  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Használhatja a javasolt értéket is. |
    | **OS** | Előnyben részesített operációs rendszer | A prémium csomagon a Linux és a Windows is támogatott. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. Csak a kiválasztott **operációs rendszeren** támogatott nyelvek jelennek meg. |
    | **[Tárolás](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** egy olyan [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) -földrajzban, ahol az adatait tárolni szeretné. |

1. A Beállítások ellenőrzése után válassza a **Létrehozás**lehetőséget.

1. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-premium-create/function-app-create-notification.png)

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton**lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.