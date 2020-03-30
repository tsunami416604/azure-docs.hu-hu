---
title: StorSimple virtuális tömb beállítása fájlkiszolgálóként | Microsoft dokumentumok
description: Ez a harmadik oktatóanyag a StorSimple virtuális tömb központi telepítésében arra utasítja, hogy hozzon létre egy virtuális eszközt fájlkiszolgálóként.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297651"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple virtuális tömb telepítése – Fájlkiszolgálóként beállítás az Azure Portalon keresztül
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Bevezetés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez a cikk bemutatja, hogyan hajthatja végre a kezdeti telepítést, regisztrálhatja a StorSimple fájlkiszolgálót, hogyan végezheti el az eszköz telepítését, és hogyan hozhat létre és csatlakozhat Az SMB-megosztásokhoz. Ez az utolsó cikk a telepítési oktatóanyagok sorozatában, amely a virtuális tömb fájlkiszolgálóként vagy iSCSI-kiszolgálóként való teljes telepítéséhez szükséges.

A beállítási és konfigurációs folyamat körülbelül 10 percet is igénybe vehet. A cikkben szereplő információk csak a StorSimple virtuális tömb központi telepítésére vonatkoznak. A StorSimple 8000 sorozatú eszközök telepítéséhez válassza a [következő](storsimple-deployment-walkthrough-u2.md)ta-

## <a name="setup-prerequisites"></a>Telepítési előfeltételek
A StorSimple virtuális tömb konfigurálása és beállítása előtt győződjön meg arról, hogy:

* Kiépített egy virtuális tömböt, és a [Hyper-V StorSimple virtuális tömb kiépítésében](storsimple-virtual-array-deploy2-provision-hyperv.md) vagy [a VMware-ben egy StorSimple virtuális tömb kiépítése](storsimple-virtual-array-deploy2-provision-vmware.md)című témakörben részletezett módon csatlakozott hozzá.
* A StorSimple virtuális tömbök kezeléséhez létrehozott StorSimple Eszközkezelő szolgáltatás szolgáltatásregisztrációs kulcsa rendelkezik. További információ: 2. lépés: A StorSimple virtuális tömb [szolgáltatásregisztrációs kulcsának beszereznie.](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)
* Ha ez a második vagy azt követő virtuális tömb, amelyet egy meglévő StorSimple Eszközkezelő szolgáltatással regisztrál, rendelkeznie kell a szolgáltatás adattitkosítási kulccsal. Ez a kulcs akkor jött létre, amikor az első eszköz sikeresen regisztrálva lett ezzel a szolgáltatással. Ha elvesztette ezt a kulcsot, [olvassa el a Szolgáltatás adattitkosítási kulcsának beszerezhetése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) a StorSimple virtuális tömbhöz című témakört.

