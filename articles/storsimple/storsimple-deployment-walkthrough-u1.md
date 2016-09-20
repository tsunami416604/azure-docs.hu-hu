<properties 
   pageTitle="A StorSimple eszköz (1. frissítés) üzembe helyezése | Microsoft Azure"
   description="Ez a cikk ismerteti a StorSimple eszköz (1. frissítés) és szolgáltatás üzembe helyezésének szükséges lépéseit és ajánlott eljárásait."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# A helyszíni StorSimple eszköz (1. frissítés) üzembe helyezése

> [AZURE.SELECTOR]
- [2. frissítés](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [1. frissítés](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA kiadás](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Áttekintés

Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a StorSimple 8000 Series 1.0-s frissítési verzióra vonatkoznak. Ez az oktatóanyag-sorozat bemutatja a StorSimple eszköz konfigurálásának módját, továbbá tartalmazza a konfigurációhoz szükséges ellenőrzőlistát, előfeltételeket és részletes lépéseket.

Az oktatóanyagban szereplő információk arra a feltételezésre alapulnak, hogy áttekintette a biztonsági óvintézkedéseket, valamint hogy kicsomagolta, az állványba helyezte és bekábelezte a StorSimple eszközt. Ha még nem hajtotta végre ezeket a feladatokat, kezdje a [biztonsági óvintézkedések](storsimple-safety.md) áttekintésével. A modelltől függően ezután kicsomagolhatja, állványra szerelheti és bekábelezheti az eszközt, a következő helyen található utasításokat követve:

- [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
- [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy mielőtt hozzákezdene, tekintse át a konfigurációs ellenőrzőlistát. Az üzembehelyezési és konfigurációs folyamatok végrehajtása eltarthat egy ideig.

> [AZURE.NOTE] A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire érvényesek. Az 5000-es és 7000-es sorozathoz tartozó eszközökről teljes körű információkat itt talál: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Az 5000-es és 7000-es sorozatra vonatkozó üzembehelyezési információkat a [StorSimple rendszer gyors üzembehelyezési útmutatójában](http://onlinehelp.storsimple.com/111_Appliance/) találja.

## A központi telepítés lépései

Ezen szükséges lépések végrehajtásával konfigurálhatja a StorSimple eszközt és csatlakoztathatja a StorSimple Manager szolgáltatáshoz. A szükséges lépéseken kívül opcionális lépések és eljárások végrehajtására is szükség lehet az üzembe helyezés során. A részletes üzembehelyezési utasítások jelzik, amikor ezeket az opcionális lépéseket végre kell hajtania.


| Lépés                                                                                   | Leírás                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ELŐFELTÉTELEK**                                                                      | Ezeknek kell teljesülniük az üzembe helyezésre való felkészülés során.                                                                                        |
| Üzembehelyezési konfigurációs ellenőrzőlista.                                                     | Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben.                                                                       |
| Üzembehelyezési előfeltételek.                                                               | Ezek ellenőrzik, hogy a környezet készen áll-e az üzembe helyezésre.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **RÉSZLETES ÜZEMBE HELYEZÉS**                                                                   | Ezek a lépések szükségesek a StorSimple eszköz üzemi környezetben való telepítéséhez.                                                                                      |
| 1. lépés: Új szolgáltatás létrehozása.                                                         | A felhőfelügyelet és a felhőalapú tárolás beállítása a StorSimple eszközhöz. Hagyja ki ezt a lépést, ha már rendelkezik meglévő szolgáltatással más StorSimple eszközökhöz.                |
| 2. lépés: Szolgáltatásregisztrációs kulcs lekérése.                                               | Ezzel a kulccsal regisztrálhatja és csatlakoztathatja az eszközét a felügyeleti szolgáltatáshoz.                                                                         |
| 3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel.    | Csatlakoztassa az eszközt a hálózathoz, és a beállítás befejezéséhez regisztrálja az Azure-ban a felügyeleti szolgáltatás segítségével.                                            |
| 4. lépés: Minimális eszközbeállítások végrehajtása.</br>Választható lehetőség: A StorSimple eszköz frissítése.      | A felügyeleti szolgáltatással végezze el az eszköz beállítását, és engedélyezze rajta a tárolást.                                                                      |
| 5. lépés: Kötettároló létrehozása.                                                      | Hozzon létre egy tárolót a kötetek kiépítéséhez. A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára.    |
| 6. lépés: Kötet létrehozása.                                                                | Tárkötet(ek)et építhet ki a StorSimple eszközön a kiszolgálói számára.                                                                                        |
| 7. lépés: Kötet csatlakoztatása, inicializálása és formázása.</br>Választható lehetőség: Az MPIO konfigurálása.            | Csatlakoztassa a kiszolgálókat az eszköz által biztosított iSCSI-tárolóhoz. Választható lehetőségként konfigurálhatja az MPIO-t, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.                                                                                                                                                              |
| 8. lépés: Biztonsági mentés készítése.                                                                  | A biztonsági mentés házirendjének beállítása az adatok védelme érdekében                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **EGYÉB ELJÁRÁSOK**                                                                   | Előfordulhat, hogy a megoldás üzembe helyezése során ezekre az eljárásokra kell majd hivatkoznia.                                                                                        |
| Új tárfiók konfigurálása a szolgáltatáshoz.                                      |                                                                                                                                                               |
| A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz.                                    |                                                                                                                                                               |
| Egy Windows Server-állomás IQN-nevének lekérése                                                   |                                                                                                                                                               |
| Manuális biztonsági mentés létrehozása.                                                                 | 


## Üzembehelyezési konfigurációs ellenőrzőlista

Az alábbi üzembehelyezési konfigurációs ellenőrzőlista azokat az információkat sorolja fel, amelyeket a StorSimple eszköz szoftverének konfigurálása előtt és közben össze kell gyűjtenie. Az információk időben történő előkészítésével leegyszerűsíthető a StorSimple eszköz üzembe helyezésének folyamata a környezetben. Továbbá ezzel az ellenőrzőlistával lejegyezheti a konfigurációs részleteket az eszköz üzembe helyezése során.

| Fázis                                  | Paraméter                                         | Részletek                                                                                                                                                                | Értékek |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Az eszköz bekábelezése**                      | Soros hozzáférés                                     | Az eszköz kezdeti konfigurációja                                                                  | Igen/nem |
|   |   |  |  |
| **Az eszköz konfigurálása és regisztrálása**          | Data 0 hálózati beállítások                           | Data 0 IP-címe:</br>Alhálózati maszk:</br>Átjáró:</br>Elsődleges DNS-kiszolgáló:</br>Elsődleges NTP-kiszolgáló:</br>IP/FQDN webes proxykiszolgáló (opcionális):</br>Webes proxy portja:|        |
|                                        | Az eszköz rendszergazdai jelszava                     | A jelszónak 8–15 karakter hosszúságúnak kell lennie, és tartalmaznia kell kisbetűt, nagybetűt, számot és speciális karaktert. |        |
|                                        | StorSimple Snapshot Manager jelszava              | A jelszónak 14 vagy 15 karakter hosszúságúnak kell lennie, és tartalmaznia kell kisbetűt, nagybetűt, számot és speciális karaktert.|        |
|                                        | Szolgáltatásregisztrációs kulcs                          | Ez a kulcs a klasszikus Azure portálon jön létre.    |        |
|                                        | Szolgáltatásadat-titkosítási kulcs                       | Ez a kulcs akkor jön létre, amikor a rendszer regisztrálja az eszközt a felügyeleti szolgáltatással a StorSimple-höz készült Windows PowerShell-lel. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre.|  |
|   |   |  |  |
| **Minimális eszközbeállítások végrehajtása**          | Rövid név megadása az eszközhöz                     | Ez egy leíró név az eszköz számára. |        |
|                                        | Időzóna                                          | Az eszköz minden ütemezett művelethez ezt az időzónát használja.  |        |
|                                        | Másodlagos DNS-kiszolgáló                              | Ez egy szükséges konfiguráció.                                  |        |
|                                        | Hálózati adapter: Data 0 vezérlő rögzített IP-címei                                     | Ezeknek az IP-címeknek irányíthatóknak kell lenniük az internet felé.</br>0. vezérlő rögzített IP-címe:</br>1. vezérlő rögzített IP-címe:|
|   |   |  |  |
| **További hálózatiadapter-beállítások**  | Hálózati adapter: Data 1</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót.      | Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró:|
|                                        | Hálózati adapter: Data 2</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót.      | Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró:|
|                                        | Hálózati adapter: Data 3</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót.      | Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró:|
|                                        | Hálózati adapter: Data 4</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót.      | Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró:|
|                                        | Hálózati adapter: Data 5</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót.      | Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró:|
|   |   |  |  |
| **Kötettároló létrehozása**                      | Kötettároló neve:                            | A tároló neve                                                                                                                                                 |        |
|                                        | Azure Storage-fiók:                            | A kötettárolóhoz társítandó tárfióknév és elérési kulcs                                                                                              |        |
|                                        | Felhőalapú tárolás titkosítási kulcsa:                     | Titkosítási kulcs az egyes tárolókban való tároláshoz                                                                                                                           |        |
|   |   |  |  |
| **Kötet létrehozása**                        | Az egyes kötetek részletei                           | Kötet neve:                                                                                                                                                           |        |
|                                        |                                                   | Méret:                                                                                                                                                                  |        |
|                                        |                                                   | Használat típusa:                                                                                                                                                            |        |
|                                        |                                                   | ACR neve:                                                                                                                                                              |        |
|                                        |                                                   | Alapértelmezett biztonsági mentési házirend:                                                                                                                                                 |        |
|   |   |  |  |
| **Kötet csatlakoztatása, inicializálása és formázása** | A tárolóhoz csatlakozó egyes gazdakiszolgálók részletei | Windows Server neve:                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Windows Server kötetneve:                                                                                                                                                   |        |
|                                        |                                                   | NTFS csatlakozási pont/meghajtóbetűjel:                                                                                                                                      |        |

## Üzembehelyezési előfeltételek

Az alábbi szakaszok ismertetik a StorSimple Manager szolgáltatás és a StorSimple eszköz konfigurációs előfeltételeit.

### A StorSimple Manager szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

- A Microsoft Azure-előfizetés engedélyezve van a StorSimple Manager szolgáltatáshoz. Az előfizetést a [Nagyvállalati Szerződésen](https://azure.microsoft.com/pricing/enterprise-agreement/) keresztül kell megvásárolni.

- Rendelkezik hozzáféréssel olyan terminálemulációs szoftverekhez, mint a PuTTY.

### Az adatközpontban található eszköz esetén

Az eszköz konfigurálása előtt győződjön meg az alábbiakról:

- Az eszköz teljesen ki van csomagolva, állványra van rögzítve és minden kábel be van kötve a tápellátáshoz, a hálózathoz és a soros hozzáféréshez, a következő helyen leírtak szerint:

    -  [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
    -  [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)


### Az adatközpont hálózata esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpont tűzfalának portjai nyitva vannak az iSCSI és a felhőalapú forgalom számára, [A StorSimple eszköz hálózatkezelési követelményei](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device) című szakaszban leírtaknak megfelelően.


## Részletes üzembe helyezés

Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## 1. lépés: Új szolgáltatás létrehozása

A StorSimple Manager szolgáltatás több StorSimple eszközt is tud kezelni. Az alábbi lépések végrehajtásával hozza létre a StorSimple Manager szolgáltatás egy új példányát.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor fogja használni a rendszer, amikor egy kötettárolót hoz létre. 
>
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg. 
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.

## 2. lépés: Szolgáltatásregisztrációs kulcs lekérése

Ha a StorSimple Manager szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatja és csatlakoztathatja StorSimple eszközét a szolgáltatáshoz.

Hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## 3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel

A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre lesz szüksége. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz.](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## 4. lépés: Minimális eszközbeállítások végrehajtása.

A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania: 

- Állítsa be a másodlagos DNS-kiszolgálót.
- Engedélyezze az iSCSI-t legalább egy hálózati adapteren.
- Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

Hajtsa végre az alábbi lépéseket a klasszikus Azure portálon a minimális eszközbeállítások teljesítéséhez.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 5. lépés: Kötettároló létrehozása

A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót. 

A kötettároló létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 6. lépés: Kötet létrehozása

A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. A kötet létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

> [AZURE.IMPORTANT] A StorSimple Manager csak dinamikusan kiosztott köteteket képes létrehozni. Nem hozhat létre teljesen vagy részben kiosztott köteteket. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 7. lépés: Kötet csatlakoztatása, inicializálása és formázása

A következő lépéseket a Windows Server-állomásán kell végrehajtania. 


> [AZURE.IMPORTANT]

> - Annak érdekében, hogy a StorSimple-megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a gazdakiszolgálókon. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.

> - Az MPIO és az iSCSI Windows Server-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című szakaszban találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.

> - Az MPIO és az iSCSI Linux-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 8. lépés: Biztonsági mentés készítése

Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**. 

Ütemezett biztonsági mentés létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) című szakaszban találja. 

## Új tárfiók konfigurálása a szolgáltatáshoz

Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) című szakaszban.

Hajtsa végre a következő lépéseket a klasszikus Azure portál **StorSimple Manager szolgáltatás** lapján.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz

A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## Frissítések keresése és telepítése

Az eszköz frissítése több órát is igénybe vehet. Az alábbi lépések végrehajtásával frissítéseket kereshet, és telepítheti azokat az eszközre.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Az eszköz frissítése

1.  Az eszköz **Gyors üzembe helyezés** oldalán kattintson az **Eszközök** elemre. Válassza ki a fizikai eszközt, és kattintson a **Karbantartás**, majd a **Frissítések keresése** lehetőségre.  

2.  Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. Ha vannak elérhető frissítések, a **Frissítések keresése** felirat helyett a **Frissítések telepítése** szöveg látható. Kattintson a **Frissítések telepítése** elemre. 

3.  A rendszer létrehoz egy frissítési feladatot. A frissítés állapotának nyomon követéséhez lépjen a **Feladatok** elemre.

    > [AZURE.NOTE] A frissítési feladat elindulásakor az állapot azonnal 50 százalékosként jelenik meg. Az állapot ezután csak a frissítési feladat végeztével vált 100 százalékosra. A frissítési folyamat nem jeleníti meg valós időben a frissítés állapotát.

4.  Az eszköz sikeres frissítése után engedélyezze a Data 2 és a Data 3 hálózati adaptert, ha ezek le voltak tiltva.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## Egy Windows Server-állomás IQN-nevének lekérése

Hajtsa végre a következő lépéseket egy Windows Server® 2012 rendszert futtató Windows-állomás iSCSI teljes nevének (IQN) lekéréséhez.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## Manuális biztonsági mentés létrehozása

Ha szeretne egy azonnali manuális mentést létrehozni a StorSimple eszköze egyik kötetéről, akkor hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## Az MPIO konfigurálása

A többutas I/O (MPIO) egy opcionális szolgáltatás, és alapesetben nincs telepítve a Windows Serveren. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül. Az MPIO telepítési utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című szakaszban találja.

Az MPIO Linux-állomáshoz csatlakoztatott StorSimple eszközre való telepítési utasításait [Az MPIO konfigurálása Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.


> [AZURE.NOTE] StorSimple virtuális eszközökön az MPIO használata nem támogatott. 



## Következő lépések

- [Virtuális eszköz](storsimple-virtual-device-u2.md) konfigurálása.

- A StorSimple eszközt a [StorSimple Manager szolgáltatás](storsimple-manager-service-administration.md) segítségével kezelheti.
 



<!--HONumber=sep16_HO1-->


