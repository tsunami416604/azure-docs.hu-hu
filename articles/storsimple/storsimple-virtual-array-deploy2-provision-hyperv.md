---
title: "A Hyper-v-ben a StorSimple virtuális tömb kiépítése |} Microsoft Docs"
description: "A StorSimple virtuális tömb telepítési második oktatóanyag magában foglalja a kiépítés a Hyper-V egy virtuális tömb."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad431c8958f7d381bb9c0410caa3a57c6e75c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>A StorSimple virtuális tömb - Provision a Hyper-V telepítése
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan lehet kiépíteni a StorSimple virtuális tömb állomás operációs rendszert futtató Hyper-V a Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. Ez a cikk a központi telepítést a StorSimple virtuális tömbök, Azure-portál és a Microsoft Azure Government felhő vonatkozik.

Kiépítés rendszergazdai jogosultságokat igényel, és konfigurálja a virtuális tömb. Az üzembe helyezési és a kezdeti telepítés befejezéséhez körülbelül 10 percig is eltarthat.

## <a name="provisioning-prerequisites"></a>Telepítési előfeltételek
Itt megtalálja az előfeltételek telepítéséhez egy virtuális tömb állomás operációs rendszert futtató Hyper-V a Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Összes lépését végrehajtotta [készítse elő a portál a StorSimple virtuális tömb](storsimple-virtual-array-deploy1-portal-prep.md).
* A Hyper-V virtuális tömb kép már letöltötte az Azure portálról. További információkért lásd: **3. lépés: Töltse le a virtuális tömb kép** a [készítse elő a portál a StorSimple virtuális tömb útmutató](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > A StorSimple virtuális tömb futó szoftver csak a StorSimple Device Manager szolgáltatás is használhatók.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb
Egy virtuális tömb telepítése előtt győződjön meg arról, hogy:

* Gazdagép operációs rendszert futtató Hyper-V a Windows Server 2008 R2 vagy újabb, amely hozzáféréssel rendelkezik a kiépítését használt eszköz.
* A gazdagép rendszere tudni jelölt ki a virtuális tömb telepítéséhez a következőket:

  * Legalább 4 mag.
  * Legalább 8 GB RAM-mal. Ha szeretné konfigurálni a virtuális tömb fájlkiszolgálóként, a 8 GB legalább 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájlok támogatásához szükséges.
  * Egy hálózati csatoló.
  * 500 GB-os virtuális lemez adatait.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt elkezdené, tekintse át a StorSimple virtuális tömb telepítéséhez, és az Adatközpont-hálózat megfelelő konfigurálása a hálózati követelmények. További információkért lásd: [StorSimple virtuális tömb hálózati követelményei](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Részletes kiépítése
A kiépítése, és kapcsolódjon a virtuális tömb, hajtsa végre a következő lépéseket kell:

1. Győződjön meg arról, hogy a gazdagép rendszere a minimális virtuális tömb követelményeinek megfelelően elegendő erőforrással rendelkezik-e.
2. Egy virtuális tömbhöz a hipervizor kiépítéséhez.
3. Indítsa el a virtuális tömb, és az IP-cím beolvasása.

A lépések esetén, tekintse meg a következő szakaszokban.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a virtuális tömb minimális követelményeknek
Hozzon létre egy virtuális tömb, az alábbiak szükségesek:

* A Hyper-V szerepkör telepítése a Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 SP1.
* Microsoft Hyper-V kezelőjével, a Microsoft Windows-ügyfél kapcsolódik a gazdagéphez.

Gondoskodjon arról, hogy a mögöttes hardver (gazdagép rendszere), amely létrehozza a virtuális tömb tudni jelölt ki a következő források a virtuális tömbhöz:

* Legalább 4 mag.
* Legalább 8 GB RAM-mal. Ha szeretné konfigurálni a virtuális tömb fájlkiszolgálóként, a 8 GB legalább 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájlok támogatásához szükséges.
* Egy hálózati csatoló.
* 500 GB-os virtuális lemez Rendszeradatok.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>2. lépés: A hipervizor egy virtuális tömb kiépítése
A következő lépésekkel egy eszközt a hipervizor kiépítéséhez.

#### <a name="to-provision-a-virtual-array"></a>Egy virtuális tömb kiépítése
1. A Windows Server-állomáson másolja át a virtuális tömb lemezképet egy helyi meghajtóra. Ez a rendszerkép (VHD- vagy VHDX-) letöltötte az Azure portálon keresztül. Jegyezze fel a használ a kép az eljárás későbbi szakaszában, ahová másolása a lemezképet.
2. Nyissa meg **Kiszolgálókezelő**. Kattintson a jobb felső sarokban, **eszközök** válassza **Hyper-V kezelője**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Ha Windows Server 2008 R2 fut, nyissa meg a Hyper-V kezelőt. A Kiszolgálókezelőben kattintson **szerepkörök > Hyper-V > Hyper-V kezelője**.
3. A **Hyper-V kezelője**, a hatókör ablaktáblán kattintson a jobb gombbal a csomópont a helyi menü megnyitásához, és kattintson a **új** > **virtuális gép**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Az a **megkezdése előtt** lapon kattintson az új virtuális gép varázsló **következő**.
5. Az a **név és hely megadása** lapján adjon meg egy **neve** a virtuális tömbhöz. Kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Az a **adnia generációját** lapon, válassza ki az eszköz kép típusát, majd **következő**. Ez a lap nem jelenik meg, a Windows Server 2008 R2 használata esetén.

   * Válasszon **2. generációs** Ha letöltötte a .vhdx-lemezkép a Windows Server 2012 vagy újabb.
   * Válasszon **1. generáció** Ha letöltötte a .vhd lemezképek a Windows Server 2008 R2 vagy újabb.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Az a **memóriát rendelnek** adja meg azokat a **indítási memória** a legalább **8192 MB**, ne engedélyezze a dinamikus memóriát, és kattintson a **következő**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Az a **Hálózatkezelés beállítása** lapon, adja meg a virtuális kapcsoló, amely kapcsolódik az internethez, és kattintson **következő**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Az a **virtuális merevlemez csatlakoztatása** lapon, válassza ki **meglévő virtuális merevlemez használata**, adja meg a helyet a virtuális tömb kép (.vhdx vagy .vhd), és kattintson a **következő**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Tekintse át a **összegzés** majd **Befejezés** a virtuális gép létrehozásához.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. A minimális követelményeknek, 4 mag van szükség. 4 virtuális processzor hozzáadásához válassza a gazdagép rendszere a a **Hyper-V kezelője** ablak. A jobb oldali ablaktáblában listája alatt **virtuális gépek**, keresse meg az imént létrehozott virtuális gép. Válassza ki, és kattintson a jobb gombbal a számítógép nevét, majd **beállítások**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Az a **beállítások** oldalon, a bal oldali ablaktáblán, kattintson a **processzor**. A jobb oldali ablaktáblában, állítson be **a virtuális processzorok száma** 4 (vagy több). Kattintson az **Alkalmaz** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. A minimális követelmények teljesítéséhez is kell hozzáadnia 500 GB-os virtuális adatlemezt. Az a **beállítások** lap:

    1. A bal oldali panelen válassza ki a **SCSI-vezérlő**.
    2. A jobb oldali ablaktáblában jelölje ki a **merevlemez,** kattintson **Hozzáadás**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Az a **merevlemez-meghajtóról** lapon jelölje be a **virtuális merevlemez** lehetőséget, majd kattintson a **új**. A **új virtuális merevlemez varázsló** kezdődik.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Az a **megkezdése előtt** lapon kattintson az új virtuális merevlemez varázsló **következő**.
16. Az a **lemezformátum lap**, fogadja el az alapértelmezett beállítás a **VHDX** formátumban. Kattintson a **Tovább** gombra. Ez a képernyő nem jelenik meg, ha a Windows Server 2008 R2 rendszerű.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Az a **lemeztípus lap**, állítsa be a virtuális merevlemez típusát, **dinamikusan bővülő** (ajánlott). **Rögzített méretű** lemez működik, de előfordulhat, hogy hosszú ideig várjon. Azt javasoljuk, hogy nem használja a **Differencing** lehetőséget. Kattintson a **Tovább** gombra. A Windows Server 2012 R2 és Windows Server 2012-ben **dinamikusan bővülő** lesz az alapértelmezett beállítás, mivel a Windows Server 2008 R2, az alapértelmezett érték **mérete rögzített**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. A a **név és hely megadása** lapján adjon meg egy **neve** , valamint **hely** (tallózással megkereshet egy) az adatok lemez. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Az a **lemez beállítása** lapon, a beállításnak a **hozzon létre egy új üres virtuális merevlemez** , és adja meg a méret a **500 GB** (vagy több). A minimális követelmény 500 GB pedig egy nagyobb méretű lemezt mindig oszthat meg. Vegye figyelembe, hogy nem kibontása vagy Zsugorítás többször kiépített lemez. Kiépítését lemez mérete további információkért tekintse át a méretezési részt a [ajánlott eljárások a dokumentum](storsimple-ova-best-practices.md). Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Az a **összegzés** lapon ellenőrizze a virtuális adatlemez részleteit, és ha mindent megfelelőnek talált, kattintson a **Befejezés** hozhat létre a lemezt. A varázsló bezárása után, és a virtuális merevlemez ad hozzá a számítógépet.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Lépjen vissza a **beállítások** lap. Kattintson a **OK** bezárásához a **beállítások** lapon, és visszatérhet a Hyper-V Manager ablakát.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>3. lépés: Indítsa el a virtuális tömb, és az IP-cím beszerzése
A következő lépésekkel indítsa el a virtuális tömb és kapcsolódni hozzá.

#### <a name="to-start-the-virtual-array"></a>A virtuális tömb elejéig
1. Indítsa el a virtuális tömb.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Miután az eszköz fut, válassza ki az eszközt, kattintson a jobb gombbal és válassza ki **Connect**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Előfordulhat, hogy készen áll arra, hogy az eszköz 5 – 10 percig is várakoznia. Állapotüzenet jelenik meg a konzol jelzik, hogy. Miután az eszköz készen áll, lépjen **művelet**. Nyomja le az `Ctrl + Alt + Delete` bejelentkezni a virtuális tömb. Az alapértelmezett felhasználó *StorSimpleAdmin* és az alapértelmezett jelszó *jelszó1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Biztonsági okokból az eszköz rendszergazdai jelszava lejár, az első bejelentkezéskor. Módosítsa a jelszavát kéri.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Adjon meg legalább 8 karakterből álló jelszót. A jelszót meg kell felelnie legalább 3 kívül az alábbi 4 követelményeknek: nagybetűk, nagybetűk, numerikus és speciális karaktereket. Adja meg újra a jelszót a megerősítéshez. Értesítés jelenik meg, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. A jelszó sikeresen módosítását követően a virtuális tömb újraindulhat. Várjon, amíg az eszköz elindításához.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    A Windows PowerShell-konzolon, az eszköz együtt egy folyamatjelző jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. 6 – 8. lépések csak akkor alkalmazható, ha a-DHCP környezetben rendszerindítást. Ha egy DHCP-környezetben, hagyja ki ezeket a lépéseket, és folytassa a 9. Ha az eszköz nem DHCP-környezet telepítése rendszerindításhoz, látni fogja a következő képernyő.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Ezután konfigurálja a hálózaton.
7. Használja a `Get-HcsIpAddress` paranccsal listát készíthet a hálózati csatolót engedélyezni kell a virtuális tömbben. Ha az eszköz egyetlen hálózati adapter engedélyezve van, az alapértelmezés szerint ez az interfész rendelt ez `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Használja a `Set-HcsIpAddress` parancsmagot, hogy a hálózat konfigurálása. Tekintse meg a következő példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. A kezdeti telepítés befejezése után, az eszköz fel betöltődött, látni fogja az eszköz szalagcím szöveg. Jegyezze fel az IP-cím és az URL-címet a szalagcím szövegként jelenik meg az eszköz felügyeletére. Az IP-cím segítségével csatlakozzon a webes felhasználói felület, a virtuális tömb, és fejezze be a helyi telepítését és regisztrálását.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Választható) Hajtsa végre ezt a lépést, csak akkor, ha az eszköz a kormányzati felhőben telepíti. Most már az eszközön az Amerikai Egyesült Államokban Federal Information Processing Standard (FIPS) mód lehetővé teszi. A FIPS 140 szabvány határozza meg a bizalmas adatok védelmének amerikai szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat.

    1. Ahhoz, hogy a FIPS-módban, futtassa az alábbi parancsmagot:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, engedélyeznie kell a FIPS-módban, hogy a kriptográfiai ellenőrzések érvénybe léptetéséhez.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS-módban az eszközön. Váltakozó az eszköz közötti FIPS és a FIPS módban nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeinek, tekintse meg a következő hiba a szalagcím szöveg (lásd alább). Módosítsa az eszközök konfigurációját úgy, hogy a gép meg a minimális követelményeknek megfelelő erőforrásokat. Ezután indítsa újra, és csatlakozzon az eszközhöz. Tekintse meg a minimális konfigurációs követelményeinek [1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a minimális virtuális tömb követelmények](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Ha a helyi webes felhasználói felület használata a kezdeti konfiguráció során bármilyen hiba akkor szembesülhetnek, tekintse meg a következő munkafolyamatok:

* Diagnosztikai tesztek futtatása annak [webes felhasználói felületen való beállításának hibaelhárítása](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Napló csomagot és naplófájlban](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Következő lépések
* [Állítsa be a StorSimple virtuális tömb fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [Állítsa be a StorSimple virtuális tömb iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
