---
title: StorSimple virtuális tömb kiépítése a VMware-ben
description: Ez a második oktatóanyag a StorSimple virtuális tömb központi telepítési sorozat magában foglalja a virtuális eszköz kiépítése a VMware.This second tutorial in StorSimple Virtual Array deployment series involveing a virtual device in VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272097"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple virtuális tömb telepítése – üzembe helyezés a VMware-ben
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az oktatóanyag bemutatja, hogyan lehet kiépíteni és csatlakozni egy StorSimple virtuális tömbhöz a VMware ESXi 5.0, 5.5, 6.0 vagy 6.5 rendszert futtató gazdarendszeren. Ez a cikk a StorSimple virtuális tömbök azure portalon és a Microsoft Azure Government Cloud-ban történő üzembe helyezésére vonatkozik.

A virtuális eszközök üzembe helyezéséhez és a hozzájuk való csatlakozáshoz rendszergazdai jogosultság szükséges. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Előfeltétel-kiépítés
A virtuális eszköz kiépítésének előfeltételei a VMware ESXi 5.0, 5.5, 6.0 vagy 6.5 rendszert futtató gazdarendszeren a következők.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az összes lépést [a Portál előkészítése a StorSimple virtuális tömbhöz](storsimple-virtual-array-deploy1-portal-prep.md)című részben.
* A VMware virtuális eszközképét az Azure Portalról töltötte le. További információ: **3. lépés: A Portál** [előkészítése a StorSimple virtuális tömbhöz című](storsimple-virtual-array-deploy1-portal-prep.md)virtuális eszközkép letöltése című témakörben talál.

### <a name="for-the-storsimple-virtual-device"></a>A StorSimple virtuális eszköz
A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* Hozzáféréssel rendelkezik egy Hyper-V (2008 R2 vagy újabb) rendszert futtató gazdarendszerhez, amely egy eszköz kiépítéséhez használható.
* A gazdarendszer képes elkülöníteni az alábbi erőforrásokat a virtuális eszköz üzembe helyezése érdekében:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha a virtuális tömböt fájlkiszolgálóként kívánja konfigurálni, a 8 GB kevesebb mint 2 millió fájlt támogat. Szüksége van 16 GB RAM-ra a 2-4 millió fájl támogatásához.
  * Egy hálózati adapter.
  * 500 GB-os virtuális lemez a rendszeradatokhoz.

### <a name="for-the-network-in-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Áttekintette a storSimple virtuális eszköz üzembe helyezéséhez vonatkozó hálózati követelményeket, és konfigurálta az adatközpont-hálózatot a követelmények nek megfelelően. 

## <a name="step-by-step-provisioning"></a>Lépésről lépésre történő kiépítés
Virtuális eszköz kiépítéséhez és csatlakozáshoz a következő lépéseket kell végrehajtania:

1. Győződjön meg arról, hogy a gazdarendszer elegendő erőforrással rendelkezik a virtuális eszközök minimális követelményeinek teljesítéséhez.
2. Virtuális eszköz kiépítése a hipervizorban.
3. Indítsa el a virtuális eszközt, és az IP-címet.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>1. lépés: Győződjön meg arról, hogy a gazdarendszer megfelel a virtuális eszközök minimális követelményeinek
Virtuális eszköz létrehozásához a következőkre lesz szükség:

* Hozzáférés a VMware ESXi Server 5.0, 5.5, 6.0 vagy 6.5 rendszert futtató gazdarendszerhez.
* VMware vSphere-ügyfél az adott rendszeren az ESXi-gazdagép kezeléséhez.

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha a virtuális tömböt fájlkiszolgálóként kívánja konfigurálni, a 8 GB kevesebb mint 2 millió fájlt támogat. Szüksége van 16 GB RAM-ra a 2-4 millió fájl támogatásához.
  * Egy, a hálózatra csatlakozó hálózati adapter, amely képes a forgalmat az internetre irányítani. A minimális internetes sávszélességnek 5 Mbps-nak kell lennie az eszköz optimális működésének lehetővé teszi.
  * Egy 500 GB-os virtuális lemez az adatokhoz.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>2. lépés: Virtuális eszköz kiépítése hipervizorban
A következő lépések végrehajtásával helyezzen üzembe egy virtuális eszközt a hipervizoron.

1. Másolja a virtuáliseszköz-rendszerképet a rendszerre. Ezt a virtuális lemezképet az Azure Portalon keresztül töltötte le.

   1. Győződjön meg arról, hogy a legújabb képfájlt töltötte le. Ha korábban töltötte le a képet, töltse le újra, hogy biztosan a legújabb képpel rendelkezzen. A legújabb kép két fájlt tartalmaz (egy helyett).
   2. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.

