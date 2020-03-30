---
title: Provision StorSimple virtuális tömb a Hyper-V-ben | Microsoft dokumentumok
description: Ez a második oktatóanyag a StorSimple virtuális tömb központi telepítésében magában foglalja egy virtuális tömb kiépítését a Hyper-V-ben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267533"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple virtuális tömb telepítése – provision in Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az oktatóanyag bemutatja, hogyan lehet storSimple virtuális tömböt létesíteni a Hyper-V rendszert futtató gazdarendszeren Windows Server 2012, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. Ez a cikk a StorSimple virtuális tömbök azure portalon és a Microsoft Azure Government Cloud-ban történő üzembe helyezésére vonatkozik.

A virtuális tömb kiépítéséhez és konfigurálásához rendszergazdai jogosultságokra van szükség. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Előfeltétel-kiépítés
Itt találhatók a Hyper-V rendszert futtató gazdarendszer virtuális tömbjének kiépítésének előfeltételei Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Elvégezte az összes lépést [a Portál előkészítése a StorSimple virtuális tömbhöz](storsimple-virtual-array-deploy1-portal-prep.md)című részben.
* A Hyper-V virtuális tömbképét az Azure Portalról töltötte le. További információ: **3. lépés: A Portál** [előkészítése a StorSimple virtuális tömb útmutatóhoz](storsimple-virtual-array-deploy1-portal-prep.md)című virtuális tömbkép letöltése című témakörben talál.

  > [!IMPORTANT]
  > A StorSimple virtuális tömbön futó szoftver csak a StorSimple Eszközkezelő szolgáltatással használható.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz
Virtuális tömb telepítése előtt győződjön meg arról, hogy:

