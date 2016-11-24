---
title: "Azure Traffic Manager-profilok kezelése | Microsoft Docs"
description: "Ennek a cikknek a segítségével létrehozhatja, letilthatja, engedélyezheti, törölheti és megtekintheti az Azure Traffic Manager-profilok előzményeit."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 105ba0b27725ccc4846c30a2d8e29fbdebb8d86c

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Az Azure Traffic Manager-profilok kezelése

A Traffic Manager-profilok forgalom-útválasztási módszereket használnak a felhőszolgáltatások vagy webhelyek végpontjaira érkező forgalom elosztásának szabályozásához. Ez a cikk ismerteti a profilok létrehozásának és kezelésének módját.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Traffic Manager-profil létrehozása a Gyorslétrehozás használatával

A klasszikus Azure portálon rendelkezésre álló Gyorslétrehozás segítségével gyorsan létrehozhat Traffic Manager-profilokat. A Gyorslétrehozás lehetővé teszi alapszintű konfigurációs beállításokkal rendelkező profilok létrehozását. A Gyorslétrehozás azonban nem használható az olyan beállítások megadásához, mint a végpontok készlete (felhőszolgáltatások és webhelyek), a feladatátvételi forgalom-útválasztási módszer feladatátvételi sorrendje vagy a figyelési beállítások. A profil létrehozása után ezeket a beállításokat a klasszikus Azure portálon konfigurálhatja. A Traffic Manager profilonként legfeljebb 200 végpontot támogat. Azonban a használati forgatókönyvhöz csak néhány végpont szükséges.

### <a name="to-create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. **Telepítse a felhőszolgáltatásokat és webhelyeket az éles környezetben.** A felhőszolgáltatásokkal kapcsolatos további információért tekintse meg a [Felhőszolgáltatások](http://go.microsoft.com/fwlink/p/?LinkId=314074) című cikket. A webhelyekkel kapcsolatos további információkért tekintse meg a [Webhelyek](http://go.microsoft.com/fwlink/p/?LinkId=393327) című cikket.
2. **Jelentkezzen be a klasszikus Azure-portálra.** A portál bal alsó részén kattintson az **Új**, majd a **Hálózati szolgáltatások > Traffic Manager**, végül pedig a **Gyorslétrehozás** elemre a profil konfigurálásához.
3. **Konfigurálja a DNS-előtagot.** Adjon a Traffic Manager-profilnak egyedi DNS-előtagnevet. A Traffic Manager tartománynevének csak az előtagját adhatja meg.
4. **Válassza ki az előfizetést.** Válassza ki a megfelelő Azure-előfizetést. Az egyes profilok egy-egy előfizetéshez vannak társítva. Ha csak egy előfizetése van, ez a lehetőség nem jelenik meg.
5. **Válassza ki a forgalom-útválasztási módszert.** Válassza ki a forgalom-útválasztási módszert a **Forgalom-útválasztási házirend** területen. A forgalom-útválasztási módszerekkel kapcsolatos további információkért tekintse meg [A Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md) című cikket.
6. **A profil létrehozásához kattintson a Létrehozás parancsra**. A profilkonfiguráció befejezése után megkeresheti a profilt a klasszikus Azure portál Traffic Manager panelén.
7. **Konfigurálja a végpontokat, a figyelési és további beállításokat a klasszikus Azure-portálon.** A Gyorslétrehozás használatával csak az alapszintű beállítások konfigurálhatók. A további beállításokat (például a végpontok listáját és a végpont feladatátviteli sorrendjét) is konfigurálni kell.

## <a name="disable-enable-or-delete-a-profile"></a>Profilok letiltása, engedélyezése vagy törlése

Letilthat létező profilokat, így a Traffic Manager nem hivatkozik a konfigurált végpontokra irányuló felhasználói kérelmekre. Amikor letilt egy Traffic Manager-profilt, a profil és az általa tartalmazott információk használhatók és a Traffic Manager-felületen szerkeszthetők maradnak.  Amikor újra engedélyezi a profilt, a hivatkozások ismét elérhetővé válnak. Amikor létrehoz egy Traffic Manager-profilt a klasszikus Azure-portálon, a rendszer automatikusan engedélyezi azt. Ha úgy dönt, többé nincs szüksége egy profilra, törölheti.