2. Jelentkezzen be az ESXi-kiszolgálóra a vSphere-ügyféllel. A virtuális gép létrehozásához rendszergazdai jogosultsággal kell rendelkeznie.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. A vSphere-ügyfélben a bal oldali ablaktábla készletszakaszában válassza ki az ESXi-kiszolgálót.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Töltse fel a VMDK-t az ESXi-kiszolgálóra. Keresse meg a jobb oldali ablaktábla **Konfiguráció** lapját. A **Hardver**csoportban válassza a **Tárolás**lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. A jobb oldali panelen a **Datastores** (Adattárak) területen válassza ki azt az adattárat, amelybe fel kívánja tölteni a VMDK-t. Az adattárnak elegendő szabad területtel kell rendelkeznie az operációs rendszer és az adatlemezek számára.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kattintson a jobb gombbal, és válassza a **Browse Datastore** (Adattár tallózása) lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Megjelenik a **Datastore Browser** (Adattártallózó) ablak.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Az eszköztáron kattintson az ikonra ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) új mappa létrehozásához. Adja meg a mappa nevét, és jegyezze fel. Ezt a mappanevet fogja használni később, amikor létrehoz egy virtuális gépet (ajánlott eljárás). Kattintson az **OK** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Az új mappa megjelenik a **Datastore Browser** (Adattártallózó) bal oldali panelén.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kattintson a ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) Feltöltés ikonra, és válassza **a Fájl feltöltése lehetőséget.**

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Tallózással keresse meg a letöltött VMDK-fájlokat. Kettő fájl érhető el. Válasszon ki egy feltöltendő fájlt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kattintson az **Open** (Megnyitás) elemre. Megkezdődik a VMDK-fájl feltöltése a megadott adattárra. A feltöltés több percig is eltarthat.
13. A feltöltést követően a fájl megjelenik az adattáron a létrehozott mappában.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Most töltse fel a második VMDK-fájlt is ugyanarra az adattárra.
14. Lépjen vissza a vSphere-ügyfél ablakába. Ha az ESXi kiszolgáló tikket van kiválasztva, kattintson a jobb gombbal, és válassza **az Új virtuális gép parancsot.**

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Megjelenik **egy Új virtuális gép létrehozása** ablak. A **Konfiguráció** lapon válassza az **Egyéni** lehetőséget. Kattintson a **Tovább** gombra.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. A **Név és hely** lapon adja meg a virtuális gép nevét. Ennek a névnek meg kell egyeznie a 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. A **Storage** lapon válassza ki a virtuális gép kiépítéséhez használni kívánt adattár.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. A **Virtuálisgép-verzió** lapon válassza a **Virtuálisgép-verzió: 8**lehetőséget.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. A **Vendég operációs rendszer** lapon válassza a **Vendég operációs rendszer** **Windows**rendszerként lehetőséget. A **Verzió**listában válassza a **Microsoft Windows Server 2012 (64 bites)** elemet.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. A **PROCESSZOROK** lapon állítsa be a **virtuális szoftvercsatornák számát** és a **magok számát virtuális szoftvercsatornánként** úgy, hogy a **magok teljes száma** 4 (vagy több). Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. A **Memória** lapon adjon meg 8 GB (vagy több) RAM-ot. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. A **Hálózat** lapon adja meg a hálózati adapterek számát. A minimális követelmény egy hálózati adapter.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Az **SCSI-vezérlő** lapon fogadja el az alapértelmezett **LSI Logikai SAS-vezérlőt**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. A **Lemez kiválasztása** lapon válassza **a Meglévő virtuális lemez használata**lehetőséget. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. A **Meglévő lemez kijelölése** lap **Lemezfájl elérési útja**csoportban kattintson a **Tallózás gombra.** Ezzel megnyílik egy **Tallózó adattár** párbeszédpanel. Keresse meg azt a helyet, ahol feltöltötte a VMDK-t. Most már csak egy fájlt lát az adattárban, mivel az eredetileg feltöltött két fájl egyesítésre került. Jelölje ki a fájlt, és kattintson az **OK gombra.** Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. A **Speciális beállítások** lapon fogadja el az alapértelmezett értéket, és kattintson a **Tovább**gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. A **Ready to Complete** (Befejezésre kész) oldalon tekintse át az új virtuális gépre vonatkozó beállításokat. Ellenőrizze **a virtuális gép beállításainak szerkesztése a befejezés előtt**. Kattintson a **Folytatás** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. A **Virtuális gépek tulajdonságai** lap **Hardver** lapján keresse meg az eszköz hardverét. Válassza az **Új merevlemez**lehetőséget . Kattintson a **Hozzáadás** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Megjelenik a **Hardver hozzáadása** ablak. Az **Eszköz típusa** lap **Válassza ki a hozzáadni kívánt eszköz típusát,** válassza a **Merevlemez**lehetőséget, majd kattintson a **Tovább**gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. A **Lemez kiválasztása** lapon válassza **az Új virtuális lemez létrehozása**lehetőséget. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. A **Lemez létrehozása** lapon módosítsa a **Lemezméretet** 500 GB-ra (vagy többre). Míg 500 GB a minimális követelmény, mindig kiépíthet egy nagyobb lemezt. Ne feledje, hogy a lemez kiépítés után nem bontható ki és nem zsugorítható. A kiépítandó lemez méretéről további információt a ajánlott [eljárásokat tartalmazó dokumentum méretezési](storsimple-ova-best-practices.md)szakaszában talál. A **Lemezkiépítés**csoportban válassza a **Vékony kiépítés**lehetőséget. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. A **Speciális beállítások** lapon fogadja el az alapértelmezett értéket.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. A **Kész a befejezésre** lapon tekintse át a lemezbeállításokat. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Visszatérés a Virtuálisgép tulajdonságai lapra. A rendszer új merevlemezt ad a virtuális géphez. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Ha a virtuális gép ki van jelölve a jobb oldali ablaktáblában, keresse meg az **Összegzés** lapot.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A rendszer üzembe helyezi a virtuális gépet. A következő lépés a virtuális gép bekapcsolása és az IP-cím lekérése.

