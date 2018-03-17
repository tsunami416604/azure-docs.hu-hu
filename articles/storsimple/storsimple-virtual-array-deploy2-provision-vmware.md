---
title: "Kiépíteni a StorSimple virtuális tömb VMware-ben |} Microsoft Docs"
description: "A StorSimple virtuális tömb telepítési sorozat második oktatóanyag magában foglalja a VMware-ben a virtuális eszköz kiépítése."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be737550aa1cff22bc413ee8ce243f8d8588f6a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>A StorSimple virtuális tömb - Provision VMware-ben telepítése
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan szeretnék telepíteni, és a StorSimple virtuális tömbhöz a gazdagép operációs rendszert futtató VMware ESXi 5.0, 5.5 vagy 6.0 csatlakozni. Ez a cikk a központi telepítést a StorSimple virtuális tömbök, Azure-portál és a Microsoft Azure Government felhő vonatkozik.

Kiépítés rendszergazdai jogosultságokat igényel, és csatlakozzon a virtuális eszközhöz. Az üzembe helyezési és a kezdeti telepítés befejezéséhez körülbelül 10 percig is eltarthat.

## <a name="provisioning-prerequisites"></a>Telepítési előfeltételek
A gazdagép operációs rendszert futtató VMware ESXi 5.0, 5.5 vagy 6.0, a virtuális eszköz kiépítése Előfeltételek a következők:

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Összes lépését végrehajtotta [készítse elő a portál a StorSimple virtuális tömb](storsimple-virtual-array-deploy1-portal-prep.md).
* A virtuális eszköz kép VMware az Azure-portálról letöltött. További információkért lásd: **3. lépés: Töltse le a virtuális eszköz kép** a [készítse elő a portál a StorSimple virtuális tömb útmutató](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>A StorSimple virtuális eszköz
A virtuális eszköz telepítése előtt győződjön meg arról, hogy:

* Rendelkezik hozzáféréssel a rendszeren futó Hyper-V gazdagéphez (2008 R2 vagy újabb), amely lehet egy kiépítéséhez használt eszköz.
* A gazdagép rendszere tudni jelölt ki a virtuális eszköz létrehozásához a következőket:

  * Legalább 4 mag.
  * Legalább 8 GB RAM-mal. Ha szeretné konfigurálni a virtuális tömb fájlkiszolgálóként, a 8 GB legalább 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájlok támogatásához szükséges.
  * Egy hálózati csatoló.
  * 500 GB-os virtuális lemez Rendszeradatok.

### <a name="for-the-network-in-datacenter"></a>Az Adatközpont hálózati
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A StorSimple virtuális eszköz telepítése a hálózati követelmények áttekintése és konfigurálni az adatközponti hálózathoz, a követelményeknek. 

## <a name="step-by-step-provisioning"></a>Részletes kiépítése
Ellátásához, majd csatlakozzon a virtuális eszközhöz, hajtsa végre a következő lépéseket kell:

1. Győződjön meg arról, hogy a gazdagép rendszere a virtuális eszköz minimális követelmények teljesítéséhez elegendő erőforrással rendelkezik-e.
2. A hipervizor a virtuális eszköz kiépítése.
3. Indítsa el a virtuális eszköz és az IP-cím beolvasása.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>1. lépés: Győződjön meg arról, gazdagép rendszere megfelel a virtuális eszköz minimális követelményei
A virtuális eszköz létrehozásához szüksége lesz:

* VMware ESXi Server 5.0, 5.5 vagy 6.0 futó gazdarendszer való hozzáférést.
* VMware vSphere ügyfél, a rendszer kezeléséhez az ESXi-állomáson.

  * Legalább 4 mag.
  * Legalább 8 GB RAM-mal. Ha szeretné konfigurálni a virtuális tömb fájlkiszolgálóként, a 8 GB legalább 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájlok támogatásához szükséges.
  * Több hálózati adapter képes az internetre irányuló forgalom útválasztási hálózathoz csatlakoztatva. A minimális internetes sávszélességet 5 MB/s, hogy az eszköz optimális működéséhez kell lennie.
  * 500 GB-os virtuális lemez adatait.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>2. lépés: A hipervizor virtuális eszköz kiépítése
A következő lépésekkel a hipervizor a virtuális eszköz létrehozásához.

1. Másolja át a virtuális eszköz lemezképet, a rendszeren. A virtuális lemezképet, az Azure portálon keresztül letöltött.

   1. Győződjön meg arról, hogy már letöltötte a legfrissebb lemezképfájlt. Ha korábban le a lemezképet, töltse le újra a győződjön meg arról, hogy rendelkezik-e a legújabb kép. A legújabb lemezképben van két fájlt (helyett egy).
   2. Jegyezze fel a használ a kép az eljárás későbbi szakaszában, ahová másolása a lemezképet.

2. Jelentkezzen be a vSphere-ügyfélprogrammal ESXi-kiszolgálón. Virtuális gép létrehozása rendszergazdai jogosultsággal kell.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. A vSphere ügyfél, a bal oldali ablaktáblán, a készlet szakaszban válassza ki az ESXi-kiszolgálón.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. A vmdk-fájl feltöltése az ESXi-kiszolgálón. Keresse meg a **konfigurációs** fülre a jobb oldali ablaktáblán. A **hardver**, jelölje be **tárolási**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. A jobb oldali ablaktáblában a **Datastores**, válassza ki az adattár feltöltése a vmdk-fájl helyét. Az adattárral az operációsrendszer- és adatlemezek elegendő szabad hellyel kell rendelkeznie.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kattintson a jobb gombbal, és válassza ki **Tallózás Datastore**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. A **Datastore böngésző** ablak jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Kattintson az eszköztáron ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonra kattintva hozzon létre egy új mappát. Adja meg a mappa nevét, és jegyezze fel a azt. A mappa neve használandó később (ajánlott az ajánlott eljárás) virtuális gép létrehozásakor. Kattintson az **OK** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A bal oldali ablaktábláján megjelenik az új mappába a **Datastore böngésző**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kattintson a Feltöltés ikonra ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) válassza **fájl feltöltése**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Keresse meg, és a letöltött VMDK fájlok. Két fájl van. Válassza ki a fájlt a feltöltéshez.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kattintson a **nyitott**. Megkezdődik a feltöltés, a VMDK-fájl a megadott adattárral. A feltöltendő fájl több percig is eltarthat.
13. A feltöltés befejeződése után megjelenik a fájl a mappában létrehozott az adattárral.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Az azonos adattárral töltsön fel a második VMDK-fájl.
14. Térjen vissza a vSphere client ablak. Kijelölt ESXi-kiszolgálóval, kattintson a jobb gombbal, és válassza ki **új virtuális gép**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **hozzon létre új virtuális gép** ablak jelenik meg. Az a **konfigurációs** lapon jelölje be a **egyéni** lehetőséget. Kattintson a **Tovább** gombra.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Az a **név és hely** adja meg azokat a virtuális gép nevét. Ezt a nevet meg kell felelnie a 8. lépés korábban megadott mappanév (ajánlott).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Az a **tárolási** lapon, válassza ki a virtuális gép kiépítéséhez használni kívánt adattárolót.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Az a **virtuális gép verziójának** lapon jelölje be **virtuális gép verziójának: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Az a **vendég operációs rendszer** lapon jelölje be a **vendég operációs rendszer** , **Windows**. A **verzió**, a legördülő listából válassza ki a **Microsoft Windows Server 2012 (64 bites)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Az a **processzorok** lapján állítsa be a **virtuális szoftvercsatornák számát** és **virtuális szoftvercsatorna magok számát** , hogy a **magok száma** 4 (vagy több). Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Az a **memória** csoportjában adja meg a 8 GB (vagy több) RAM-mal. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Az a **hálózati** adja meg azokat a hálózati adapterek száma. A minimális mérete legalább egy hálózati illesztőt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Az a **SCSI-vezérlő** lap, fogadja el az alapértelmezett **LSI Logic vezérlő**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Az a **válasszon ki egy lemezt** lapon, válassza ki **meglévő virtuális lemez használata**. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Az a **meglévő lemez kiválasztása** lap **lemezt fájlútvonallal**, kattintson a **Tallózás**. Ekkor megnyílik egy **Tallózás Datastores** párbeszédpanel. Keresse meg a helyet, ahol a VMDK feltöltött. Mivel a két fájl kezdetben feltöltött egyesített ekkor megjelenik az adattárral csak egy fájlt. Válassza ki a fájlt, és kattintson a **OK**. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Az a **speciális beállítások** lapon fogadja el az alapértelmezett beállításokat, majd kattintson **következő**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Az a **készen áll a Complete** lapján tekintse át az új virtuális géphez társított összes beállítást. Ellenőrizze **még a befejeződése előtt a virtuális gép beállításainak szerkesztése**. Kattintson a **továbbra is**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Az a **virtuális gépek tulajdonságai** lap a **hardver** lapján keresse meg a hardver. Válassza ki **új merevlemez**. Kattintson a **Hozzáadás** parancsra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Megjelenik egy **hardver hozzáadása** ablak. A a **eszköztípus** lap **válassza ki a hozzáadni kívánt eszköz**, jelölje be **merevlemez**, és kattintson a **következő**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Az a **válasszon ki egy lemezt** lapon, válassza ki **hozzon létre egy új virtuális lemez**. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. A a **lemez** lapján módosíthatja a **lemezméretet** 500 GB (vagy több). A minimális követelmény 500 GB pedig egy nagyobb méretű lemezt mindig oszthat meg. Vegye figyelembe, hogy nem kibontása vagy Zsugorítás többször kiépített lemez. Kiépítését lemez mérete további információkért tekintse át a méretezési részt a [ajánlott eljárások a dokumentum](storsimple-ova-best-practices.md). A **lemez kiépítés**, jelölje be **vékony létesítési**. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Az a **speciális beállítások** lap, fogadja el az alapértelmezett.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Az a **készen áll a Complete** lapján tekintse át a lemezt beállításokat. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. A virtuális gép tulajdonságainak laphoz való visszatéréshez. A virtuális gép bekerül egy új merevlemezre. Kattintson a **Befejezés** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. A virtuális géppel, a jobb oldali panelen kiválasztott, navigáljon a **összegzés** fülre. Tekintse át a virtuális gép beállításait.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A virtuális gép ezzel ki van építve. A következő lépésre az IP-címet, és kapcsolja be ezt a gépet.

