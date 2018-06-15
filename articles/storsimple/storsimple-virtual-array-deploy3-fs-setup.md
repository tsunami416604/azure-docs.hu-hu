---
title: Állítsa be a StorSimple virtuális tömb fájlkiszolgálóként |} Microsoft Docs
description: A StorSimple virtuális tömb telepítési harmadik oktatóanyag arra utasítja, hogy a fájlkiszolgáló virtuális eszköz beállítása.
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
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927823"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Központi telepítése a StorSimple virtuális tömb - beállítva fel fájlkiszolgálóként Azure-portálon
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Bevezetés
Ez a cikk ismerteti, hogyan hajtsa végre a kezdeti beállítás regisztrálása a StorSimple fájlkiszolgáló, az eszköz beállításának befejezése és hozzon létre és SMB-megosztások csatlakozni. Ez a teljesen telepítsen az adott virtuális tömb, egy fájl vagy iSCSI-kiszolgáló szükséges az üzembehelyezési oktatóanyagok az adatsorozat utolsó cikkében.

A beállítás és konfiguráció folyamat körülbelül 10 percig tarthat befejezéséhez. A cikkben szereplő információk csak a központi telepítés a StorSimple virtuális tömb vonatkozik. A StorSimple 8000 sorozat eszközeire telepítéshez Ugrás: [a StorSimple 8000 series eszköz 2. frissítést futtató üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Telepítési előfeltételek
Előtt konfigurálja, és állítsa be a StorSimple virtuális tömb, győződjön meg arról, hogy:

* Egy virtuális tömb kiosztása és a hozzá kapcsolódó a [kiépíteni a StorSimple virtuális tömb a Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [kiépíteni a StorSimple virtuális tömb VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md).
* Hogy a szolgáltatás regisztrációs kulcsának a StorSimple virtuális tömbök kezeléséhez létrehozott StorSimple Device Manager szolgáltatásból. További információkért lásd: [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) a StorSimple virtuális tömb.
* Ha ez a második vagy virtuális tömb, amely egy meglévő StorSimple Device Manager szolgáltatással regisztrál, a szolgáltatásadat-titkosítási kulcs kell rendelkeznie. Ezt a kulcsot hozott létre, ha a szolgáltatás sikeresen regisztrálva lett az első eszköz. Ha elvesztette ezt a kulcsot, lásd: [a szolgáltatásadat-titkosítási kulcs beszerzése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a StorSimple virtuális tömbhöz.

## <a name="step-by-step-setup"></a>Részletes beállítása
Az alábbi részletes útmutatás segítségével beállítása és konfigurálása a StorSimple virtuális tömbjét.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: A helyi webes felhasználói felület beállításának befejezése, és regisztrálja az eszközt
#### <a name="to-complete-the-setup-and-register-the-device"></a>A telepítés befejeződését, és regisztrálja az eszközt
1. Nyisson meg egy böngészőablakot, és csatlakozzon a helyi webes felhasználói felületen. Típus:
   
   `https://<ip-address of network interface>`
   
   A kapcsolat az előző lépésben feljegyzett URL-címet használja. Arról, hogy a webhely biztonsági tanúsítványával kapcsolatos problémára hibaüzenet jelenik meg. Kattintson a **tovább a weblap a**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Jelentkezzen be a webes felhasználói felület, a virtuális tömb, **StorSimpleAdmin**. Adja meg az eszköz rendszergazdai jelszava, amely módosította a 3. lépés: Indítsa el a virtuális tömb [kiépíteni a StorSimple virtuális tömb a Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [kiépíteni a StorSimple virtuális tömb VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. A következő lépés a **Home** lap. Ez a lap ismerteti a különböző beállításokat konfigurálja, és a virtuális tömb regisztrálása a StorSimple Device Manager szolgáltatásban kell. A **hálózati beállításai**, **webalkalmazás-proxy beállításainak**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **eszközbeállítások** és **Felhőbeállítások**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Az a **hálózati beállításai** lapon az **hálózati illesztőt**, DATA 0 automatikusan megtörténik az Ön. Mindegyik hálózati interfész értéke alapértelmezés szerint automatikusan tud IP-címet (DHCP). Emiatt egy IP-cím, alhálózati és átjáró automatikusan rendelt (az IPv4 és IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Ha az eszköz kiépítése során hozzáadott egynél több hálózati adapter, konfigurálhatja őket itt. Vegye figyelembe, hogy beállíthat a hálózati adapter IPv4-alapú csak vagy mint IPv4 és IPv6. Csak IPv6-konfigurációk nem támogatottak.
5. DNS-kiszolgálók szükség, mert azok amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatók, vagy hárítsa el az eszköz által használt nevét, ha a fájl konfigurálva. Az a **hálózati beállításai** lapon az a **DNS-kiszolgálók**:
   
   1. Egy elsődleges és másodlagos DNS-kiszolgáló automatikusan megtörténik. Ha statikus IP-címek konfigurálása mellett dönt, a DNS-kiszolgálót is megadhat. A magas rendelkezésre állás érdekében azt javasoljuk, hogy egy elsődleges és másodlagos DNS-kiszolgáló konfigurálása.
   2. Kattintson a **alkalmaz** alkalmazásához, és érvényesítse a hálózati beállításokat.
6. Az a **eszközbeállítások** lap:
   
   1. Rendeljen egy egyedi **neve** az eszközre. Ez a név 1 – 15 karakterből állhat, és betűvel, számokat és kötőjeleket tartalmazhat.
   2. Kattintson a **fájlkiszolgáló** ikon ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) a a **típus** eszköz létrehozása. Egy fájlkiszolgáló lehetővé teszi a megosztott mappák létrehozása.
   3. Mivel az eszköz egy fájlkiszolgálón, szüksége lesz az eszköz csatlakoztatása a tartományhoz. Adjon meg egy **tartománynév**.
   4. Kattintson az **Alkalmaz** gombra.
7. Megjelenik egy párbeszédpanel. A megadott formátumban adja meg a tartományi hitelesítő adatokat. Kattintson a pipa ikonra. A tartományi hitelesítő adatok ellenőrzése. Egy hibaüzenetet lát, ha a hitelesítő adatok helytelenek.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kattintson az **Alkalmaz** gombra. Ez alkalmazza, és az eszközök beállításainak ellenőrzéséhez.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) az Active Directory és a nem a csoportházirend-objektumok (GPO) vannak alkalmazva vagy örökölt. Csoportházirend alkalmazások, például víruskereső szoftver is telepíthető a StorSimple virtuális tömb. További szoftverek telepítése nem támogatott, és előfordulhat, hogy a program sérült adatokat. 
   > 
   > 
9. A webes proxykiszolgáló (opcionális) konfigurálása. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Az a **webalkalmazás-proxy** lap:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** ebben a formátumban: *http://&lt;állomás IP-cím vagy teljes Tartománynevet&gt;: portszámát*. Vegye figyelembe, hogy HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapvető** vagy **nincs**.
   3. Ha használ hitelesítést, akkor is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson az **Alkalmaz** gombra. A érvényesítése és a konfigurált webes proxykiszolgáló beállításait alkalmazza.
10. (Opcionális) időbeállításait az eszközét, például az időzóna és az elsődleges és másodlagos NTP-kiszolgálók. NTP-kiszolgáló szükség, mert az eszköz kell próbálta szinkronizálni az időt, hogy azt a felhőszolgáltatók hitelesíthető.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Az a **időbeállítások** lap:
    
    1. A legördülő listából válassza ki a **időzóna** , amelyben az eszköz üzembe helyezésének földrajzi helye alapján. Az eszköz az alapértelmezett időzónát a csendes-óceáni TÉLI. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Megadhat egy **másodlagos NTP-kiszolgáló** az eszközhöz.
    4. Kattintson az **Alkalmaz** gombra. A érvényesítése és a konfigurált beállításokat alkalmazni.
11. Az eszköz a felhő beállításainak konfigurálása. Ebben a lépésben a helyi eszköz konfigurálásának befejezése, és regisztrálja az eszközt a StorSimple eszköz Manager szolgáltatásban.
    
    1. Adja meg a **Szolgáltatásregisztrációs kulcs** portáltól [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) a StorSimple virtuális tömb.
    2. Ha ez az első eszköz regisztrálása a ezt a szolgáltatást, akkor számára jelenik meg a **szolgáltatásadat-titkosítási kulcs**. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. Ezt a kulcsot meg kell adni a szolgáltatás regisztrációs kulcsot, további eszközök regisztrálása a StorSimple Device Manager szolgáltatásban. 
       
       Ha nem ez az első eszköz, amely ezzel a szolgáltatással regisztrál, akkor adja meg a szolgáltatásadat-titkosítási kulcs. További információkért tekintse meg az beszerzése a [szolgáltatásadat-titkosítási kulcs](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a helyi webes felhasználói felülete.
    3. Kattintson a **regisztrálása**. Ez az eszköz újraindul. Szükség lehet az eszköz regisztrálása sikeres volt 2-3 percet várni. Az eszköz újraindítása után megnyílik a bejelentkezési oldalon.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Térjen vissza az Azure-portálon. Nyissa meg a **összes erőforrás**, keressen rá a StorSimple eszköz Manager szolgáltatást.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. A szűrt listában válassza ki a StorSimple Device Manager szolgáltatást, és navigáljon arra **felügyeleti > eszközök**. Az a **eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott a szolgáltatáshoz, és állapota **már beállíthat**.
    
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2. lépés: Konfigurálja úgy az eszközt, fájlkiszolgáló
Hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com/) a szükséges eszköz beállításának befejezéséhez.

#### <a name="to-configure-the-device-as-file-server"></a>Az eszköz fájlkiszolgáló konfigurálása
1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és folytassa a **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki az eszköz nemrég létrehozott. Ez az eszköz akkor jelenik meg **már beállíthat**.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet arról, hogy a telepítő készen áll-e az eszközön.
   
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kattintson a **konfigurálása** a parancssávon. Ezzel megnyílik a **konfigurálása** panelen. Az a **konfigurálása** panelen tegye a következőket:
   
    1. A fájlkiszolgáló nevéhez automatikusan feltöltődik értékkel.
    
    2. Győződjön meg arról, hogy a felhőalapú tárolás titkosításának értéke **engedélyezve**. Ez titkosítja a felhőbe küldött összes adatot. 
    
    3. A 256 bites AES kulccsal a titkosításhoz a felhasználó által definiált kulccsal. Adjon meg egy 32 karakter hosszúságú kulcs, és majd még egyszer a kulcsot a megerősítéshez. Jegyezze fel a kulcs a jövőben kulcskezelés alkalmazásban.
    
    4. Kattintson a **kötelező beállítások konfigurálása** adhatja meg a tárfiók hitelesítő adatait az eszköz használható. Kattintson a **új hozzáadása** nincs tárfiók hitelesítő adatainak konfigurálva vannak. **Győződjön meg arról, hogy a tárfiók támogatja a blokkblobok használata. Nem támogatja a lapblobokat.** További információ a [blokkolja a blobokat és lapblobokat](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Az a **adja hozzá a tárfiók hitelesítő adatainak** panelen tegye a következőket: 

    1. A jelenlegi előfizetés válassza, ha a tárfiók ugyanahhoz az előfizetéshez a szolgáltatást. Adja meg a másik pedig a tárhely fiókot a szolgáltatás előfizetésével kívül esik. 
    
    2. A legördülő listából válassza ki a meglévő tárfiókot. 
    
    3. A hely adatok automatikusan kitöltődnek a megadott tárfiók alapján. 
    
    4. Ahhoz, hogy a biztonságos hálózati kommunikációs csatornát az eszköz és a felhő közötti SSL engedélyezése.
    
    5. Kattintson a **Hozzáadás** hozzáadása a tárolási fiók hitelesítő adatait. 
   
        ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Ha a tárolási fiók hitelesítő adatait sikeresen létrejött, a **konfigurálása** panel frissülni fog a megadott tárfiók hitelesítő adatainak megjelenítéséhez. Kattintson a **Configure** (Konfigurálás) elemre.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Megjelenik egy, a fájl kiszolgáló létrehozása folyamatban van. Ha a fájlkiszolgáló sikeresen létrejött, értesítést fog kapni.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Az eszköz állapotát is módosul, **Online**.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Adjon hozzá egy megosztást lépne.

## <a name="step-3-add-a-share"></a>3. lépés: A Megosztás hozzáadása
Hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com/) megosztás létrehozásához.

#### <a name="to-create-a-share"></a>Megosztás létrehozása
1. Válassza ki a fájl server eszközt, az előző lépésben konfigurált, és kattintson a **...**  (vagy kattintson a jobb gombbal). Válassza ki a helyi menü **Hozzáadás megosztás**. Másik lehetőségként kattinthat **+ adja hozzá a megosztás** eszköz parancssávon.
   
   ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Adja meg a következő beállításokat:

    1. Egy egyedi nevet a megosztáshoz. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
    
    2. Egy nem kötelező **leírás** a megosztáshoz. A leírás segít azonosítani a fájlmegosztás-tulajdonosok.
    
    3. A **típus** a megosztáshoz. A típus lehet **rétegzett** vagy **helyileg rögzített**, a rétegzett az alapértelmezett alatt. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterhelésekhez, válassza ki a **helyileg rögzített** megosztani. Minden más adathoz válasszon egy **rétegzett** megosztani.
    A helyileg rögzített megosztás kiosztása, és biztosítja, hogy a megosztás elsődleges adatai helyben az eszközre, és nem kerülnek a felhőbe. A rétegzett megosztás viszont kiosztása. Amikor létrehoz egy rétegzett megosztást, 10 %-tér ki van építve a helyi rétegen, és 90 %-tér ki van építve a felhőben. Például ha 1 TB-os kötet létesített, 100 GB kellene lennie, a helyi terület és 900 GB használni a felhőben során az adatok szinteket. Ez viszont azt jelenti, hogy ha elfogy a helyi terület az eszközön, nem használhatók egy rétegzett megosztást.
   
    4. Az a **értékre alapértelmezett teljes körű engedélyekkel** mezőben, az engedélyek hozzárendelése a felhasználó vagy a csoportot, amelyhez ez a megosztás fér hozzá. Adja meg a felhasználó vagy a felhasználói csoport nevét  *john@contoso.com*  formátumban. Azt javasoljuk, hogy egy felhasználói csoport (helyett egy-egy felhasználóhoz) használatával biztosíthatja a rendszergazdai jogosultsággal a megosztást. Miután hozzárendelt itt az engedélyeket, majd segítségével Fájlkezelőben módosítani ezeket az engedélyeket.
   
    5. Kattintson a **Hozzáadás** a megosztás létrehozásához. 
    
        ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Értesítést kap, hogy a fájlmegosztás létrehozása folyamatban van.
   
        ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    A megadott beállításokkal a megosztás létrehozása után a **megosztások** tükrözzék az új megosztás frissíteni fogja a panelt. Alapértelmezés szerint figyelése és a biztonsági mentés engedélyezve vannak a megosztáshoz.
   
    ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4. lépés: A megosztáshoz való csatlakozáshoz
Most egy vagy több megosztást, amelyet az előző lépésben hozott létre csatlakozni kell. Hajtsa végre ezeket a lépéseket a Windows Server-állomáson, a StorSimple virtuális tömb csatlakozik.

#### <a name="to-connect-to-the-share"></a>A megosztáshoz való csatlakozáshoz
1. Nyomja le az ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. A Futtatás ablakba, adja meg a *&#92; &#92;&lt; a fájlkiszolgáló nevének&gt;*  a elérési útjaként cseréje *fájlkiszolgáló nevéhez* , hogy a fájlkiszolgáló hozzárendelve eszköz nevű. Kattintson az **OK** gombra.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Ekkor megnyílik a Fájlkezelőben fel. Most kell látni a megosztásokat létrehozott mappaként. Válassza ki, és kattintson duplán a megosztást (mappa) a tartalom megtekintését.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Most fájlok hozzáadása a megosztást, és készítsen biztonsági mentést.

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan használhatja a helyi webes felhasználói felülete a [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