* Hozzáférése van egy Hyper-V rendszert futtató gazdarendszerhez Windows Server 2008 R2 vagy újabb rendszeren, amely egy eszköz kiépítéséhez használható.
* A gazdarendszer a következő erőforrásokat tudja a virtuális tömb kiépítésére fordítani:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha a virtuális tömböt fájlkiszolgálóként kívánja konfigurálni, a 8 GB kevesebb mint 2 millió fájlt támogat. Szüksége van 16 GB RAM-ra a 2-4 millió fájl támogatásához.
  * Egy hálózati adapter.
  * Egy 500 GB-os virtuális lemez az adatokhoz.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt elkezdené, tekintse át a storSimple virtuális tömb központi telepítéséhez és az adatközpont-hálózat megfelelő konfigurálásához a hálózati követelmények. További információ: [StorSimple Virtual Array hálózati követelmények](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Lépésről lépésre történő kiépítés
A virtuális tömbhöz való csatlakozáshoz a következő lépéseket kell végrehajtania:

1. Győződjön meg arról, hogy a gazdarendszer elegendő erőforrással rendelkezik a minimális virtuális tömbkövetelmények teljesítéséhez.
2. Virtuális tömb kiépítése a hipervizorban.
3. Indítsa el a virtuális tömböt, és az IP-címet.

A lépések mindegyikét a következő szakaszok ismertetik.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>1. lépés: Annak ellenőrzése, hogy a gazdarendszer megfelel-e a virtuális tömb minimális követelményeinek
Virtuális tömb létrehozásához a következőkre van szükség:

* A Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 SP1 rendszerre telepített Hyper-V szerepkör.
* Egy, a gazdagépre csatlakoztatott Microsoft Windows-ügyfélen futó Microsoft Hyper-V-kezelőre.

Győződjön meg arról, hogy az alapul szolgáló hardver (gazdarendszer), amelyen a virtuális tömböt létrehozza, képes-e a következő erőforrásokat a virtuális tömbnek szentelni:

* Legalább 4 mag.
* Legalább 8 GB RAM. Ha a virtuális tömböt fájlkiszolgálóként kívánja konfigurálni, a 8 GB kevesebb mint 2 millió fájlt támogat. Szüksége van 16 GB RAM-ra a 2-4 millió fájl támogatásához.
* Egy hálózati adapter.
* 500 GB-os virtuális lemez a rendszeradatokhoz.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>2. lépés: Virtuális tömb kiépítése hipervizorban
A következő lépések végrehajtásával helyezzen üzembe egy eszközt a hipervizoron.

#### <a name="to-provision-a-virtual-array"></a>Virtuális tömb kiépítése
1. A Windows Server gazdagépen másolja a virtuális tömblemezképét egy helyi meghajtóra. Ezt a lemezképet (VHD vagy VHDX) az Azure Portalon keresztül töltötte le. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.
2. Nyissa meg a **Kiszolgálókezelőt**. A jobb felső sarokban kattintson az **Eszközök** gombra, és válassza a **Hyper-V kezelője** lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Windows Server 2008 R2 rendszer esetén nyissa meg a Hyper-V Manager t. A Kiszolgálókezelőben kattintson **a Szerepkörök > Hyper-V > Hyper-V Manager**elemre.
3. A **Hyper-V kezelője** hatókörpanelén kattintson jobb gombbal a rendszercsomópontra, majd a megnyíló helyi menüben kattintson az **Új** > **Virtuális gép** elemre.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Az Új virtuális gép varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
5. A **Név és hely megadása** lapon adja meg a virtuális tömb **nevét.** Kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. A **Létrehozás megadása** lapon válassza ki az eszköz képtípusát, majd kattintson a **Tovább**gombra. Ez a lap nem jelenik meg, ha Windows Server 2008 R2 rendszert használ.

   * Válassza **a 2.**
   * Válassza **az 1.**

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. A **Memória hozzárendelése** lapon adjon meg egy legalább **8192 MB** méretű **indítási memóriát**, ne engedélyezze a dinamikus memóriát, majd kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. A **Hálózatkezelés beállítása** lapon adja meg az internetre csatlakoztatott virtuális kapcsolót, majd kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. A **Virtuális merevlemez csatlakoztatása** lapon válassza **a Meglévő virtuális merevlemez használata**lehetőséget, adja meg a virtuális tömbkép helyét (.vhdx vagy .vhd) , majd kattintson a **Tovább**gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Tekintse át az **összefoglalást**, majd kattintson a **Befejezés** gombra a virtuális gép létrehozásához.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 4 magra van szükség ahhoz, hogy meg tudjon felelni a minimális követelményeknek. A 4 virtuális processzor hozzáadásához válassza ki a gazdarendszert a **Hyper-V kezelő** ablakában. A jobb oldali panel **Virtuális gépek** listájában keresse meg az imént létrehozott virtuális gépet. Jelölje ki, kattintson a jobb gombbal a gép nevére, majd válassza a **Beállítások** elemet.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. A **Beállítások** lap bal oldali panelén kattintson a **Processzor** lehetőségre. A jobb oldali panelen állítsa a **virtuális processzorok számát** 4-re (vagy többre). Kattintson az **Alkalmaz** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. A minimális követelmények teljesítéséhez 500 GB-os virtuális adatlemezt is hozzá kell adnia. A **Beállítások** lapon:

    1. A bal oldali panelen válassza az **SCSI-vezérlő** lehetőséget.
    2. A jobb oldali panelen válassza a **Merevlemez** lehetőséget, és kattintson a **Hozzáadás** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. A **Merevlemez** lapon válassza a **Virtuális merevlemez** lehetőséget, és kattintson az **Új** gombra. Megnyílik az **Új virtuális merevlemez varázsló**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Az Új virtuális merevlemez varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
16. A **Lemezformátum kiválasztása** lapon fogadja el az alapértelmezett **VHDX** formátumot. Kattintson a **Tovább** gombra. Ez a képernyő nem jelenik meg, ha Windows Server 2008 R2 rendszert futtat.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. A **Lemeztípus kiválasztása** lapon állítsa a virtuális merevlemez típusát a **Dinamikusan bővülő** típusra (ajánlott). A **Rögzített méretű** lemez is működhet, de előfordulhat, hogy túl sokat kellene várnia. Azt javasoljuk, hogy ne alkalmazza a **Különbség** beállítást. Kattintson a **Tovább** gombra. Windows Server 2012 R2 és Windows Server 2012 rendszerben a **dinamikus bővítés** az alapértelmezett beállítás, míg a Windows Server 2008 R2 rendszerben az alapértelmezett érték a **Rögzített méret**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. A **Név és hely megadása** lapon adja meg az adatlemez **nevét** és **helyét** (ez utóbbit tallózva is megkeresheti). Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. A **Lemez konfigurálása** lapon válassza az **Új üres virtuális merevlemez létrehozása** lehetőséget, és adja meg a méretet **500 GB(vagy** több) értékben. Míg 500 GB a minimális követelmény, mindig kiépíthet egy nagyobb lemezt. Ne feledje, hogy a lemez kiépítés után nem bontható ki és nem zsugorítható. A kiépítandó lemez méretéről további információt a ajánlott [eljárásokat tartalmazó dokumentum méretezési](storsimple-ova-best-practices.md)szakaszában talál. Kattintson a **Tovább** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Az **Összefoglalás** lapon tekintse át a virtuális adatlemez adatait, és ha elégedett, kattintson a **Befejezés** gombra a lemez létrehozásához. A varázsló bezáródik, és a rendszer hozzáadja a virtuális merevlemezt a géphez.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Lépjen vissza a **Beállítások** lapra. Az **OK** gombra kattintva zárja be a **Beállítások** lapot, és lépjen vissza a Hyper-V kezelőjének ablakára.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>3. lépés: Indítsa el a virtuális tömböt, és kap az IP
Hajtsa végre az alábbi lépéseket a virtuális tömb elindításához és az ahhoz való csatlakozáshoz.

#### <a name="to-start-the-virtual-array"></a>A virtuális tömb indítása
1. Indítsa el a virtuális tömböt.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Amikor az eszköz már fut, jelölje ki, kattintson rá a jobb gombbal, és válassza a **Csatlakozás** lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Előfordulhat, hogy 5-10 percet kell várnia, hogy a készülék készen álljon. A folyamat előrehaladtát egy állapotüzenet jelzi a konzolon. Ha az eszköz kész, lépjen a **Művelet** területre. Nyomja `Ctrl + Alt + Delete` meg a virtuális tömbbe való bejelentkezéshez. Az alapértelmezett felhasználó a *StorSimpleAdmin,* az alapértelmezett jelszó pedig a *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak a következő négy típusból legalább hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek. Írja be ismét a jelszót a megerősítéséhez. A rendszer értesíti, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. A jelszó sikeres módosítása után a virtuális tömb újraindulhat. Várjon, amíg az eszköz elindul.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Megjelenik az eszköz Windows PowerShell-konzolja és egy folyamatjelző.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Ha nem DHCP-környezetben indította el az eszközt, a következő képernyő jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Ezután konfigurálja a hálózatot.
7. A `Get-HcsIpAddress` paranccsal listázhatja a virtuális tömbben engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Lásd a következő példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Ezzel az IP-címmel csatlakozhat a virtuális tömb webes felhasználói felületéhez, és befejezheti a helyi telepítést és regisztrációt.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Nem kötelező) Ezt a lépést csak akkor hajtsa végre, ha az eszközt a Kormányzati felhőben telepíti. Most antól engedélyezi az Egyesült Államok Szövetségi Információfeldolgozási Szabvány (FIPS) üzemmódját az eszközén. A FIPS 140 szabvány az Egyesült Államok szövetségi szövetségi számítógépes rendszerei által a bizalmas adatok védelmére jóváhagyott kriptográfiai algoritmusokat határozza meg.

    1. A FIPS mód engedélyezéséhez futtassa a következő parancsmast:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, miután engedélyezte a FIPS módot, hogy a kriptográfiai érvényesítés érvénybe lépjen.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS módot az eszközön. Az eszköz FIPS és nem FIPS mód közötti váltakozása nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, a szalagcím szövegében a következő hibaüzenet jelenik meg (lásd alább). Módosítsa az eszköz konfigurációját, hogy a gép elegendő erőforrással rendelkezzen a minimális követelmények kielégítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. Tekintse meg a minimális konfigurációs követelményeket az 1.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Ha a helyi webes felhasználói felület használatával történő kezdeti konfiguráció során bármilyen más hibával szembesül, olvassa el a következő munkafolyamatokat:

* Diagnosztikai tesztek futtatása a [webes felhasználói felület telepítésének elhárításához.](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)
* [Naplócsomag létrehozása és naplófájlok megtekintése](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>További lépések
* [A StorSimple virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [A StorSimple virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
