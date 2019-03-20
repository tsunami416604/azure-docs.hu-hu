---
title: Az MPIO konfigurálása a StorSimple eszközhöz |} A Microsoft Docs
description: A StorSimple eszköz egy Windows Server 2012 R2 rendszerű gazdagép csatlakozik a többutas I/O (MPIO) beállításának módját ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078142"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Többutas I/O konfigurálása a StorSimple-eszköz

Ez az oktatóanyag ismerteti, hogyan telepítheti és használhatja a többutas I/O (MPIO) szolgáltatást a Windows Server 2012 R2 rendszert futtató gazdagépekre kell követniük, és a StorSimple fizikai eszköz csatlakozik. A StorSimple 8000 sorozat csak a fizikai eszközök érvényes ebben a cikkben leírtakat. MPIO-t a StorSimple Cloud Appliance a jelenleg nem támogatott.

A Microsoft beépített támogatása a többutas I/O (MPIO) szolgáltatás a Windows Server iSCSI magas rendelkezésre állású, hibatűrő hálózati konfigurációk létrehozása érdekében. MPIO-t használ a redundáns összetevők – adapterek, kábelek és kapcsolók – a kiszolgáló és a tárolóeszköz közötti logikai útvonalak létrehozásához. Ha sikertelen egy összetevője, egy logikai útvonal is meghibásodik, ami többutas logika egy alternatív elérési utat használja az i/o úgy, hogy az alkalmazások továbbra is hozzáférhetnek az adataikhoz. Emellett a konfigurációtól függően az MPIO is is növelheti a teljesítményt a terhelés kiegyenlítése az elérési utak között. További információkért lásd: [többutas I/O áttekintése](https://technet.microsoft.com/library/cc725907.aspx "többutas I/O áttekintése és a szolgáltatások").

A magas rendelkezésre állású, hogy a StorSimple megoldás, az MPIO-t a StorSimple eszközön kell konfigurálni. Ha az MPIO telepítve van a Windows Server 2012 R2 rendszert futtató kiszolgálók, a kiszolgálók majd tűri egy hivatkozás, hálózati vagy adapterhibákat.

## <a name="mpio-configuration-summary"></a>Az MPIO konfigurációja összegzése

Az MPIO egy opcionális funkció a Windows Server, és alapértelmezés szerint nincs telepítve. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül.

Kövesse az alábbi lépéseket az MPIO konfigurálása a StorSimple-eszköz:

* 1. lépés: A Windows Server-gazdagépen MPIO szolgáltatás telepítésének részletei
* 2. lépés: Az MPIO konfigurálása a StorSimple-kötetek
* 3. lépés: Csatlakoztatási StorSimple-köteteket a gazdagép
* 4. lépés: Az MPIO konfigurálása a magas rendelkezésre álláshoz és terheléselosztási funkciók

Az előző lépések mindegyike a következő szakaszokban a következő cikkben.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1. lépés: A Windows Server-gazdagépen MPIO szolgáltatás telepítésének részletei

Ez a funkció a Windows Server-gazdagépen telepítéséhez kövesse az alábbi lépéseket.

#### <a name="to-install-mpio-on-the-host"></a>Az MPIO szolgáltatás telepítésének részletei a gazdagépen

1. Nyissa meg a Kiszolgálókezelőt a Windows Server-gazdagépen. Alapértelmezés szerint a Kiszolgálókezelő elindul, ha tagja a Rendszergazdák csoport jelentkezik be Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató számítógépre. Ha a Kiszolgálókezelő még nem nyitott, kattintson a **Start > Kiszolgálókezelő**.
   
   ![Kiszolgálókezelő](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kattintson a **Kiszolgálókezelő > irányítópult > szerepkörök és szolgáltatások hozzáadása**. Ekkor elindul a **szerepkörök és szolgáltatások hozzáadása** varázsló.
   
   ![Szerepkörök és szolgáltatások hozzáadása varázsló 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Az a **szerepkörök és szolgáltatások hozzáadása** varázsló, hajtsa végre az alábbi lépéseket:
   
   1. Az a **megkezdése előtt** kattintson **tovább**.
   2. Az a **telepítés típusának kiválasztása** lap, fogadja el az alapértelmezett beállítás a **szerepköralapú vagy szolgáltatásalapú** telepítését. Kattintson a **tovább**.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Az a **célkiszolgáló kijelölése** lapon a **kiszolgáló kijelölése a kiszolgálókészletből**. A gazdakiszolgálón kell automatikus felderítése. Kattintson a **tovább**.
   4. Az a **kiszolgálói szerepkörök kiválasztása** kattintson **tovább**.
   5. A a **jellemzőket** lapra, jelölje be **többutas i/o**, és kattintson a **tovább**.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Az a **telepítendő összetevők megerősítése** lapon, majd válassza ki és a kijelölés megerősítéséhez **a célkiszolgáló automatikus újraindítása, ha szükséges**lent látható módon. Kattintson az **Install** (Telepítés) gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Értesítést kap a telepítés befejeződött. A varázsló bezárásához kattintson a **Bezárás** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2. lépés: Az MPIO konfigurálása a StorSimple-kötetek

A többutas I/O azonosítására a StorSimple-kötetek kell konfigurálni. StorSimple-kötetek felismerni az MPIO konfigurálása, hajtsa végre az alábbi lépéseket.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Az MPIO konfigurálása a StorSimple-kötetek

1. Nyissa meg a **az MPIO konfigurációja**. Kattintson a **Kiszolgálókezelő > irányítópult > eszközök > MPIO**.
2. Az a **MPIO tulajdonságok** párbeszédpanelen válassza ki a **felderítése többutas** fülre.
3. Válassza ki **iSCSI-eszközök támogatása**, és kattintson a **Hozzáadás**.  
   ![Az MPIO tulajdonságainak felderítése több elérési utak](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Indítsa újra a kiszolgálót, amikor a rendszer kéri.
5. Az a **MPIO tulajdonságok** párbeszédpanelen kattintson a **MPIO eszközök** fülre. Kattintson az **Hozzáadás** parancsra.
    </br>![Az MPIO tulajdonságok MPIO eszközök](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Az a **MPIO-támogatás hozzáadása** párbeszédpanel **eszközök Hardverazonosító**, adja meg az eszköz sorozatszámát. Az eszköz sorozatszámának, nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Navigáljon a **eszközök > irányítópult**. A jobb oldalon jelenik meg az eszköz sorozatszáma **Quick Glance** ablaktábláján az irányítópultját.
    </br>
    ![Az MPIO-támogatás hozzáadása](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Indítsa újra a kiszolgálót, amikor a rendszer kéri.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3. lépés: Csatlakoztatási StorSimple-köteteket a gazdagép

Az MPIO konfigurálása után a Windows Server, a StorSimple eszközön létrehozott (ek) is csatlakoztathatók, és majd kihasználhatja az MPIO a redundancia biztosítása érdekében. Kötet csatlakoztatása, hajtsa végre az alábbi lépéseket.

#### <a name="to-mount-volumes-on-the-host"></a>Kötetek csatlakoztatása a gazdagépen

1. Nyissa meg a **iSCSI-kezdeményező tulajdonságai** ablak a Windows Server-gazdagépen. Kattintson a **Kiszolgálókezelő > irányítópult > eszközök > iSCSI-kezdeményező**.
2. Az a **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen kattintson a felderítés fülre, és kattintson **felderítése**.
3. Az a **felderítése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   1. Adja meg az adatportot a StorSimple eszköz IP-címét (például adja meg az adatok 0).
   2. Kattintson a **OK** térjen vissza a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel bezárásához.
     
      > [!IMPORTANT]
      > **Ha iSCSI-kapcsolatok magánhálózaton használ, adja meg az adatportot, amely csatlakozik a magánhálózati IP-címét.**
    
4. Ismételje meg a 2-3. lépéseket egy második hálózati adapter (például adatok 1) az eszközön. Ne feledje, hogy ezeket az adaptereket engedélyezni kell az iSCSI számára. További információkért lásd: [módosítása a hálózati adapterek](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Válassza ki a **célok** lapján a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel bezárásához. A StorSimple-eszköz kell megjelennie cél IQN-név alapján **Felderített tárolók**.

   ![iSCSI kezdeményező tulajdonságai cél lap](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kattintson a **Connect** iSCSI-munkamenet létrehozásához a StorSimple-eszközre. A **cél csatlakozhat** párbeszédpanel jelenik meg.
7. Az a **cél csatlakozhat** párbeszédpanelen válassza ki a **engedélyezése több utas** jelölőnégyzetet. Kattintson a **speciális**.
8. Az a **speciális beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   1. Az a **helyi Adapter** legördülő listában válassza **Microsoft iSCSI-kezdeményező**.
   2. Az a **kezdeményező IP-cím** legördülő listára, válassza ki a gazdagép IP-címét.
   3. Az a **Célportál** IP legördülő listára, válassza ki az IP-címét az eszköz felületén.
   4. Kattintson a **OK** térjen vissza a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel bezárásához.
9. Kattintson a **Tulajdonságok** elemre. Az a **tulajdonságok** párbeszédpanelen kattintson a **munkamenet hozzáadása**.
10. Az a **cél csatlakozhat** párbeszédpanelen válassza ki a **engedélyezése több utas** jelölőnégyzetet. Kattintson a **speciális**.
11. Az a **speciális beállítások** párbeszédpanel:

    1. Az a **helyi adapter** legördülő listában válassza ki a Microsoft iSCSI-kezdeményező.
    2. Az a **kezdeményező IP-cím** legördülő listára, válassza ki a gazdagéphez tartozó IP-címét. Ebben az esetben két hálózati adaptert az eszköz kapcsolódik az állomáson egyetlen hálózati adapterrel. Ez az interfész Ezért ugyanaz, mint az első munkamenet biztosított.
    3. Az a **cél Portal IP-címet** legördülő listában válassza az IP-cím, a második adatok kapcsolat engedélyezve van az eszközön.
    4. Kattintson a **OK** térjen vissza az iSCSI-kezdeményező tulajdonságai párbeszédpanel bezárásához. A cél hozzáadta a második munkamenet.
12. Nyissa meg **számítógép-kezelés** lépve **Kiszolgálókezelő > irányítópult > Számítógép-kezelés**. A bal oldali ablaktáblán kattintson a **tárolás > Lemezkezelés**. A StorSimple-eszköz számára a gazdagép látható a létrehozott kötet alatt megjelenő **Lemezkezelés** , új (eke) t.
13. Inicializálja a lemezt, és hozzon létre egy új kötetet. A formátum során válassza ki a 64 KB-os blokkméret.
    
    ![Lemezkezelés](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. A **Lemezkezelés**, kattintson a jobb gombbal a **lemez** válassza **tulajdonságok**.
15. A StorSimple-modellben ### **többutas lemez eszköztulajdonságok** párbeszédpanelen kattintson a **MPIO** fülre.
    
    ![A StorSimple 8100-as többutas lemez DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Az a **DSM neve** területén kattintson **részletek** , és ellenőrizze, hogy a paraméterek alapértelmezett paraméterek beállítása. Az alapértelmezett paraméterek a következők:
    
    * Elérési út ellenőrizze időszak = 30
    * Ismétlések száma = 3
    * PDO-eltávolítási időszak = 20
    * Újrapróbálkozási időköz = 1
    * Elérési út ellenőrzése engedélyezve = nincs bejelölve.

> [!NOTE]
> **Ne módosítsa az alapértelmezett paramétereket.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>4. lépés: Az MPIO konfigurálása a magas rendelkezésre álláshoz és terheléselosztási funkciók

Többutas alapuló, magas rendelkezésre állást és terheléselosztást, több munkamenetet kell manuálisan hozzá kell deklarálnia az elérhető különböző elérési utak. Például ha a gazdagép iSCSI hálózathoz csatlakozó két felület és az eszköz iSCSI-hálózathoz csatlakozó két adapterrel rendelkezik, majd kell konfigurálni megfelelő elérési út permutációk négy munkamenetek (csak két munkamenet lesz szükség, ha egyes adatillesztő és gazdagép felületet egy másik IP-alhálózat és nem irányítható).

**Azt javasoljuk, hogy rendelkezik-e legalább 8 aktív párhuzamos munkamenetek az eszköz és az alkalmazás-állomás között.** Ez megvalósítható a Windows Server rendszeren 4 hálózati adapter engedélyezésével. Használja a hálózati virtualizálási technológiái segítségével fizikai hálózati illesztőkre vagy virtuális a hardver vagy operációs rendszer szintjén, a Windows Server-gazdagépen. Az eszköz két hálózati adapterrel rendelkező ezt a konfigurációt eredményez 8 aktív munkamenetek. Ez a konfiguráció segít az eszköz és a felhő teljesítmény optimalizálása.

> [!IMPORTANT]
> **Azt javasoljuk, hogy ne használja együtt a 1 gbe-s és 10 GbE hálózati adapterek. Ha két hálózati adaptert használ, mindkét felületek egy azonos típusúnak kell lennie.**

Az alábbi eljárás ismerteti a adjon hozzá előadásokat a két hálózati adapterrel rendelkező gazdagépre egy két hálózati adapterrel rendelkező StorSimple-eszköz csatlakoztatásakor. Ez lehetővé teszi csak 4 munkamenetek. A StorSimple eszköz négy hálózati adapterrel rendelkező egy gazdagéphez csatlakoztatott két hálózati adapterrel rendelkező ugyanezt az eljárást használata. Az itt leírtak szerint 4 munkamenetek helyett 8 konfigurálása kell.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Az MPIO konfigurálása a magas rendelkezésre álláshoz és terheléselosztás

1. A cél felderítést végrehajtani: az a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel a **felderítési** lapra, majd **kapu felderítése**.
2. Az a **cél csatlakozhat** párbeszédpanelen adja meg az IP-cím, az eszköz hálózati adapterek egyikének.
3. Kattintson a **OK** térjen vissza a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel bezárásához.
4. Az a **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen válassza ki a **célok** fülre, jelölje ki a felderített cél, majd **Connect**. A **cél csatlakozhat** párbeszédpanel jelenik meg.
5. Az a **cél csatlakozhat** párbeszédpanel:
   
   1. Hagyja az alapértelmezett kiválasztott cél beállításának **hozzáadása ehhez a kapcsolathoz** kedvenc célok listájára. Ez lehetővé teszi az eszköz automatikusan megkísérli újraindítani a kapcsolat minden alkalommal, amikor a számítógép újraindul.
   2. Válassza ki a **engedélyezése több utas** jelölőnégyzetet.
   3. Kattintson a **speciális**.
6. Az a **speciális beállítások** párbeszédpanel:
   
   1. Az a **helyi Adapter** legördülő listában válassza **Microsoft iSCSI-kezdeményező**.
   2. Az a **kezdeményező IP-cím** legördülő listára, válassza ki a megfelelő az első adaptert rendelte a gazdagépen (iSCSI-illesztő) IP-cím.
   3. Az a **cél Portal IP-címet** legördülő listában válassza az IP-cím, az első adatok kapcsolat engedélyezve van az eszközön.
   4. Kattintson a **OK** térjen vissza az iSCSI-kezdeményező tulajdonságai párbeszédpanel bezárásához.
7. Kattintson a **tulajdonságok**, majd a a **tulajdonságok** párbeszédpanelen kattintson a **munkamenet hozzáadása**.
8. Az a **cél csatlakozhat** párbeszédpanelen válassza ki a **engedélyezése több utas** jelölőnégyzetet, majd kattintson a **speciális**.
9. Az a **speciális beállítások** párbeszédpanel:
   
   1. Az a **helyi adapter** legördülő listában válassza **Microsoft iSCSI-kezdeményező**.
   2. Az a **kezdeményező IP-cím** legördülő listára, válassza ki a megfelelő a második iSCSI-adaptert a gazdagép IP-cím.
   3. Az a **cél Portal IP-címet** legördülő listában válassza az IP-cím, a második adatok kapcsolat engedélyezve van az eszközön.
   4. Kattintson a **OK** térjen vissza a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel bezárásához. Ezzel hozzáadta a második munkamenet a cél.
10. Adjon hozzá további munkamenetek (elérési út) a cél ismételje meg a 8 – 10. Két adapter a gazdagép és az eszköz két összesen négy munkamenetek is hozzáadhat.
11. Miután hozzáadta a kívánt munkamenetek (görbék), az a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel mezőbe, jelölje ki a cél, és kattintson a **tulajdonságok**. A munkamenetek lapján a **tulajdonságok** párbeszédpanel megnyitásához, vegye figyelembe a négy munkamenet azonosítók, amelyek megfelelnek a legrövidebb út permutációk. Munkamenet megszakítása, jelölje be a munkamenet-azonosítót, és kattintson a **Disconnect**.
12. Munkamenetek belül eszközök megtekintéséhez jelölje ki a **eszközök** fülre. A kijelölt eszközre vonatkozóan a többutas I/O szabályzat konfigurálásához kattintson **MPIO**. A **eszközadatok** párbeszédpanel jelenik meg. Az a **MPIO** lapon kiválaszthatja, hogy a megfelelő **terheléselosztási házirend** beállításait. Megtekintheti a **aktív** vagy **készenléti** elérési út típusa.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatással a StorSimple-eszköz konfigurációjának módosítása](storsimple-8000-modify-device-config.md).

