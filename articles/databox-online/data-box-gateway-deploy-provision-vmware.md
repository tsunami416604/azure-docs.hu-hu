---
title: 'Oktatóanyag: Az Azure Data Box Gateway üzembe helyezése a VMware-ben | Microsoft Docs'
description: Az Azure Data Box Gateway üzembe helyezésével foglalkozó második oktatóanyag, amely a virtuális eszközök VMware-ben való telepítését tárgyalja.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 00415cab4d5c36c74cf78a10cb71682d97236517
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099158"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware-preview"></a>Oktatóanyag: VMware (előzetes verzió) üzembe helyezése az Azure Data Box-átjáró

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan építheti ki a gazdagép operációs rendszert futtató VMware ESXi 6.0, 6.5-ös vagy 6.7 Data Box Gateway. 

A virtuális eszközök üzembe helyezéséhez és a hozzájuk való csatlakozáshoz rendszergazdai jogosultság szükséges. Az üzembe helyezés és a kezdeti beállítás körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A gazdagép minimális eszközkövetelményeknek való megfelelésének ellenőrzése
> * Virtuális eszköz üzembe helyezése a VMware-ben
> * A virtuális eszköz elindítása és az IP-cím lekérése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> - A Data Box Gateway előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Gazdagép operációs rendszert futtató VMware ESXi 6.0, 6.5-ös vagy 6.7, a virtuális eszköz előfeltételei a következők.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* [Az Azure Portal a Data Box Gatewayhez való előkészítését](data-box-gateway-deploy-prep.md) ismertető szakaszban foglalt összes lépést végrehajtotta.
* Letöltötte a virtuális eszköz VMware-rendszerképét az Azure Portalról [a portál a Data Box Gatewayhez való előkészítését](data-box-gateway-deploy-prep.md) ismertető szakaszban leírtak szerint.

  > [!IMPORTANT]
  > A Data Box Gatewayen futó szoftver csak a Data Box Gateway-erőforrással használható.

### <a name="for-the-data-box-gateway-virtual-device"></a>Virtuális Data Box Gateway-eszköz esetén

A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* Gazdagép operációs rendszert futtató VMware (ESXi 6.0, 6.5-ös vagy 6.7), amely hozzáféréssel rendelkezik egy kiépítéséhez használt eszköz.
* A gazdarendszer képes elkülöníteni az alábbi erőforrásokat a virtuális eszköz üzembe helyezése érdekében:

  * Legalább 4 mag.
  * Legalább 8 GB RAM.
  * Egy hálózati adapter.
  * 250 GB-os operációsrendszer-lemez.
  * 2 TB-os virtuális lemez a rendszeradatok számára.

### <a name="for-the-network-in-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Data Box Gateway üzembe helyezésére vonatkozó hálózati követelményeket, és azoknak megfelelően konfigurálja az adatközponti hálózatot. További információkért lásd [a Data Box Gateway hálózati követelményeit](data-box-gateway-system-requirements.md#networking-requirements) ismertető szakaszt.
- Az eszköz optimális működéséhez gondoskodjon róla, hogy legalább 20 Mb/s sebességű internetes sávszélesség rendelkezésre álljon.

## <a name="check-the-host-system"></a>A gazdarendszer ellenőrzése

Virtuális eszköz létrehozásához a következőkre lesz szüksége:

* Gazdagép operációs rendszert futtató VMware ESXi-kiszolgáló 6.0-s, 6.5-ös vagy 6.7 való hozzáférést. A gazdarendszer képes az alábbi erőforrásokat a virtuális eszköz számára elkülöníteni:
 
  * Legalább 4 mag.
  * Legalább 8 GB RAM. 
  * Egy, a hálózatra csatlakozó hálózati adapter, amely képes a forgalmat az internetre irányítani. 
  * 250 GB-os operációsrendszer-lemez.
  * 2 TB-os virtuális lemez az adatok számára.
* VMware vSphere-ügyfél az adott rendszeren az ESXi-gazdagép kezeléséhez.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Virtuális eszköz üzembe helyezése a hipervizorban

A következő lépések végrehajtásával helyezzen üzembe egy virtuális eszközt a hipervizoron.

1. Másolja a virtuáliseszköz-rendszerképet a rendszerre. Ezt a rendszerképet (két fájlt) az Azure Portalról töltötte le. Jegyezze fel a helyet, ahová a rendszerképet másolta, mivel az eljárás későbbi szakaszában szükség lesz rá.

