---
title: 'Oktatóanyag: Az Azure Spring Cloud erőforrásainak figyelése riasztások és műveletcsoportok használatával | Microsoft dokumentumok'
description: További információ a Spring Cloud-riasztások használatáról.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77920076"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Oktatóanyag: A Spring Cloud erőforrásainak figyelése riasztások és műveletcsoportok használatával

Az Azure Spring Cloud-riasztások olyan feltételek alapján támogatják a figyelési erőforrásokat, mint a rendelkezésre álló tárhely, a kérelmek sebessége vagy az adathasználat. A riasztás értesítést küld, ha a díjak vagy feltételek megfelelnek a meghatározott előírásoknak.

A riasztási folyamat beállításához két lépésből áll: 
1. Állítson be egy műveletcsoportot a riasztás aktiválásakor végrehajtandó műveletekkel, például e-mail, SMS, Runbook vagy Webhook. A műveletcsoportok újra felhasználhatók a különböző riasztások között.
2. Riasztási szabályok beállítása. A szabályok a célerőforrás, a metrika, a feltétel, az időösszesítés stb.

## <a name="prerequisites"></a>Előfeltételek
Az Azure Spring követelményei mellett ez az oktatóanyag a következő erőforrásoktól függ.

* Üzembe helyezett Azure Spring Cloud-példány.  Kövesse [a rövid útmutatót](spring-cloud-quickstart-launch-app-cli.md) az első lépésekhez.

* Egy Figyelni figyelt Azure-erőforrás. Ez a példa egy spring cloud-példányt figyel.
 
A következő eljárások inicializálják a **műveletcsoportot** és a **riasztást** a **Riasztások** beállításból a Spring Cloud-példány bal oldali navigációs ablakában. (Az eljárás az Azure Portal **Figyelő áttekintése** lapjáról is megkezdhető.) 

Navigáljon egy erőforráscsoportból a Spring Cloud-példányba. Válassza a **Riasztások lehetőséget** a bal oldali ablaktáblában, majd a **Műveletek kezelése**lehetőséget:

![Képernyőkép portál erőforráscsoport lapja](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Műveletcsoport beállítása

Új **műveletcsoport**inicializálásának megkezdéséhez válassza a **+ Műveletcsoport hozzáadása**lehetőséget .

![Képernyőkép-portál Műveletcsoport hozzáadása](media/alerts-action-groups/action-1.png)

A **Műveletcsoport hozzáadása** lapon tegye a következőket:

 1. Adja meg **a műveletcsoport nevét** és **rövid nevét.**

 1. Előfizetés **Subscription** és **erőforrás csoport**megadása .

 1. Adja meg **a művelet nevét**.

 1. Válassza a **Művelettípus lehetőséget**.  Ezzel megnyílik egy másik ablaktábla a jobb oldalon, hogy meghatározza a műveletet, hogy kerül sor az aktiválás.

 1. Adja meg a műveletet a jobb oldali ablaktábla beállításaival.  Ez az eset e-mail értesítést használ.

 1. Kattintson az **OK gombra** a jobb oldali műveletpanelen.

 1. Kattintson az **OK** gombra a **Műveletcsoport hozzáadása** párbeszédpanelen. 

  ![Képernyőkép-portál műveletdefiniálása](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Riasztás beállítása 

Az előző lépések létrehozott egy e-mailt használó **műveletcsoportot.** Használhatja a telefonos értesítéseket, a webhookokat, az Azure-függvényeket stb.  

**A riasztás**konfigurálásához lépjen vissza a **Riasztások** lapra, és kattintson a **Riasztási szabályok kezelése gombra.**

  ![Képernyőkép-portál riasztást határoz meg](media/alerts-action-groups/alerts-2.png)

1. Válassza ki a riasztás **erőforrását.**

1. Kattintson **a + Új riasztási szabály gombra.**

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-3.png)

1. A **Szabály létrehozása** lapon adja meg az **ERŐFORRÁS**t.

1. A **CONDITION** beállítás számos lehetőséget kínál a **Spring Cloud** erőforrásainak figyelésére.  A **Hozzáadás** gombra kattintva nyissa meg a **Jellogika konfigurálása** ablaktáblát.

1. Jelöljön ki egy feltételt. Ez a példa **a Rendszerprocesszor-használati százalékot**használja.

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-3-1.png)

1. Görgessen le a **Jellogika konfigurálása** ablaktáblán a **Figyelnii érték** beállításához.

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-3-2.png)

1. Kattintson a **Done** (Kész) gombra.

A figyelésre rendelkezésre álló feltételekről a [Felhasználói portál mutatóinak beállításai](spring-cloud-concept-metrics.md#user-metrics-options)című témakörben talál részletesen.

 A **MŰVELETEK csoportban**kattintson **a Műveletcsoport kijelölése gombra.** A **MŰVELETEK** ablaktáblán válassza ki a korábban definiált **műveletcsoportot.**

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-3-3.png) 

1. Görgessen le, és az **ALERT DETAILS (Figyelmeztetés részletei)** csoportban nevezze el a riasztási szabályt.

1. Állítsa be a **Súlyossági .**

1. Kattintson **a Figyelmeztetési szabály létrehozása gombra.**

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-3-4.png)

Ellenőrizze, hogy az új riasztási szabály engedélyezve van-e.

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-4.png)

A szabály a **Metrikák** oldalon is létrehozható:

   ![Képernyőkép portál új riasztási szabály](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>További lépések
* [Felhasználói portál mutatóinak beállításai](spring-cloud-concept-metrics.md#user-metrics-options)
* [Műveletcsoportok létrehozása és kezelése az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [SMS-riasztások viselkedése műveletcsoportokban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Oktatóanyag: Elosztott nyomkövetés használata az Azure Spring Cloud szolgáltatással](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
