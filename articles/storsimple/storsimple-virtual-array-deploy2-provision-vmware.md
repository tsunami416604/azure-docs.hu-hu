---
title: VMware-ben a StorSimple Virtual Array üzembe helyezése |} A Microsoft Docs
description: A StorSimple Virtual Array telepítési sorozat második oktatóanyaga magában foglalja a VMware-ben a virtuális eszköz kiépítése.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247843"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>A StorSimple Virtual Array – üzembe helyezés VMware rendszerben üzembe helyezése
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti kiépítése, és a StorSimple Virtual Array a gazdagép operációs rendszert futtató VMware ESXi 5.0-, 5.5-ös, 6.0-s vagy 6.5-ös csatlakozni. Ez a cikk a StorSimple Virtual Arrayt az Azure Portalon és a Microsoft Azure Government Cloud központi telepítés vonatkozik.

A virtuális eszközök üzembe helyezéséhez és a hozzájuk való csatlakozáshoz rendszergazdai jogosultság szükséges. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Üzembe helyezési Előfeltételek
Gazdagép operációs rendszert futtató VMware ESXi 5.0-, 5.5-ös, 6.0-s vagy 6.5-ös, a virtuális eszköz előfeltételei a következők.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az összes lépés elvégzését követően [a portál előkészítése a StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* A virtuális eszköz kép: VMware-ről az Azure Portalról letöltött. További információkért lásd: **3. lépés: Töltse le a virtuális eszköz kép** , [StorSimple Virtual Array útmutató a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>A StorSimple virtuális eszköz
A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* Hyper-V rendszerű gazdagépen rendszerhez való hozzáférés rendelkezik (2008 R2 vagy újabb), amely lehet egy kiépítéséhez használt eszköz.
* A gazdarendszer képes elkülöníteni az alábbi erőforrásokat a virtuális eszköz üzembe helyezése érdekében:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha azt tervezi, konfigurálja a virtuális tömb fájlkiszolgálóként, 8 GB támogatja a kevesebb mint 2 millió fájlokat. 16 GB RAM, 2 – 4 millió fájl támogatásához szükséges.
  * Egy hálózati adapter.
  * 500 GB-os virtuális lemez a rendszer adatokat.

### <a name="for-the-network-in-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A StorSimple virtuális eszköz üzembe helyezéséhez a hálózati követelmények áttekintése és a követelmények az Adatközpont hálózatán konfigurálva. 

## <a name="step-by-step-provisioning"></a>Részletes kiépítése
És a virtuális eszköz csatlakozik, hajtsa végre a következő lépéseket kell:

1. Győződjön meg arról, hogy a gazdagép rendszere rendelkezik-e elegendő erőforrás a virtuális eszköz minimális követelményeinek.
2. A hipervizor a virtuális eszköz kiépítése.
3. A virtuális eszköz indítása és IP-címének lekéréséhez.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>1. lépés: Győződjön meg, hogy a gazdagép rendszere megfelel-e virtuális eszköz minimális követelményeknek
A virtuális eszköz létrehozásához szüksége lesz:

* Gazdagép operációs rendszert futtató VMware ESXi kiszolgáló 5.0-, 5.5-ös, 6.0-s vagy 6.5-ös való hozzáférést.
* VMware vSphere-ügyfél az adott rendszeren az ESXi-gazdagép kezeléséhez.

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha azt tervezi, konfigurálja a virtuális tömb fájlkiszolgálóként, 8 GB támogatja a kevesebb mint 2 millió fájlokat. 16 GB RAM, 2 – 4 millió fájl támogatásához szükséges.
  * Egy, a hálózatra csatlakozó hálózati adapter, amely képes a forgalmat az internetre irányítani. A minimális internetes sávszélességet, hogy az eszköz optimális működéséhez 5 MB/s kell lennie.
  * 500 GB-os virtuális lemez adatait.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>2. lépés: Virtuális eszköz üzembe helyezése a hipervizorban
A következő lépések végrehajtásával helyezzen üzembe egy virtuális eszközt a hipervizoron.

1. Másolja a virtuáliseszköz-rendszerképet a rendszerre. Letöltötte a virtuális lemezképet az Azure Portalon keresztül.

   1. Győződjön meg arról, hogy letöltötte a legfrissebb képfájlt. Ha a rendszerkép korábban letöltötte, töltse le újra a ellenőrizze, hogy a legújabb rendszerképet. A legújabb rendszerképet (helyett egy) két fájl tartozik.
   2. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.

2. Jelentkezzen be az ESXi-kiszolgálóra a vSphere-ügyféllel. A virtuális gép létrehozásához rendszergazdai jogosultsággal kell rendelkeznie.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. A vSphere-ügyfél, a készlet a szakaszban a bal oldali panelen válassza ki az ESXi-kiszolgálóhoz.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Töltse fel a VMDK-t az ESXi-kiszolgálóra. Keresse meg a **konfigurációs** lapon a jobb oldali ablaktáblán. A **hardver**válassza **tárolási**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. A jobb oldali panelen a **Datastores** (Adattárak) területen válassza ki azt az adattárat, amelybe fel kívánja tölteni a VMDK-t. Az adattárhoz az operációs rendszer és az adatlemezek elegendő szabad hellyel kell rendelkeznie.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kattintson a jobb gombbal, és válassza a **Browse Datastore** (Adattár tallózása) lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Megjelenik a **Datastore Browser** (Adattártallózó) ablak.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Az eszköztáron kattintson ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonra kattintva hozzon létre egy új mappát. Adja meg a mappa nevét, és jegyezze fel. Ezt a mappanevet fogja használni később, amikor létrehoz egy virtuális gépet (ajánlott eljárás). Kattintson az **OK** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Az új mappa megjelenik a **Datastore Browser** (Adattártallózó) bal oldali panelén.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. A Feltöltés ikonra ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) válassza **fájl feltöltése**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Tallózással keresse meg a letöltött VMDK-fájlokat. Kettő fájl érhető el. Válasszon ki egy feltöltendő fájlt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kattintson az **Open** (Megnyitás) elemre. Megkezdődik a VMDK-fájl feltöltése a megadott adattárra. A feltöltés több percig is eltarthat.
13. A feltöltést követően a fájl megjelenik az adattáron a létrehozott mappában.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Most töltse fel a második VMDK-fájlt is ugyanarra az adattárra.
14. Lépjen vissza a vSphere-ügyfél ablakába. A kiválasztott ESXi-kiszolgáló, kattintson a jobb gombbal, és válassza ki **új virtuális gép**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **hozzon létre új virtuális gép** ablak jelenik meg. Az a **konfigurációs** lapon válassza ki a **egyéni** lehetőséget. Kattintson a **tovább**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Az a **nevének és helyének** adja meg azokat a virtuális gép nevét. Ezt a nevet meg kell egyeznie a korábban megadott 8. lépése mappanév (ajánlott).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Az a **tárolási** lapra, jelölje be a virtuális gép kiépítéséhez használni kívánt adattárolót.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Az a **virtuális gép verziójának** lapon jelölje be **virtuálisgép-verzió: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Az a **vendég operációs rendszer** lapon válassza ki a **vendég operációs rendszer** , **Windows**. A **verzió**, a legördülő listából válassza ki a **Microsoft Windows Server 2012 (64 bites)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. A a **processzorok** lapon, módosítsa a **virtuális szoftvercsatornák számát** és **száma virtuális magok száma** úgy, hogy a **magok teljes száma** 4 (vagy több). Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Az a **memória** 8 GB (vagy több) RAM adja meg azokat. Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Az a **hálózati** adja meg azokat a hálózati adapterek számát. A minimális követelmény, több hálózati adapter.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Az a **SCSI-vezérlő** lap, fogadja el az alapértelmezett **LSI Logic vezérlő**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Az a **jelöljön ki egy lemezt** lapon a **használja a meglévő virtuális lemez**. Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Az a **meglévő lemez kiválasztása** lap **lemez elérési útja**, kattintson a **Tallózás**. Ekkor megnyílik egy **Tallózás adattárainak** párbeszédpanel. Keresse meg a helyet, ahol a VMDK feltöltött. Most már láthatja az adattárhoz csak egy fájlt, a két fájlt, amely eredetileg feltöltött egyesített. Válassza ki a fájlt, és kattintson a **OK**. Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Az a **speciális beállítások** lapon fogadja el az alapértelmezett, és kattintson **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. A **Ready to Complete** (Befejezésre kész) oldalon tekintse át az új virtuális gépre vonatkozó beállításokat. Ellenőrizze **befejezése előtt a virtuális gép beállításainak szerkesztése**. Kattintson a **Folytatás** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Az a **virtuális gépek tulajdonságai** lap a **hardver** lapra, keresse meg a hardver. Válassza ki **új merevlemez**. Kattintson a **Hozzáadás** parancsra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Megjelenik egy **hardver hozzáadása** ablak. A a **eszköztípus** lap **válassza ki a hozzáadni kívánt eszköz**, jelölje be **merevlemez**, és kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Az a **jelöljön ki egy lemezt** lapon a **hozzon létre egy új virtuális lemez**. Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Az a **hozzon létre egy lemezt** lapon, módosítsa a **lemezméret** 500 GB-os (vagy még több). A minimális követelmény 500 GB-os pedig mindig helyezhet nagyobb lemez. Vegye figyelembe, hogy nem bontsa ki a vagy a egyszer üzembe helyezett lemez zsugorítani. Kiépítés lemez mérete további információkért tekintse át a méretezési részt a [ajánlott eljárások a dokumentum](storsimple-ova-best-practices.md). A **lemez kiépítés**válassza **vékony létesítési**. Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Az a **speciális beállítások** lap, fogadja el az alapértelmezett.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Az a **Ready to Complete** lapon, a lemez beállítások áttekintéséhez. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Térjen vissza a virtuális gép tulajdonságai lapon. A virtuális gép bekerül egy új merevlemezre. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. A virtuális gépek, a jobb oldali panelen kiválasztott, keresse meg a **összefoglalás** fülre. Tekintse át a virtuális gép beállításait.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A rendszer üzembe helyezi a virtuális gépet. A következő lépés a virtuális gép bekapcsolása és az IP-cím lekérése.

> [!NOTE]
> Azt javasoljuk, hogy telepítse a VMware-eszközök a virtuális tömb (az üzembe helyezett fent). A VMware-eszközök telepítése egy nem támogatott konfigurációt eredményez.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>3. lépés: A virtuális eszköz elindítása és az IP-cím lekérése
Az alábbi lépések végrehajtásával indítsa el a virtuális eszközt, és csatlakozzon hozzá.

#### <a name="to-start-the-virtual-device"></a>A virtuális eszköz indítása
1. Indítsa el a virtuális eszközt. A vsphere Configuration Manager, a bal oldali panelen válassza ki az eszközt, és kattintson a jobb gombbal a helyi menü megjelenítéséhez. Válassza a **Power** (Főkapcsoló), majd a **Power on** (Bekapcsolás) lehetőséget. Ezzel bekapcsolja a virtuális gépet. Megtekintheti az állapotát alsó **legutóbbi tevékenységek** a vSphere client ablaktábláján.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. A beállítási feladatok végrehajtásához néhány percet vesz igénybe. Miután az eszköz fut, lépjen a **konzol** fülre. Jelentkezzen be az eszközt a Ctrl + Alt + Delete küldése. Másik lehetőségként a kurzort a konzolablakban, és nyomja le a Ctrl + Alt + Insert. Az alapértelmezett felhasználó *StorSimpleAdmin* és az alapértelmezett jelszó az *jelszó1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak tartalmaznia kell a 3 / 4 e követelmények közül: nagybetűk, kisbetűk, számjegyeket és speciális karaktereket. Írja be ismét a jelszót a megerősítéséhez. Értesíteni fogjuk, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. A jelszó sikeresen módosítását követően a virtuális eszköz előfordulhat, hogy újraindítás. Várjon, amíg befejeződik az újraindítás. A Windows PowerShell-konzolon az eszköz a előfordulhat, hogy együtt egy folyamatjelző jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Az eszköz nem DHCP-környezetben konzoljához, ha a következő képernyő jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Ezután konfigurálja a hálózatot.
7. Használja a `Get-HcsIpAddress` paranccsal listát készíthet a hálózati adapterek engedélyezve van a virtuális eszközön. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Erre alább láthat egy példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Az IP-cím használatával fog a webes felhasználói felületen, a virtuális eszköz csatlakozik, és fejezze be a helyi telepítését és regisztrálását.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Nem kötelező) Csak akkor, ha az eszköz a Government Cloud végzi, hajtsa végre ezt a lépést. Mostantól az eszközön az Egyesült Államok Szövetségi információk feldolgozása Standard (FIPS) üzemmód lehetővé teszi. A FIPS 140 szabvány határozza meg, bizalmas adatok védelme az USA szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat.

    1. Ahhoz, hogy a FIPS-módban, futtassa a következő parancsmagot:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, miután engedélyezte a FIPS-módban, hogy a kriptográfiai ellenőrzések érvénybe léptetéséhez.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS-módban az eszközön. Váltakozó az eszköz közötti FIPS és a FIPS módban nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, egy hibaüzenet jelenik meg a szalagcím szövegében (lásd alább). Módosítania kell az eszköz konfigurációját, hogy elegendő erőforrással rendelkezzen a minimális követelmények teljesítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. Tekintse meg a minimális konfigurációs követelményeinek [1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a virtuális eszköz minimális követelményeknek](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Ha a helyi webes felhasználói felület a kezdeti konfiguráció során bármilyen hiba között, tekintse meg a következő munkafolyamatok:

* Diagnosztikai tesztek futtatása annak [webes felhasználói felület beállításával kapcsolatos hibák elhárítása](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Napló csomagjának létrehozása és a naplók](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>További lépések
* [Állítsa be a StorSimple Virtual Array fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [Állítsa be a StorSimple Virtual Array egy iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