## <a name="step-by-step-setup"></a>Részletes beállítás
A StorSimple virtuális tömb beállításához és konfigurálásához kövesse az alábbi lépésenkénti utasításokat.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1. lépés: A helyi webes felhasználói felület beállításának befejezése és az eszköz regisztrálása
#### <a name="to-complete-the-setup-and-register-the-device"></a>A beállítás befejezéséhez és az eszköz regisztrálásához
1. Nyisson meg egy böngészőablakot, és csatlakozzon a helyi webes felhasználói felülethez. Típus:
   
   `https://<ip-address of network interface>`
   
   Használja az előző lépésben feljegyzett kapcsolat URL-címét. Hibaüzenet jelenik meg, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. Kattintson **a Folytatás erre a weblapra gombra.**
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Jelentkezzen be a virtuális tömb webes felhasználói felületére **StorSimpleAdmin**néven. Adja meg a [3.](storsimple-virtual-array-deploy2-provision-vmware.md) [Provision a StorSimple Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. A **kezdőlapra** kerül. Ez a lap a virtuális tömb storSimple Eszközkezelő szolgáltatással történő konfigurálásához és regisztrálásához szükséges különböző beállításokat ismerteti. A **Hálózati beállítások**, a **Webproxy-beállítások**és **az Idő beállítások** megadása nem kötelező. Az egyetlen szükséges beállítás az **Eszközbeállítások** és a **Felhőbeállítások.**
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. A **Hálózati beállítások** lap **Hálózati csatolók**területén a DATA 0 automatikusan konfigurálva lesz. Alapértelmezés szerint minden hálózati adapter automatikusan megkapja az IP-címet (DHCP). Ezért a rendszer automatikusan hozzárendeli az IP-címet, az alhálózatot és az átjárót (mind az IPv4, mind az IPv6 protokollhoz).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Ha az eszköz kiépítése során egynél több hálózati adaptert adott hozzá, itt konfigurálhatja őket. Megjegyzés: a hálózati adapter t csak IPv4-ként, illetve IPv4- és IPv6-ként is konfigurálhatja. Csak iPv6-konfigurációk nem támogatottak.
5. Dns-kiszolgálókra azért van szükség, mert akkor használatosak, amikor az eszköz megpróbál kommunikálni a felhőalapú tárhelyszolgáltatóival, vagy név szerint feloldani az eszközt, ha fájlkiszolgálóként van konfigurálva. A **DNS-kiszolgálók**alatti **Hálózati beállítások** lapon:
   
   1. A rendszer automatikusan konfigurálja az elsődleges és másodlagos DNS-kiszolgálót. Ha statikus IP-címek konfigurálását választja, megadhat DNS-kiszolgálókat. A magas rendelkezésre állás érdekében azt javasoljuk, hogy konfiguráljon egy elsődleges és egy másodlagos DNS-kiszolgálót.
   2. Kattintson az **Alkalmaz** gombra a hálózati beállítások alkalmazásához és érvényesítéséhez.
6. Az **Eszközbeállítások** lapon:
   
   1. Rendeljen egyedi **nevet** az eszközhöz. Ez a név 1-15 karakter lehet, és tartalmazhat betűket, számokat és kötőjeleket.
   2. Kattintson a ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) létrehozásra szánt **eszköztípus** **Fájlkiszolgáló** ikonjára. A fájlkiszolgáló lehetővé teszi megosztott mappák létrehozását.
   3. Mivel az eszköz fájlkiszolgáló, csatlakoztatnia kell az eszközt egy tartományhoz. Írjon be **egy tartománynevet**.
   4. Kattintson az **Alkalmaz** gombra.
7. Megjelenik egy párbeszédpanel. Adja meg a tartomány hitelesítő adatait a megadott formátumban. Kattintson az ellenőrzés ikonra. A rendszer ellenőrzi a tartományi hitelesítő adatokat. Hibaüzenet jelenik meg, ha a hitelesítő adatok helytelenek.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kattintson az **Alkalmaz** gombra. Ez érvényes és érvényesíti az eszköz beállításait.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) található az Active Directory számára, és nincs csoportházirend-objektum a rendszerre, illetve nem örököl. A csoportházirend alkalmazásokat, például víruskereső szoftvereket telepíthet a StorSimple virtuális tömbre. További szoftverek telepítése nem támogatott, és adatsérüléshez vezethet. 
   > 
   > 
9. (Opcionálisan) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   A **webproxy** lapon:
   
   1. Adja meg a **webproxy URL-címét** a következő formátumban: *&lt;http:// állomás-IP-cím vagy fqdn&gt;:Port szám*. Ne feledje, hogy a HTTPS URL-címek nem támogatottak.
   2. Adja meg **a hitelesítést** **alapszintűként** vagy **Nincsként.**
   3. Hitelesítés használata esetén **felhasználónevet** és jelszót is meg kell **adnia.**
   4. Kattintson az **Alkalmaz** gombra. Ez ellenőrzi és alkalmazza a beállított webproxy-beállításokat.
10. (Opcionálisan) konfigurálja az eszköz időbeállításait, például az időzónát, valamint az elsődleges és másodlagos NTP-kiszolgálókat. NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt, hogy hitelesíthesse magát a felhőszolgáltatókkal.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Az **Időbeállítások** lapon:
    
    1. A legördülő listából válassza ki az **időzónát** az eszköz üzembe helyezése földrajzi helye alapján. A készülék alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adjon meg **egy elsődleges NTP-kiszolgálót** az eszközhöz, vagy fogadja el az alapértelmezett time.windows.com értéket. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Tetszés szerint adjon meg **egy másodlagos NTP-kiszolgálót** az eszközhöz.
    4. Kattintson az **Alkalmaz** gombra. Ez ellenőrzi és alkalmazza a beállított időbeállításokat.
