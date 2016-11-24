---
title: "Végpontok kezelése az Azure Traffic Managerben | Microsoft Docs"
description: "Ez a cikk a végpontok Azure Traffic Managerben végzett felvételében, eltávolításában, engedélyezésében és letiltásában segít."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 47a2090e4f5a6da2879201aba5dbcd7beebcfa58

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Végpontok felvétele, letiltása, engedélyezése és törlése

Az Azure App Service Web Apps szolgáltatása is biztosít feladatátvételi és ciklikus időszeletelési forgalom-útválasztási funkciót az adatközponton belüli webhelyekhez, a webhely működési módjától függetlenül. Az Azure Traffic Manager azonban lehetővé teszi a feladatátvételi és a ciklikus időszeletelési forgalom-útválasztás beállítását a különböző adatközpontokban elhelyezett webhelyek és felhőszolgáltatások esetén is. A funkció üzembe állításának első lépése a felhőszolgáltatás vagy a webhely típusú végpont felvétele a Traffic Manager szolgáltatásba.

> [!NOTE]
> Ez a cikk leírja, hogyan használható a klasszikus portál. A klasszikus Azure portál kizárólag a felhőszolgáltatások és a webalkalmazások létrehozását és végpontként való hozzárendelését támogatja. Az előnyben részesített felület az új [Azure Portal](https://portal.azure.com).

A Traffic Manager-profil részét képező egyedi végpontok is letilthatók. A végpont letiltása után a végpont a profil része marad, de a profil úgy viselkedik, mintha a végpont nem szerepelne benne. Ez a művelet a karbantartási módban lévő vagy újratelepítés alatt álló végpont átmeneti eltávolítására való. Amint a végpont újra működik, engedélyezhető.

> [!NOTE]
> A végpont letiltása nem érinti a végpont Azure rendszerbeli üzembe helyezési állapotát. Egy megfelelően működő végpont a Traffic Managerben történő letiltás után is aktív marad és képes az adatforgalom fogadására. Továbbá a végpont egyik profilban végzett letiltása nem befolyásolja a többi profilban érvényes állapotát.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Felhőszolgáltatás vagy webhely típusú végpont hozzáadása

1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager profilt. A beállítások panel megnyitásához kattintson a profil nevétől jobbra található nyílra.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson az oldal alján megjelenő **Hozzáadás** lehetőségre a **Szolgáltatásvégpontok hozzáadása** oldal megnyitásához. Alapértelmezés szerint az oldalon a felhőszolgáltatások a **Szolgáltatásvégpontok** területen láthatók.
4. Felhőszolgáltatások esetén a felhőszolgáltatások profil végpontjaiként történő hozzáadásához válassza ki őket a listából. A felhőszolgáltatások nevének törlésével eltávolítja őket a végpontok listájáról.
5. A webhelyek esetén kattintson a **Szolgáltatástípus** legördülő listára, és válassza ki **Webalkalmazás** lehetőséget.
6. A webhelyek profil végpontjaiként történő hozzáadásához válassza ki őket a listából. A webhelyek nevének törlésével eltávolíthatja a webhelyeket a végpontok listájáról. Minden Azure adatközponthoz (más néven régióhoz) legfeljebb egy webhelyet választhat ki. Az első webhely kiválasztása után az ugyanazon adatközpontban található többi webhely kiválasztása le lesz tiltva. Ügyeljen arra is, hogy csak a Standard webhelyek szerepelnek a listában.
7. Miután kiválasztotta a végpontokat a profilhoz, a módosítások mentéséhez kattintson a pipa ikonra az oldal jobb alsó részen.

> [!NOTE]
> Miután a *Feladatátvitel* forgalom-útválasztási módszer segítségével hozzáad vagy eltávolít egy végpontot a profilból, előfordulhat, hogy a feladatátvitel prioritási listát nem rendezheti át úgy, ahogy szeretné. A feladatátvétel prioritási lista sorrendjét a konfigurációs lapon adhatja meg. További információkért tekintse meg a [Feladatátvételi forgalom-útválasztás beállítása](traffic-manager-configure-failover-routing-method.md) szakaszt.

## <a name="to-disable-an-endpoint"></a>A végpontok letiltása

1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager profilt. A beállítások panel megnyitásához kattintson a profil nevétől jobbra található nyílra.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson a letiltani kívánt végpontra, majd a **Letiltás** gombra az oldal alján.
4. Az ügyfelek az élettartam (TTL) végéig továbbítják az adatforgalmat a végpont felé. Az élettartamot a Traffic Manager profil konfigurációs panelén módosíthatja.

## <a name="to-enable-an-endpoint"></a>A végpontok engedélyezése

1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager profilt. A beállítások panel megnyitásához kattintson a profil nevétől jobbra található nyílra.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson az engedélyezni kívánt végpontra, majd az **Engedélyezés** gombra az oldal alján.
4. Az ügyfelek a profil által meghatározott engedélyezett végponthoz lesznek irányítva.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Felhőszolgáltatás vagy webhely típusú végpont törlése

1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager profilt. A beállítások panel megnyitásához kattintson a profil nevétől jobbra található nyílra.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. A Végpontok oldalon kattintson annak a végpontnak a nevére, amelyet törölni szeretne a profilból.
4. Kattintson az oldal alján található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

* [Traffic Manager-profilok kezelése](traffic-manager-manage-profiles.md)
* [Útválasztási módszerek konfigurálása](traffic-manager-configure-routing-method.md)
* [A Traffic Manager csökkentett teljesítményének elhárítása](traffic-manager-troubleshooting-degraded.md)
* [A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
* [A Traffic Manager műveletei (REST API-referencia)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


