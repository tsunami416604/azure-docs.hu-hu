---
title: 'Oktatóanyag: Az Azure FXT Edge Filer gyorsítótár-fürt létrehozása'
description: Hibrid tárolási gyorsítótár-fürt létrehozása az Azure FXT Edge Filer segítségével
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239209"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Oktatóanyag: Az Azure FXT Edge Filer-fürt létrehozása

Miután telepítette és inicializálja az Azure FXT Edge Filer hardvercsomópontjait a gyorsítótárhoz, használja az FXT fürtszoftvert a gyorsítótár-fürt létrehozásához. 

Ez az oktatóanyag végigvezeti a hardvercsomópontok fürtként történő konfigurálásához szükséges lépéseken. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Milyen információkra van szükség a fürt létrehozásának megkezdése előtt?
> * A fürt felügyeleti hálózata, a fürthálózat és az ügyfélfelé néző hálózat közötti különbség
> * Fürtcsomóponthoz való csatlakozás 
> * Kezdeti fürt létrehozása egy Azure FXT Edge Filer-csomópont használatával
> * Bejelentkezés a fürt vezérlőpultjára a fürt beállításainak konfigurálásához

Ez az eljárás 15 és 45 perc között tart, attól függően, hogy mennyi kutatást kell végeznie az IP-címek és a hálózati erőforrások azonosításához.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt hajtsa végre az alábbi előfeltételeket:

* Telepítse az Azure FXT Edge Filer hardverrendszereit az adatközpontba 

  A fürt létrehozásához csak egy csomópontra van szükség, de [legalább két további csomópontot](fxt-add-nodes.md) kell hozzáadnia a fürt konfigurálásához és használatra való felkészítéséhez. 

