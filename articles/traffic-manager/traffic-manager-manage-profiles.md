<properties
   pageTitle="Azure Traffic Manager-profilok kezelése | Microsoft Azure"
   description="Ez cikk az Azure Traffic Manager-profilok létrehozásában, letiltásában, engedélyezésében, törlésében, valamint előzményeinek megtekintésében segít."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Az Azure Traffic Manager-profilok kezelése

A Traffic Manager-profil használatával adhatja meg a Traffic Manager által figyelt felhőszolgáltatásokat vagy webhelyvégpontokat, valamint azt, hogy milyen forgalom-útválasztási módszerrel szeretné terjeszteni a kapcsolatokat ezekre a végpontokra.

## Traffic Manager-profil létrehozása a Gyorslétrehozás használatával

A klasszikus Azure portálon rendelkezésre álló Gyorslétrehozás segítségével gyorsan létrehozhat Traffic Manager-profilokat. A Gyorslétrehozás lehetővé teszi alapszintű konfigurációs beállításokkal rendelkező profilok létrehozását. A Gyorslétrehozás azonban nem használható az olyan beállítások megadásához, mint a végpontok készlete (felhőszolgáltatások és webhelyek), a feladatátvételi forgalom-útválasztási módszer feladatátvételi sorrendje vagy a figyelési beállítások. A profil létrehozása után ezeket a beállításokat a klasszikus Azure portálon konfigurálhatja. A Traffic Manager profilonként legfeljebb 200 végpontot támogat. A legtöbb használati forgatókönyv azonban csak kevés végpont használatát igényli. 

### Új Traffic Manager-profil létrehozása

