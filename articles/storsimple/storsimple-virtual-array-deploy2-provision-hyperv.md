---
title: A Hyper-V-ben a StorSimple Virtual Array üzembe helyezése |} A Microsoft Docs
description: A StorSimple Virtual Array üzembe helyezési oktatóanyag második magában foglalja a kiépítés egy Hyper-v virtuális tömböt.
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
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810541"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>A StorSimple Virtual Array – üzembe helyezés Hyper-V rendszerben üzembe helyezése
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag leírja, hogyan helyezhet üzembe egy StorSimple Virtual Array állomás operációs rendszert futtató Hyper-V Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. Ez a cikk a StorSimple Virtual Arrayt az Azure Portalon és a Microsoft Azure Government Cloud központi telepítés vonatkozik.

Kiépítés rendszergazdai jogosultságok szükségesek, és konfigurálja a virtuális tömb. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

## <a name="provisioning-prerequisites"></a>Üzembe helyezési Előfeltételek
Itt megtalálja az előfeltételeket a virtuális tömb létrehozása egy gazdagépen rendszeren futó Hyper-V Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az összes lépés elvégzését követően [a portál előkészítése a StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* A Hyper-V a virtuális tömb lemezképének már letöltötte az Azure Portalról. További információkért lásd: **3. lépés: A virtuális tömb lemezképének letöltése** , [StorSimple Virtual Array útmutató a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > A StorSimple Virtual Array futó szoftver csak a StorSimple-Eszközkezelő szolgáltatással használható.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb
Mielőtt telepít egy virtuális tömböt, ellenőrizze, hogy:

* Gazdagép operációs rendszert futtató Hyper-V a Windows Server 2008 R2 vagy újabb, amely hozzáféréssel rendelkezik egy kiépítéséhez használt eszköz.
* A gazdagép rendszere viszont rendelnie a virtuális tömb telepítéséhez a következőket:

  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha azt tervezi, konfigurálja a virtuális tömb fájlkiszolgálóként, 8 GB támogatja a kevesebb mint 2 millió fájlokat. 16 GB RAM, 2 – 4 millió fájl támogatásához szükséges.
  * Egy hálózati adapter.
  * 500 GB-os virtuális lemez adatait.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt elkezdené, tekintse át a StorSimple Virtual Array üzembe helyezése és az Adatközpont-hálózat megfelelő konfigurálása a hálózati követelményeket. További információkért lásd: [StorSimple Virtual Array hálózati követelményei](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Részletes kiépítése
Kiépítése, és csatlakozzon a virtuális tömb, kell hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy a gazdagép rendszere rendelkezik-e elegendő erőforrás a virtuális tömb minimális követelményeinek.
2. Virtuális tömb létrehozása a hipervizor az.
3. A virtuális tömb indítása és IP-címének lekéréséhez.

Egyes lépéseket az alábbiakban kifejtett.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a minimális virtual array – követelmények
Hozzon létre egy virtuális tömböt, az alábbiak szükségesek:

* A Hyper-V szerepkör telepítése a Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 SP1.
* Egy, a gazdagépre csatlakoztatott Microsoft Windows-ügyfélen futó Microsoft Hyper-V-kezelőre.

Ügyeljen arra, hogy az alapul szolgáló hardver (gazdagép rendszere), amely létrehozza a virtuális tömb képes a virtuális tömb az alábbi erőforrások forrásfájloktól:

* Legalább 4 mag.
* Legalább 8 GB RAM. Ha azt tervezi, konfigurálja a virtuális tömb fájlkiszolgálóként, 8 GB támogatja a kevesebb mint 2 millió fájlokat. 16 GB RAM, 2 – 4 millió fájl támogatásához szükséges.
* Egy hálózati adapter.
* 500 GB-os virtuális lemez a rendszer adatokat.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>2. lépés: A hipervizor virtuális tömb létrehozása
A következő lépések végrehajtásával helyezzen üzembe egy eszközt a hipervizoron.

#### <a name="to-provision-a-virtual-array"></a>A virtuális tömb létrehozása
1. A Windows Server-gazdagépen másolja át a virtuális tömb lemezképet egy helyi meghajtóra. Ez a rendszerkép (VHD vagy vhdx-fájlt) letöltötte az Azure Portalon keresztül. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.
2. Nyissa meg a **Kiszolgálókezelőt**. A jobb felső sarokban kattintson az **Eszközök** gombra, és válassza a **Hyper-V kezelője** lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Ha futtatja a Windows Server 2008 R2, nyissa meg a Hyper-V kezelőjét. A Kiszolgálókezelőben kattintson a **szerepkörök > Hyper-V > Hyper-V Manager**.
3. A **Hyper-V kezelője** hatókörpanelén kattintson jobb gombbal a rendszercsomópontra, majd a megnyíló helyi menüben kattintson az **Új** > **Virtuális gép** elemre.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Az Új virtuális gép varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
5. Az a **név és hely megadása** lapon egy **neve** a virtuális tömb. Kattintson a **tovább**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Az a **adja meg a generáció** lapon válassza ki az eszköz lemezképet, és kattintson a **tovább**. Ezen a lapon nem jelenik meg, ha Windows Server 2008 R2 használja.

   * Válasszon **2. generációs** Ha letöltötte a .vhdx kép a Windows Server 2012 vagy újabb verzió.
   * Válasszon **1. generációs** Ha letöltötte a .vhd-lemezképeket, a Windows Server 2008 R2 vagy újabb verzió.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. A **Memória hozzárendelése** lapon adjon meg egy legalább **8192 MB** méretű **indítási memóriát**, ne engedélyezze a dinamikus memóriát, majd kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. A **Hálózatkezelés beállítása** lapon adja meg az internetre csatlakoztatott virtuális kapcsolót, majd kattintson a **Tovább** gombra.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Az a **virtuális merevlemez csatlakoztatása** lapon a **egy meglévő virtuális merevlemez használata**, adja meg (.vhdx vagy .vhd), a virtuális tömb lemezképének helyét, és kattintson **tovább**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Tekintse át az **összefoglalást**, majd kattintson a **Befejezés** gombra a virtuális gép létrehozásához.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 4 magra van szükség ahhoz, hogy meg tudjon felelni a minimális követelményeknek. A 4 virtuális processzor hozzáadásához válassza ki a gazdarendszert a **Hyper-V kezelő** ablakában. A jobb oldali panel **Virtuális gépek** listájában keresse meg az imént létrehozott virtuális gépet. Jelölje ki, kattintson a jobb gombbal a gép nevére, majd válassza a **Beállítások** elemet.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. A **Beállítások** lap bal oldali panelén kattintson a **Processzor** lehetőségre. A jobb oldali panelen állítsa a **virtuális processzorok számát** 4-re (vagy többre). Kattintson az **Alkalmaz** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. A minimális követelményeknek is hozzá kell 500 GB-os virtuális adatlemezt. A **Beállítások** lapon:

    1. A bal oldali panelen válassza az **SCSI-vezérlő** lehetőséget.
    2. A jobb oldali panelen válassza a **Merevlemez** lehetőséget, és kattintson a **Hozzáadás** gombra.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. A **Merevlemez** lapon válassza a **Virtuális merevlemez** lehetőséget, és kattintson az **Új** gombra. Megnyílik az **Új virtuális merevlemez varázsló**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Az Új virtuális merevlemez varázsló **Előkészületek** lapján kattintson a **Tovább** gombra.
16. A **Lemezformátum kiválasztása** lapon fogadja el az alapértelmezett **VHDX** formátumot. Kattintson a **tovább**. Ezen a képernyőn nem jelenik meg, ha a Windows Server 2008 R2 rendszerű.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. A **Lemeztípus kiválasztása** lapon állítsa a virtuális merevlemez típusát a **Dinamikusan bővülő** típusra (ajánlott). A **Rögzített méretű** lemez is működhet, de előfordulhat, hogy túl sokat kellene várnia. Azt javasoljuk, hogy ne alkalmazza a **Különbség** beállítást. Kattintson a **tovább**. A Windows Server 2012 R2 és Windows Server 2012-ben **dinamikusan bővülő** lesz az alapértelmezett beállítás, mivel a Windows Server 2008 R2, az alapértelmezett érték **rögzített méretű**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. A **Név és hely megadása** lapon adja meg az adatlemez **nevét** és **helyét** (ez utóbbit tallózva is megkeresheti). Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Az a **lemez beállítása** lapra, jelölje be a beállítást **hozzon létre egy új üres virtuális merevlemezzel** , és adja meg a méretűnek **500 GB-os** (vagy több). A minimális követelmény 500 GB-os pedig mindig helyezhet nagyobb lemez. Vegye figyelembe, hogy nem bontsa ki a vagy a egyszer üzembe helyezett lemez zsugorítani. Kiépítés lemez mérete további információkért tekintse át a méretezési részt a [ajánlott eljárások a dokumentum](storsimple-ova-best-practices.md). Kattintson a **tovább**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Az **Összefoglalás** lapon tekintse át a virtuális adatlemez adatait, és ha elégedett, kattintson a **Befejezés** gombra a lemez létrehozásához. A varázsló bezáródik, és a rendszer hozzáadja a virtuális merevlemezt a géphez.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Lépjen vissza a **Beállítások** lapra. Az **OK** gombra kattintva zárja be a **Beállítások** lapot, és lépjen vissza a Hyper-V kezelőjének ablakára.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>3. lépés: A virtuális tömb indítása és az IP-cím kérése
Hajtsa végre az alábbi lépéseket a virtuális tömb indítása és csatlakozni hozzá.

#### <a name="to-start-the-virtual-array"></a>A virtuális tömb indítása
1. A virtuális tömb indítása.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Amikor az eszköz már fut, jelölje ki, kattintson rá a jobb gombbal, és válassza a **Csatlakozás** lehetőséget.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Előfordulhat, hogy az eszköz készen áll az 5 – 10 percet várnia kell. A folyamat előrehaladtát egy állapotüzenet jelzi a konzolon. Ha az eszköz kész, lépjen a **Művelet** területre. Nyomja meg `Ctrl + Alt + Delete` jelentkezzen be a virtuális tömb. Az alapértelmezett felhasználó *StorSimpleAdmin* és az alapértelmezett jelszó az *jelszó1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Biztonsági okokból az eszköz rendszergazdai jelszava az első bejelentkezéskor lejár. A rendszer megkéri a jelszó módosítására.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Olyan jelszót adjon meg, amely legalább 8 karakterből áll. A jelszónak a következő négy típusból legalább hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek. Írja be ismét a jelszót a megerősítéséhez. A rendszer értesíti, hogy a jelszó megváltozott.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. A jelszó sikeresen módosítását követően a virtuális tömb újraindulhat. Várjon, amíg az eszköz elindul.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Megjelenik az eszköz Windows PowerShell-konzolja és egy folyamatjelző.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. A 6–8. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 9. lépéssel. Az eszköz nem DHCP-környezetben konzoljához, ha a következő képernyő jelenik meg.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Ezután konfigurálja a hálózatot.
7. Használja a `Get-HcsIpAddress` paranccsal listát készíthet a hálózati adapterek engedélyezve a virtuális tömb. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Lásd a következő példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Az IP-cím használatával a webes felhasználói Felületére, a virtuális tömb csatlakozhat, és fejezze be a helyi telepítését és regisztrálását.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Nem kötelező) Csak akkor, ha az eszköz a Government Cloud végzi, hajtsa végre ezt a lépést. Mostantól az eszközön az Egyesült Államok Szövetségi információk feldolgozása Standard (FIPS) üzemmód lehetővé teszi. A FIPS 140 szabvány határozza meg, bizalmas adatok védelme az USA szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat.

    1. Ahhoz, hogy a FIPS-módban, futtassa a következő parancsmagot:

        `Enable-HcsFIPSMode`
    2. Indítsa újra az eszközt, miután engedélyezte a FIPS-módban, hogy a kriptográfiai ellenőrzések érvénybe léptetéséhez.

       > [!NOTE]
       > Engedélyezheti vagy letilthatja a FIPS-módban az eszközön. Váltakozó az eszköz közötti FIPS és a FIPS módban nem támogatott.
       >
       >

Ha az eszköz nem felel meg a minimális konfigurációs követelményeinek, láthatja a következő hiba a szalagcím szövegben (lásd alább). Módosítsa az eszköz konfigurációját, hogy a gép elegendő erőforrással rendelkezzen a minimális követelmények kielégítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. Tekintse meg a minimális konfigurációs követelményeinek, az 1. lépés: Győződjön meg arról, hogy a gazdagép rendszere megfelel-e a minimális virtual array – követelmények.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Ha a helyi webes felhasználói felület a kezdeti konfiguráció során bármilyen hiba között, tekintse meg a következő munkafolyamatok:

* Diagnosztikai tesztek futtatása annak [webes felhasználói felület beállításával kapcsolatos hibák elhárítása](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Napló csomagjának létrehozása és a naplók](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>További lépések
* [Állítsa be a StorSimple Virtual Array fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)
* [Állítsa be a StorSimple Virtual Array egy iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md)