> [!NOTE]
> Azt javasoljuk, hogy ne telepítse a VMware-eszközöket a virtuális tömbre (a fent kiépített ek szerint). A VMware-eszközök telepítése egy nem támogatott konfigurációt eredményez.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>3. lépés: Indítsa el a virtuális eszközt, és kap az IP
Az alábbi lépések végrehajtásával indítsa el a virtuális eszközt, és csatlakozzon hozzá.

#### <a name="to-start-the-virtual-device"></a>A virtuális eszköz indítása
1. Indítsa el a virtuális eszközt. A vSphere Configuration Manager, a bal oldali ablaktáblában válassza ki az eszközt, és a jobb gombbal, hogy ki a helyi menüben. Válassza a **Power** (Főkapcsoló), majd a **Power on** (Bekapcsolás) lehetőséget. Ezzel bekapcsolja a virtuális gépet. Az állapotot a vSphere-ügyfél **Legutóbbi feladatok** ablaktáblájában tekintheti meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. A beállítási feladatok végrehajtása néhány percet vesz igénybe. Az eszköz futása után lépjen a **Konzol** lapra. Másik lehetőségként a kurzort a konzolablakra irányíthatja, és lenyomja a Ctrl+Alt+Insert billentyűkombinációt. Az alapértelmezett felhasználó a *StorSimpleAdmin,* az alapértelmezett jelszó pedig a *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak a következő követelmények közül 4-et kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karaktereket. Írja be ismét a jelszót a megerősítéséhez. Értesítést kap arról, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. A jelszó sikeres módosítása után a virtuális eszköz újraindulhat. Várja meg, amíg az újraindítás befejeződik. Előfordulhat, hogy az eszköz Windows PowerShell konzolja megjelenik egy folyamatjelző sávval együtt.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Ha nem DHCP-környezetben indította el az eszközt, a következő képernyő jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Ezután konfigurálja a hálózatot.
7. A `Get-HcsIpAddress` paranccsal felsorolhatja a virtuális eszközön engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Erre alább láthat egy példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Ezt az IP-címet fogja használni a virtuális eszköz webes felhasználói felületéhez való csatlakozáshoz, és befejezheti a helyi telepítést és regisztrációt.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Nem kötelező) Ezt a lépést csak akkor hajtsa végre, ha az eszközt a Kormányzati felhőben telepíti. Most antól engedélyezi az Egyesült Államok Szövetségi Információfeldolgozási Szabvány (FIPS) üzemmódját az eszközén. A FIPS 140 szabvány az Egyesült Államok szövetségi szövetségi számítógépes rendszerei által a bizalmas adatok védelmére jóváhagyott kriptográfiai algoritmusokat határozza meg.

    1. A FIPS mód engedélyezéséhez futtassa a következő parancsmast:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, miután engedélyezte a FIPS módot, hogy a kriptográfiai érvényesítés érvénybe lépjen.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS módot az eszközön. Az eszköz FIPS és nem FIPS mód közötti váltakozása nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, egy hibaüzenet jelenik meg a szalagcím szövegében (lásd alább). Módosítania kell az eszköz konfigurációját, hogy elegendő erőforrással rendelkezzen a minimális követelmények teljesítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. A minimális konfigurációs követelményeket az [1. lépésben tekintheti meg, amely ismerteti, hogyan ellenőrizheti a gazdarendszer megfelelését a virtuális eszközökre vonatkozó minimális követelményeknek](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Ha a helyi webes felhasználói felület használatával történő kezdeti konfiguráció során bármilyen más hibával szembesül, olvassa el a következő munkafolyamatokat:

* Diagnosztikai tesztek futtatása a [webes felhasználói felület telepítésének elhárításához.](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)
* [Naplócsomag létrehozása és naplófájlok megtekintése](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>További lépések
* [A StorSimple virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [A StorSimple virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