2. Jelentkezzen be az ESXi-kiszolgáló a vSphere webes ügyfél használatával. A virtuális gép létrehozásához rendszergazdai jogosultsággal kell rendelkeznie.

   ![](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Töltse fel a VMDK-t az ESXi-kiszolgálóra. A Navigátor panelen kattintson a **Storage** (Tároló) lehetőségre.

   ![](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. A jobb oldali panelen a **Datastores** (Adattárak) területen válassza ki azt az adattárat, amelybe fel kívánja tölteni a VMDK-t. 

    - Az adattárnak VMFS5 típusúnak kell lennie. 
    - Emellett elegendő szabad hellyel kell rendelkeznie az operációsrendszer-lemez és az adatlemezek tárolásához.
   
5. Kattintson a jobb gombbal, és válassza a **Browse Datastore** (Adattár tallózása) lehetőséget.

   ![](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Megjelenik a **Datastore Browser** (Adattártallózó) ablak.

   ![](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Az eszköztáron kattintson a **Create directory** (Könyvtár létrehozása) gombra egy új mappa létrehozásához. Adja meg a mappa nevét, és jegyezze fel. Ezt a mappanevet fogja használni később, amikor létrehoz egy virtuális gépet (ajánlott eljárás). Kattintson a **Create directory** (Könyvtár létrehozása) parancsra.

   ![](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. Az új mappa megjelenik a **Datastore Browser** (Adattártallózó) bal oldali panelén. Kattintson a **feltöltési** ikonra és válassza az **Upload File** (Fájl feltöltése) lehetőséget.

    ![](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Tallózással keresse meg a letöltött VMDK-fájlokat. Kettő fájl érhető el. Válasszon ki egy feltöltendő fájlt.

    ![](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Kattintson az **Open** (Megnyitás) elemre. Megkezdődik a VMDK-fájl feltöltése a megadott adattárra. A feltöltés több percig is eltarthat.
11. A feltöltést követően a fájl megjelenik az adattáron a létrehozott mappában. Most töltse fel a második VMDK-fájlt is ugyanarra az adattárra. Ha mindkét fájlt feltöltötte, a rendszer egyesíti őket egyetlen fájlba. Ezután a könyvtárban egyetlen fájl lesz látható.

    ![](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Lépjen vissza a vSphere-ügyfél ablakába. A Navigátor panelen kattintson a **Virtual Machines** (Virtuális gépek) lehetőségre. A jobb oldali panelen kattintson a **Create/Register VM** (Virtuális gép létrehozása/regisztrálása) elemre.

    ![](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Megjelenik egy **új virtuális gép**. A Létrehozási típus kiválasztásánál válassza a **Create a new virtual machine** (Új virtuális gép létrehozása) lehetőséget, és kattintson a **Next** (Tovább) gombra.
    ![](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. A **Select a Name and OS Name and Location** (Név és operációsrendszer-név és hely kiválasztása) lapon adja meg a virtuális gép **nevét**. Ez legyen ugyanaz, mint a korábban, a 7. lépésben megadott mappanév (ajánlott eljárás). Válassza a **Guest OS family** (Vendég operációsrendszer-család) beállításnál a Windowst, majd a **Guest OS version** (Vendég operációsrendszer-verzió) beállításánál a Microsoft Windows Server 2016 (64 bites) lehetőséget. Kattintson a **tovább**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. A **Select storage** (Tár kiválasztása) oldalon válassza ki azt az adattárat, amelyen üzembe kívánja helyezni a virtuális gépet. Kattintson a **tovább**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. A **Customize settings** (Beállítások testreszabása) lapon állítsa a **CPU**-k számát 4-re, a **Memóriát** 8192 MB-ra (vagy többre), az **1. merevlemez** kapacitását pedig 2 TB-ra (vagy többre). Válassza ki a hozzáadni kívánt **SCSI-merevlemezt**. Ebben az esetben ez az LSI Logic SAS. **A statikus IDE-lemezek használata nem támogatott.** Az **1. merevlemez** a virtuális adatlemez. Vegye figyelembe, hogy a lemez mérete az üzembe helyezést követően nem csökkenthető.

    ![](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Ugyanezen az oldalon kattintson az **Add hard disk** (Merevlemez hozzáadása) elemre, majd az **Existing hard disk** (Létező merevlemez) lehetőségre. Válassza ki a VMDK fájlt az adattárban. Ezzel hozzáad egy operációsrendszer-lemezt. 

     ![](./media/data-box-gateway-deploy-provision-vmware/image14.png)

    Görgessen lefelé, amíg meg nem látja a **New hard disk** (Új merevlemez) elemet, és bontsa azt ki a beállítások megtekintéséhez. Állítsa be a **Virtual Device Node** (Virtuális eszköz csomópontja) beállításnál az **IDE controller 0** értéket. Kattintson a **tovább**.

     ![](./media/data-box-gateway-deploy-provision-vmware/image15.png)

27. A **Ready to Complete** (Befejezésre kész) oldalon tekintse át az új virtuális gépre vonatkozó beállításokat. Ellenőrizze a következőket: a CPU-k száma 4, a memória mérete 8192 MB, a hálózati adapter értéke 1, valamint a 2. merevlemez beállítása: IDE controller 0. Kattintson a **Befejezés** gombra. 
   
    ![](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

A rendszer üzembe helyezi a virtuális gépet. Megjelenik erről egy értesítés, és az új virtuális gép hozzáadódik a virtuális gépek listájához. 

![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

A következő lépés a virtuális gép bekapcsolása és az IP-cím lekérése.

> [!NOTE]
> Azt javasoljuk, hogy ne telepítsen VMware-eszközöket a (fenti módon üzembe helyezett) virtuális eszközre. A VMware-eszközök telepítése egy nem támogatott konfigurációt eredményez.

## <a name="start-the-virtual-device-and-get-the-ip"></a>A virtuális eszköz elindítása és az IP-cím lekérése

Az alábbi lépések végrehajtásával indítsa el a virtuális eszközt, és csatlakozzon hozzá.

#### <a name="to-start-the-virtual-device"></a>A virtuális eszköz indítása
1. Indítsa el a virtuális eszközt. A jobb oldali panelen válassza ki az eszközt a virtuális gépek listájából, és kattintson rá a jobb gombbal a helyi menü megjelenítéséhez. Válassza a **Power** (Főkapcsoló), majd a **Power on** (Bekapcsolás) lehetőséget. Ezzel bekapcsolja a virtuális gépet. A gép állapotát a webes ügyfél alsó panelén tekintheti meg.

    ![](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Válassza ki ismét a virtuális gépét. Kattintson rá a jobb gombbal, és válassza a **Console** (Konzol), majd az **Open in a new window** (Megnyitás új ablakban) lehetőséget.

    ![](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. A virtuális gép konzolja megnyílik egy új ablakban. 

    ![](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Ha az eszköz fut, kattintson a konzolablak felső részének közepén található kurzorra. Válassza ki a **Guest OS > Send keys > Ctrl+Alt+Delete** (Vendég operációs rendszer > Billentyűk küldése > Ctrl + Alt + Delete) lehetőséget. Ezzel feloldja a virtuális gép zárolását.

   ![](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Adja meg a jelszót a gépre való bejelentkezéshez. Az alapértelmezett jelszó: Password1.

   ![](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. Az 5–7. lépést csak akkor kell végrehajtani, ha nem DHCP-környezetben végzi a rendszerindítást. Ha DHCP-környezetben van, hagyja ki ezeket a lépéseket, és folytassa a 8. lépéssel. Ha az eszköz nem DHCP-környezetben konzoljához, a hatás, egy üzenet jelenik meg: **A Set-HcsIPAddress parancsmag használatával konfigurálja a hálózatot**. 
   
7. A hálózat konfigurálásához a parancssorban a `Get-HcsIpAddress` parancs használatával listázza ki a virtuális eszközön engedélyezett hálózati adaptereket. Ha az eszközön egyetlen hálózati adapter van engedélyezve, az ehhez az adapterhez rendelt alapértelmezett név az `Ethernet`.

8. A `Set-HcsIpAddress` parancsmaggal konfigurálhatja a hálózatot. Erre alább láthat egy példát:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Miután a kezdeti beállítás befejeződött és az eszköz elindult, az eszköz szalagcímének szövege jelenik meg. Jegyezze fel a szalagcímen megjelenő IP- és URL-címet az eszköz kezeléséhez. Az IP-címmel csatlakozhat a virtuális eszköz webes kezelőfelületéhez, ahol elvégezheti a helyi beállítást és az aktiválást.

   ![](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Ha az eszköz nem felel meg a minimális konfigurációs követelményeknek, egy hibaüzenet jelenik meg a szalagcím szövegében (lásd alább). Módosítania kell az eszköz konfigurációját, hogy elegendő erőforrással rendelkezzen a minimális követelmények teljesítéséhez. Ezután újraindíthatja az eszközt, és csatlakozhat hozzá. A minimális konfigurációs követelményeket [a gazdarendszer a virtuális eszközökre vonatkozó minimális követelményeknek való megfelelését ellenőrző](#check-the-host-system) szakaszban tekintheti meg.

<!---If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A gazdagép minimális eszközkövetelményeknek való megfelelésének ellenőrzése
> * Virtuális eszköz üzembe helyezése a VMware-ben
> * A virtuális eszköz elindítása és az IP-cím lekérése

A következő oktatóanyag a virtuális eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

* [Megosztások beállítása a Data Box Gatewayen, és csatlakozás a megosztásokhoz](data-box-gateway-deploy-connect-setup-activate.md)