> [!NOTE]
> Azt javasoljuk, hogy nem telepíti a VMware-eszközök a virtuális tömb (ahogy fent kiépítése). A VMware-eszközök telepítése egy nem támogatott konfiguráció eredményez.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>3. lépés: Indítsa el a virtuális eszköz és az IP-cím beszerzése
A következő lépésekkel indítsa el a virtuális eszköz és kapcsolódni hozzá.

#### <a name="to-start-the-virtual-device"></a>A virtuális eszköz elindításához
1. Indítsa el a virtuális eszköz. A vSphere Configuration Manager, a bal oldali panelen válassza ki azt az eszközt, és kattintson a jobb gombbal a helyi menüből nyithat. Válassza ki **Power** majd **Bekapcsolással**. Ez a virtuális gépen kell kapcsolja. Megtekintheti a állapotát alsó **legutóbbi feladatok** a vSphere client panelén.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. A beállítási feladatok befejezéséhez néhány percet vesz igénybe. Ha az eszköz fut, nyissa meg a **konzol** fülre. Ctrl + Alt + Delete próbál bejelentkezni az eszköz küldése. Másik lehetőségként a kurzort a konzolablakban a, és nyomja le a Ctrl + Alt + Insert. Az alapértelmezett felhasználó *StorSimpleAdmin* és az alapértelmezett jelszó *jelszó1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Biztonsági okokból az eszköz rendszergazdai jelszava lejár, az első bejelentkezéskor. Módosítsa a jelszavát kéri.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Adjon meg legalább 8 karakterből álló jelszót. 3 kívüli 4 ezeket a követelményeket a jelszóban: nagybetű, nagybetűk, numerikus és speciális karaktereket. Adja meg újra a jelszót a megerősítéshez. Értesítést fog kapni, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. A jelszó sikeresen módosította, a virtuális eszköz előfordulhat, hogy újraindítása után. Várjon, amíg befejeződik az újraindítás. A Windows PowerShell-konzolon, az eszköz a lehetséges, hogy együtt egy folyamatjelző jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. 6 – 8. lépések csak akkor alkalmazható, ha a-DHCP környezetben rendszerindítást. Ha egy DHCP-környezetben, hagyja ki ezeket a lépéseket, és folytassa a 9. Ha az eszköz nem DHCP-környezet telepítése rendszerindításhoz, látni fogja a következő képernyő.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Ezután konfigurálja a hálózaton.
7. Használja a `Get-HcsIpAddress` paranccsal listát készíthet a hálózati csatolót engedélyezni kell a virtuális eszközön. Ha az eszköz egyetlen hálózati adapter engedélyezve van, az alapértelmezés szerint ez az interfész rendelt ez `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Használja a `Set-HcsIpAddress` parancsmagot, hogy a hálózat konfigurálása. Példa alatt:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. A kezdeti telepítés befejezése után, az eszköz fel betöltődött, látni fogja az eszköz szalagcím szöveg. Jegyezze fel az IP-cím és az URL-címet a szalagcím szövegként jelenik meg az eszköz felügyeletére. Az IP-cím használatával fog a webes felhasználói felület, a virtuális eszköz csatlakozhat, és fejezze be a helyi telepítését és regisztrálását.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Választható) Hajtsa végre ezt a lépést, csak akkor, ha az eszköz a kormányzati felhőben telepíti. Most már az eszközön az Amerikai Egyesült Államokban Federal Information Processing Standard (FIPS) mód lehetővé teszi. A FIPS 140 szabvány határozza meg a bizalmas adatok védelmének amerikai szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat.

    1. Ahhoz, hogy a FIPS-módban, futtassa az alábbi parancsmagot:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, engedélyeznie kell a FIPS-módban, hogy a kriptográfiai ellenőrzések érvénybe léptetéséhez.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS-módban az eszközön. Váltakozó az eszköz közötti FIPS és a FIPS módban nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeinek, látni fogja a hibát jelez a fejléc szövege (lásd alább). Szüksége lesz az eszköz konfigurációjának módosítása, hogy a minimális követelményeknek megfelelő erőforrások. Ezután indítsa újra, és csatlakozzon az eszközhöz. Tekintse meg a minimális konfigurációs követelményeinek [1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a virtuális eszköz minimális követelmények](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Ha a helyi webes felhasználói felület használata a kezdeti konfiguráció során bármilyen hiba akkor szembesülhetnek, tekintse meg a következő munkafolyamatok:

* Diagnosztikai tesztek futtatása annak [webes felhasználói felületen való beállításának hibaelhárítása](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Napló csomagot és naplófájlban](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>További lépések
* [Állítsa be a StorSimple virtuális tömb fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [Állítsa be a StorSimple virtuális tömb iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