1. **Helyezze üzembe a felhőszolgáltatásokat és webhelyeket az éles környezetben.** A felhőszolgáltatásokkal kapcsolatos további információért tekintse meg a [Felhőszolgáltatások](http://go.microsoft.com/fwlink/p/?LinkId=314074) című cikket. A felhőszolgáltatásokkal kapcsolatos információkért tekintse meg az [Ajánlott eljárások](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile) című cikket. A webhelyekkel kapcsolatos további információkért tekintse meg a [Webhelyek](http://go.microsoft.com/fwlink/p/?LinkId=393327) című cikket.

2. **Jelentkezzen be a klasszikus Azure portálra.** Új Traffic Manager-profil létrehozásához kattintson az **Új** elemre a portál bal alsó részén, kattintson a **Hálózati szolgáltatások > Traffic Manager** pontra, majd kattintson a **Gyorslétrehozás** lehetőségre a profil konfigurálásának megkezdéséhez.
3. **Konfigurálja a DNS-előtagot.** Adjon a Traffic Manager-profilnak egyedi DNS-előtagnevet. A Traffic Manager tartománynevének csak az előtagját adhatja meg.
4. **Válassza ki az előfizetést.** Válassza ki a megfelelő Azure-előfizetést. Az egyes profilok egy-egy előfizetéshez vannak társítva. Ha csak egy előfizetése van, ez a lehetőség nem jelenik meg.
5. **Válassza ki a forgalom-útválasztási módszert.** Válassza ki a forgalom-útválasztási módszert a **Forgalom-útválasztási házirend** területen. A forgalom-útválasztási módszerekkel kapcsolatos további információkért tekintse meg [A Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md) című cikket.
6. **Kattintson a „Létrehozás” gombra az új profil létrehozásához**. A profilkonfiguráció befejezése után megkeresheti a profilt a klasszikus Azure portál Traffic Manager panelén.
7. **Konfigurálja a végpontokat, a figyelést és az egyéb beállításokat a klasszikus Azure portálon.** Mivel a Gyorslétrehozás használatával csak az alapbeállításokat lehet konfigurálni, a kívánt konfiguráció beállításának befejezéséhez meg kell adni további beállításokat is – például a végpontok listáját és a végpontok feladatátvételi sorrendjét. 


## Profilok letiltása, engedélyezése vagy törlése

Lehetősége van letiltani a meglévő Traffic Manager-profilokat, így azok nem továbbítják a felhasználói kérelmeket a beállított végpontjaikra. Ha letilt egy Traffic Manager-profilt, a profil és a profilban található információk változatlanok maradnak, és továbbra is szerkeszthetők a Traffic Manager felületén. Ha újra engedélyezni szeretné a profilt, egyszerűen megteheti a klasszikus Azure portálon. Az engedélyezés után az átirányítások visszaállnak. A klasszikus Azure portálon létrehozott Traffic Manager-profilok automatikusan engedélyezettek lesznek. Ha úgy dönt, hogy egy profilra már nincs szüksége, törölheti.

### A profilok letiltása

1. Módosítsa a DNS-erőforrásrekordot az internetes DNS-kiszolgálón úgy, hogy megfelelő rekordtípust és egy másik névre, vagy egy adott internetes hely IP-címére mutató mutatót használjon. Más szóval módosítsa úgy a DNS-erőforrásrekordot az internetes DNS-kiszolgálón, hogy ne a Traffic Manager-profil tartománynevére mutató CNAME-erőforrást használjon.
2. A forgalom Traffic Manager-profil beállításain keresztüli végpontokra irányítása leáll.
3. Válassza ki a letiltani kívánt profilt. A profil kiválasztásához a Traffic Manager oldalon jelölje ki a profilt a profil neve melletti oszlopra kattintva. Ne kattintson a profil nevére vagy a név melletti nyílra, mivel ezzel a profil beállításait tartalmazó oldalt nyitja meg.
4. A profil kijelölése után kattintson a **Letiltás** elemre az oldal alján.

### A profilok engedélyezése

1. Válassza ki az engedélyezni kívánt profilt. A profil kiválasztásához a Traffic Manager oldalon jelölje ki a profilt a profil neve melletti oszlopra kattintva. Ne kattintson a profil nevére vagy a név melletti nyílra, mivel ezzel a profil beállításait tartalmazó oldalt nyitja meg.
2. A profil kijelölése után kattintson az **Engedélyezés** elemre az oldal alján.
3. Módosítsa a DNS-erőforrásrekordot az internetes DNS-kiszolgálón úgy, hogy azt a CNAME-rekordtípust használja, amely a vállalati tartománynevet a Traffic Manager-profil tartománynevére képezi le. További információkért tekintse meg a [Vállalati internetes tartomány átképezése Traffic Manager-tartományra](traffic-manager-point-internet-domain.md) című cikket.
4. A forgalmat a rendszer ismét a végpontokra irányítja.

### A profilok törlése

1. Győződjön meg arról, hogy az internetes DNS-kiszolgálón érvényes DNS-erőforrásrekord már nem használ olyan CNAME-erőforrásrekordot, amely a Traffic Manager-profil tartománynevére mutat.
2. Válassza ki a törölni kívánt profilt. A profil kiválasztásához a Traffic Manager oldalon jelölje ki a profilt a profil melletti oszlopra kattintva. Ne kattintson a profil nevére vagy a név melletti nyílra, mivel ezzel a profil beállításait tartalmazó oldalt nyitja meg.
4. A profil kijelölése után kattintson a **Törlés** elemre a lap alján.

## A Traffic Manager-profil módosítási előzményeinek megtekintése

A Traffic Manager-profil módosítási előzményeit a klasszikus Azure portál Felügyeleti szolgáltatások területén tekintheti meg.

### A Traffic Manager módosítási előzményeinek megtekintése

1. A klasszikus Azure portál bal oldali panelén kattintson a **Felügyeleti szolgáltatások** elemre.
2. A Felügyeleti szolgáltatások oldalon kattintson a **Műveletnaplók** pontra.
3. A Műveletnaplók oldalon szűrők beállításával tekintheti meg a Traffic Manager-profil korábbi módosításait. A szűrési beállítások kiválasztása után kattintson a pipára az eredmények megtekintéséhez.
   - Az összes profil módosításainak megtekintéséhez válassza ki az előfizetését és a kívánt időtartományt, majd válassza a **Típus** helyi menü **Traffic Manager** pontját.
   - A profilnév alapján történő szűréshez írja be a profil nevét a **Szolgáltatás neve** mezőbe, vagy válassza ki a helyi menüből.
   - Az egyes módosítások részleteinek megtekintéséhez jelölje ki a megtekinteni kívánt módosítás sorát, és kattintson a **Részletek** lehetőségre az oldal alján. A **Művelet részletei** ablakban megtekintheti a művelet részeként létrejött vagy frissített API-objektum XML-ábrázolását, és az XML-kódot a vágólapra is másolhatja.


## Következő lépések

[Végpont hozzáadása](traffic-manager-endpoints.md)

[Configure failover routing method (Feladatátvételi útválasztási módszer konfigurálása)](traffic-manager-configure-failover-routing-method.md)

[Configure round robin routing method (Ciklikus időszeletelés típusú útválasztási módszer konfigurálása)](traffic-manager-configure-round-robin-routing-method.md)

[Configure performance routing method (Teljesítményalapú útválasztási módszer konfigurálása)](traffic-manager-configure-performance-routing-method.md)

[Hibaelhárítás a Traffic Manager „Csökkentett teljesítményű” állapota esetén](traffic-manager-troubleshooting-degraded.md)


<!--HONumber=jun16_HO2-->


