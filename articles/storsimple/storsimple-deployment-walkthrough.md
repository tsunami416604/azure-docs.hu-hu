<properties
   pageTitle="Helyszíni StorSimple-eszköz üzembe helyezése | Microsoft Azure"
   description="A cikk a StorSimple eszköz és szolgáltatás üzembe helyezésének szükséges lépéseit és ajánlott eljárásait írja le. (A Microsoft Azure StorSimple .3-as és korábbi verzióira vonatkozik.)"
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


# A helyszíni StorSimple eszköz üzembe helyezése

> [AZURE.SELECTOR]
- [2. frissítés](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [1. frissítés](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA kiadás](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Áttekintés

Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a StorSimple 8000 Series kiadási verziójára, a StorSimple 8000 Series 0.1-es frissítésére, a StorSimple 8000 Series 0.2-es frissítésére és a StorSimple 8000 Series Update 0.3-as frissítésére vonatkoznak. Ez az oktatóanyag-sorozat bemutatja a StorSimple eszköz konfigurálásának módját, továbbá tartalmazza a konfigurációhoz szükséges ellenőrzőlistát, előfeltételeket és részletes lépéseket.


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

Az alábbi szakaszok ismertetik a StorSimple Manager szolgáltatás és a StorSimple eszköz és az adatközpont hálózatának konfigurációs előfeltételeit.

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
- Az adatközpontjában található eszköz képes külső hálózathoz csatlakozni. Futtassa a következő [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855)-parancsmagokat (a lenti táblázatban láthatóak), hogy ellenőrizze a kapcsolatot a külső hálózattal. Az ellenőrzést egy olyan számítógépen hajtsa végre (az adatközpont hálózatán), amely kapcsolódik az Azure-hoz és ahhoz a helyhez, ahol üzembe helyezi majd a StorSimple eszközt.  

| Ehhez a paraméterhez…       | Az érvényesség ellenőrzéséhez…                                                                                                                                                                                | Ezeket a parancsokat/parancsmagokat futtassa.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Alhálózat**</br>**Átjáró** | Ez egy érvényes IPv4- vagy IPv6-cím?</br>Ez egy érvényes alhálózat?</br>Ez egy érvényes átjáró?</br>Ez egy ismétlődő IP-cím a hálózaton?                                                                          | `ping ip`</br>`arp -a`</br>A `ping` és az `arp` parancsok sikertelenek, ami azt jelzi, hogy az adatközpont hálózatában egy eszköz sem használja ezt az IP-címet.
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | Ez egy érvényes DNS, amely képes Azure URL-címeket feloldani?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Alternatív parancsként használható a következő:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Ellenőrizze, hogy nyitva van-e az 53-as port. Ez csak abban az esetben érvényes, ha az eszközhöz külső DNS-t használ. A belső DNS-nek automatikusan fel kell oldania a külső URL-címeket.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[További információ erről a parancsmagról](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | Az NTP-kiszolgáló megadása után azonnal elindítunk egy időszinkronizálást. A `time.windows.com` vagy nyilvános időkiszolgálók megadásakor ellenőrizze, hogy nyitva van-e a 123-as port. | [Töltse le és használja ezt a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca).                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (opcionális)**          | Érvényes ez a proxy URI és port? </br> Helyes a hitelesítési mód?                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Ezt a parancsot közvetlenül a webproxy konfigurálása után kell futtatni. Ha a rendszer a 200-as állapotkódot adja vissza, akkor a kapcsolódás sikeres volt.                                                                                                                                 |
|                           | Irányítható proxyn keresztül a forgalom?                                                                                                                                 | Miután konfigurálta a proxyt az eszközén, futtassa egyszer a DNS-ellenőrzést, az NTP-ellenőrzést vagy a HTTP-ellenőrzést. Ezáltal világossá válik, hogy blokkolva van-e a forgalom a proxynál vagy máshol.                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Regisztráció**              | Ellenőrizze, hogy a 443-as, 80-as és 9354-es kimeneti TCP-portok nyitva vannak-e.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[További információ a Test-NetConnection parancsmagról](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## Részletes üzembe helyezés

Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## 1. lépés: Új szolgáltatás létrehozása

A StorSimple Manager szolgáltatás több StorSimple eszközt is tud kezelni. Az első StorSimple eszköz üzembe helyezéséhez létre kell hoznia egy új StorSimple Manager szolgáltatást.

> [AZURE.IMPORTANT] Hagyja ki ezt a lépést, ha már rendelkezik egy meglévő StorSimple Manager szolgáltatással, és a StorSimple eszközét ezzel szeretné telepíteni.

Az alábbi lépések végrehajtásával hozza létre a StorSimple Manager szolgáltatás egy új példányát.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor fogja használni a rendszer, amikor egy kötettárolót hoz létre.
>
> Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2:-get-the-service-registration-key) című szakasszal.

## 2. lépés: Szolgáltatásregisztrációs kulcs lekérése

Ha a StorSimple Manager szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatja és csatlakoztathatja StorSimple eszközét a szolgáltatáshoz.

Hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## 3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel

> [AZURE.IMPORTANT] A konfigurálás előtt mindkét vezérlőből (aktív és passzív) húzzon ki minden hálózati adaptert a DATA 0 kivételével.

A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre lesz szüksége. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz.](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## 4. lépés: Minimális eszközbeállítások végrehajtása.

A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania:

- Állítsa be a másodlagos DNS-kiszolgálót.
- Engedélyezze az iSCSI-t legalább egy hálózati adapteren.
- Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

Hajtsa végre az alábbi lépéseket a klasszikus Azure portálon a minimális eszközbeállítások teljesítéséhez.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Az eszköz konfigurálása után frissítéseket kell keresnie, és ha rendelkezésre állnak, akkor telepítenie kell őket. A frissítések akár több órát is igénybe vehetnek. Kövesse a [Frissítések keresése és telepítése](#scan-for-and-apply-updates) témakör utasításait.


## 5. lépés: Kötettároló létrehozása

A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót.

A kötettároló létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 6. lépés: Kötet létrehozása

A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. A kötet létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

> [AZURE.IMPORTANT] A StorSimple Manager csak dinamikusan kiosztott köteteket képes létrehozni.  Nem hozhat létre teljesen vagy részben kiosztott köteteket.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 7. lépés: Kötet csatlakoztatása, inicializálása és formázása

> [AZURE.IMPORTANT]

> - Annak érdekében, hogy a StorSimple megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a Windows Server-állomáson. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.

> - Az MPIO és az iSCSI telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című részben találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 8. lépés: Biztonsági mentés készítése

Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**.

Ütemezett biztonsági mentés létrehozásához hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#Create-a-manual-backup) című szakaszban találja.

## Új tárfiók konfigurálása a szolgáltatáshoz

Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) című szakaszban.

Hajtsa végre a következő lépéseket a klasszikus Azure portál **StorSimple Manager szolgáltatás** lapján.

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz

A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## Frissítések keresése és telepítése

Az eszköz frissítése 1-4 órát is igénybe vehet. Az alábbi lépések végrehajtásával frissítéseket kereshet, és telepítheti azokat az eszközre.

> [AZURE.NOTE] Ha a hálózati adapteren van más átjáró is konfigurálva, mint a Data 0, akkor a frissítés telepítése előtt le kell tiltania a Data 2 és a Data 3 hálózati adaptereket. Lépjen az **Eszközök > Konfigurálás** elemre, és tiltsa le a Data 2 és a Data 3 adaptereket. Az eszköz frissítése után engedélyezze újra ezeket az adaptereket.

#### Az eszköz frissítése
1.  Az eszköz **Gyors üzembe helyezés** oldalán kattintson az **Eszközök** elemre. Válassza ki a fizikai eszközt, és kattintson a **Karbantartás**, majd a **Frissítések keresése** lehetőségre.  
2.  Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. Ha vannak elérhető frissítések, a **Frissítések keresése** felirat helyett a **Frissítések telepítése** szöveg látható. Kattintson a **Frissítések telepítése** elemre. Lehetséges, hogy a frissítések telepítése előtt a rendszer a Data 2 és a Data 3 letiltására kéri. Ha nem tiltja le ezeket a hálózati adaptereket, akkor a frissítések sikertelenek lehetnek.
3.  A rendszer létrehoz egy frissítési feladatot. A frissítés állapotának nyomon követéséhez lépjen a **Feladatok** elemre.

    > [AZURE.NOTE] A frissítési feladat elindulásakor az állapot azonnal 50 százalékosként jelenik meg. Az állapot ezután csak a frissítési feladat végeztével vált 100 százalékosra. A frissítési folyamat nem jeleníti meg valós időben a frissítés állapotát.

4.  Az eszköz sikeres frissítése után engedélyezze a Data 2 és a Data 3 hálózati adaptert, ha ezek le voltak tiltva.



## Egy Windows Server-állomás IQN-nevének lekérése

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás iSCSI minősített nevének (IQN) lekéréséhez.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## Manuális biztonsági mentés létrehozása

Ha szeretne egy azonnali manuális mentést létrehozni a StorSimple eszköze egyik kötetéről, akkor hajtsa végre a következő lépéseket a klasszikus Azure portálon.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## Következő lépések

- [Virtuális eszköz](storsimple-virtual-device-u2.md) konfigurálása.

- A StorSimple eszközt a [StorSimple Manager szolgáltatás](https://msdn.microsoft.com/library/azure/dn772396.aspx) segítségével kezelheti.



<!--HONumber=Sep16_HO4-->


