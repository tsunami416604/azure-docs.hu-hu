---
title: Állítsa be a StorSimple Virtual Array fájlkiszolgálóként |} A Microsoft Docs
description: A StorSimple Virtual Array üzembe helyezési oktatóanyag harmadik arra utasítja a fájlkiszolgáló virtuális eszköz létrehozása.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f699e40a4a31b6d57b12a43ae307806d3f010015
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267181"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Üzembe helyezni a StorSimple Virtual Array – Set mentése, a fájlkiszolgáló Azure-portálon
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Bevezetés
Ez a cikk azt ismerteti, hogyan hajtsa végre a kezdeti beállítás, Regisztráljon a StorSimple fájlkiszolgáló, az eszköz beállításának befejezése és a létrehozása és csatlakoztatása az SMB-megosztások. Ez az a teljes telepítéséhez a virtuális tömb egy fájlkiszolgáló vagy iSCSI-kiszolgáló szükséges az üzembehelyezési oktatóanyagok a sorozat utolsó cikket.

A beállítási és konfigurációs folyamat befejezéséhez is igénybe vehet körülbelül 10 percet. Ebben a cikkben található információk csak a központi telepítését a StorSimple Virtual Array vonatkozik. A StorSimple 8000 sorozatú eszközök központi telepítés nyissa meg: [2. frissítést futtató, a StorSimple 8000 sorozatú eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Telepítési előfeltételek
Mielőtt konfigurálja, és állítsa be a StorSimple Virtual Array, ellenőrizze, hogy:

* A virtuális tömb kiosztása és leírt módon csatlakozik a [kiépíteni a StorSimple Virtual Array a Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [üzembe helyezése VMware-ben a StorSimple Virtual Array](storsimple-virtual-array-deploy2-provision-vmware.md).
* A Szolgáltatásregisztrációs kulcsot a StorSimple-Eszközkezelő szolgáltatás kezelése a StorSimple Virtual Arrayt létrehozott rendelkezik. További információkért lásd: [2. lépés: A Szolgáltatásregisztrációs kulcs lekérése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) a StorSimple Virtual Arrayhez.
* Ha ez a második vagy virtuális tömb, amely egy meglévő StorSimple-Eszközkezelő szolgáltatással regisztrálja, a szolgáltatásadat-titkosítási kulcs kell. Ez a kulcs lett létrehozva, amikor az első eszköz sikeresen regisztrálva lett az ezt a szolgáltatást. Ha elvesztette ezt a kulcsot, lásd: [a szolgáltatásadat-titkosítási kulcs beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a StorSimple Virtual Array számára.

## <a name="step-by-step-setup"></a>Részletes beállítása
Az alábbi részletes útmutatás segítségével beállíthatja és konfigurálhatja a StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: Végezze el a helyi webes felhasználói felület beállítását és az eszköz regisztrálása
#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejezéséhez, és regisztrálja az eszközt
1. Nyisson meg egy böngészőablakot, és csatlakozzon a helyi webes felületén. Típus:
   
   `https://<ip-address of network interface>`
   
   Használja az előző lépésben feljegyzett kapcsolati URL-cím. Jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára hibaüzenet jelenik meg. Kattintson a **tovább a weblapra a**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Jelentkezzen be a webes felhasználói felületen, a virtuális tömb, **StorSimpleAdmin**. Adja meg az eszköz rendszergazdai jelszava módosított 3. lépés: A virtuális tömb indítása [kiépíteni a StorSimple Virtual Array a Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy a [üzembe helyezése VMware-ben a StorSimple Virtual Array](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Ekkor megnyílik a **kezdőlap** lapot. Ezen a lapon konfigurálja, és regisztrálja a virtuális tömböt a StorSimple-Eszközkezelő szolgáltatással szükséges különböző beállításokat ismerteti. A **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **eszközbeállítások** és **Felhőbeállítások**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Az a **hálózati beállítások** lap **hálózati adapterek**, DATA 0 automatikusan megtörténik az Ön számára. Mindegyik hálózati interfész értéke alapértelmezés szerint automatikusan az IP-cím beszerzése (DHCP). Ezért egy IP-cím, alhálózat és átjárókiszolgáló automatikusan (az IPv4 és IPv6) hozzárendelve.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Ha az eszköz kiépítése során hozzáadott több hálózati adapter, konfigurálhatja őket itt. Vegye figyelembe, hogy konfigurálni lehet a hálózati adapter IPv4, csak vagy IPv4- és IPv6-alapú. Csak IPv6-konfigurációk nem támogatottak.
5. DNS-kiszolgálókra szükség, mert, amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatók, vagy oldja meg az eszköz által használt nevét, amikor a fájlkiszolgálóként konfigurált. Az a **hálózati beállítások** lap a **DNS-kiszolgálók**:
   
   1. Egy elsődleges és másodlagos DNS-kiszolgáló automatikusan megtörténik. Ha statikus IP-címek konfigurálása választja, megadhatja a DNS-kiszolgálók. A magas rendelkezésre állás érdekében azt javasoljuk, hogy egy elsődleges és másodlagos DNS-kiszolgáló konfigurálása.
   2. Kattintson a **alkalmaz** a alkalmazni, és ellenőrizze a hálózati beállításokat.
6. Az a **eszközbeállítások** oldalon:
   
   1. Rendelje hozzá egy egyedi **neve** az eszközre. Ez a név hossza 1 – 15 karakter lehet, és betűvel, számokat és kötőjeleket tartalmazhat.
   2. Kattintson a **fájlkiszolgáló** ikon ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) a a **típus** eszköz, amely hoz létre. Lehetővé teszi a fájlkiszolgáló megosztott mappák létrehozását.
   3. Mivel az eszköz egy fájlkiszolgáló, szüksége lesz az eszköz csatlakoztatása a tartományhoz. Adjon meg egy **tartománynév**.
   4. Kattintson az **Alkalmaz** gombra.
7. Ekkor egy párbeszédpanel jelenik meg. A megadott formátumban adja meg a tartományi hitelesítő adatait. Kattintson a pipa ikonra. A tartományi hitelesítő adatok ellenőrzése. Akkor egy hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kattintson az **Alkalmaz** gombra. Ezzel alkalmazni, és az Eszközbeállítások ellenőrzése.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory, és nem a csoportházirend-objektumok (GPO) vannak, vagy az örökölt. A csoportházirend előfordulhat, hogy telepítse a StorSimple Virtual Array alkalmazások, például víruskereső szoftver. További szoftver telepítése nem támogatott, és adatok megsérüléséhez vezethet. 
   > 
   > 
9. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Az a **webalkalmazás-proxy** oldalon:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** a következő formátumban: *http://&lt;gazdagép IP-cím vagy teljes tartománynév&gt;: portszám*. Vegye figyelembe, hogy HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapszintű** vagy **None**.
   3. Hitelesítés használata esetén is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a konfigurált webes proxykiszolgáló beállításait a alkalmazni.
10. (Opcionális) beállításokat az idő az eszköz, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálót. Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Az a **időbeállítások** oldalon:
    
    1. A legördülő listából válassza ki a **időzóna** , amelyben az eszközre telepített földrajzi helye alapján. Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Nem kötelezően megadhatja a **másodlagos NTP-kiszolgáló** az eszközhöz.
    4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a beállított idő beállítások alkalmazásához.
11. Az eszköz a felhő beállításainak konfigurálása. Ebben a lépésben a helyi eszköz konfigurálásának befejezéséhez, és ezután regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatásban.
    
    1. Adja meg a **Szolgáltatásregisztrációs kulcs** kapott [2. lépés: A Szolgáltatásregisztrációs kulcs lekérése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) a StorSimple Virtual Arrayhez.
    2. Ha ez az első eszköz, a szolgáltatásban regisztrál, akkor megjelenik a **szolgáltatásadat-titkosítási kulcs**. Másolja ki a kulcsot, és mentse egy biztonságos helyre. Ez a kulcs megadása kötelező a szolgáltatás regisztrációs kulcsával együtt szeretne további eszközöket regisztrálni a StorSimple-Eszközkezelő szolgáltatásban. 
       
       Ha ez nem az első eszköz, amely a szolgáltatásban regisztrál, szüksége lesz a szolgáltatásadat-titkosítási kulcsot adjon meg. További információkért tekintse meg beolvasni a [szolgáltatásadat-titkosítási kulcs](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a helyi webes felhasználói Felületét.
    3. Kattintson a **regisztrálása**. A művelet újraindítja az eszközt. Szükség lehet várjon 2-3 percet, mielőtt az eszköz regisztrálása sikeres volt. Az eszköz újraindulása után megnyílik a bejelentkezési lapot.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Lépjen vissza az Azure Portalra. Lépjen a **összes erőforrás**, keresse meg a StorSimple-Eszközkezelő szolgáltatásban.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. A szűrt listában válassza ki a StorSimple-Eszközkezelő szolgáltatást, és navigáljon arra **felügyeleti > eszközök**. Az a **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott a szolgáltatáshoz, és az állapota **beállításra kész**.
    
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2. lépés: Az eszköz fájlkiszolgáló konfigurálása
Hajtsa végre a következő lépéseket a [az Azure portal](https://portal.azure.com/) a szükséges eszköz beállításának befejezéséhez.

#### <a name="to-configure-the-device-as-file-server"></a>Az eszköz fájlkiszolgáló konfigurálása
1. Nyissa meg a StorSimple-Eszközkezelő szolgáltatás, és folytassa a **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki az imént eszköz létrehozva. Ez az eszköz jelennek meg **beállításra kész**.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Válassza ki az eszközt, és megjelenik egy szalagcím üzenet arról, hogy a telepítő készen áll-e az eszközön.
   
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kattintson a **konfigurálása** a parancssávon. Megnyílik a **konfigurálása** panelen. Az a **konfigurálása** panelen tegye a következőket:
   
    1. A fájlkiszolgáló nevének a rendszer automatikusan kitölti.
    
    2. Ellenőrizze, hogy a felhős társzolgáltatás titkosítása értéke **engedélyezve**. Ez titkosítja az adatokat a felhőbe küldött. 
    
    3. Egy 256 bites AES-kulcsot a felhasználó által definiált titkosítási kulcsot használnak. Adjon meg egy 32 karakterből álló kulcsot, és győződjön meg arról, hogy a kulcs majd egyszer. Jegyezze fel a kulcs a későbbiekben egy kulcskezelő alkalmazásban.
    
    4. Kattintson a **kötelező beállítások konfigurálása** , adja meg a tárfiók hitelesítő adatait az eszköz használható. Kattintson a **új hozzáadása** nincs tárfiók hitelesítő adatai konfigurálva vannak. **Győződjön meg arról, hogy a tárfiók támogatja a blokkblobokat használja. Nem támogatja a lapblobokat.** További információ a [letiltja a blobok és lapblobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Az a **adja hozzá a tárfiók hitelesítő adatainak** panelen tegye a következőket: 

    1. Válassza az aktuális előfizetést, ha a tárfiók ugyanahhoz az előfizetéshez tartozik, mint a szolgáltatás. Adja meg a másik pedig a tárolási fiók a szolgáltatási előfizetés kívül esik. 
    
    2. A legördülő listából válassza ki egy meglévő tárfiókot. 
    
    3. A hely automatikusan kitöltődnek a megadott tárfiók alapján. 
    
    4. Győződjön meg, hogy az eszköz és a felhő közötti, egy biztonságos hálózati kommunikációs csatorna SSL engedélyezése.
    
    5. Kattintson a **Hozzáadás** a tárfiók-hitelesítő adat hozzáadása. 
   
        ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Ha a tárfiók-hitelesítő adat sikeresen létrejött, a **konfigurálása** panel frissülni fog a megadott tárfiók hitelesítő adatainak megjelenítéséhez. Kattintson a **Configure** (Konfigurálás) elemre.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Látni fogja, amely egy fájl kiszolgáló létrehozása folyamatban van. Ha a fájlkiszolgáló sikeresen létrejött, értesítést kap.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Az eszköz állapotát is változik **Online**.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Adjon hozzá egy megosztást lépne.

## <a name="step-3-add-a-share"></a>3. lépés: Megosztás hozzáadása
Megosztás létrehozásához hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Megosztás létrehozása
1. Válassza ki a fájl kiszolgáló eszközt az előző lépésben konfigurált, és kattintson a **...**  (vagy kattintson a jobb gombbal). A helyi menüben válassza ki a **Hozzáadás megosztás**. Másik lehetőségként kattinthat **+ fájlmegosztás hozzáadása** az eszköz parancssávon.
   
   ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Adja meg a következő beállításokat:

    1. A megosztás egyedi nevét. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
    
    2. Egy nem kötelező **leírás** a megosztáshoz. A leírás segítségével azonosíthatja a fájlmegosztás-tulajdonosok.
    
    3. A **típus** a megosztáshoz. A típus lehet **rétegzett** vagy **helyileg rögzített**, a rétegzett folyamatban van az alapértelmezett. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterhelésekhez, válassza ki a **helyileg rögzített** megosztani. Minden más adathoz válasszon egy **rétegzett** megosztani.
    Egy helyileg rögzített megosztási kiosztása, és biztosítja, hogy az elsődleges adatokat a megosztáson tárolódnak az eszközön, és nem kerülnek a felhőbe. A rétegzett megosztás másrészről kiosztása. A rétegzett megosztás létrehozásakor 10 %-a helyet a helyi rétegen van kiépítve, és 90 %-a terület a felhőben van kiépítve. Például ha Ön egy 1 TB-os kötetet, 100 GB-os helyi területet lenne tartalmazhat, és a felhőben használni kívánt 900 GB amikor az adat szintet. Ez pedig azt jelenti, hogy ha kifogy a helyi terület az eszközön, nem használhatók a rétegzett megosztás.
   
    4. Az a **teljes körű engedélyekkel alapértelmezett beállítása** mezőben az engedélyek hozzárendelése a felhasználó vagy a csoport, amely a megosztás fér hozzá. Adja meg a felhasználó vagy a felhasználói csoport nevét *john@contoso.com* formátumban. Azt javasoljuk, hogy egy felhasználói csoportot (és nem egyetlen felhasználó) használatával engedélyezze a rendszergazdai jogosultsággal a ezeket a megosztásokat. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
   
    5. Kattintson a **Hozzáadás** a megosztás létrehozásához. 
    
        ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van.
   
        ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    A megadott beállításokkal a megosztás létrehozása után a **megosztások** panel frissül, hogy az új megosztásban. Alapértelmezés szerint figyelése és a biztonsági mentés engedélyezve van a megosztáshoz.
   
    ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4. lépés: Csatlakozás a megosztáshoz
Most kell csatlakozni az előző lépésben létrehozott egy vagy több megosztást. Hajtsa végre ezeket a lépéseket a Windows Server-gazdagép csatlakozik a StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>A megosztáshoz való csatlakozáshoz
1. Nyomja meg ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Adja meg a Futtatás ablakba a *&#92; &#92; &lt;fájlkiszolgáló neve&gt;* útvonalaként, és cserélje le *fájlkiszolgáló neve* az az eszköz nevét, amelyet a fájlhoz rendelt a kiszolgáló. Kattintson az **OK** gombra.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Megnyílik a fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. Válasszon ki és duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Most fájlokat adjon hozzá ezeket a megosztásokat, és készítsen biztonsági mentést.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használhatja a helyi webes felhasználói Felületét a [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

