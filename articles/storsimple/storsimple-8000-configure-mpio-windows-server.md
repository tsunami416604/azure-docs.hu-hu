---
title: Az MPIO konfigurálása a StorSimple eszközhöz | Microsoft dokumentumok
description: A cikk azt ismerteti, hogy miként konfigurálható a többutas I/O (MPIO) a Windows Server 2012 R2 rendszert futtató állomáshoz csatlakoztatott StorSimple-eszközhöz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60363332"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Többutas I/O konfigurálása a StorSimple eszközhöz

Ez az oktatóanyag ismerteti azokat a lépéseket, amelyekkel a Windows Server 2012 R2 rendszert futtató és StorSimple fizikai eszközhöz csatlakoztatott gazdagépen telepíteni és használni kell a Többutas I/O (MPIO) szolgáltatást. Az ebben a cikkben található útmutatás csak a StorSimple 8000 sorozatú fizikai eszközökre vonatkozik. Az MPIO jelenleg nem támogatott a StorSimple felhőalapú berendezésen.

A Microsoft a Windows Server többutas I/O (MPIO) szolgáltatásának támogatását a magas rendelkezésre állású, hibatűrő iSCSI-hálózati konfigurációk létrehozásához nyújtotta. Az MPIO redundáns fizikai elérési út-összetevőket – adaptereket, kábeleket és kapcsolókat – használ a kiszolgáló és a tárolóeszköz közötti logikai elérési utak létrehozásához. Ha összetevő hiba van, ami logikai elérési út sikertelensítéséhez vezet, a többutas logika egy alternatív elérési utat használ az I/O-hoz, hogy az alkalmazások továbbra is hozzáférhessenek az adataikhoz. Emellett a konfigurációtól függően az MPIO is javíthatja a teljesítményt azáltal, hogy újrakiegyensúlyozza a terhelést az összes ilyen elérési úton. További információt az [MPIO áttekintése című témakörben talál.](https://technet.microsoft.com/library/cc725907.aspx "MPIO áttekintése és szolgáltatásai")

A StorSimple-megoldás magas rendelkezésre állása érdekében az MPIO-t a StorSimple-eszközön kell konfigurálni. Ha az MPIO telepítve van a Windows Server 2012 R2 rendszert futtató gazdakiszolgálókon, a kiszolgálók elviselik a kapcsolat, a hálózat vagy a kapcsolat hibáját.

## <a name="mpio-configuration-summary"></a>Többpio-konfiguráció összegzése

Az MPIO a Windows Server egyik választható szolgáltatása, és alapértelmezés szerint nincs telepítve. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül.

Az Alábbi lépésekkel konfigurálhatja a többórás t a StorSimple-eszközön:

* 1. lépés: Többmint a Pio telepítése a Windows Server gazdagépre
* 2. lépés: Az MPIO konfigurálása StorSimple kötetekhez
* 3. lépés: Mount StorSimple kötetek a gazdagépen
* 4. lépés: Az MPIO konfigurálása a magas rendelkezésre állás és a terheléselosztás érdekében

Az előző lépések mindegyikét a következő szakaszok ismertetik.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1. lépés: Többmint a Pio telepítése a Windows Server gazdagépre

Ha telepíteni szeretné ezt a szolgáltatást a Windows Server gazdagépen, hajtsa végre az alábbi eljárást.

#### <a name="to-install-mpio-on-the-host"></a>Az MPIO telepítése az állomásra

1. Nyissa meg a Kiszolgálókezelőt a Windows Server-állomáson. Alapértelmezés szerint a Kiszolgálókezelő akkor indul el, amikor a Rendszergazdák csoport egyik tagja bejelentkezik egy Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató számítógépre. Ha a Kiszolgálókezelő még nincs megnyitva, kattintson **a Start > Kiszolgálókezelő**gombra.
   
   ![Kiszolgálókezelő](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Kattintson **a Kiszolgálókezelő > irányítópult > Szerepkörök és szolgáltatások hozzáadása parancsra.** Ezzel elindítja a **Szerepkörök és szolgáltatások hozzáadása varázslót.**
   
   ![Szerepkörök és szolgáltatások hozzáadása varázsló 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. A **Szerepkörök és szolgáltatások hozzáadása** varázslóban hajtsa végre az alábbi lépéseket:
   
   1. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
   2. A **Telepítés típusának kiválasztása** lapon fogadja el a **szerepkör- vagy szolgáltatásalapú** telepítés alapértelmezett beállítását. Kattintson a **Tovább** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. A **Célkiszolgáló kiválasztása** lapon válassza **a Kiszolgáló kiválasztása a kiszolgálókészletből**lehetőséget. A gazdakiszolgálót a rendszer automatikusan fel kell deríteni. Kattintson a **Tovább** gombra.
   4. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
   5. A **Szolgáltatások kiválasztása** lapon válassza a **Többutas I/O**lehetőséget, majd kattintson a **Tovább**gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. A Telepítési beállítások megerősítése lapon erősítse meg a **kijelölést,** majd szükség esetén válassza **a Célkiszolgáló automatikus újraindítása**lehetőséget , az alábbiak szerint. Kattintson a **Telepítés gombra.**
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. A telepítés befejezése után értesítést kap. A varázsló bezárásához kattintson a **Bezárás** gombra.
   
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2. lépés: Az MPIO konfigurálása StorSimple kötetekhez

Az MPIO-t úgy kell konfigurálni, hogy azonosítsa a StorSimple köteteket. Ha az MPIO-t a StorSimple kötetek felismerésére szeretné beállítani, hajtsa végre a következő lépéseket.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Az MPIO konfigurálása StorSimple kötetekhez

1. Nyissa meg az **MPIO-konfigurációt**. Kattintson **a Kiszolgálókezelő > irányítópult > eszközök > többmint egy encikpia parancsra.**
2. Az **MPIO tulajdonságai** párbeszédpanelen jelölje be a **Többútvonalas elérési utak felderítése** lapot.
3. Válassza **az ISCSI-eszközök támogatásának hozzáadása**lehetőséget, majd kattintson a **Hozzáadás gombra.**  
   ![MPIO-tulajdonságok többútvonalat fedeznek fel](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Amikor a rendszer kéri, indítsa újra a kiszolgálót.
5. Az **MPIO tulajdonságai** párbeszédpanelen kattintson az **Add** **MPIO-eszközök** fülre.
    </br>![MPIO tulajdonságai MPIO eszközök](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Az **MPIO-támogatás hozzáadása** párbeszédpanel **Eszközhardver-azonosító**csoportjában adja meg az eszköz sorozatszámát. Az eszköz sorozatszámának lekérődéséhez férjen hozzá a StorSimple Eszközkezelő szolgáltatáshoz. Nyissa meg az **Eszközök > irányítópultját.** Az eszköz sorozatszáma az eszköz irányítópultjának jobb oldali **Quick Glance** ablaktáblájában jelenik meg.
    </br>
    ![MPIO-támogatás hozzáadása](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Amikor a rendszer kéri, indítsa újra a kiszolgálót.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3. lépés: Mount StorSimple kötetek a gazdagépen

Miután az MPIO konfigurálva van a Windows Server rendszerben, a StorSimple eszközön létrehozott kötet(ek) csatlakoztathatók, és ezután kihasználhatják az MPIO-t a redundancia érdekében. Kötet csatlakoztatásához hajtsa végre az alábbi lépéseket.

#### <a name="to-mount-volumes-on-the-host"></a>Kötetek csatlakoztatása a gazdagépen

1. Nyissa meg az **iSCSI-kezdeményező tulajdonságai** ablakot a Windows Server gazdagépen. Kattintson **a Kiszolgálókezelő > irányítópult > eszközök > az iSCSI-kezdeményező parancsra.**
2. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen kattintson a Felderítés fülre, majd a **Célportál felderítése parancsra.**
3. A **Célportál felderítése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   1. Adja meg a StorSimple-eszköz DATA-portjának IP-címét (például írja be a DATA 0-t).
   2. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez kattintson az **OK** gombra.
     
      > [!IMPORTANT]
      > **Ha iSCSI-kapcsolatokhoz magánhálózatot használ, adja meg a magánhálózathoz csatlakoztatott ADATport IP-címét.**
    
4. Ismételje meg a 2-3. Ne feledje, hogy ezeket a felületeket engedélyezni kell az iSCSI számára. További információt a [Hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces)című témakörben talál.
5. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen jelölje be a **Célok** lapot. Meg kell jelennie a StorSimple eszköz cél IQN alatt **felderített célok**.

   ![az iSCSI-kezdeményező tulajdonságai – tárolók lap](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kattintson **a Csatlakozás** gombra az iSCSI-munkamenet létrehozásához a StorSimple eszközzel. Megjelenik **a Csatlakozás a célhoz** párbeszédpanel.
7. A Csatlakozás a **célhoz** párbeszédpanelen jelölje be a **Többútvonalas elérési út engedélyezése** jelölőnégyzetet. Kattintson **a Speciális gombra.**
8. A **Speciális beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   1. A **Helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező**lehetőséget.
   2. A **Kezdeményező IP legördülő** listájában válassza ki az állomás IP-címét.
   3. A **Célportál** IP legördülő listájában válassza ki az eszközillesztő IP-címét.
   4. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez kattintson az **OK** gombra.
9. Kattintson a **Tulajdonságok** elemre. A **Tulajdonságok** párbeszédpanelen kattintson a **Munkamenet hozzáadása**gombra.
10. A Csatlakozás a **célhoz** párbeszédpanelen jelölje be a **Többútvonalas elérési út engedélyezése** jelölőnégyzetet. Kattintson **a Speciális gombra.**
11. A **Speciális beállítások** párbeszédpanelen:

    1. A **Helyi adapter** legördülő listában válassza a Microsoft iSCSI-kezdeményező lehetőséget.
    2. A **Kezdeményező IP** legördülő listájában válassza ki az állomásnak megfelelő IP-címet. Ebben az esetben az eszközön lévő két hálózati adaptert egyetlen hálózati adapterhez csatlakoztatja az állomáson. Ezért ez a felület megegyezik az első munkamenethez biztosított felülettel.
    3. A **Célportál IP** legördülő listájában válassza ki az eszközön engedélyezett második adatkapcsolat IP-címét.
    4. Az iSCSI-kezdeményező tulajdonságai párbeszédpanelre való visszatéréshez kattintson az **OK** gombra. Egy második munkamenetet adott hozzá a célhoz.
12. Nyissa meg **a Számítógép-kezelés** ablakot úgy, hogy a **Kiszolgálókezelő > irányítópult> a Számítógép-kezelés elemre**navigál. A bal oldali ablaktáblában kattintson a **Tárolás > lemezkezelés elemre.** A StorSimple-eszközön létrehozott kötet, amely látható az állomás számára, a **Lemezkezelés csoportban** új lemezként jelenik meg.
13. Inicializálja a lemezt, és hozzon létre egy új kötetet. A formázási folyamat során válasszon egy 64 KB-os blokkméretet.
    
    ![Lemezkezelés](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. A **Lemezkezelés csoportban**kattintson a jobb gombbal a **Lemez** elemre, és válassza a **Tulajdonságok parancsot.**
15. A StorSimple modell #### **többelérési lemezeszköz tulajdonságai** párbeszédpanelen kattintson az **MPIO** fülre.
    
    ![StorSimple 8100 többútvonalas lemez DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. A **DSM-név** csoportban kattintson a **Részletek** gombra, és ellenőrizze, hogy a paraméterek az alapértelmezett paraméterekre vannak-e beállítva. Az alapértelmezett paraméterek a következők:
    
    * Elérési út ellenőrzési időszaka = 30
    * Újrapróbálkozások száma = 3
    * OEM eltávolítási időszaka = 20
    * Újrapróbálkozási időköz = 1
    * Elérési út ellenőrzése engedélyezve = Nincs bejelölve.

> [!NOTE]
> **Ne módosítsa az alapértelmezett paramétereket.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>4. lépés: Az MPIO konfigurálása a magas rendelkezésre állás és a terheléselosztás érdekében

Többútvonalas magas rendelkezésre állás és terheléselosztás esetén több munkamenetet manuálisan kell hozzáadni a rendelkezésre álló különböző elérési utak deklarálásához. Ha például az állomás két, az iSCSI-hálózathoz csatlakoztatott csatolmányral rendelkezik, és az eszköz két, az iSCSI-hálózathoz csatlakoztatott csatolóval rendelkezik, akkor négy, megfelelő elérési úttal konfigurált munkamenetre van szükség (csak két munkamenetre lesz szükség, ha minden ADAT-illesztő és az állomáskapcsolat egy másik IP-alhálózaton található, és nem irányítható).

**Azt javasoljuk, hogy legalább 8 aktív párhuzamos munkamenetek az eszköz és az alkalmazás gazdagép között.** Ez úgy érhető el, hogy 4 hálózati adaptert engedélyez a Windows Server rendszeren. Fizikai hálózati adapterek vagy virtuális illesztések használata hálózati virtualizációs technológiákon keresztül a Windows Server gazdagép hardver- vagy operációsrendszer-szintjén. Az eszközön lévő két hálózati csatoló esetén ez a konfiguráció 8 aktív munkamenetet eredményezne. Ez a konfiguráció segít optimalizálni az eszköz és a felhő átviteli.

> [!IMPORTANT]
> **Azt javasoljuk, hogy ne keverjen 1 GbE és 10 GbE hálózati interfészt. Ha két hálózati összeköttetést használ, mindkét kapcsolatnak azonos típusúnak kell lennie.**

Az alábbi eljárás azt ismerteti, hogyan lehet munkameneteket hozzáadni, ha egy két hálózati csatolmányral rendelkező StorSimple-eszköz két hálózati csatolmányral rendelkező állomáshoz csatlakozik. Ez ad ön csak 4 ülés. Ugyanezt az eljárást használja egy StorSimple-eszközzel, amely két hálózati csatolóval rendelkezik, amelyek négy hálózati csatolóval rendelkező állomáshoz csatlakoznak. Az itt leírt 4 munkamenet helyett 8-at kell konfigurálnia.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Az MPIO konfigurálása a magas rendelkezésre állás és a terheléselosztás érdekében

1. A cél felderítése: az **iSCSI-kezdeményező tulajdonságai** párbeszédpanel **Felderítés** lapján kattintson a **Portál felderítése gombra.**
2. A Csatlakozás a **célhoz** párbeszédpanelen adja meg az egyik eszköz hálózati csatolójának IP-címét.
3. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez kattintson az **OK** gombra.
4. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen jelölje be a **Célok** lapot, jelölje ki a felderített célt, majd kattintson a **Csatlakozás gombra.** Megjelenik **a Csatlakozás a célhoz** párbeszédpanel.
5. A Csatlakozás a **célhoz** párbeszédpanelen:
   
   1. Hagyja meg a **kapcsolat hozzáadása** a kedvenc célok listájához alapértelmezettként kijelölt célbeállítást. Ez automatikusan megkísérli az eszköz újraindítani a kapcsolatot minden alkalommal, amikor a számítógép újraindul.
   2. Jelölje be a **Többútvonalas elérési út engedélyezése** jelölőnégyzetet.
   3. Kattintson **a Speciális gombra.**
6. A **Speciális beállítások** párbeszédpanelen:
   
   1. A **Helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező**lehetőséget.
   2. A **Kezdeményező IP legördülő** listájában válassza ki az állomás első összeköttetésének (iSCSI-összeköttetésnek) megfelelő IP-címet.
   3. A **Célportál IP** legördülő listájában válassza ki az eszközön engedélyezett első adatkapcsolat IP-címét.
   4. Az iSCSI-kezdeményező tulajdonságai párbeszédpanelre való visszatéréshez kattintson az **OK** gombra.
7. Kattintson a **Tulajdonságok gombra,** majd a **Tulajdonságok** párbeszédpanelen kattintson a **Munkamenet hozzáadása**gombra.
8. A Csatlakozás a **célhoz** párbeszédpanelen jelölje be a **Többútvonalas elérési út engedélyezése** jelölőnégyzetet, majd kattintson a Speciális **gombra.**
9. A **Speciális beállítások** párbeszédpanelen:
   
   1. A **Helyi adapter** legördülő listában válassza a **Microsoft iSCSI-kezdeményező**lehetőséget.
   2. A **Kezdeményező IP legördülő** listájában válassza ki az állomás második iSCSI-összeköttetésének megfelelő IP-címet.
   3. A **Célportál IP** legördülő listájában válassza ki az eszközön engedélyezett második adatkapcsolat IP-címét.
   4. Az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelre való visszatéréshez kattintson az **OK** gombra. Most egy második munkamenetet adott hozzá a célhoz.
10. Ismételje meg a 8-10. A gazdagépen két felület, az eszközön pedig kettő van, így összesen négy munkamenetet adhat hozzá.
11. A kívánt munkamenetek (elérési utak) hozzáadása után az **iSCSI-kezdeményező tulajdonságai** párbeszédpanelen jelölje ki a célt, és kattintson a **Tulajdonságok gombra.** A **Tulajdonságok** párbeszédpanel Munkamenetek lapján jegyezze fel a lehetséges elérési út permutációinak megfelelő négy munkamenet-azonosítót. Munkamenet megszakításához jelölje be a munkamenet-azonosító melletti jelölőnégyzetet, majd kattintson a **Kapcsolat bontása**gombra.
12. A munkameneteken belül bemutatott eszközök megtekintéséhez válassza az **Eszközök** lapot. A kijelölt eszköz Többipió-házirendjének konfigurálásához kattintson az **MPIO gombra.** Megjelenik **az Eszköz adatai** párbeszédpanel. Az **MPIO** lapon kiválaszthatja a megfelelő **terheléselosztási** házirend-beállításokat. Megtekintheti az **Aktív** vagy **a Készenléti** útvonal típusát is.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple eszköz konfigurációjának módosításához.](storsimple-8000-modify-device-config.md)

