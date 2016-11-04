---
title: Végpontok kezelése az Azure Traffic Managerben | Microsoft Docs
description: Ez a cikk a végpontok Azure Traffic Managerben végzett felvételében, eltávolításában, engedélyezésében és letiltásában segít.
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee

---
# Végpontok felvétele, letiltása, engedélyezése és törlése
Az Azure App Service Web Apps szolgáltatása is biztosít feladatátvételi és ciklikus időszeletelési forgalom-útválasztási funkciót az adatközponton belüli webhelyekhez, a webhely működési módjától függetlenül. Az Azure Traffic Manager azonban lehetővé teszi a feladatátvételi és a ciklikus időszeletelési forgalom-útválasztás beállítását a különböző adatközpontokban elhelyezett webhelyek és felhőszolgáltatások esetén is. A funkció üzembe állításának első lépése a felhőszolgáltatás vagy a webhely típusú végpont felvétele a Traffic Manager szolgáltatásba.

> [!NOTE]
> A klasszikus Azure portál használatával nem lehet külső helyeket és Traffic Manager-profilokat felvenni végpontokként. Ennek elvégzéséhez a [Definíció létrehozása](http://go.microsoft.com/fwlink/p/?LinkId=400772) REST API-t vagy az [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774) Windows PowerShell-parancsmagot kell használni.
> 
> 

A Traffic Manager-profil részét képező egyedi végpontok is letilthatók. A végpontok közé tartoznak a felhőszolgáltatások és a webhelyek is. A végpont letiltása után a végpont a profil része marad, de a profil úgy viselkedik, mintha a végpont nem szerepelne benne. Ez a művelet nagyon hasznos a karbantartási módban lévő vagy újratelepítés alatt álló végpontok ideiglenes eltávolításához. Amint a végpont újra működik, engedélyezhető.

> [!NOTE]
> A végpont letiltása nem érinti a végpont Azure rendszerbeli üzembe helyezési állapotát. A kifogástalan állapotú végpontok akkor is online állapotúak és a forgalom fogadására alkalmasak maradnak, ha letiltják őket a Traffic Manager szolgáltatásban. Továbbá a végpont egyik profilban végzett letiltása nem befolyásolja a többi profilban érvényes állapotát.
> 
> 

## Felhőszolgáltatás vagy webhely típusú végpont hozzáadása
1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager-profilt, majd kattintson a profil nevétől jobbra látható nyílra. Ekkor megnyílik a profilbeállítások oldala.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson az oldal alján megjelenő **Hozzáadás** lehetőségre a **Szolgáltatásvégpontok hozzáadása** oldal megnyitásához. Alapértelmezés szerint az oldalon a felhőszolgáltatások a **Szolgáltatásvégpontok** területen láthatók.
4. A felhőszolgáltatások profil végpontjaiként történő engedélyezéséhez válassza ki őket a listából. A felhőszolgáltatások nevének törlésével eltávolítja őket a végpontok listájáról.
5. A webhelyek esetén kattintson a **Szolgáltatástípus** legördülő listára, és válassza ki **Webalkalmazás** lehetőséget.
6. A webhelyek profil végpontjaiként történő hozzáadásához válassza ki őket a listából. A webhelyek nevének törlésével eltávolíthatja a webhelyeket a végpontok listájáról. Vegye figyelembe, hogy Azure-adatközpontonként (más néven régiónként) csak egyetlen webhelyet választhat ki. Ha olyan webhelyet választ ki egy adatközpontban, amely több webhelyet is üzemeltet, akkor az első webhely kiválasztása után az ugyanabban az adatközpontban lévő többi webhelyet már nem lehet kiválasztani. Ügyeljen arra is, hogy csak a Standard webhelyek szerepelnek a listában.
7. Miután kiválasztotta a végpontokat a profilhoz, a módosítások mentéséhez kattintson a pipa ikonra az oldal jobb alsó részen.

> [!NOTE]
> Ha a *Feladatátvétel* forgalom-útválasztási módszert használja, akkor a végpontok hozzáadása vagy eltávolítása után ne feledje átállítani a feladatátvételi prioritási listát a Konfiguráció oldalon a konfigurációhoz használni kívánt feladatátvételi sorrendnek megfelelően. További információkért tekintse meg a [Feladatátvételi forgalom-útválasztás beállítása](traffic-manager-configure-failover-routing-method.md) szakaszt.
> 
> 

## A végpontok letiltása
1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager-profilt, majd kattintson a profil nevétől jobbra látható nyílra. Ekkor megnyílik a profilbeállítások oldala.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson a letiltani kívánt végpontra, majd a **Letiltás** gombra az oldal alján.
4. A végpontra irányuló forgalom a Traffic Manager-tartománynévhez beállított élettartam (TTL) letelte után megszűnik. Az élettartam a Traffic Manager-profil Konfiguráció lapján módosítható.

## A végpontok engedélyezése
1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager-profilt, majd kattintson a profil nevétől jobbra látható nyílra. Ekkor megnyílik a profilbeállítások oldala.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. Kattintson az engedélyezni kívánt végpontra, majd az **Engedélyezés** gombra az oldal alján.
4. A rendszer ismét a profil által meghatározott módon irányítja a forgalmat a szolgáltatáshoz.

## Felhőszolgáltatás vagy webhely típusú végpont törlése
1. A klasszikus Azure portál Traffic Manager panelén keresse meg a módosítani kívánt végpontbeállításokat tartalmazó Traffic Manager-profilt, majd kattintson a profil nevétől jobbra látható nyílra. Ekkor megnyílik a profilbeállítások oldala.
2. Kattintson az oldal tetején megjelenő **Végpontok** elemre a már a konfiguráció részét képező végpontok megtekintéséhez.
3. A Végpontok oldalon kattintson annak a végpontnak a nevére, amelyet törölni szeretne a profilból.
4. Kattintson az oldal alján található **Törlés** gombra.

> [!NOTE]
> A klasszikus Azure portál használatával nem törölhetők a végpontként beállított külső helyek és a Traffic Manager-profilok. Ehhez a Windows PowerShellt kell használni. További információért tekintse meg a [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx) dokumentációját.
> 
> 

## Következő lépések
[Configure failover routing method (Feladatátvételi útválasztási módszer konfigurálása)](traffic-manager-configure-failover-routing-method.md)

[Configure round robin routing method (Ciklikus időszeletelés típusú útválasztási módszer konfigurálása)](traffic-manager-configure-round-robin-routing-method.md)

[Configure performance routing method (Teljesítményalapú útválasztási módszer konfigurálása)](traffic-manager-configure-performance-routing-method.md)

[Hibaelhárítás a Traffic Manager „Csökkentett teljesítményű” állapota esetén](traffic-manager-troubleshooting-degraded.md)

[Operations on Traffic Manager (A Traffic Managerrel végezhető műveletek) (REST API-referencia)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

<!--HONumber=Sep16_HO4-->