### <a name="to-disable-a-profile"></a>A profilok letiltása

1. Ha egyéni tartománynevet használ, módosítsa az internetes DNS-kiszolgáló CNAME-rekordját, hogy többé ne mutasson a Traffic Manager-profilra.
2. A forgalom többé nem lesz a végpontokra irányítva a Traffic Manager-profil beállításain keresztül.
3. Válassza ki a letiltani kívánt profilt. A Traffic Manager lapon jelölje ki a profilt a profil neve melletti oszlopra kattintva. Vegye figyelembe, hogy a profil nevére vagy a név melletti nyílra kattintva a profilbeállítások lapja nyílik meg.
4. A profil kijelölése után kattintson a **Letiltás** elemre az oldal alján.

### <a name="to-enable-a-profile"></a>A profilok engedélyezése

1. Válassza ki a letiltani kívánt profilt. A Traffic Manager lapon jelölje ki a profilt a profil neve melletti oszlopra kattintva. Vegye figyelembe, hogy a profil nevére vagy a név melletti nyílra kattintva a profilbeállítások lapja nyílik meg.
2. A profil kijelölése után kattintson az **Engedélyezés** elemre az oldal alján.
3. Ha egyéni tartománynevet használ, hozzon létre egy CNAME-erőforrásrekordot az internetes DNS-kiszolgálón, amely a Traffic Manager-profil tartománynevére mutat.
4. A forgalom ismét a végpontokra lesz irányítva.

### <a name="to-delete-a-profile"></a>A profilok törlése

1. Győződjön meg arról, hogy az internetes DNS-kiszolgálón érvényes DNS-erőforrásrekord már nem használ olyan CNAME-erőforrásrekordot, amely a Traffic Manager-profil tartománynevére mutat.
2. Válassza ki a letiltani kívánt profilt. A Traffic Manager lapon jelölje ki a profilt a profil neve melletti oszlopra kattintva. Vegye figyelembe, hogy a profil nevére vagy a név melletti nyílra kattintva a profilbeállítások lapja nyílik meg.
3. A profil kijelölése után kattintson a **Törlés** elemre a lap alján.

## <a name="view-traffic-manager-profile-change-history"></a>A Traffic Manager-profil módosítási előzményeinek megtekintése

A Traffic Manager-profil módosítási előzményeit a klasszikus Azure portál Felügyeleti szolgáltatások területén tekintheti meg.

### <a name="to-view-your-traffic-manager-change-history"></a>A Traffic Manager módosítási előzményeinek megtekintése

1. A klasszikus Azure portál bal oldali panelén kattintson a **Felügyeleti szolgáltatások** elemre.
2. A Felügyeleti szolgáltatások oldalon kattintson a **Műveletnaplók** pontra.
3. A Műveletnaplók oldalon szűrők beállításával tekintheti meg a Traffic Manager-profil korábbi módosításait. A szűrési beállítások kiválasztása után kattintson a pipára az eredmények megtekintéséhez.

   * Az összes profil módosításainak megtekintéséhez válassza ki az előfizetését és az időtartományt, majd válassza a **Traffic Manager** elemet a **Típus** helyi menüből.
   * A profilnév alapján történő szűréshez írja be a profil nevét a **Szolgáltatás neve** mezőbe, vagy válassza ki a helyi menüből.
   * Az egyes módosítások részleteinek megtekintéséhez jelölje ki a megtekinteni kívánt módosítás sorát, és kattintson a **Részletek** lehetőségre az oldal alján. A **Művelet részletei** ablakban megtekintheti a művelet részeként létrehozott vagy frissített API-objektum XML-ábrázolását.

## <a name="next-steps"></a>Következő lépések

* [Végpont hozzáadása](traffic-manager-endpoints.md)
* [Feladatátvétel útválasztási módjának konfigurálása](traffic-manager-configure-failover-routing-method.md)
* [Ciklikus időszeleteléses útválasztási mód beállítása](traffic-manager-configure-round-robin-routing-method.md)
* [Teljesítménycentrikus útválasztási mód beállítása](traffic-manager-configure-performance-routing-method.md)
* [Vállalati internetes tartomány átirányítása egy Traffic Manager-tartománynévre](traffic-manager-point-internet-domain.md)
* [A Traffic Manager csökkentett teljesítményének elhárítása](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO2-->