11. Konfigurálja az eszköz felhőbeállításait. Ebben a lépésben befejezi a helyi eszközkonfigurációt, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatással.
    
    1. Adja meg a **szolgáltatás regisztrációs kulcs,** hogy van a [2.](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)
    2. Ha ez az első eszköz regisztrációezzel a szolgáltatással, akkor megjelenik a **Szolgáltatás adattitkosítási kulcsa.** Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. Ez a kulcs szükséges a szolgáltatás regisztrációs kulcsregisztrálásához további eszközök a StorSimple Eszközkezelő szolgáltatás regisztrálásához. 
       
       Ha nem ez az első eszköz, amelyet ezzel a szolgáltatással regisztrál, meg kell adnia a szolgáltatás adattitkosítási kulcsát. További információkért olvassa el a [szolgáltatás adattitkosítási kulcsának](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) beolvassa a helyi webes felhasználói felületen.
    3. Kattintson a **Regisztráció gombra.** Ez újraindítja az eszközt. Előfordulhat, hogy 2-3 percet kell várnia, mielőtt az eszköz sikeresen regisztrálva van. Az eszköz újraindítása után a bejelentkezési oldalra kerül.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Lépjen vissza az Azure Portalra. Nyissa meg **a Minden erőforrás**lehetőséget, keresse meg a StorSimple Eszközkezelő szolgáltatást.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. A szűrt listában jelölje ki a StorSimple Eszközkezelő szolgáltatást, majd keresse meg **a Management > Devices**( Felügyeleti > eszközök ) lapot. Az **Eszközök** panelen ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz, és készen áll a **beállításra.**
    
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2. lépés: Az eszköz konfigurálása fájlkiszolgálóként
Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/) a szükséges eszközbeállítás végrehajtásához.

