---
title: Az iSCSI-kiszolgáló telepítése a Microsoft Azure StorSimple Virtual Array |} A Microsoft Docs
description: Ismerteti, hogyan hajtsa végre a kezdeti beállítás, a StorSimple iSCSI-kiszolgáló regisztrálása és eszközbeállítások teljesítéséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: c0a86b76622862b477d539c25dd98c925f09192c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812038"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Üzembe helyezni a StorSimple Virtual Array – Set mentése, iSCSI-kiszolgáló Azure Portalon

![iSCSI-telepítés folyamatábrája](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple Virtual Array üzembe helyezési oktatóanyag vonatkozik. Ez az oktatóanyag leírja, hogyan hajtsa végre a kezdeti beállítás, a StorSimple iSCSI-kiszolgáló regisztrálása, az eszköz beállításának befejezése és majd létrehozása, csatlakoztatása, inicializálása és formázása köteteket a StorSimple Virtual Array egy iSCSI-kiszolgálóként konfigurált. 

Leírt eljárások végrehajtásához 1 órára itt körülbelül 30 percet igénybe vehet. Ez a cikk a közzétett információk csak a StorSimple Virtual Arrayt vonatkoznak.

## <a name="setup-prerequisites"></a>Telepítési előfeltételek

Mielőtt konfigurálja, és állítsa be a StorSimple Virtual Array, ellenőrizze, hogy:

* A virtuális tömb kiosztása és ahhoz kapcsolódó leírtak szerint [üzembe helyezése a StorSimple Virtual Array – üzembe helyezése egy virtuális tömböt a Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) vagy [üzembe helyezése a StorSimple Virtual Array – üzembe helyezése egy virtuális tömböt a VMware-ben ](storsimple-virtual-array-deploy2-provision-vmware.md).
* A Szolgáltatásregisztrációs kulcsot a StorSimple-Eszközkezelő szolgáltatás kezelése a StorSimple Virtual Arrayt létrehozott rendelkezik. További információkért lásd: **2. lépés: A Szolgáltatásregisztrációs kulcs lekérése** a [üzembe helyezése a StorSimple Virtual Array – a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Ha ez a második vagy virtuális tömb, amely egy meglévő StorSimple-Eszközkezelő szolgáltatással regisztrálja, a szolgáltatásadat-titkosítási kulcs kell. Ez a kulcs lett létrehozva, amikor az első eszköz sikeresen regisztrálva lett az ezt a szolgáltatást. Ha elvesztette ezt a kulcsot, lásd: **a szolgáltatásadat-titkosítási kulcs beszerzése** a [a webes felhasználói felület segítségével felügyelheti a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Részletes beállítása

Az alábbi részletes útmutatás segítségével beállíthatja és konfigurálhatja a StorSimple Virtual Array:

* [1. lépés: Végezze el a helyi webes felhasználói felület beállítását és az eszköz regisztrálása](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* 2. lépés: A szükséges eszköz beállításának befejezése
* [3. lépés: Kötet hozzáadása](#step-3-add-a-volume)
* [4. lépés: Csatlakoztatása, inicializálása és kötetek](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: Végezze el a helyi webes felhasználói felület beállítását és az eszköz regisztrálása

#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejezéséhez, és regisztrálja az eszközt

1. Nyisson meg egy böngészőablakot. Csatlakozás a webes felhasználói felület típusa:
   
    `https://<ip-address of network interface>`
   
    Használja az előző lépésben feljegyzett kapcsolati URL-cím. Látni fogja a hibát, amely értesíti arról, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára. Kattintson a **tovább a weblapra a**.
   
    ![biztonsági tanúsítvány hiba](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Jelentkezzen be a webes felhasználói felületen, a virtuális eszköz, **StorSimpleAdmin**. Adja meg az eszköz rendszergazdai jelszava módosított 3. lépés: Indítsa el a virtuális eszköz [üzembe helyezése a StorSimple Virtual Array – a Hyper-V virtuális eszköz üzembe helyezése](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [üzembe helyezése a StorSimple Virtual Array – VMware-ben a virtuális eszköz üzembe helyezése](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Bejelentkezési oldal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Megnyílik a a **kezdőlap** lapot. Ezen a lapon konfigurálja, és a virtuális eszköz Regisztráljon a StorSimple-Eszközkezelő szolgáltatással szükséges különböző beállításokat ismerteti. Vegye figyelembe, hogy a **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **eszközbeállítások** és **Felhőbeállítások**.
   
    ![Kezdőlap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Az a **hálózati beállítások** lap **hálózati adapterek**, DATA 0 automatikusan megtörténik az Ön számára. Mindegyik hálózati adapter IP-cím automatikusan be alapértelmezés szerint van beállítva (DHCP). Ezért egy IP-cím, alhálózat és átjáró lesz automatikusan hozzárendelve (az IPv4 és IPv6).
   
    Mivel azt tervezi, az eszköz üzembe helyezése egy iSCSI-kiszolgálóként (a blokktároló kiépítése), azt javasoljuk, hogy tiltsa le a **IP-cím automatikus beszerzése** lehetőséget, majd konfigurálja a statikus IP-címeket.
   
    ![Hálózati beállítások lap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Ha az eszköz kiépítése során hozzáadott több hálózati adapter, konfigurálhatja őket itt. Vegye figyelembe, hogy konfigurálni lehet a hálózati adapter IPv4, csak vagy IPv4- és IPv6-alapú. Csak IPv6-konfigurációk nem támogatottak.
5. DNS-kiszolgálókra szükség, mert használatosak, amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatók vagy név szerint az eszköz feloldásához, ha erre van konfigurálva, a fájlkiszolgáló. Az a **hálózati beállítások** lap a **DNS-kiszolgálók**:
   
   1. Egy elsődleges és másodlagos DNS-kiszolgáló automatikusan megtörténik. Ha statikus IP-címek konfigurálása választja, megadhatja a DNS-kiszolgálók. A magas rendelkezésre állás érdekében azt javasoljuk, hogy egy elsődleges és másodlagos DNS-kiszolgáló konfigurálása.
   2. Kattintson az **Alkalmaz** gombra. Ezzel alkalmazni, és ellenőrizze a hálózati beállításokat.
6. Az a **eszközbeállítások** oldalon:
   
   1. Rendelje hozzá egy egyedi **neve** az eszközre. Ez a név hossza 1 – 15 karakter lehet, és betűvel, számokat és kötőjeleket tartalmazhat.
   2. Kattintson a **iSCSI-kiszolgáló** ikon ![iSCSI-kiszolgáló ikonja](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) a a **típusa** eszköz, amely hoz létre. ISCSI-kiszolgáló lehetővé teszi a kiépítés blokkblob-tárolás.
   3. Adja meg, ha az eszközt a tartományhoz csatlakoztatott lehet. Ha az eszköz egy iSCSI-kiszolgálót, majd a tartományhoz nem kötelező. Ha úgy dönt, hogy nem az iSCSI-kiszolgáló csatlakoztatása egy tartományhoz, kattintson a **alkalmaz**, várja meg a beállításokat a alkalmazni, és folytassa a következő lépéssel.
      
       Ha azt szeretné, az eszköz csatlakoztatása a tartományhoz. Adjon meg egy **tartománynév**, és kattintson a **alkalmaz**.
      
      > [!NOTE]
      > Ha az iSCSI-kiszolgáló csatlakoztatása egy tartományhoz, győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) a Microsoft Azure Active Directoryhoz, és nem a csoportházirend-objektumok (GPO) beállítva rajta.
      > 
      > 
   4. Ekkor egy párbeszédpanel jelenik meg. A megadott formátumban adja meg a tartományi hitelesítő adatait. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). A tartományi hitelesítő adatokat fogja ellenőrizni. Látni fogja egy hibaüzenet, ha a hitelesítő adatok helytelenek.
      
       ![hitelesítő adatok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kattintson az **Alkalmaz** gombra. Ezzel alkalmazni, és az Eszközbeállítások ellenőrzése.
7. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
    ![Webalkalmazás-proxy konfigurálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Az a **webalkalmazás-proxy** oldalon:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** a következő formátumban:  *http://host-IP cím* vagy *FQDN: port számát*. Vegye figyelembe, hogy HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapszintű** vagy **None**.
   3. Hitelesítés használatakor is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a konfigurált webes proxykiszolgáló beállításait a alkalmazni.
8. (Opcionális) beállításokat az idő az eszköz, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálót. Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
   
    ![Időbeállítások](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Az a **időbeállítások** oldalon:
   
   1. A legördülő listából válassza ki a **időzóna** , amelyben az eszközre telepített földrajzi helye alapján. Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
   3. Nem kötelezően megadhatja a **másodlagos NTP-kiszolgáló** az eszközhöz.
   4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a beállított idő beállítások alkalmazásához.
9. Az eszköz a felhő beállításainak konfigurálása. Ebben a lépésben a helyi eszköz konfigurálásának befejezéséhez, és ezután regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatásban.
   
   1. Adja meg a **Szolgáltatásregisztrációs kulcs** kapott **2. lépés: A Szolgáltatásregisztrációs kulcs lekérése** a [üzembe helyezése a StorSimple Virtual Array – a portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Ha ez nem az első eszköz, amely a szolgáltatásban regisztrál, kell adnia a **szolgáltatásadat-titkosítási kulcs**. Ez a kulcs megadása kötelező a szolgáltatás regisztrációs kulcsával együtt szeretne további eszközöket regisztrálni a StorSimple-Eszközkezelő szolgáltatásban. További információkért tekintse meg [a szolgáltatásadat-titkosítási kulcs beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a helyi webes felhasználói Felületét.
   3. Kattintson a **regisztrálása**. A művelet újraindítja az eszközt. Szükség lehet várjon 2-3 percet, mielőtt az eszköz regisztrálása sikeres volt. Az eszköz újraindulása után megnyílik a bejelentkezési lapot.
      
      ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Lépjen vissza az Azure Portalra.
11. Keresse meg a **eszközök** panelen a szolgáltatás. Ha sok erőforrást, kattintson a **összes erőforrás**, kattintson a szolgáltatás nevét (keresése, ha szükséges), majd **eszközök**.
12. Az a **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott a szolgáltatáshoz-állapotát. Az eszköznek **Beállításra kész** állapotúnak kell lennie.
    
    ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>2. lépés: Az eszköz konfigurálása iSCSI-kiszolgálóként

Az Azure Portalon, a szükséges eszköz beállításának befejezéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Az eszköz konfigurálása iSCSI-kiszolgálóként

1. Nyissa meg a StorSimple-Eszközkezelő szolgáltatás, és folytassa a **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki az imént eszköz létrehozva. Ez az eszköz jelennek meg **beállításra kész**.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Válassza ki az eszközt, és megjelenik egy szalagcím üzenet arról, hogy a telepítő készen áll-e az eszközön.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kattintson a **konfigurálása** az eszköz parancssávon. Megnyílik a **konfigurálása** panelen. Az a **konfigurálása** panelen tegye a következőket:
   
   * Az iSCSI-kiszolgáló nevét a rendszer automatikusan kitölti.
   * Ellenőrizze, hogy a felhős társzolgáltatás titkosítása értéke **engedélyezve**. Ez biztosítja, hogy az eszközről a felhőbe küldött adatok titkosítva van.
   * Adjon meg egy 32 karakterből álló titkosítási kulcs, és rögzítse azt egy kulcskezelő alkalmazásban későbbi felhasználás céljából.
   * Válassza ki a storage-fiók, amely az eszköz használható. Ebben az előfizetésben, válassza ki egy meglévő tárfiókot, vagy kattintson **Hozzáadás** egy fiókot választhat egy másik előfizetést.
     
     ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kattintson a **konfigurálása** az iSCSI-kiszolgáló beállításának befejezéséhez.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Értesíteni fogjuk, hogy az iSCSI-kiszolgáló létrehozása folyamatban van. Az iSCSI-kiszolgáló sikeres létrehozása után a **eszközök** panel frissül, és a megfelelő eszköz állapota **Online**.
   
    ![Eszközök konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>3. lépés: Kötet hozzáadása

1. Az a **eszközök** panelen válassza ki az eszköz nemrég konfigurált egy iSCSI-kiszolgálóként. Kattintson a **...**  (másik lehetőségként kattintson a jobb gombbal a sor) és a helyi menüből válassza ki a **kötet hozzáadása**. Is **+ kötet hozzáadása** a parancssávon. Megnyílik a **kötet hozzáadása** panelen.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Az a **kötet hozzáadása** panelen tegye a következőket:
   
   * Az a **kötet neve** mezőben adjon meg egy egyedi nevet a kötet. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
   * Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** kötet. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterhelésekhez, válassza ki a **helyileg rögzített** **kötet**. Minden más adathoz válasszon **rétegzett** **kötet**.
   * Az a **kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetek 500 GB-os és 5 TB között kell lennie, és a egy helyileg rögzített kötet 50 GB és 500 GB között kell lennie.
     
     Egy helyileg rögzített kötet kiosztása, és biztosítja, hogy a kötet elsődleges adatai marad az eszközön, és nem kerülnek a felhőbe.
     
     A rétegzett kötetek azonban kiosztása. Ha rétegzett kötetet hoz létre, a tárhely körülbelül 10 % a helyi rétegen van kiépítve, és 90 %-a terület a felhőben van kiépítve. Például ha Ön egy 1 TB-os kötetet, 100 GB-os helyi területet lenne tartalmazhat, és a felhőben használni kívánt 900 GB amikor az adat szintet. Ez pedig azt jelenti, hogy ha kifogy a összes helyi területet az eszközön nem használhatók a rétegzett megosztás (mivel a 10 %-os nem érhető el).
     
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kattintson a **csatlakoztatott gazdagépek**, jelölje be egy hozzáférés-vezérlési rekord (ACR) az iSCSI-kezdeményező csatlakozhat a kötet, és kattintson a kívánt megfelelő **kiválasztása**. <br><br> 
3. Új csatlakoztatott gazdagép hozzáadásához kattintson **új hozzáadása**, adjon meg egy nevet a gazdagép és a hozzá tartozó iSCSI minősített nevét (IQN), és kattintson **Hozzáadás**. Ha nem rendelkezik az IQN-t, lépjen a [függelék: A Windows Server-gazdagép IQN Nevének lekérése](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Ha elkészült, a kötet konfigurálása, kattintson a **OK**. Egy kötet a megadott beállításokkal jön létre, és megjelenik egy értesítés. Alapértelmezés szerint figyelése és a biztonsági mentés lesznek engedélyezve a kötetet.
   
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Győződjön meg arról, hogy a kötet sikeresen létrejött, nyissa meg a **kötetek** panelen. A kötet felsorolt kell megjelennie.
   
   ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4. lépés: Kötet csatlakoztatása, inicializálása és formázása

A következő lépésekkel csatlakoztatása, inicializálása és formázása a StorSimple-köteteket egy Windows Server-gazdagépen.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Kötet csatlakoztatása, inicializálása és formázása

1. Nyissa meg a **iSCSI-kezdeményező** alkalmazást a megfelelő kiszolgálón.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Felderítés** lapon kattintson a **Kapu felderítése** elemre.
   
    ![Fedezze fel a portálon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. A **Tárolókapu felderítése** párbeszédablakban adja meg az iSCSI-kompatibilis hálózati adaptert, majd kattintson az **OK** gombra.
   
    ![IP-cím](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Tárolók** lapon keresse meg a **Felderített tárolók** elemet. (Minden olyan kötetre a felderített cél lesz.) Az eszköznek **Inaktív** állapotúnak kell lennie.
   
    ![Felderített tárolók](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Válassza ki a céleszközt, és kattintson a **Connect**. Az eszköz csatlakoztatása után az állapotnak **Csatlakoztatva** állapotra kell módosulnia. (A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért lásd: [telepítése és konfigurálása a Microsoft iSCSI-kezdeményező][1].
   
    ![Válassza ki a céleszközt](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. A Windows-gazdagépen nyomja le a Windows és az X billentyűt, majd kattintson a **Futtatás** parancsra.
7. A **Futtatás** párbeszédpanelen írja be a **Diskmgmt.msc** karakterláncot. Kattintson az **OK** gombra, és megjelenik a **Lemezkezelés**párbeszédablak. A jobb oldali panel a gazdagépen lévő köteteket jeleníti meg.
8. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.
   
    ![Lemezkezelés](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kattintson a jobb gombbal, és válassza ki **lemez inicializálása**.
   
    ![1 lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Válassza a lemezek inicializálása, és kattintson a párbeszédpanelen **OK**.
    
    ![2 lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Elindítja az új egyszerű kötet varázslóban. Válassza ki a lemez méretét, és kattintson a **tovább**.
    
    ![Új kötet varázsló 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Rendeljen hozzá meghajtóbetűjelet a kötetre, és kattintson a **tovább**.
    
    ![Új kötet varázsló 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Adja meg a paramétereket a kötet formázását. **Windows-kiszolgálón csak az NTFS fájlrendszer támogatott.** Állítsa a lemezfoglalási egység mérete 64 KB. Adjon meg egy címkét a kötet. Ajánlott eljárás a úgy, hogy a kötet neve, a StorSimple virtuális tömb a megadott név legyen. Kattintson a **tovább**.
    
    ![Új kötet varázsló 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Ellenőrizze az értékeket a kötet, és kattintson a **Befejezés**.
    
    ![Új kötet varázsló 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    A kötetek fog megjelenni **Online** a a **Lemezkezelés** lapot.
    
    ![a kötetek online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja a helyi webes felhasználói Felületét a [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>A függelék: Egy Windows Server-állomás IQN-nevének lekérése

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás iSCSI minősített nevének (IQN) lekéréséhez.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows Server-gazdagép IQN nevének lekérése

1. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő sztringet.
   
    ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Mentse ezt a sztringet.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



