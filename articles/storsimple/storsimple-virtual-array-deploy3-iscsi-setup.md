---
title: "Az iSCSI-kiszolgáló telepítése a Microsoft Azure StorSimple virtuális tömb |} Microsoft Docs"
description: "Ismerteti, hogyan hajtsa végre a kezdeti beállítás, a StorSimple iSCSI kiszolgáló regisztrálásához és eszköz beállításának befejezése."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Központi telepítése a StorSimple virtuális tömb – állítsa be Azure-portálon az iSCSI-kiszolgálóként

![az iSCSI-telepítés folyamata](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Áttekintés

A telepítési útmutató a Microsoft Azure StorSimple virtuális tömb vonatkozik. Ez az oktatóanyag leírja, hogyan hajtsa végre a kezdeti beállítás, a StorSimple iSCSI-kiszolgáló regisztrálása, az eszköz beállításának befejezése és majd létrehozása, csatlakoztatása, inicializálása és formázása köteteket a StorSimple virtuális iSCSI-kiszolgálóként konfigurált tömbjét. 

A leírt eljárások befejezéséhez 1 óra itt körülbelül 30 percet igénybe vehet. Ez a cikk a közzétett adatokat kizárólag a StorSimple virtuális tömbök.

## <a name="setup-prerequisites"></a>Telepítési előfeltételek

Előtt konfigurálja, és állítsa be a StorSimple virtuális tömb, győződjön meg arról, hogy:

* Egy virtuális tömb kiosztása és a csatlakoztatott [központi telepítése a StorSimple virtuális tömb - Provision a Hyper-V egy virtuális tömb](storsimple-ova-deploy2-provision-hyperv.md) vagy [központi telepítése a StorSimple virtuális tömb - Provision VMware-ben a virtuális tömb](storsimple-virtual-array-deploy2-provision-vmware.md).
* Hogy a szolgáltatás regisztrációs kulcsának kezelheti a StorSimple virtuális tömbök létrehozott StorSimple Device Manager szolgáltatásból. További információkért lásd: **2. lépés: Szolgáltatásregisztrációs kulcs lekérése** a [központi telepítése a StorSimple virtuális tömb - készítse elő a portál](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Ha ez a második vagy virtuális tömb, amely egy meglévő StorSimple Device Manager szolgáltatással regisztrál, a szolgáltatásadat-titkosítási kulcs kell rendelkeznie. Ezt a kulcsot hozott létre, ha a szolgáltatás sikeresen regisztrálva lett az első eszköz. Ha elvesztette ezt a kulcsot, lásd: **a szolgáltatásadat-titkosítási kulcs beszerzése** a [a webes felhasználói felület segítségével felügyelheti a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Részletes beállítása

Az alábbi részletes útmutatás segítségével beállítása és konfigurálása a StorSimple virtuális tömb:

* [1. lépés: A helyi webes felhasználói felület beállításának befejezése, és regisztrálja az eszközt](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [2. lépés: A szükséges eszköz beállításának befejezése](#step-2-complete-the-required-device-setup)
* [3. lépés: Kötet hozzáadása](#step-3-add-a-volume)
* [4. lépés: Csatlakoztassa, inicializálja és formázza a kötetet](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: A helyi webes felhasználói felület beállításának befejezése, és regisztrálja az eszközt

#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejeződését, és regisztrálja az eszközt

1. Nyisson meg egy böngészőablakot. Csatlakozás a webes felhasználói felület típusa:
   
    `https://<ip-address of network interface>`
   
    A kapcsolat az előző lépésben feljegyzett URL-címet használja. Értesítés, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára hiba jelenik meg. Kattintson a **tovább az Ez a weblap**.
   
    ![biztonsági tanúsítvány hiba](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Jelentkezzen be a webes felhasználói felület, a virtuális eszköz mint **StorSimpleAdmin**. Adja meg az eszköz rendszergazdai jelszava, amely módosította a 3. lépés: Indítsa el a virtuális eszköz [központi telepítése a StorSimple virtuális tömb - a Hyper-V virtuális eszköz kiépítése](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [központi telepítése a StorSimple virtuális tömb - VMware-ben a virtuális eszköz kiépítése](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Bejelentkezési oldal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Megnyílik a a **Home** lap. Ez a lap ismerteti a különböző beállításokat konfigurálja, és a virtuális eszköz regisztrálása a StorSimple Device Manager szolgáltatásban kell. Vegye figyelembe, hogy a **hálózati beállításai**, **webalkalmazás-proxy beállításainak**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **eszközbeállítások** és **Felhőbeállítások**.
   
    ![Kezdőlap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Az a **hálózati beállításai** lapon az **hálózati illesztőt**, DATA 0 automatikusan megtörténik az Ön. Mindegyik hálózati interfész értéke alapértelmezés szerint automatikusan tud egy IP-címet (DHCP). Ezért egy IP-cím, alhálózati és az átjáró a rendszer automatikusan hozzárendeli (az IPv4 és IPv6).
   
    Az iSCSI-kiszolgálóként (a kiépítés blokktárolást) az eszköz telepítésének tervezésekor, azt javasoljuk, hogy tiltsa le a **IP-cím automatikus beszerzése** lehetőséget, majd a statikus IP-címek konfigurálásához.
   
    ![Hálózati beállítások lap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Ha az eszköz kiépítése során hozzáadott egynél több hálózati adapter, konfigurálhatja őket itt. Vegye figyelembe, hogy beállíthat a hálózati adapter IPv4-alapú csak vagy mint IPv4 és IPv6. Csak IPv6-konfigurációk nem támogatottak.
5. DNS-kiszolgálók szükség, mert azokat használják, amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatók, vagy hárítsa el az eszköz neve, ha erre van konfigurálva, mint a fájlkiszolgáló. Az a **hálózati beállításai** lapon az a **DNS-kiszolgálók**:
   
   1. Egy elsődleges és másodlagos DNS-kiszolgáló automatikusan megtörténik. Ha statikus IP-címek konfigurálása mellett dönt, a DNS-kiszolgálót is megadhat. A magas rendelkezésre állás érdekében azt javasoljuk, hogy egy elsődleges és másodlagos DNS-kiszolgáló konfigurálása.
   2. Kattintson az **Alkalmaz** gombra. Ez alkalmazza, és érvényesítse a hálózati beállításokat.
6. Az a **eszközbeállítások** lap:
   
   1. Rendeljen egy egyedi **neve** az eszközre. Ez a név 1 – 15 karakterből állhat, és betűvel, számokat és kötőjeleket tartalmazhat.
   2. Kattintson a **iSCSI server** ikon ![iSCSI kiszolgáló ikonja](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) a a **típus** eszköz létrehozása. Az iSCSI-kiszolgáló lehetővé teszi a kiépítés blokktárolókhoz csatlakoznának.
   3. Adja meg, ha az eszköz regisztrációjának frissítésével kell a tartományhoz. Ha az eszköz egy iSCSI-kiszolgálót, majd a tartományhoz nem kötelező megadni. Ha úgy dönt, hogy az iSCSI-kiszolgáló nem csatlakoztatása a tartományhoz, kattintson a **alkalmaz**, várja meg, és ugorjon a következő beállításokat.
      
       Ha azt szeretné, az eszköz csatlakoztatása a tartományhoz. Adjon meg egy **tartománynév**, és kattintson a **alkalmaz**.
      
      > [!NOTE]
      > Ha az iSCSI-kiszolgáló csatlakoztatása egy tartományhoz, győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) a Microsoft Azure Active Directoryhoz, és nem csoportházirend-objektumok (GPO) vonatkoznak rá.
      > 
      > 
   4. Megjelenik egy párbeszédpanel. A megadott formátumban adja meg a tartományi hitelesítő adatokat. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). A tartományi hitelesítő adatokat fogja ellenőrizni. Egy hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
      
       ![hitelesítő adatok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kattintson az **Alkalmaz** gombra. Ez alkalmazza, és az eszközök beállításainak ellenőrzéséhez.
7. A webes proxykiszolgáló (opcionális) konfigurálása. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
    ![webalkalmazás-proxy konfigurálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Az a **webalkalmazás-proxy** lap:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** ebben a formátumban: *http://host-IP cím* vagy *FDQN:Port szám*. Vegye figyelembe, hogy HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapvető** vagy **nincs**.
   3. Ha hitelesítést használ, akkor is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson az **Alkalmaz** gombra. A érvényesítése és a konfigurált webes proxykiszolgáló beállításait alkalmazza.
8. (Opcionális) időbeállításait az eszközét, például az időzóna és az elsődleges és másodlagos NTP-kiszolgálók. NTP-kiszolgáló szükség, mert az eszköz kell próbálta szinkronizálni az időt, hogy azt a felhőszolgáltatók hitelesíthető.
   
    ![Idő beállítása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Az a **időbeállítások** lap:
   
   1. A legördülő listából válassza ki a **időzóna** , amelyben az eszköz üzembe helyezésének földrajzi helye alapján. Az eszköz az alapértelmezett időzónát a csendes-óceáni TÉLI. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
   3. Megadhat egy **másodlagos NTP-kiszolgáló** az eszközhöz.
   4. Kattintson az **Alkalmaz** gombra. A érvényesítése és a konfigurált beállításokat alkalmazni.
9. Az eszköz a felhő beállításainak konfigurálása. Ebben a lépésben a helyi eszköz konfigurálásának befejezése, és regisztrálja az eszközt a StorSimple eszköz Manager szolgáltatásban.
   
   1. Adja meg a **Szolgáltatásregisztrációs kulcs** portáltól **2. lépés: Szolgáltatásregisztrációs kulcs lekérése** a [központi telepítése a StorSimple virtuális tömb - készítse elő a portál](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Ha nem ez az első eszköz, amely ezzel a szolgáltatással regisztrál, meg kell adnia a **szolgáltatásadat-titkosítási kulcs**. Ezt a kulcsot meg kell adni a szolgáltatás regisztrációs kulcsot, további eszközök regisztrálása a StorSimple Device Manager szolgáltatásban. További információkért tekintse meg [a szolgáltatásadat-titkosítási kulcs beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a helyi webes felhasználói felülete.
   3. Kattintson a **regisztrálása**. Ez az eszköz újraindul. Szükség lehet az eszköz regisztrálása sikeres volt 2-3 percet várni. Az eszköz újraindítása után megnyílik a bejelentkezési oldalon.
      
      ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Térjen vissza az Azure-portálon.
11. Keresse meg a **eszközök** panel a szolgáltatás. Ha nagy mennyiségű erőforrást, kattintson a **összes erőforrás**, kattintson a szolgáltatás neve (keresse meg azt, ha szükséges), majd **eszközök**.
12. Az a **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott a szolgáltatás által állapotát. Az eszköznek **Beállításra kész** állapotúnak kell lennie.
    
    ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>2. lépés: Az eszköz konfigurálása iSCSI-kiszolgálóként

Az Azure-portálon a szükséges eszköz beállításának befejezéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Az eszköz konfigurálása iSCSI-kiszolgálóként

1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és folytassa a **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki az eszköz nemrég létrehozott. Ez az eszköz akkor jelenik meg **már beállíthat**.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet arról, hogy a telepítő készen áll-e az eszközön.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kattintson a **konfigurálása** eszköz parancssávon. Ezzel megnyílik a **konfigurálása** panelen. Az a **konfigurálása** panelen tegye a következőket:
   
   * Az iSCSI-kiszolgáló neve automatikusan feltöltődik értékkel.
   * Győződjön meg arról, hogy a felhőalapú tárolás titkosításának értéke **engedélyezve**. Ez biztosítja, hogy az eszközről a felhőbe küldött adatok titkosítva van.
   * Adjon meg egy 32 karakterből álló titkosítási kulcs, és jegyezze fel a jövőben kulcskezelés alkalmazásban.
   * Válasszon egy tárfiókot, az eszköz használható. Ebben az előfizetésben, válassza ki egy meglévő tárfiókot használ, vagy kattintson **Hozzáadás** egy fiókot választhat egy másik előfizetést.
     
     ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kattintson a **konfigurálása** az iSCSI-kiszolgáló beállításának befejezéséhez.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Értesítést fog kapni, hogy az iSCSI-kiszolgáló létrehozása folyamatban van. Az iSCSI-kiszolgáló sikeres létrehozása után a **eszközök** panel frissül, és a megfelelő eszköz állapota **Online**.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>3. lépés: Kötet hozzáadása

1. Az a **eszközök** panelen válassza az eszköz most egy iSCSI-kiszolgálóként konfigurált. Kattintson a **...**  (másik lehetőségként kattintson a jobb gombbal a sorhoz), és válassza ki a helyi menü **kötet hozzáadása**. Is **+ kötet hozzáadása** a parancssávon. Ezzel megnyílik a **kötet hozzáadása** panelen.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Az a **kötet hozzáadása** panelen tegye a következőket:
   
   * Az a **kötetneve** mezőben adjon meg egy egyedi nevet a kötethez. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
   * Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** kötet. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **helyileg rögzített** **kötet**. Minden más adathoz válasszon **rétegzett** **kötet**.
   * Az a **kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetek 500 GB és 5 TB között kell lennie, és egy helyileg rögzített kötet 50 GB-os és 500 GB között kell lennie.
     
     Egy helyileg rögzített kötet kiosztása, és biztosítja, hogy a kötet elsődleges adatai marad az eszközön, és nem kerülnek a felhőbe.
     
     A rétegzett kötetek viszont kiosztása. Ha rétegzett kötetet hoz létre, körülbelül 10 %-a a terület a helyi rétegen ki van építve, és 90 %-tér ki van építve a felhőben. Például ha 1 TB-os kötet létesített, 100 GB kellene lennie, a helyi terület és 900 GB használni a felhőben során az adatok szinteket. Ez viszont azt jelenti, hogy ha már nem álló helyi területet az eszközön nem használhatók a rétegzett megosztás (mert nem lesz elérhető a 10 %).
     
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kattintson a **csatlakozó állomások**, jelölje be egy hozzáférés-vezérlési rekordot (ACR) az iSCSI-kezdeményezőt, amelyet szeretne csatlakozni a kötet, és kattintson a megfelelő **válasszon**. <br><br> 
3. Új csatlakoztatott gazdagép hozzáadásához kattintson **új hozzáadása**, adjon meg egy nevet a gazdagép és az iSCSI minősített nevét (IQN), és kattintson **Hozzáadás**. Ha még nem rendelkezik a IQN, folytassa a [függelék: a Windows Server-állomás IQN-Nevének lekérése](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Ha végzett a kötet konfigurálása, kattintson a **OK**. A kötet a megadott beállításokkal jön létre, és megjelenik egy értesítés. Alapértelmezés szerint figyelése és a biztonsági mentés engedélyezve lesz a kötet.
   
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Győződjön meg arról, hogy a kötet sikeresen létrejött, keresse fel a **kötetek** panelen. Meg kell jelenniük a felsorolt kötetet.
   
   ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4. lépés: Csatlakoztassa, inicializálja és formázza a kötetet

A következő lépésekkel csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Kötet csatlakoztatása, inicializálása és formázása

1. Nyissa meg a **iSCSI-kezdeményező** alkalmazást a megfelelő kiszolgálón.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Felderítés** lapon kattintson a **Kapu felderítése** elemre.
   
    ![Fedezze fel a portálra](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. A **Tárolókapu felderítése** párbeszédablakban adja meg az iSCSI-kompatibilis hálózati adaptert, majd kattintson az **OK** gombra.
   
    ![IP-cím](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Tárolók** lapon keresse meg a **Felderített tárolók** elemet. (Minden olyan kötetre lesz felderített target.) Az eszköznek **Inaktív** állapotúnak kell lennie.
   
    ![Felderített tárolók](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Jelöljön ki egy cél eszközt, és kattintson a **Connect**. Az eszköz csatlakoztatása után az állapotnak **Csatlakoztatva** állapotra kell módosulnia. (A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért lásd: [telepítése és konfigurálása a Microsoft iSCSI-kezdeményező][1].
   
    ![Válassza ki a céleszközt](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. A Windows-gazdagépen nyomja le a Windows és az X billentyűt, majd kattintson a **Futtatás** parancsra.
7. A **Futtatás** párbeszédpanelen írja be a **Diskmgmt.msc** karakterláncot. Kattintson az **OK** gombra, és megjelenik a **Lemezkezelés**párbeszédablak. A jobb oldali panel a gazdagépen lévő köteteket jeleníti meg.
8. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.
   
    ![Lemezkezelés](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kattintson a jobb gombbal, és válassza ki **lemez inicializálása**.
   
    ![1 lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Válassza ki a lemez(ek) inicializálása, és kattintson a párbeszédpanelen **OK**.
    
    ![2 lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Az új egyszerű kötet varázsló elindul. Válassza ki a lemez méretét, és kattintson a **következő**.
    
    ![Új kötet varázsló 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. A meghajtóbetűjelet rendelje a kötetre, és kattintson **következő**.
    
    ![Új kötet varázsló 2. régiója](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Adja meg a paramétereket a kötet formázását. **Windows Server rendszeren csak az NTFS fájlrendszer esetén támogatott.** 64 k foglalásiegység-méret megadása Adjon meg egy címkét a kötet. Ez a név lehet a StorSimple virtuális tömb a megadott kötet neve azonos az ajánlott eljárás. Kattintson a **Tovább** gombra.
    
    ![Új kötet varázsló 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Ellenőrizze az értékeket a kötet, és kattintson a **Befejezés**.
    
    ![Új kötet varázsló 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    A kötetek frissítésként jelenik meg **Online** a a **Lemezkezelés** lap.
    
    ![a kötetek online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja a helyi webes felhasználói felülete a [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>A függelék: a Windows Server-állomás IQN-Nevének lekérése

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás iSCSI minősített nevének (IQN) lekéréséhez.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows Server-gazdagép IQN nevének lekérése

1. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő karakterláncot.
   
    ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Mentse ezt a karakterláncot.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



