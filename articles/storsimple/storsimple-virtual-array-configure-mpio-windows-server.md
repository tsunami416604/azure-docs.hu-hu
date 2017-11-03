---
title: "Konfigurálhatja az MPIO-gazdagép csatlakozik a StorSimple virtuális tömb |} Microsoft Docs"
description: "A StorSimple virtuális tömb csatlakozott egy Windows Server 2012 R2 rendszerű többutas I/O (MPIO) beállításának módját ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Többutas I/O konfigurálása a StorSimple virtuális tömb Windows Server-gazdagépen
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, hogyan többutas I/O szolgáltatást (MPIO) telepítése a Windows Server-állomáson, csak a StorSimple-kötetek megadott konfigurációs beállítások alkalmazásához és az MPIO ellenőrizze a StorSimple-köteteket. Az eljárás azt feltételezi, hogy a StorSimple 1200 két hálózati adapterrel rendelkező virtuális tömb csatlakozik-e két hálózati adapterrel rendelkező Windows Server-állomáson. Ebben a cikkben szereplő információk csak a virtuális tömb vonatkozik. További információ a StorSimple 8000 sorozat eszközeire, [StorSimple gazdagép MPIO konfigurálása](storsimple-configure-mpio-windows-server.md). 

A Windows Server segítségével build magas rendelkezésre állású, és hibatűrő tárolási konfigurációk MPIO szolgáltatás. Az MPIO használ a redundáns összetevők – adapterek, kábelek és kapcsolók – a kiszolgáló és a tárolóeszköz közötti logikai útvonalak létrehozásához. Ha sikertelen egy összetevő, és ezáltal a logikai útvonal is meghibásodik, többutas logika egy alternatív útvonalat használ az i/o, hogy az alkalmazások továbbra is hozzáférjenek az adataikhoz. Emellett a konfigurációtól függően az MPIO is a jobb teljesítmény érdekében a terheléselosztás újra a közötti összes elérési utat. További információkért lásd: [MPIO áttekintése](https://technet.microsoft.com/library/cc725907.aspx "MPIO áttekintése és a szolgáltatások").

A magas rendelkezésre állású a StorSimple megoldás, az MPIO konfigurálását a Windows Server a StorSimple virtuális tömb (1200-as modell) csatlakozó gazdagépeknek. A gazdakiszolgálókon majd tűri egy hivatkozás, hálózati vagy adapterhibákat. 

Szüksége lesz az MPIO konfigurálása a következő lépéseket: 

* Konfigurációs előfeltételek
* 1. lépés: Telepítse a Windows Server-gazdagép MPIO
* 2. lépés: Az MPIO konfigurálása a StorSimple-köteteket
* 3. lépés: Csatlakoztatási StorSimple-köteteket a gazdagépen

A fenti lépések mindegyikét a következő szakaszban tárgyalt.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen a Windows Server-állomás és a virtuális tömb konfigurációs előfeltételeit.

### <a name="on-windows-server-host"></a>Windows Server-állomáson
* Győződjön meg arról, hogy a Windows Server-gazdagép 2 hálózati csatolót engedélyezni kell.

### <a name="on-storsimple-virtual-array"></a>A StorSimple virtuális tömb
* A virtuális tömbhöz az iSCSI-kiszolgálóként kell konfigurálni. További tudnivalókért lásd: [állítsa be az iSCSI-kiszolgálóként virtuális tömb](storsimple-virtual-array-deploy3-iscsi-setup.md). Egy vagy több hálózati illesztőre engedélyezni kell a tömb.   
* A virtuális tömb hálózati csatolókat a Windows Server gazdagépről elérhetőnek kell lennie.
* Egy vagy több kötetet kell létrehozni a StorSimple virtuális tömbjét. További tudnivalókért lásd: [kötet hozzáadása](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) a StorSimple virtuális tömbben. Az eljárás 3 kötetek (1 helyileg rögzített és 2 rétegzett kötetek látható az alábbi) a virtuális tömbben létrehozott.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>A StorSimple virtuális tömb hardverkonfiguráció
Az alábbi ábra a hardverkonfigurációja magas rendelkezésre állás és a többutas terheléselosztás a Windows Server rendszerű gazdagép és a StorSimple virtuális tömb használja az alábbi eljárással.

![az MPIO hardverkonfiguráció](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

A fenti ábrán látható:

* A StorSimple virtuális tömb kiépítve a Hyper-V az iSCSI-kiszolgálóként konfigurált egycsomópontos aktív eszköz,
* A tömb két virtuális hálózati adaptere engedélyezve vannak. A helyi webes felhasználói felületén a virtuális tömb, győződjön meg arról, hogy két hálózati adapterrel való engedélyezve vannak-e **hálózati beállítások** alább látható módon:
  
    ![Hálózati illesztők 1200 engedélyezve](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Vegye figyelembe az IPv4-címet a kompatibilis hálózati adapterek (Ethernet, Ethernet 2 alapértelmezés szerint), és mentse a gazdagépen későbbi használatra.
* Két hálózati adapterrel engedélyezve vannak a Windows Server-állomáson. Ha a gazdagép és a tömb csatlakoztatott felületek ugyanazon az alhálózaton, majd lesz 4 útvonal érhető el. Ez az eset ezzel az eljárással. Azonban ha a tárolótömböt és az állomás felület minden hálózati adapternek a különböző IP-alhálózat (és nem irányítható), majd csak 2 elérési utak lesz elérhető.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1. lépés: Telepítse a Windows Server-gazdagép MPIO
Az MPIO választható lehetőség a Windows Server, és alapértelmezés szerint nincs telepítve. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül. A szolgáltatás telepítéséhez a Windows Server-állomáson, hajtsa végre az alábbi eljárást.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2. lépés: Az MPIO konfigurálása a StorSimple-köteteket
Az MPIO kell úgy, hogy azonosítsa a StorSimple-köteteket. MPIO-t ismeri fel a StorSimple-köteteket konfigurálásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3. lépés: Csatlakoztatási StorSimple-köteteket a gazdagépen
Az MPIO konfigurálása után a Windows Server, a StorSimple tömbben létrehozott kötetek lehet csatlakoztatni, és majd kihasználhatja az MPIO a redundancia érdekében. Kötet csatlakoztatása, hajtsa végre a következő lépéseket.

#### <a name="to-mount-volumes-on-the-host"></a>Kötet csatlakoztatása a gazdagépen
1. Nyissa meg a **iSCSI-kezdeményező tulajdonságai** ablakban, a Windows Server-állomáson. Ugrás a **Kiszolgálókezelő > irányítópult > eszközök > iSCSI-kezdeményező**.
2. Az a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel, kattintson a **felderítési**, és kattintson a **tárolókapu felderítése**.
3. Az a **tárolókapu felderítése** párbeszédpanelen tegye a következőket:
   
    1. Az első engedélyezett hálózati illesztő IP-címét adja meg a StorSimple virtuális tömbjét. Alapértelmezés szerint ez lenne **Ethernet**. 
    2. Kattintson a **OK** való visszatéréshez a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel megnyitásához.
     
    > [!IMPORTANT]
    > Egy magánhálózaton használatakor az iSCSI-kapcsolatok adja meg az adatok portot, amelyet csatlakozik-e a magánhálózati IP-címét.
     
4. Ismételje meg a 2-3 a második hálózati adapter (például Ethernet 2) a tömb. 
5. Válassza ki a **célok** lapra a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel megnyitásához. A virtuális tömb kell megjelennie minden kötet felületet a cél **Felderített tárolók**. Ebben az esetben a három célok (három kötetek megfelelő) felderített lenne.
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Kattintson a **Connect** a StorSimple virtuális tömbbel rendelkező iSCSI-munkamenetet létrehozni. A **cél kapcsolódás** párbeszédpanel jelenik meg. Válassza ki a **engedélyezése többutas** jelölőnégyzetet. Kattintson a **speciális**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. Az a **speciális beállítások** párbeszédpanelen tegye a következőket:                                        
   
    1. Az a **helyi Adapter** legördülő listában válassza **Microsoft iSCSI-kezdeményező**.
    2. Az a **kezdeményező IP** legördülő listára, válassza ki a gazdagép IP-címét.
    3. Az a **tárolókapu** IP legördülő listára, válassza ki az IP-címe array felületet.
    4. Kattintson a **OK** való visszatéréshez a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel megnyitásához.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Kattintson a **Tulajdonságok** elemre. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. Az a **tulajdonságok** párbeszédpanel, kattintson a **munkamenet hozzáadása**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. Az a **cél kapcsolódás** párbeszédpanelen jelölje ki a **engedélyezése többutas** jelölőnégyzetet. Kattintson a **speciális**.
11. Az a **speciális beállítások** párbeszédpanel:                                        
    
    1. Az a **helyi adapter** legördülő listában válassza ki a Microsoft iSCSI-kezdeményező.

    2. Az a **kezdeményező IP** legördülő listára, válassza ki a gazdagép megfelelő IP-cím. Ebben az esetben kapcsolódik a tömb két hálózati adapterek az állomáson egyetlen hálózati adapterrel. Ezért ez az interfész, ugyanaz, mint az első munkamenete az előírt.

    3. Az a **cél portál IP-címet** legördülő listáról, válassza ki az IP-cím, a második adatok kapcsolat engedélyezve van a tömbben.

    4. Kattintson a **OK** való visszatéréshez az iSCSI-kezdeményező tulajdonságai párbeszédpanel megnyitásához. A cél hozzáadta a második munkamenet.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. A kívánt munkamenetek (elérési útja), a hozzáadása után a **iSCSI-kezdeményező tulajdonságai** párbeszédpanel mezőben jelölje ki a cél, és kattintson **tulajdonságok**. A munkamenetek lapján a **tulajdonságok** párbeszédpanel, vegye figyelembe a négy munkamenet a lehetséges útvonal kombinációinak megfelelő. A munkamenet megszakítása, jelölje be a munkamenet-azonosítót melletti jelölőnégyzetet, majd **Disconnect**.

    6. Munkamenetek belül eszközök megtekintéséhez jelölje ki a **eszközök** fülre. A többutas I/O házirend a kiválasztott eszköz konfigurálásához kattintson **MPIO**.

    7. A **részletek** párbeszédpanel jelenik meg. Az a **MPIO** lapon kiválaszthatja a megfelelő **terheléselosztási házirend** beállításait. Megtekintheti továbbá a **aktív** vagy **készenléti** elérési út típusa.
12. Ismételje meg a 8-11 további munkamenetek (elérési út) hozzáadása a célkiszolgálóhoz. Két felületet a gazdagépen és a virtuális tömbben két összesen négy munkamenetek mindegyik tárolóhoz is hozzáadhat. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Ismételje ezeket a lépéseket minden olyan kötetre (cél felületek) kell.
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Nyissa meg **számítógép-kezelés** útvonalon **Kiszolgálókezelő > irányítópult > Számítógép-kezelés**. Kattintson a bal oldali ablaktáblában **tárolási > Lemezkezelés**. A StorSimple virtuális tömbben létrehozott kötet(ek), az állomás számára látható jelenik meg a **Lemezkezelés** , új lemez(ek).
15. Végezze el a lemez inicializálását, és hozzon létre egy új kötetet. A formátum során válassza ki egy foglalásiegység-méret (Ausztráliai) 64 KB. Ismételje meg a műveletet az összes rendelkezésre álló köteteken.
    
    ![Lemezkezelés](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. A **Lemezkezelés**, kattintson a jobb gombbal a **lemez** válassza **tulajdonságok**.
17. Az a **többutas lemez eszköztulajdonságok** párbeszédpanel, kattintson a **MPIO** fülre.
    
    ![Lemeztulajdonságok](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. Az a **DSM neve** területén kattintson **részletek** , és ellenőrizze, hogy a paraméterek vannak-e beállítva az alapértelmezett paramétereket. Az alapértelmezett paraméterek a következők:
    
    * Elérési út ellenőrizze időszak = 30
    * Újbóli próbálkozások száma = 3
    * OEM eltávolítása időszak = 20
    * Újrapróbálkozási időköz = 1
    * Elérési út ellenőrzése engedélyezve = nincs bejelölve.
    
    > [!NOTE]
    > **Ne módosítsa az alapértelmezett paramétereket.**
   
## <a name="next-steps"></a>Következő lépések
További információ [a StorSimple Device Manager szolgáltatás segítségével felügyelheti a StorSimple virtuális tömb](storsimple-virtual-array-manager-service-administration.md).