#### <a name="to-configure-the-device-as-file-server"></a>Az eszköz konfigurálása fájlkiszolgálóként
1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, majd a **Management > Devices**lehetőséget. Az **Eszközök** panelen válassza ki az imént létrehozott eszközt. Ez az eszköz **készen áll a beállításra.**
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kattintson az eszközre, és megjelenik egy szalagcím üzenet, amely jelzi, hogy az eszköz készen áll a beállításra.
   
    ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kattintson a parancssáv **Konfigurálás** gombjára. Ez **megnyitja** a Configure panelt. A **Konfigurálás** panelen tegye a következőket:
   
   1. A fájlkiszolgáló neve automatikusan kitöltődik.
    
   2. Győződjön meg arról, hogy a felhőalapú tároló titkosítása **Engedélyezve**van. Ez titkosítja a felhőbe küldött összes adatot. 
    
   3. A 256 bites AES-kulcs a felhasználó által definiált titkosítási kulccsal együtt használatos. Adjon meg egy 32 karakteres kulcsot, majd írja be újra a kulcsot a fájl megerősítéséhez. Rögzítse a kulcsot egy kulcskezelő alkalmazásban későbbi használatra.
    
   4. Kattintson **a Szükséges beállítások konfigurálása** elemre az eszközhöz használandó tárfiók hitelesítő adatainak megadásához. Kattintson **az Új hozzáadása** gombra, ha nincs konfigurálva tárfiók hitelesítő adatai. **Győződjön meg arról, hogy a használt tárfiók támogatja a blokkblobokat. A lapblobok nem támogatottak.** További információ a [blokkok blobok és a lap blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. A **Tárfiók hitelesítő adatainak hozzáadása** panelen tegye a következőket: 

    1. Válassza ki az aktuális előfizetést, ha a tárfiók ugyanabban az előfizetésben van, mint a szolgáltatás. Adja meg a másik, hogy a tárfiók kívül esik a szolgáltatás-előfizetésen. 
    
    2. A legördülő listából válasszon egy meglévő tárfiókot. 
    
    3. A hely automatikusan kitöltődik a megadott tárfiók alapján. 
    
    4. Engedélyezze a TLS-t, hogy biztonságos hálózati kommunikációs csatornát biztosítson az eszköz és a felhő között.
    
    5. Kattintson **a Hozzáadás** gombra a tárfiók hitelesítő adatainak hozzáadásához. 
   
        ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. A tárfiók hitelesítő adatainak sikeres létrehozása után a **Konfigurálás** panel frissül a megadott tárfiók hitelesítő adatainak megjelenítéséhez. Kattintson a **Configure** (Konfigurálás) elemre.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Látni fogja, hogy egy fájlkiszolgáló jön létre. A fájlkiszolgáló sikeres létrehozása után értesítést kap.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Az eszköz állapota online **állapotra**is változik.
   
   ![Fájlkiszolgáló konfigurálása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Folytathatja a megosztás hozzáadását.

## <a name="step-3-add-a-share"></a>3. lépés: Megosztás hozzáadása
Megosztás létrehozásához hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Megosztás létrehozása
1. Jelölje ki az előző lépésben konfigurált fájlkiszolgáló-eszközt, és kattintson **a ...** (vagy a jobb gombbal) elemre. A helyi menüben válassza a **Megosztás hozzáadása parancsot.** Másik lehetőségként kattintson a **+ Megosztás hozzáadása** az eszköz parancssávjára.
   
   ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Adja meg a következő megosztási beállításokat:

   1. A megosztás egyedi neve. A névnek 3–127 karakterből legtartalmazott karakterláncnak kell lennie.
    
   2. A megosztás nem kötelező **leírása.** A leírás segít azonosítani a részvénytulajdonosokat.
    
   3. A megosztás **típusa.** A típus lehet **rétegzett** vagy **helyileg rögzített,** a rétegzett az alapértelmezett. A helyi garanciákat, alacsony késéseket és nagyobb teljesítményt igénylő számítási feladatokhoz válasszon egy **helyileg rögzített megosztást.** Az összes többi adathoz válasszon egy **rétegzett megosztást.**
      A helyileg rögzített megosztás vastagon van kiépítve, és biztosítja, hogy a megosztáson lévő elsődleges adatok helyi ek maradnak az eszközön, és ne terjedjenek ki a felhőbe. A többszintű megosztás viszont vékonyan ki van építve. Amikor többszintű megosztást hoz létre, a terület 10%-a a helyi szinten van kiépítve, és a terület 90%-a a felhőben van kiépítve. Például ha egy 1 TB-os kötetet, 100 GB-os a helyi térben található, és 900 GB-ot kell használni a felhőben, amikor az adatrétegek. Ez viszont azt jelenti, hogy ha elfogy az összes helyi terület az eszközön, nem lehet kiépíteni egy rétegzett megosztás.
   
   4. A **Beállítás alapértelmezett teljes engedélyek beállítása** mezőben rendelje hozzá az engedélyeket a felhasználóhoz vagy a megosztást elérő csoporthoz. Adja meg a felhasználó vagy a felhasználói csoport nevét *john\@contoso.com* formátumban. Azt javasoljuk, hogy egy felhasználói csoport (nem pedig egy felhasználó), hogy rendszergazdai jogosultságokat elérni ezeket a megosztásokat. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
   
   5. A megosztás létrehozásához kattintson a **Hozzáadás** gombra. 
    
       ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van.
   
       ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Miután a megosztás a megadott beállításokkal jött létre, a **Megosztások** panel frissül, hogy tükrözze az új megosztást. Alapértelmezés szerint a figyelés és a biztonsági mentés engedélyezve van a megosztáshoz.
   
      ![Megosztás hozzáadása](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4. lépés: Csatlakozás a megosztáshoz
Most egy vagy több, az előző lépésben létrehozott megosztáshoz kell csatlakoznia. Hajtsa végre ezeket a lépéseket a StorSimple virtuális tömbhöz csatlakoztatott Windows Server-állomáson.

#### <a name="to-connect-to-the-share"></a>Csatlakozás a megosztáshoz
1. Nyomja ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) meg a + R gombot. A Futtatás ablakban adja meg a *&#92;&#92;&lt;fájlkiszolgáló nevét&gt; * elérési útként, és cserélje le a *fájlkiszolgáló nevét* a fájlkiszolgálóhoz rendelt eszköznévre. Kattintson az **OK** gombra.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Ezzel megnyitja a Fájlkezelőt. A létrehozott megosztásoknak meg kell jelenniük mappaként. Válasszon ki és duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Most már hozzáadhat fájlokat ezekhez a megosztásokhoz, és biztonsági másolatot készíthet.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használhatja a helyi webes felhasználói felületet [a StorSimple virtuális tömb felügyeletére.](storsimple-ova-web-ui-admin.md)

