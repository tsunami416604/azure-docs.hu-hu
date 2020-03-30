---
title: Microsoft Azure StorSimple Virtual Array iSCSI-kiszolgáló beállítása | Microsoft dokumentumok
description: Ez a témakör a kezdeti telepítés tése, a StorSimple iSCSI-kiszolgáló regisztrálása és az eszköz beállításának befejezése.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254494"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple virtuális tömb telepítése – iSCSI-kiszolgálóként való beállítás az Azure Portalon keresztül

![iscsi beállítási folyamatfolyamat](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez a központi telepítési oktatóanyag a Microsoft Azure StorSimple virtuális tömbre vonatkozik. Ez az oktatóanyag bemutatja, hogyan hajthatja végre a kezdeti telepítést, regisztrálhatja a StorSimple iSCSI-kiszolgálót, hogyan végezheti el az eszköz telepítését, majd hogyan hozhat létre, csatlakoztathat, inicializálhat és formázhat köteteket az iSCSI-kiszolgálóként konfigurált StorSimple virtuális tömbön. 

Az itt leírt eljárások körülbelül 30 perc és 1 óra között tart. A cikkben közzétett információk csak a StorSimple virtuális tömbökre vonatkoznak.

## <a name="setup-prerequisites"></a>Telepítési előfeltételek

A StorSimple virtuális tömb konfigurálása és beállítása előtt győződjön meg arról, hogy:

* Kiépített egy virtuális tömböt, és a [StorSimple virtuális tömb telepítése – Virtuális tömb kiépítése a Hyper-V-ben](storsimple-ova-deploy2-provision-hyperv.md) vagy [a StorSimple virtuális tömb telepítése – Virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md)leírtak szerint.
* A StorSimple Virtuális tömbök kezeléséhez létrehozott StorSimple Eszközkezelő szolgáltatás szolgáltatásregisztrációs kulcsa rendelkezik. További információ: **2. lépés: A szolgáltatás regisztrációs kulcsának beszereznie a** [StorSimple virtuális tömb telepítése – A portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)című témakörben.
* Ha ez a második vagy azt követő virtuális tömb, amelyet egy meglévő StorSimple Eszközkezelő szolgáltatással regisztrál, rendelkeznie kell a szolgáltatás adattitkosítási kulccsal. Ez a kulcs akkor jött létre, amikor az első eszköz sikeresen regisztrálva lett ezzel a szolgáltatással. Ha elvesztette ezt a kulcsot, olvassa **el A szolgáltatás adattitkosítási kulcsának beolvassa a** [StorSimple virtuális tömb felügyeletéhez a Webes felhasználói felület használata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)című témakört.

## <a name="step-by-step-setup"></a>Részletes beállítás

A StorSimple virtuális tömb beállításához és konfigurálásához kövesse az alábbi lépésenkénti utasításokat:

* [1. lépés: A helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* 2. lépés: A szükséges eszközbeállítás befejezése
* [3. lépés: Kötet hozzáadása](#step-3-add-a-volume)
* [4. lépés: Kötet csatlakoztatása, inicializálása és formázása](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: A helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása

#### <a name="to-complete-the-setup-and-register-the-device"></a>A beállítás befejezéséhez és az eszköz regisztrálásához

1. Nyisson meg egy böngészőablakot. A webes felhasználói felülethez való csatlakozáshoz írja be a következőt:
   
    `https://<ip-address of network interface>`
   
    Használja az előző lépésben feljegyzett kapcsolat URL-címét. Megjelenik egy hibaüzenet, amely arról értesíti, hogy probléma van a webhely biztonsági tanúsítványával. Kattintson **a Folytatás gombra erre a weblapra**.
   
    ![biztonsági tanúsítvány hiba](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Jelentkezzen be a virtuális eszköz webes felhasználói felületére **StorSimpleAdmin**néven. Adja meg a [3.](storsimple-virtual-array-deploy2-provision-vmware.md) [Deploy StorSimple Virtual Array - Provision a virtual device in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
   
    ![Bejelentkezési lap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. A kezdőlapra kerül. **Home** Ez a lap a virtuális eszköz storSimple Eszközkezelő szolgáltatással való konfigurálásához és regisztrálásához szükséges különböző beállításokat ismerteti. Ne feledje, hogy a **Hálózati beállítások**, **a Webproxy-beállítások**és **az Idő beállítások** megadása nem kötelező. Az egyetlen szükséges beállítás az **Eszközbeállítások** és a **Felhőbeállítások.**
   
    ![Kezdőlap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. A **Hálózati beállítások** lap **Hálózati csatolók**területén a DATA 0 automatikusan konfigurálva lesz. Alapértelmezés szerint minden hálózati adapter úgy van beállítva, hogy automatikusan megkapja az IP-címet (DHCP). Ezért a rendszer automatikusan hozzárendel i.b. IP-címet, alhálózatot és átjárót (mind az IPv4, mind az IPv6 protokollhoz).
   
    Az eszköz iSCSI-kiszolgálóként történő központi telepítésének tervezésekor azt javasoljuk, hogy tiltsa le az **IP-cím automatikus begyűjtése** beállítást, és konfigurálja a statikus IP-címeket.
   
    ![Hálózati beállítások lap](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Ha az eszköz kiépítése során egynél több hálózati adaptert adott hozzá, itt konfigurálhatja őket. Megjegyzés: a hálózati adapter t csak IPv4-ként, illetve IPv4- és IPv6-ként is konfigurálhatja. Csak iPv6-konfigurációk nem támogatottak.
5. Dns-kiszolgálókra azért van szükség, mert akkor használatosak, amikor az eszköz kommunikálni próbál a felhőtárhely-szolgáltatókkal, vagy név szerint feloldja az eszközt, ha fájlkiszolgálóként van konfigurálva. A **Hálózati beállítások** lapon a **DNS-kiszolgálók**alatt:
   
   1. A rendszer automatikusan konfigurálja az elsődleges és másodlagos DNS-kiszolgálót. Ha statikus IP-címek konfigurálását választja, megadhat DNS-kiszolgálókat. A magas rendelkezésre állás érdekében azt javasoljuk, hogy konfiguráljon egy elsődleges és egy másodlagos DNS-kiszolgálót.
   2. Kattintson az **Alkalmaz** gombra. Ez érvényes és érvényesíti a hálózati beállításokat.
6. Az **Eszközbeállítások** lapon:
   
   1. Rendeljen egyedi **nevet** az eszközhöz. Ez a név 1-15 karakter lehet, és tartalmazhat betűket, számokat és kötőjeleket.
   2. Kattintson a létrehozás ![közben létrehozott](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) eszköz **típusának** **iSCSI-kiszolgálóikonjára.** Az iSCSI-kiszolgáló lehetővé teszi a blokktárolás kiépítését.
   3. Adja meg, hogy az eszközt tartományhoz kívánja-e csatlakoztatni. Ha az eszköz iSCSI-kiszolgáló, akkor a tartományhoz való csatlakozás nem kötelező. Ha úgy dönt, hogy nem csatlakozik az iSCSI-kiszolgálóhoz egy tartományhoz, kattintson az **Alkalmaz**gombra, várja meg a beállítások alkalmazását, majd ugorjon a következő lépésre.
      
       Ha tartományhoz szeretne csatlakozni az eszközhöz. Írjon be egy **tartománynevet**, majd kattintson **az Alkalmaz gombra.**
      
      > [!NOTE]
      > Ha az iSCSI-kiszolgálót tartományhoz csatlakoztatja, győződjön meg arról, hogy a virtuális tömb a Microsoft Azure Active Directory saját szervezeti egységében (OU) található, és nincs enek csoportházirend-objektumok ra.
      > 
      > 
   4. Megjelenik egy párbeszédpanel. Adja meg a tartomány hitelesítő adatait a megadott formátumban. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). A rendszer ellenőrzi a tartományi hitelesítő adatokat. Hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
      
       ![hitelesítő adatok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kattintson az **Alkalmaz** gombra. Ez érvényes és érvényesíti az eszköz beállításait.
7. (Opcionálisan) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani.
   
    ![webproxy konfigurálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    A **webproxy** lapon:
   
   1. Adja meg a **webproxy URL-címét** a következő formátumban: *\/http: /host-IP-cím* vagy *FQDN:Port szám*. Ne feledje, hogy a HTTPS URL-címek nem támogatottak.
   2. Adja meg **a hitelesítést** **alapszintűként** vagy **Nincsként.**
   3. Ha hitelesítést használ, meg kell adnia egy **felhasználónevet** és egy **jelszót**is.
   4. Kattintson az **Alkalmaz** gombra. Ez ellenőrzi és alkalmazza a beállított webproxy-beállításokat.
8. (Opcionálisan) konfigurálja az eszköz időbeállításait, például az időzónát, valamint az elsődleges és másodlagos NTP-kiszolgálókat. NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt, hogy hitelesíthesse magát a felhőszolgáltatókkal.
   
    ![Időbeállítások](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Az **Időbeállítások** lapon:
   
   1. A legördülő listából válassza ki az **időzónát** az eszköz üzembe helyezése földrajzi helye alapján. A készülék alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   2. Adjon meg **egy elsődleges NTP-kiszolgálót** az eszközhöz, vagy fogadja el az alapértelmezett time.windows.com értéket. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
   3. Tetszés szerint adjon meg **egy másodlagos NTP-kiszolgálót** az eszközhöz.
   4. Kattintson az **Alkalmaz** gombra. Ez ellenőrzi és alkalmazza a beállított időbeállításokat.
9. Konfigurálja az eszköz felhőbeállításait. Ebben a lépésben befejezi a helyi eszközkonfigurációt, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatással.
   
   1. Adja meg a **szolgáltatás regisztrációs kulcsát,** amelyet a [Deploy StorSimple Virtual Array - Prepare the Portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) **2.**
   2. Ha nem ez az első eszköz, amelyet ezzel a szolgáltatással regisztrál, meg kell adnia a **Szolgáltatás adattitkosítási kulcsát.** Ez a kulcs szükséges a szolgáltatás regisztrációs kulcsregisztrálásához további eszközök a StorSimple Eszközkezelő szolgáltatás regisztrálásához. További információt a [Szolgáltatás adattitkosítási kulcsának beszereznie a](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) helyi webes felhasználói felületen című dokumentumban talál.
   3. Kattintson a **Regisztráció gombra.** Ez újraindítja az eszközt. Előfordulhat, hogy 2-3 percet kell várnia, mielőtt az eszköz sikeresen regisztrálva van. Az eszköz újraindítása után a bejelentkezési oldalra kerül.
      
      ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Lépjen vissza az Azure Portalra.
11. Keresse meg a szolgáltatás **Eszközök** paneljét. Ha sok erőforrással rendelkezik, kattintson a **Minden erőforrás**elemre, kattintson a szolgáltatás nevére (szükség esetén keresse meg), majd kattintson **az Eszközök gombra.**
12. Az **Eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz az állapot megkeresésével. Az eszköznek **Beállításra kész** állapotúnak kell lennie.
    
    ![Eszköz regisztrálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>2. lépés: Az eszköz konfigurálása iSCSI-kiszolgálóként

Hajtsa végre a következő lépéseket az Azure Portalon a szükséges eszközbeállítás végrehajtásához.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Az eszköz konfigurálása iSCSI-kiszolgálóként

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, majd a **Management > Devices**lehetőséget. Az **Eszközök** panelen válassza ki az imént létrehozott eszközt. Ez az eszköz **készen áll a beállításra.**
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet, amely jelzi, hogy az eszköz készen áll a beállításra.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kattintson a **Konfigurálás** gombra az eszköz parancssávján. Ez **megnyitja** a Configure panelt. A **Konfigurálás** panelen tegye a következőket:
   
   * Az iSCSI-kiszolgáló neve automatikusan kitöltődik.
   * Győződjön meg arról, hogy a felhőalapú tároló titkosítása **Engedélyezve**van. Ez biztosítja, hogy az eszközről a felhőbe küldött adatok titkosítva legyenek.
   * Adjon meg egy 32 karakterből álló titkosítási kulcsot, és rögzítse azt egy kulcskezelő alkalmazásban későbbi használatra.
   * Válassza ki az eszközhöz használandó tárfiókot. Ebben az előfizetésben kiválaszthat egy meglévő tárfiókot, vagy a **Hozzáadás** gombra kattintva választhat egy fiókot egy másik előfizetésből.
     
     ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Az iSCSI-kiszolgáló beállításának befejezéséhez kattintson a **Konfigurálás** gombra.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Értesítést kap arról, hogy az iSCSI-kiszolgáló létrehozása folyamatban van. Az iSCSI-kiszolgáló sikeres létrehozása után az **Eszközök** panel frissül, és a megfelelő eszközállapot **online**állapot.
   
    ![Eszköz konfigurálása iSCSI-kiszolgálóként](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>3. lépés: Kötet hozzáadása

1. Az **Eszközök** panelen válassza ki az iSCSI-kiszolgálóként beállított eszközt. Kattintson **a ...** (másik lehetőségként kattintson a jobb gombbal ebben a sorban), és a helyi menüben válassza a **Kötet hozzáadása parancsot.** A parancssávon a **+ Hangerő hozzáadása** gombra is kattinthat. Ezzel megnyitja a **Kötet hozzáadása** panelt.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. A **Kötet hozzáadása** panelen tegye a következőket:
   
   * A **Kötet neve** mezőben adja meg a kötet egyedi nevét. A névnek 3–127 karakterből legtartalmazott karakterláncnak kell lennie.
   * A **Típus** legördülő listában adja meg, hogy **rétegzett** vagy **helyileg rögzített kötetet** szeretne-e létrehozni. A helyi garanciákat, alacsony késéseket és nagyobb teljesítményt igénylő számítási feladatok esetében válassza a **Helyileg rögzített** **kötet lehetőséget.** Az összes többi adat esetében válassza **a Rétegzett** **kötet lehetőséget.**
   * A **Kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetnek 500 GB és 5 TB között kell lennie, és a helyileg rögzített kötetnek 50 GB és 500 GB között kell lennie.
     
     A helyileg rögzített kötet vastagon van kiépítve, és biztosítja, hogy a kötetben lévő elsődleges adatok az eszközön maradnak, és ne ömlenjön ki a felhőbe.
     
     A rétegzett kötet viszont vékonyan ki van építve. Amikor többszintű kötetet hoz létre, a terület körülbelül 10%-a a helyi szinten van kiépítve, és a terület 90%-a a felhőben van kiépítve. Például ha egy 1 TB-os kötetet, 100 GB-os a helyi térben található, és 900 GB-ot kell használni a felhőben, amikor az adatrétegek. Ez viszont azt jelenti, hogy ha elfogy az összes helyi terület az eszközön, nem lehet kiépíteni egy rétegzett megosztás (mert a 10%, nem lesz elérhető).
     
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kattintson **a Csatlakoztatott állomások**elemre, jelöljön ki egy, a kötethez csatlakozni kívánt iSCSI-kezdeményezőnek megfelelő hozzáférés-vezérlési rekordot (ACR), majd kattintson a Kijelölés **gombra.** <br><br> 
3. Új csatlakoztatott állomás hozzáadásához kattintson az **Új hozzáadása**gombra, adja meg az állomás és az iSCSI minősített neve (IQN) nevét, majd kattintson a **Hozzáadás**gombra. Ha nem rendelkezik IQN-nal, folytassa [az A függelék: A Windows Server gazdagép IQN-je](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Ha befejezte a kötet konfigurálását, kattintson az **OK**gombra. A kötet jön létre a megadott beállításokat, és megjelenik egy értesítést. Alapértelmezés szerint a figyelés és a biztonsági mentés engedélyezve lesz a köteten.
   
     ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. A kötet sikeres létrehozásának ellenőrzéséhez lépjen a **Kötetek** panelre. Meg kell jelennie a listában.
   
   ![Kötet hozzáadása](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4. lépés: Kötet csatlakoztatása, inicializálása és formázása

Hajtsa végre az alábbi lépéseket a StorSimple-kötetek Windows Server-gazdagépen való csatlakoztatásához, inicializálásához és formázásához.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Kötet csatlakoztatása, inicializálása és formázása

1. Nyissa meg az **iSCSI-kezdeményező** alkalmazást a megfelelő kiszolgálón.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Felderítés** lapon kattintson a **Kapu felderítése** elemre.
   
    ![felderítési portál](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. A **Tárolókapu felderítése** párbeszédablakban adja meg az iSCSI-kompatibilis hálózati adaptert, majd kattintson az **OK** gombra.
   
    ![IP-cím](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Tárolók** lapon keresse meg a **Felderített tárolók** elemet. (Minden kötet felderített cél lesz.) Az eszköz állapotának **inaktívként kell megjelennie.**
   
    ![felfedezett célok](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Jelöljön ki egy céleszközt, majd kattintson a **Csatlakozás gombra.** Az eszköz csatlakoztatása után az állapotnak **Csatlakoztatva** állapotra kell módosulnia. (A Microsoft iSCSI-kezdeményező használatáról a [Microsoft iSCSI-kezdeményező telepítése és konfigurálása][1]című témakörben talál további információt.
   
    ![céleszköz kiválasztása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. A Windows-gazdagépen nyomja le a Windows és az X billentyűt, majd kattintson a **Futtatás** parancsra.
7. A **Futtatás** párbeszédpanelen írja be a **Diskmgmt.msc** karakterláncot. Kattintson az **OK** gombra, és megjelenik a **Lemezkezelés**párbeszédablak. A jobb oldali panel a gazdagépen lévő köteteket jeleníti meg.
8. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.
   
    ![lemezkezelés](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kattintson a jobb gombbal, és válassza **a Lemez inicializálása parancsot.**
   
    ![1. lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. A párbeszédpanelen jelölje ki az inicializálni kívánt lemez(eke)t, majd kattintson az **OK**gombra.
    
    ![2. lemez inicializálása](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Elindul az Új egyszerű kötet varázsló. Adja meg a lemezméretét, majd kattintson a **Tovább gombra.**
    
    ![új kötet varázsló 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Rendeljen meghajtóbetűjelet a kötethez, majd kattintson a **Tovább**gombra.
    
    ![új kötet varázsló 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Adja meg a kötet formázásához megadott paramétereket. **Windows Server rendszerben csak az NTFS támogatott.** Állítsa a foglalási egység méretét 64K-ra. Adjon meg egy címkét a kötethez. Ajánlott eljárás, hogy ez a név megegyezik a StorSimple virtuális tömbön megadott kötetnévvel. Kattintson a **Tovább** gombra.
    
    ![új kötet varázsló 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Ellenőrizze a kötet értékeit, majd kattintson a **Befejezés gombra.**
    
    ![új kötet varázsló 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    A kötetek **online** állapotban jelennek meg a **Lemezkezelés** lapon.
    
    ![kötetek online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja a helyi webes felhasználói felületet [a StorSimple virtuális tömb felügyeletére.](storsimple-ova-web-ui-admin.md)

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>A függelék: A Windows Server gazdagép IQN-jének beolvasása

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás iSCSI minősített nevének (IQN) lekéréséhez.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows Server-gazdagép IQN nevének lekérése

1. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen.
2. Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő sztringet.
   
    ![iSCSI-kezdeményező tulajdonságai](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Mentse ezt a sztringet.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