* Csatlakoztassa a megfelelő táp- és hálózati kábeleket a rendszerhez  
* Legalább egy Azure FXT Edge Filer csomópont bekapcsolása és [gyökérjelszó beállítása](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Információk gyűjtése a fürthöz 

Az Azure FXT Edge Filer-fürt létrehozásához a következő információkra van szüksége:

* Fürt neve

* A fürthöz beállított felügyeleti jelszó

* IP-címek:

  * Egyetlen IP-cím a fürtkezeléshez, valamint a kezelőhálózathoz használandó hálózati maszk és útválasztó
  * A fürt (csomópont tól csomópontig) kommunikáció IP-címtartományában lévő IP-címek első és utolsó IP-címei. A részleteket lásd alább az [IP-cím terjesztésében.](#ip-address-distribution) 
  * (Az ügyfélfelé néző IP-címek a fürt létrehozása után vannak beállítva.)

* Hálózati infrastruktúra adatai:

  * A fürt DNS-kiszolgálójának IP-címe
  * A fürt DNS-tartományának neve
  * A fürt NTP-kiszolgálóinak neve vagy IP-címe (egy kiszolgáló, három vagy több) 
  * Engedélyezni kívánja-e az IEEE 802.1AX-2008 kapcsolatösszesítést a fürt összeköttetésein
  * Ha engedélyezi a kapcsolatösszesítést, függetlenül attól, hogy az IEEE 802.3ad (LACP) dinamikus összesítést használja-e vagy sem

Ezeket a hálózati infrastruktúra-elemeket a fürt létrehozása után konfigurálhatja, de jobb, ha a létrehozás kor teszi meg. 

### <a name="ip-address-distribution"></a>IP-cím terjesztése

Az Azure FXT Edge Filer hibrid tárológyorsítótár-fürt három kategóriában használja az IP-címeket:

* Felügyeleti IP-cím: Egyetlen IP-cím a fürtkezeléshez

  Ez a cím szolgál belépési pontként a fürtkonfigurációs segédprogramok (a webalapú vezérlőpult vagy az XML-RPC API) eléréséhez. Ez a cím automatikusan hozzá van rendelve a fürt elsődleges csomópontjéhez, és automatikusan megváltozik, ha az elsődleges csomópont megváltozik.

  Más IP-címek is használhatók a vezérlőpult eléréséhez, de a felügyeleti IP-cím úgy van kialakítva, hogy hozzáférést biztosítson, még akkor is, ha az egyes csomópontok feladatátvételt nyújtanak.

* Fürthálózat: Ip-címek tartománya a fürtkommunikációhoz

  A fürthálózat a fürtcsomópontok közötti kommunikációra és a háttértárolóból (core filers) származó fájlok lekérésére szolgál.

  **Legjobb gyakorlat:** Minden Azure FXT Edge Filer csomóponton a fürtkommunikációhoz használt fizikai portonként egy IP-címet foglal le. A fürt automatikusan hozzárendeli a megadott tartományban lévő címeket az egyes csomópontokhoz.

* Ügyféloldali hálózat: Az ügyfelek által fájlok kéréséhez és írásához használt IP-címek tartománya

  Az ügyfélhálózati címeket az ügyfelek a fürtön keresztül iradt fájlkezelő adatok elérésére használják. Előfordulhat például, hogy egy NFS-ügyfél csatlakoztatja az egyik ilyen címet.

  **Legjobb gyakorlat:** Minden FXT-sorozatú csomóponton az ügyfélkommunikációhoz használt fizikai portonként egy IP-címet foglaljon le.

  A fürt a lehető legegyenletesebben osztja el az ügyfélfelé irányuló IP-címeket az alkotó csomópontok között.

  Az egyszerűség kedvéért sok rendszergazda egyetlen DNS-nevet konfigurál ciklikus multiplexeléses DNS -konfigurációval, hogy megkönnyítse az ügyfélkérelmek terjesztését a címtartományban. Ez a beállítás azt is lehetővé teszi, hogy minden ügyfél ugyanazt a csatlakoztatási parancsot használja a fürt eléréséhez. További információ: [A DNS konfigurálása](fxt-configure-network.md#configure-dns-for-load-balancing) című olvasni.

Új fürt létrehozásához meg kell adni a felügyeleti IP-címet és a fürt hálózati címtartományát. Az ügyfélfelé néző címek a fürt létrehozása után kerülnek megadva.

## <a name="connect-to-the-first-node"></a>Csatlakozás az első csomóponthoz

Bármelyik telepített FXT-csomóponthoz csatlakozhat, és az operációs rendszer szoftverével állíthatja be a fürtöt.

Ha még nem tette meg, kapcsolja be a fürt legalább egy FXT-csomópontját, és győződjön meg arról, hogy rendelkezik hálózati kapcsolattal és IP-címmel. Új gyökérjelszót kell beállítania a csomópont aktiválásához, ezért kövesse a [Hardverjelszavak beállítása](fxt-node-password.md) című részben leírt lépéseket, ha még nem tette meg.

A hálózati kapcsolat ellenőrzéséhez győződjön meg arról, hogy a csomópont hálózati kapcsolati LED-jei világítanak (és ha szükséges, a hálózati kapcsoló jelzői, amelyhez csatlakoztatva van). A jelző LED-ek leírása az [Azure FXT Edge Filer hardverállapotának figyelője.](fxt-monitor.md)

Amikor a csomópont elindul, ip-címet kér. Ha DHCP-kiszolgálóhoz csatlakozik, elfogadja a DHCP által megadott IP-címet. (Ez az IP-cím ideiglenes. A fürt létrehozásakor megváltozik.)

Ha nem csatlakozik DHCP-kiszolgálóhoz, vagy nem kap választ, a csomópont bonjour szoftverrel állítja be az önhozzárendelt IP-címet a 169.254-es űrlapon. \*. \*. Azonban be kell állítania egy ideiglenes statikus IP-címet a csomópont egyik hálózati kártyáján, mielőtt fürt létrehozásához használná. Az utasítások ebben az örökölt dokumentumban találhatók; a frissített információkért forduljon a Microsoft szervizéhez és támogatásához: [A függelék: Statikus IP-cím beállítása fxt-csomóponton](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Az IP-cím megkeresése

Csatlakozzon az Azure FXT Edge Filer csomóponthoz az IP-cím megkereséséhez. Használhat soros kábelt, közvetlen csatlakozást az USB- és VGA-portokhoz, vagy kvm-kapcsolón keresztül csatlakozhat. (A portkapcsolat részleteiről lásd: [Kezdeti jelszavak beállítása.)](fxt-node-password.md)

A csatlakozás után jelentkezzen be `root` a felhasználónévvel és a jelszóval, amelyet a csomópont első indításakor állított be.  

A bejelentkezés után meg kell határoznia a csomópont IP-címét.

A paranccsal `ifconfig` megtekintheti a rendszerhez rendelt címeket.

A parancs `ifconfig | grep -B5 inet` például internetes címekkel rendelkező portokat keres, és öt sornyi kontextust ad a portazonosító megjelenítéséhez.

Írjon le minden IP-címet, amely megjelenik az ifconfig jelentésben. Címek felsorolt port nevek, mint az e0a vagy e0b jó lehetőség. Ne használjon e7* nevű IP-címeket, mivel ezek a nevek csak az iDRAC/IPMI szolgáltatásportokhoz használatosak.  

## <a name="load-the-cluster-configuration-wizard"></a>A fürtkonfigurációs varázsló betöltése

A fürt létrehozásához használja a böngészőalapú fürtkonfigurációs eszközt. 

Adja meg a csomópont IP-címét egy webböngészőben. Ha a böngésző arról ad üzenetet, hogy a webhely nem megbízható, mindenképpen folytassa a webhelyet. (Az egyes Azure FXT Edge Filer-csomópontok nem rendelkeznek hitelesítésszolgáltató által biztosított biztonsági tanúsítványokkal.)

![Vezérlőpult bejelentkezési lapja a böngészőablakban](media/fxt-cluster-create/unconfigured-node-gui.png)

Hagyja üresen a **Felhasználónév** és **jelszó** mezőt. A fürtlétrehozási lap betöltéséhez kattintson a **Bejelentkezés** gombra.

![A böngészőalapú grafikus felhasználói felület vezérlőpultján lévő, konfigurálatlan csomópont kezdeti beállítási képernyője. Bemutatja a szoftverek frissítésének, a fürt manuális konfigurálásának vagy a fürt telepítőfájlból történő konfigurálásának lehetőségeit.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>A fürt létrehozása

A fürtkonfigurációs eszköz végigvezeti a képernyők egy soraz Azure FXT Edge Filer-fürt létrehozásához. A kezdés előtt győződjön meg arról, hogy a [szükséges információk](#gather-information-for-the-cluster) készen állnak. 

### <a name="creation-options"></a>Létrehozási beállítások

Az első képernyő három lehetőséget ad. Használja a kézi konfigurációs lehetőséget, kivéve, ha speciális utasításokat kap a kisegítő személyzettől.

Kattintson **a I will configure the cluster manuálisan** az új fürt konfigurációs beállítások képernyőjének betöltéséhez. 

A többi lehetőség ritkán használatos:

* A "Rendszerlemezkép frissítése" üzenet ben új operációsrendszer-szoftver telepítését kéri a fürt létrehozása előtt. (A jelenleg telepített szoftververzió a képernyő tetején található.) Meg kell adnia a szoftvercsomag fájl - vagy egy URL-t és felhasználónevet / jelszót, vagy feltöltésével egy fájlt a számítógépről. 

* A fürttelepítő fájl beállítását a Microsoft ügyfélszolgálata és ügyfélszolgálata is használja. 

## <a name="cluster-options"></a>Fürtbeállítások

A következő képernyő az új fürt beállításainak konfigurálását kéri.

Az oldal két fő részre oszlik: **Alapkonfiguráció** és **Hálózati konfiguráció**. A hálózati konfigurációs szakasz alszakaszokkal is rendelkezik: egyet a **felügyeleti** hálózathoz, egyet pedig a **fürthálózathoz.**

### <a name="basic-configuration"></a>Alapkonfiguráció

A felső szakaszban adja meg az új fürt alapvető adatait.

![Az "Alapkonfiguráció" szakasz részletei a böngésző grafikus felületének oldalán. Három mezőt mutat (fürtnév, rendszergazdai jelszó, jelszó megerősítése)](media/fxt-cluster-create/basic-configuration.png) 

* **Fürtnév** – Adja meg a fürt egyedi nevét.

  A fürtnevének meg kell felelnie a következő feltételeknek:
  
  * 1–16 karakter hosszúsága
  * Tartalmazhat betűket, számokat, valamint kötőjel (-) és aláhúzás (_) karaktereket 
  * Nem tartalmazhat más írásjeleket vagy speciális karaktereket
  
  Ezt a nevet később a **Fürt** > **általános beállításának konfigurációs** lapján módosíthatja. (A fürtbeállításokról további információt a [fürtkonfigurációs útmutatóban](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)talál, amely nem része ennek a dokumentációkészletnek.)

  > [!NOTE] 
  > A fürtneve a figyelés vagy hibaelhárítás támogatására feltöltött rendszeradatok azonosítására szolgál, ezért hasznos lehet a vállalat nevét megadni.

* **Rendszergazdai jelszó** - Állítsa be az `admin`alapértelmezett rendszergazdai felhasználó jelszavát, .
  
  A fürtöt felügyelő minden egyes személyhez egyéni felhasználói fiókokat kell `admin`beállítania, de a felhasználót nem távolíthatja el. Jelentkezzen be, mintha `admin` további felhasználókat kellene létrehoznia.
 
  A `admin` vezérlőpult fürtjének **Felügyeleti** > **felhasználók** beállításai lapján módosíthatja a jelszó módosítását. További információt a **Felhasználók** dokumentációjában, a [Fürtkonfigurációs útmutatóban talál.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html)

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Hálózati konfiguráció

A **Hálózat** szakasz kéri a fürt által használt hálózati infrastruktúra megadását. 

Két külön hálózatot kell konfigurálni:

* A *felügyeleti hálózat* rendszergazdai hozzáférést biztosít a fürthöz a konfigurációhoz és a figyeléshez. Az itt megadott IP-cím a Vezérlőpulthoz vagy az SSH-hozzáféréshez való csatlakozáskor használatos. 

  A legtöbb fürt csak egyetlen felügyeleti IP-címet használ, de ha csatolók hozzáadását szeretné használni, ezt a fürt létrehozása után teheti meg.

* A *fürthálózat* a fürtcsomópontok, valamint a fürtcsomópontok és a háttértárolók (core filers) közötti kommunikációra szolgál.

Az ügyfélfelé néző hálózat később, a fürt létrehozása után konfigurálva van.

Ez a szakasz a két hálózat által használt DNS- és NTP-kiszolgálók konfigurációját is tartalmazza.

### <a name="configure-the-management-network"></a>A felügyeleti hálózat konfigurálása

A **Kezelés** szakasz beállításai a fürthöz rendszergazdai hozzáférést biztosító hálózatra vannak.

![a "Kezelés" szakasz részletei, 5 beállítás mezőivel és egy 1 Gb-os felügyeleti hálózat jelölőnégyzetével](media/fxt-cluster-create/management-network-filled.png)

* **Felügyeleti IP-** – Adja meg a vezérlőpult fürtjének eléréséhez használni kívánt IP-címet. Ezt a címet a fürt elsődleges csomópontja igényli, de automatikusan egy kifogástalan állapotú csomópontra kerül, ha az eredeti elsődleges csomópont elérhetetlenné válik.

  A legtöbb fürt csak egy felügyeleti IP-címet használ. Ha egynél többet szeretne, a fürt létrehozása után hozzáadhatja őket a **Fürtfelügyeleti** > **hálózat** beállításai lapon. További információ: [Fürtkonfigurációs útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmask** - Adja meg a felügyeleti hálózat hálózati maszkját.

* **Útválasztó** – Adja meg a felügyeleti hálózat által használt alapértelmezett átjárócímet.

* **VLAN-címke (nem kötelező)** – Ha a fürt VLAN-címkéket használ, adja meg a felügyeleti hálózat címkéjét.

  A további VLAN-beállítások a **Fürt** > **VLAN-beállítások** lapján vannak konfigurálva. További információ [a VLAN-okkal](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) és [a fürt > VLAN-nal](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) való együttműködés a fürtkonfigurációs útmutatóban.

* **MTU** – Szükség esetén állítsa be a fürt felügyeleti hálózatának maximális átviteli egységét (MTU).

* **1 Gb-os mgmt hálózat használata** – Jelölje be ezt a jelölőnégyzetet, ha az FXT-csomópontok két 1GbE hálózati portját csak a felügyeleti hálózathoz szeretné hozzárendelni. (Az összes többi forgalomhoz 25GbE/10GbE porttal kell rendelkeznie.) Ha nem jelöli be ezt a jelölőnégyzetet, a felügyeleti hálózat a rendelkezésre álló legnagyobb sebességű portot használja. 

### <a name="configure-the-cluster-network"></a>A fürthálózat konfigurálása 

A fürt hálózati beállításai a fürtcsomópontok közötti, valamint a fürtcsomópontok és az alapvető fájlkezelők közötti forgalomra vonatkoznak.

![a "Fürt" szakasz részlete, hat értéket beíró mezőkkel](media/fxt-cluster-create/cluster-network-filled.png)

* **Első IP** és **Utolsó IP** – Adja meg azokat az IP-címeket, amelyek meghatározzák a belső fürtkommunikációhoz használandó tartományt. Az itt használt IP-címeknek összefüggőeknek kell lenniük, és nem rendelhető hozzá a DHCP-nek.

  A fürt létrehozása után további IP-címeket adhat hozzá. Használja a > **Fürtfürthálózatok** beállításai lapot ([Fürtkonfigurációs útmutató dokumentációja](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)). **Cluster**

  A **tartományban lévő IP-k száma** mező értékét a program kiszámítja, és automatikusan megjelenik.

* **Nem mgmt hálózati maszk (nem kötelező)** - Adja meg a hálózati maszkot a fürthálózathoz. 

  A rendszer automatikusan javasolja a felügyeleti hálózathoz megadott hálózati maszk értéket; szükség esetén módosítsa.

* **Fürtútválasztó (nem kötelező)** – Adja meg a fürthálózat által használt alapértelmezett átjárócímet. 

  A rendszer automatikusan ugyanazt az átjárócímet javasolja, amelyet a felügyeleti hálózathoz megadott.

* **Fürt VLAN-címke (nem kötelező)** – Ha a fürt VLAN-címkéket használ, adja meg a fürthálózat címkéjét.

* **Nem mgmt MTU (nem kötelező)** - Ha szükséges, állítsa be a maximális átviteli egység (MTU) a fürthálózat.

### <a name="configure-cluster-dns-and-ntp"></a>Fürt DNS- és NTP-jének konfigurálása 

A **Fürt** szakasz alatt vannak mezők a DNS- és NTP-kiszolgálók megadásához, valamint a kapcsolatösszesítés engedélyezéséhez. Ezek a beállítások a fürt által használt összes hálózatra vonatkoznak.

![A DNS/NTP-konfiguráció szakaszának részletei, a DNS-kiszolgálók három mezője, a DNS-tartomány és a DNS-keresés mezői, az NTP-kiszolgálók három mezője, valamint a csatolásösszesítési beállítások legördülő menüje](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-kiszolgáló(k)** – Adja meg egy vagy több DNS-kiszolgáló IP-címét.

  A DNS minden fürthöz ajánlott, és az SMB, AD vagy Kerberos használatához szükséges. 
  
  Az optimális teljesítmény érdekében konfigurálja a fürt DNS-kiszolgálóját ciklikus multiplexeléses terheléselosztáshoz [az Azure FXT Edge Filer fürt DNS-konfigurálása című részen leírtak](fxt-configure-network.md#configure-dns-for-load-balancing)szerint.

* **DNS-tartomány** – Adja meg azt a hálózati tartománynevet, amelyet a fürt használni fog.

* **DNS-keresés** – Tetszés szerint adjon meg további tartományneveket, amelyeket a rendszernek keresnie kell a DNS-lekérdezések feloldásához. Legfeljebb hat tartománynevet adhat hozzá, szóközökkel elválasztva.

* **NTP-kiszolgáló(k)** – Adja meg egy vagy három hálózati időprotokoll-kiszolgáló (NTP) kiszolgálót a megadott mezőkben. Állomásneveket vagy IP-címeket is használhat.

* **Link aggregáció** - Link aggregáció lehetővé teszi, hogy testre szabhatja, hogy az ethernet portok a fürt FXT csomópontok kezelni a különböző típusú forgalmat. További információ: A fürtkonfigurációs útmutató a [Csatolás aggregálása.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation)

### <a name="click-the-create-button"></a>Kattintson a létrehozás gombra

Miután megadt minden szükséges beállítást az űrlapon, kattintson a **Fürt létrehozása** gombra.

![a kitöltött űrlap alja, a jobb alsó sarokban lévő létrehozás gomb fölé mutató kurorral](media/fxt-cluster-create/create-cluster.png)

A rendszer üzenetet jelenít meg a fürt létrehozásakor.

![fürtkonfigurációs állapotüzenet a böngészőben: "Az FXT-csomópont most hozza létre a fürtöt. Ez több percet vesz igénybe. A fürt létrehozásakor látogasson el erre a hivatkozásra a konfiguráció befejezéséhez." a linka "látogassa meg ezt a linket"](media/fxt-cluster-create/creating-message.png)

Néhány pillanat múlva az üzenetben lévő hivatkozásra kattintva lépjen a Vezérlőpult fürtvezérlő pultjára. (Ez a hivatkozás a **Felügyeleti IP-címben**megadott IP-címre mutat.) A létrehozás gombra kattintás után a hivatkozás 15 másodpercig, egy percig tart, amíg a hivatkozás aktívvá válik. Ha a webes felület nem töltődik be, várjon még néhány másodpercet, majd kattintson ismét a hivatkozásra. 

A fürt létrehozása egy percet vagy többet vesz igénybe, de a folyamat közben bejelentkezhet a Vezérlőpultra. Normális, hogy a vezérlőpult irányítópult-lapja figyelmeztetéseket jelenít meg, amíg a fürt létrehozási folyamata be nem fejeződik. 

## <a name="open-the-settings-pages"></a>A Beállítások lapok megnyitása 

A fürt létrehozása után testre kell szabnia a hálózat és a munkafolyamat konfigurációját. 

A Vezérlőpult webes felületén állíthatja be az új fürtöt. Kövesse a fürt létrehozási állapotképernyőjén található hivatkozást, vagy keresse meg a fürtön beállított felügyeleti IP-címet.

Jelentkezzen be a webes felületre `admin` a fürt létrehozásakor megadott felhasználónévvel és jelszóval.

![webböngésző a vezérlőpult bejelentkezési mezőivel](media/fxt-cluster-create/admin-login.png)

Megnyílik a Vezérlőpult, és megjelenik az **Irányítópult** lap. A fürt létrehozásának befejezésekor a figyelmeztető üzeneteknek ki kell üríteniük a kijelzőről.

A fürt konfigurálásához kattintson a **Beállítások** fülre.

A **Beállítások** lapon a bal oldali oldalsávon megjelenik a konfigurációs lapok menüje. Az oldalak kategóriák szerint vannak rendezve. Kattintson a kategórianév tetején található + vagy - vezérlőre az egyes oldalak kibontásához vagy elrejtéséhez.

![A vezérlőpult Beállítások lapja (böngészőben) a Fürt > általános beállítási lapjának betöltésével](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>A fürt beállításának lépései

A folyamat ezen pontján a fürt létezik, de csak egy csomó, nincs ügyfél felé néző IP-cím, és nincs háttértároló. További telepítési lépésekre van szükség az újonnan létrehozott fürtről a munkafolyamat kezelésére kész gyorsítótár-rendszerre való ugráshoz.

### <a name="required-configuration"></a>Szükséges konfiguráció

Ezekre a lépésekre a legtöbb vagy az összes fürt esetében szükség van. 

* Csomópontok hozzáadása a fürthöz 

  Három csomópont szabványos, de sok termelési fürtök több - legfeljebb 24 csomópont.

  A [Fürtcsomópontok hozzáadása című, a fürthöz](fxt-add-nodes.md) való hozzáadás című, további Azure FXT Edge Filer-egységek fürthöz való hozzáadásának és a magas rendelkezésre állás engedélyezésének című elolvasása című elolvasása című elolvasása.

* Háttértároló megadása

  Adja hozzá a fürt által használt minden háttértároló rendszer *hez.* Olvassa [el A háttérbeli tárolás hozzáadása és a virtuális névtér konfigurálása](fxt-add-storage.md#about-back-end-storage) című további információ című elolvassa.

* Ügyfélhozzáférés és a virtuális névtér beállítása 

  Hozzon létre legalább egy virtuális kiszolgálót (vserver), és rendeljen hozzá egy IP-címtartományt az ügyfélgépek számára. Konfigurálnia kell a fürtnévteret (más néven globális névteret vagy GNS-t), egy virtuális fájlrendszer-szolgáltatást, amely lehetővé teszi a háttérbeli tárolóexportálás leképezését a virtuális útvonalakhoz. A fürtnévtér egységes és hozzáférhető fájlrendszer-struktúrát biztosít az ügyfelekszámára, még akkor is, ha háttérrendszerbeli adathordozót vált át. A névtér is biztosíthat egy felhasználóbarát virtuális tárolási hierarchia Az Azure Blob-tárolók vagy más támogatott felhőobjektum-tároló.

  Olvassa [el A névtér konfigurálása](fxt-add-storage.md#configure-the-namespace) a részletekért. Ez a lépés a következőket tartalmazza:
  * V-kiszolgálók létrehozása
  * Az ügyfélhálózati nézet és a háttértároló közötti csomópontok beállítása 
  * Annak meghatározása, hogy mely ügyfél IP-címeket szolgálja ki az egyes virtuális kiszolgálók

  > [!Note] 
  > A fürt GNS-ének beállítása előtt ajánlott jelentős tervezést. További segítséget a Fürtkonfigurációs útmutató [Globális névtér használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) [és a VServers-kiszolgálók létrehozása és használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) című szakaszában talál.

* [Hálózati beállítások módosítása](fxt-configure-network.md)

  Számos, hálózattal kapcsolatos beállítás létezik, amelyeket ellenőrizni vagy testre kell szabni egy új fürthöz. Olvassa [el A hálózati beállítások módosítása](fxt-configure-network.md) az elemekkel kapcsolatos részletekért:

  * Dns- és NTP-konfiguráció ellenőrzése 
  * Címtárszolgáltatások konfigurálása, ha szükséges 
  * A VLAN-ok beállítása
  * Proxykiszolgálók konfigurálása
  * IP-címek hozzáadása a fürthálózathoz
  * Titkosítási tanúsítványok tárolása

* [Támogatásfigyelés beállítása](#enable-support)

  El kell fogadnia a konfigurációs eszköz adatvédelmi szabályzatát, és ezzel egyidejűleg kell konfigurálnia a támogatási feltöltési beállításokat.

  A fürt automatikusan feltöltheti a fürthiba-elhárítási adatait, beleértve a statisztikákat és a hibakeresési fájlokat. Ezek a feltöltések lehetővé teszik, hogy a Microsoft ügyfélszolgálata és ügyfélszolgálata a lehető legjobb szolgáltatást nyújtsa. Testreszabhatja a figyelt adatokat, és szükség esetén engedélyezheti a proaktív támogatást és a távoli hibaelhárítási szolgáltatást.  

### <a name="optional-configuration"></a>Választható konfiguráció

Ezek a lépések nem szükségesek minden fürthöz. Bizonyos típusú munkafolyamatokhoz vagy bizonyos fürtkezelési stílusokhoz szükségesek. 

* Csomópontbeállítások testreszabása

  A csomópontneveket beállíthatja, és konfigurálhatja a csomópont IPMI-portjait fürtszintű vagy egyénileg. Ha ezeket a beállításokat a csomópontok fürthöz való hozzáadása előtt konfigurálja, az új csomópontok automatikusan felvehetik a beállításokat, amikor csatlakoznak. A beállításokat az örökölt fürtlétrehozási dokumentum ismerteti [a Csomópontbeállítások testreszabása](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)című dokumentumban.

  > [!TIP]
  > A termék egyes dokumentációi még nem érhetők el a Microsoft Azure dokumentációs webhelyén. A [fürtkonfigurációs útmutatóra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) és a [fürtlétrehozási útmutató](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) örökölt verziójára mutató hivatkozások egy külön GitHub-üzemeltetett webhelyre vezetnek. 

* SMB konfigurálása

  Ha engedélyezni szeretné az SMB-hozzáférést a fürthöz és az NFS-hez, konfigurálnia kell az SMB-t, és be kell kapcsolnia. Az SMB (más néven CIFS) általában a Microsoft Windows ügyfelek támogatására szolgál.

  Az SMB tervezése és konfigurálása több, mint néhány gomb kattintása a Vezérlőpulton. A rendszer követelményeitől függően az SMB befolyásolhatja az alapvető fájlkezelők definiálásának módját, a létrehozott v-kiszolgálók számát, a csomópontok és névtér konfigurálását, a hozzáférési engedélyeket és egyéb beállításokat.

  További információt a Fürtkonfigurációs útmutató Az [SMB Access konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) című szakaszban talál.

* További licencek telepítése

  Ha az Azure Blobtól eltérő felhőalapú tárhelyet szeretne használni, telepítenie kell egy további szolgáltatáslicencet. A FlashCloud<sup>TM-licenc</sup> megvásárlásával kapcsolatos részletekért forduljon a Microsoft képviselőjéhez. A részleteket a [Háttérrendszerbeli tárolás hozzáadása és a virtuális névtér konfigurálása](fxt-add-storage.md#about-back-end-storage)című részben ismertetik.


### <a name="enable-support"></a>Támogatás engedélyezése

Az Azure FXT Edge Filer-fürt automatikusan feltöltheti a fürt támogatási adatait. Ezek a feltöltések lehetővé teszik a személyzet számára, hogy a lehető legjobb ügyfélszolgálatot nyújtsa.

A támogatási feltöltések beállításához kövesse az alábbi lépéseket.

1. Nyissa meg a > **Fürttámogatás** beállításai lapot. **Cluster** Fogadja el az adatvédelmi szabályzatot. 

   ![Képernyőkép a Vezérlőpultról és a Megerősítés gombbal az adatvédelmi szabályzat elfogadásához](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kattintson a csoport kibontásához kattintson az **Ügyféladatok** tól balra lévő háromszögre.
1. Kattintson a **feltöltési adatok újraérvényesítése** gombra.
1. Állítsa be a fürt támogatási nevét **az egyedi fürtnévben** – győződjön meg arról, hogy a fürt egyedileg azonosítja a fürtöt a támogató személyzet számára.
1. Jelölje be a **statisztikai figyelés,** az **általános információfeltöltés**és az **összeomlási adatok feltöltése jelölőnégyzetet.**
1. Kattintson a **Küldés gombra.**  

   ![Képernyőkép a támogatási beállítások lap kitöltött ügyféladatok at tartalmazó szakaszáról](media/fxt-cluster-create/fxt-support-info.png)

1. Kattintson a biztonságos **proaktív támogatás (SPS)** bal oldalán lévő háromszögre a szakasz kibontásához.
1. Jelölje be az **SPS-kapcsolat engedélyezése jelölőnégyzetet.**
1. Kattintson a **Küldés gombra.**

   ![A támogatási beállítások lapján a biztonságos proaktív támogatás szakaszt tartalmazó képernyőkép](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>További lépések

Miután létrehozta az alapfürtöt, és elfogadta az adatvédelmi szabályzatot, adja hozzá a fürt többi csomópontját. 

> [!div class="nextstepaction"]
> [Fürtcsomópontok hozzáadása](fxt-add-nodes.md)
