---
title: A Microsoft Azure FXT Edge Filer fürt létrehozása
description: Hibrid tárolási gyorsítótár-fürt létrehozása az Azure FXT Edge Filer a
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 94ec2b088940f4f1f683a4f88ae312879d909bc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543541"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Oktatóanyag: Az Azure FXT Edge Filer fürt létrehozása

Miután telepítette és az Azure FXT Edge Filer hardvercsomópont inicializálása a gyorsítótárhoz, használja a FXT fürt szoftvert a gyorsítótár-fürt létrehozásához. 

Ez az oktatóanyag végigvezeti a fürtöt hoz létre a hardveres csomópontok konfigurálásához szükséges lépésekről. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Milyen információ van szükség a fürt létrehozása előtt
> * A fürt felügyeleti hálózat, a fürthálózat és az ügyfél felé irányuló hálózat közötti különbség
> * Hogyan lehet csatlakozni egy fürt csomópontja 
> * Hogyan hozhat létre egy kezdeti fürtöt egy Azure FXT Edge Filer node használatával
> * Bejelentkezés a fürt Vezérlőpult a fürt-beállítások konfigurálása

Ez az eljárás 15 tart és 45 perc, attól függően, mekkora kutatási kell tennie, azonosítsa az IP-címek és hálózati erőforrások.

## <a name="prerequisites"></a>Előfeltételek

Végezze el ezeket az előfeltételeket az oktatóanyag elindítása előtt:

* Telepítse az Azure FXT Edge Filer hardverrendszer az Adatközpont 

  Csak a fürt létrehozásához egy csomópont van szükség, de kell [vegyen fel legalább két, több csomópontot](fxt-add-nodes.md) ahhoz, hogy a fürt konfigurálása és lekérése készen áll a használatra. 

* Megfelelő teljesítmény és a hálózati kábel csatlakozni a rendszer  
* Legalább egy Azure FXT Edge Filer csomóponton energiagazdálkodási és [a gyökér szintű jelszó beállítása](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Gyűjtse össze a fürt adatai 

A következő információkat az Azure FXT Edge Filer fürt létrehozására lesz szüksége:

* Fürt neve

* Rendszergazdai jelszó a fürt beállítása

* IP-címek:

  * Fürt kezelése, és a hálózati maszk és a felügyeleti hálózathoz tartozó útválasztó egyetlen IP-cím
  * Az első és utolsó IP-címek olyan egybefüggő IP-címtartományt a fürtkommunikációhoz (csomópontok). Lásd: [IP-cím kiosztása](#ip-address-distribution), az alábbi részleteket. 
  * (Ügyféloldali IP-címek fürt létrehozása után lehet beállítani.)

* Hálózati infrastruktúra információkat:

  * A fürt DNS-kiszolgáló IP-címe
  * A fürt nevét, a DNS-tartomány
  * A nevét vagy az NTP-kiszolgálók (egyetlen kiszolgáló, vagy három vagy több) a fürt IP-címe 
  * E IEEE 802.1ax engedélyezni szeretné-2008 hivatkozásra a fürt adapteren összesítés
  * Ha engedélyezi a hivatkozást az összesítés, használja az IEEE 802.3ad (LACP) kívánja-e dinamikus összesítés

A hálózati infrastruktúra-elemek is beállíthatja, miután a fürt létrehozása, de célszerűbb a létrehozáskor kell tennie. 

### <a name="ip-address-distribution"></a>IP-cím kiosztása

Az Azure FXT Edge Filer hibrid tárolási gyorsítótár fürt IP-címeket használ három kategóriába:

* Felügyeleti IP: A fürtkezelésért egyetlen IP-cím

  Ez a cím a belépési pont a fürt konfiguráció segédprogramok (a webes Vezérlőpulton vagy az XML-RPC API-t) elérésére szolgál. Ez a cím automatikusan hozzá lesz rendelve a fürt az elsődleges csomópont, és áthelyezi azt automatikusan, ha az elsődleges csomópont megváltozik.

  Más IP-címek segítségével eléréséhez a Vezérlőpulton, de a felügyeleti IP-cím célja, hogy hozzáférést biztosítson, még akkor is, ha az egyes csomópontok feladatátvételét.

* Fürthálózat: A fürtkommunikációhoz olyan IP-címek

  A fürt hálózati kommunikáció fürt csomópontjai között és lekérni a fájlokat a háttér tárolóból (core kiemelik) használatos.

  **Ajánlott eljárás:** Foglaljon le egy IP-címet minden egyes Azure FXT Edge Filer csomóponton fürtkommunikációhoz használatos fizikai port száma. A fürt automatikusan hozzárendeli a megadott tartományban a címek az egyes csomópontokhoz.

* Ügyfél felé irányuló hálózat: Az IP-címek kérelem és írási fájlok használó ügyfelek

  Az ügyfél hálózati címek használják az ügyfelek a core filer adatok eléréséhez a fürtön keresztül. Például egy NFS-ügyfél csatlakoztatása előfordulhat, hogy ezek a címek közül.

  **Ajánlott eljárás:** Foglaljon le egy IP-címet minden egyes FXT sorozat csomóponton ügyfél-kommunikációhoz használt fizikai port száma.

  A fürt lehető legegyenletesebben elosztja ügyfél által használt IP-címek között a különböző csomópontokat.

  Az egyszerűség kedvéért számos rendszergazda ciklikus időszeletelési DNS (RRDNS) konfigurációval könnyebb ügyfélkérelmek szét a címtartomány egy egyetlen DNS-nevét konfigurálja. A telepítő emellett lehetővé teszi minden ügyfél használja ugyanazt a csatlakoztatási parancsot a fürthöz való hozzáféréshez. Olvasási [DNS konfigurálása](fxt-configure-network.md#configure-dns-for-load-balancing) további információt.

A felügyeleti IP-cím és a egy fürt hálózati címtartományát meg kell adni hozzon létre egy új fürtöt. Fürt létrehozása után az ügyfél által használt címek meg van adva.

## <a name="connect-to-the-first-node"></a>Az első a csomóponthoz csatlakozás

A telepített FXT csomópontokon csatlakozzon, és az operációs rendszer szoftvert használja a fürt beállításához.

Ha még nem tette meg, így a legalább az egyik a FXT csomópontokat a fürthöz, a power és ellenőrizze, hogy a hálózati kapcsolat és a egy IP-címet. Egy új, aktiválja a csomópont, ezért kövesse a legfelső szintű jelszót kell beállítani [hardver jelszó](fxt-node-password.md) Ha rendelkezik nem tette meg.

Ellenőrizze a hálózati kapcsolatot, győződjön meg arról, hogy a csomópont hálózati kapcsolat LED-ek bocsát ki (és szükség esetén a mutatók, a hálózati kapcsoló, amelyhez kapcsolódik). Jelző LED-ek ismertetett [figyelő Azure FXT Edge Filer hardverállapot](fxt-monitor.md).

A csomópont indulásakor, akkor kérést küld egy IP-címet. Ha egy DHCP-kiszolgáló csatlakozik, a DHCP által megadott IP-cím fogad el. (Az IP-címet az ideiglenes. A befejezést a fürt létrehozásakor.)

Ha egy DHCP-kiszolgáló nincs csatlakoztatva, vagy nem kapott választ, a csomópont használatával Bonjour szoftver önálló hozzárendelt IP-cím beállításának 169.254 formájában. \*. \*. Azonban kell beállítania egy ideiglenes statikus IP-címet egy, a csomópont hálózati kártyák és a egy fürt létrehozása. Útmutatás a régi dokumentum; szerepelnek forduljon a Microsoft Service és a támogatási frissített információt: [A függelék: Egy statikus IP-cím beállítása egy FXT csomóponton](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Az IP-cím keresése

Csatlakozzon a Azure FXT Edge Filer csomópont található az IP-címét. Használja a soros kábelt, USB- és a VGA portokon, hogy közvetlen kapcsolat, vagy egy KVM kapcsolón keresztül. (Részletek: port kapcsolat [kezdeti jelszó](fxt-node-password.md).)

A csatlakozás után jelentkezzen be a username `root` és a jelszót állíthat be, amikor a csomópontot először indul el.  

Miután bejelentkezett, meg kell határoznia a csomópont IP-címet.

A parancs használata `ifconfig` ebbe a rendszerbe rendelt címek.

Ha például a parancs `ifconfig | grep -B5 inet` internetcímek portok keres, és a port azonosító megjelenítése helyi öt sornyi biztosít.

Jegyezze fel a ifconfig jelentésben szereplő IP-címeket. Port nevekkel-címek, például e0a vagy e0b megfelelő lehetőség. Ne használja a felsorolt e7 * nevekkel, mivel ezeket a neveket csak használt portok iDRAC/IPMI-szolgáltatás tetszőleges IP-címet.  

## <a name="load-the-cluster-configuration-wizard"></a>A konfigurációs varázsló betöltése

A böngésző alapú fürt konfigurálása eszköz használatával hozza létre a fürtöt. 

Adja meg az IP-címet egy webböngészőben a csomópontot. Ha a böngészőben egy üzenetet ad a webhelyet, hogy nem megbízható, folytassa a hely ennek ellenére. (Az egyes Azure FXT Edge Filer csomópontok nem rendelkeznek Hitelesítésszolgáltatói által biztosított biztonsági tanúsítványok.)

![Vezérlő panel bejelentkezési oldal böngészőablakban](media/fxt-cluster-create/unconfigured-node-gui.png)

Hagyja a **felhasználónév** és **jelszó** mező üres. Kattintson a **bejelentkezési** a fürt létrehozása lap betöltéséhez.

![A böngésző alapú grafikus felhasználói Felülettel Vezérlőpult egy nem konfigurált csomópont kezdeti beállítása képernyőre. Beállítások frissítése, manuálisan konfigurálja a fürt vagy a telepítőfájlt a fürt konfigurálása jeleníti meg.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>A fürt létrehozása

A fürt konfigurálása eszköz végigvezeti a képernyők létrehozása az Azure FXT Edge Filer fürt készletét. Ellenőrizze, hogy a [a szükséges adatok](#gather-information-for-the-cluster) kész megkezdése előtt. 

### <a name="creation-options"></a>Létrehozási beállítások

Az első képernyőn a három lehetőséget kínál. A manuális konfigurációs beállítást használja, kivéve, ha speciális utasítások a támogatási csapattal.

Kattintson a **fogom saját kezűleg konfigurálni a fürt** betölteni az új fürt konfigurációs beállítások képernyő. 

A többi beállítást a ritkán használt:

* "A rendszerkép frissítése" kéri, hogy az operációs rendszer új szoftver telepítése a fürt létrehozása előtt. (A jelenleg telepített szoftververzió jelenik meg, a képernyő felső részén.) Meg kell adnia a szoftverfrissítési fájlban – vagy egy URL-cím és a felhasználónév/jelszó vagy feltölt egy fájlt a számítógépről. 

* A Microsoft ügyfélszolgálata és a fürt telepítési fájl kapcsoló néha használja. 

## <a name="cluster-options"></a>Fürtbeállítások

A következő képernyőn kéri, hogy a beállításokat adhat meg az új fürthöz.

A lap két fő szakaszra oszlik **alapszintű konfigurációs** és **hálózati konfigurációk**. A hálózati konfigurációs szakasz is struktúrát tartalmaz: egyet a **felügyeleti** hálózatnak és a **fürt** hálózati.

### <a name="basic-configuration"></a>Alapkonfiguráció

A felső területen adja meg az új fürt alapvető adatait.

![Böngészőlap grafikus felhasználói Felülettel "Alapszintű konfiguráció" szakasz részletét. Három mezőt jeleníti meg (a fürt nevét, a rendszergazdai jelszót, erősítse meg a jelszót)](media/fxt-cluster-create/basic-configuration.png) 

* **Fürt neve** – adjon meg egy egyedi nevet a fürt.

  A fürt neve ezeknek a feltételeknek kell megfelelnie:
  
  * 1 – 16 karakterből kell állnia
  * Betűket, számokat, és a kötőjel (-) és aláhúzásjelet (_) karaktereket tartalmazhat 
  * Nem tartalmazhatnak más írásjelek vagy a speciális karakterek
  
  Ez a név később módosíthatja a **fürt** > **általános beállítások** konfigurációs lapon. (Fürt beállításaival kapcsolatos további információkért olvassa el a [fürt beállítási útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), amely nem része a dokumentációkészlet.)

  > [!NOTE] 
  > A fürt nevét rendszer-információkat, így hasznos lehet a cég emblémájának monitoring, vagy a hibaelhárítás támogatása érdekében feltöltött azonosítására szolgál.

* **Rendszergazdai jelszó** – állítsa be a jelszót az alapértelmezett rendszergazdai felhasználóhoz, `admin`.
  
  Állítson be egyéni felhasználói fiókokhoz minden felhasználó számára a fürt felügyeli, de nem tudja eltávolítani a felhasználót `admin`. Jelentkezzen be, `admin` Ha szeretne további felhasználókat létrehozni.
 
  Módosíthatja a jelszavát `admin` a a **felügyeleti** > **felhasználók** beállítások lapon a Vezérlőpult fürt. További információkért olvassa el a **felhasználók** dokumentációjában találhatók a [fürt beállítási útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Hálózati konfiguráció

A **hálózatkezelés** szakasz felszólítja, hogy adja meg a hálózati infrastruktúra, amely a fürt fog használni. 

Nincsenek konfigurálása két különálló hálózattal:

* A *felügyeleti hálózat* konfiguráláshoz és megfigyeléshez a fürt rendszergazdai hozzáférést biztosít. Az itt megadott IP-cím akkor használatos, ha a csatlakozás a Vezérlőpulton vagy az SSH-hozzáférést. 

  A legtöbb fürt csak egyetlen felügyeleti IP-címet használjon, de ha szeretne hozzáadni a felületek megteheti a fürt létrehozását követően.

* A *fürthálózat* fürt csomópontjai között és a fürtcsomópontok és a háttér-tároló (core kiemelik) közötti kommunikációra lesz használatos.

A ügyfél felé irányuló hálózat később van konfigurálva, a fürt létrehozása után.

Ez a szakasz a DNS és az NTP-kiszolgálók mindkét hálózat által használt konfigurációs is tartalmaz.

### <a name="configure-the-management-network"></a>A felügyeleti hálózat konfigurálása

A beállítások a **felügyeleti** szakasz is a hálózat, amely a fürt rendszergazdai hozzáférést biztosít.

!["Kezelés" szakaszban 5 beállítások mezőket és a egy 1 GB-os felügyeleti hálózathoz tartozó jelölőnégyzet – részletes nézet](media/fxt-cluster-create/management-network-filled.png)

* **Felügyeleti IP** -Vezérlőpult-fürt eléréséhez használt IP-címét. Ez a cím lesz nem engedte, hogy a fürt elsődleges csomópontjának, de automatikusan átkerül a kifogástalan állapotú csomópontra az, ha az eredeti elsődleges csomópont elérhetetlenné válik.

  A legtöbb fürt csak egy felügyeleti IP-cím használatára. Ha azt szeretné, egynél több, hozzáadhatja őket a a fürt létrehozása után a **fürt** > **felügyeleti hálózati** beállítások lapon. További információ: a [fürt beállítási útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Hálózati maszk** – adja meg a a felügyeleti hálózathoz tartozó hálózati maszkot.

* **Útválasztó** – adja meg az alapértelmezett átjáró címét, a felügyeleti hálózat által használt.

* **(Nem kötelező) a VLAN-címkék** – Ha a fürt használ VLAN-címkék, adja meg a címke a felügyeleti hálózat.

  További VLAN beállításait konfigurálja a **fürt** > **VLAN** beállítások lapon. Olvasási [VLAN-OK használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) és [fürt > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) az útmutató a fürt konfiguráció további.

* **MTU** – szükség esetén módosítsa a maximális átviteli egység (MTU) a fürt felügyeleti hálózat.

* **Használat 1 GB-os mgmt hálózati** -bejelöli ezt a jelölőnégyzetet, ha a két 1 gbe hálózati portok a FXT csomópontokon hozzárendelendő csak a felügyeleti hálózathoz. (Az összes többi forgalom elérhető 25GbE/darab 10 gbe-portok kell rendelkeznie.) Ha nem jelöli be ezt a jelölőnégyzetet, a felügyeleti hálózat elérhető legnagyobb sebességű portot használja. 

### <a name="configure-the-cluster-network"></a>Konfigurálja a fürthálózatot 

Fürt csomópontjai között, valamint a fürtcsomópontok és a core kiemelik közötti forgalom a fürthálózati beállításokat alkalmazni.

![a "Fürt" szakasz, hat értéket is megadhat mezőkkel részletei](media/fxt-cluster-create/cluster-network-filled.png)

* **Első IP-cím** és **utolsó IP** – adja meg az IP-címek, amelyek meghatározzák a címtartományt, amely a fürtön belüli kommunikációhoz. Az itt használt IP-címek összefüggő és a DHCP nem hozzárendelt kell lennie.

  A fürt létrehozását követően további IP-címek is hozzáadhat. Használja a **fürt** > **fürthálózatok** beállítások lap ([fürt beállítási útmutató dokumentáció](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Az értéket **szám IP-címtartomány a** kiszámítása és automatikusan látható.

* **(Nem kötelező) a nem-mgmt-maszk** – adja meg a hálózati maszkot, a fürt hálózatra. 

  A rendszer automatikusan javasol a hálózati maszk értéke a felügyeleti hálózat; megadott Ha szükséges, módosítsa azt.

* **(Nem kötelező) a fürt útválasztó** – adja meg az alapértelmezett átjáró címét, a fürt hálózat által használt. 

  A rendszer képes automatikusan észlelni az azonos átjárócímet a felügyeleti hálózathoz tartozó megadott.

* **VLAN-címke (nem kötelező) a fürt** – Ha a fürt használ VLAN-címkék és a fürt hálózati címkéjének megadása.

* **(Nem kötelező) a nem-mgmt-MTU** – szükség esetén módosítsa a fürt hálózati maximális átviteli egység (MTU).

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurálja a DNS és az NTP-fürt 

Alább a **fürt** szakaszban van olyan mezők megadása a DNS és az NTP-kiszolgálók, valamint a hivatkozást az összesítés engedélyezése. Ezek a beállítások minden hálózatból elérhető, amely a fürt használ a alkalmazni.

![Szakasz DNS/NTP-konfiguráció, a DNS-kiszolgálók, a DNS-tartomány és a DNS-keresési mezőket, három mezőt az NTP-kiszolgáló három mezőt részleteit és a egy hivatkozás összesítési lehetőségek a legördülő menü](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-kiszolgáló (ko)** – adja meg az IP-cím, egy vagy több tartománynév rendszer DNS-kiszolgáló.

  DNS összes fürtök esetén ajánlott és szükséges, hogy szeretné-e az SMB-AD- vagy Kerberos. 
  
  Az optimális teljesítmény érdekében a Ciklikus időszeleteléses terheléselosztás leírtak szerint a fürt DNS-kiszolgáló konfigurálása [DNS konfigurálása az Azure FXT Edge Filer fürt](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-tartomány** – adja meg a tartomány nevét a fürt fog használni.

* **DNS-keresési** : nem kötelezően, adja meg a DNS-lekérdezéseket a rendszer keressen további tartománynevekkel. Legfeljebb hat tartományneveket, szóközzel elválasztva adhat hozzá.

* **NTP-kiszolgáló (ko)** -megfelelő mezőkben adja meg egy vagy három network time protocol (NTP) kiszolgálók. Állomásnevek vagy IP-címeket is használhatja.

* **Hivatkozás összesítési** -hivatkozás összesítési lehetővé teszi, hogy hogyan az ethernet-port a fürtcsomópontokon FXT kezelni a különböző típusú forgalom testreszabása. További tudnivalókért olvassa el a [hivatkozás összesítési](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) az útmutató a fürt konfiguráció.

### <a name="click-the-create-button"></a>Kattintson a Létrehozás gombra

Biztosítja a szükséges beállításokat a képernyőn, kattintson a **-fürt létrehozása** gombra.

![a kurzor fölé a Létrehozás gombra a jobb alsó sarokban található befejezett űrlap alsó](media/fxt-cluster-create/create-cluster.png)

A rendszer üzenetet jelenít meg a fürt létrehozásakor.

![fürt konfigurációs állapot üzenet a böngészőben: "A FXT csomópont most létrehozza a fürtöt. Ez eltarthat néhány percig. Ha a fürt létrejött, keresse fel erre a hivatkozásra kattintva a konfigurálás befejezéséhez." a hivatkozás a "a Microsoft a hivatkozás"](media/fxt-cluster-create/creating-message.png)

Néhány pillanat múlva nyissa meg a fürt Vezérlőpulton található hivatkozásra kattinthat. (Ez a hivatkozás megnyílik az IP-cím, amelyet a **felügyeleti IP**.) A hivatkozás egy perc alatt válik aktívvá, a Létrehozás gombra kattintás után 15 másodpercet vesz igénybe. Ha a webes felületen nem töltődik be, várjon néhány másodpercet, és kattintson újra a hivatkozásra. 

Fürt létrehozása eltarthat egy-két percig, de bejelentkezhet a Vezérlőpulton, amíg a folyamat történik. Előfordulhat, hogy a Vezérlőpult irányítópult-oldalon, a Fürtlétrehozási folyamat befejezéséig figyelmeztetések megjelenítése. 

## <a name="open-the-settings-pages"></a>Nyissa meg a beállítások oldal 

A fürt létrehozása után kell testre szabhatja a hálózat és a munkafolyamat konfigurációjában. 

A Vezérlőpult webes felület segítségével állítsa be az új fürthöz. Kövesse a hivatkozást a fürt létrehozási állapota képernyőjén, vagy tallózással keresse meg a felügyeleti IP-címet állíthat be a fürtön.

Jelentkezzen be a felhasználónevet használva a webes felületén `admin` és a jelszót, megadhatja, ha a fürt létrehozásához.

![a webböngésző vezérlő megjelenítése a bejelentkezési mezők panel](media/fxt-cluster-create/admin-login.png)

A Vezérlőpult megnyílik, és megjeleníti a **irányítópult** lapot. A fürt létrehozása az előadások, a figyelmeztető üzeneteket és a törölje.

Kattintson a **beállítások** lap segítségével konfigurálhatja a fürt.

Az a **beállítások** lapon, a bal oldali oldalsáv látható a konfigurációs lapok tartalmazó menü. Az oldalak kategória szerint vannak rendszerezve. Kattintson a + vagy - vezérlőelem az eszközkategória-név, bővítését, vagy elrejtheti az egyes oldalak tetején.

![Beállítások lapon a Vezérlőpult (a böngészőben) a fürt > betölteni általános beállítások lapja](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Fürt beállítási lépéseket

Ezen a ponton a folyamat a fürt létezik, de csak egy csomópont sem ügyfél által használt IP-címek és nincs háttér-tárhely rendelkezik. Nyissa meg az újonnan létrehozott fürt, amely kezeli a munkafolyamat készen áll a gyorsítótár rendszer további beállítási lépések szükségesek.

### <a name="required-configuration"></a>Szükséges konfiguráció

Ezeket a lépéseket a legtöbb vagy összes fürtök esetén van szükség. 

* Csomópontok hozzáadása a fürthöz 

  Három csomóponttal standard szintű, de sok éles fürtökben rendelkezik – legfeljebb 24 csomópontok.

  Olvasási [fürtcsomópontok hozzáadása](fxt-add-nodes.md) megtudhatja, hogyan más Azure FXT Edge Filer egységek hozzáadása a fürthöz, és a magas rendelkezésre állás engedélyezéséhez.

* Adja meg a háttér-tároló

  Adjon hozzá *filer alapvető* definíciók minden egyes tárolási háttér-rendszerhez, amelyet a fürt használni fog. Olvasási [háttér-tároló hozzáadása és konfigurálása virtuális névtér](fxt-add-storage.md#about-back-end-storage) további.

* Ügyfél-hozzáférési és a virtuális névtér beállítása 

  Hozzon létre legalább egy virtuális kiszolgáló (vserver), és rendelje hozzá egy IP-címtartományt ügyfélszámítógépeknél használatára. Emellett konfigurálnia kell a fürt névtér (más néven a globális Namespace vagy GNS), a virtuális fájlrendszer funkciót, amellyel háttér-tárolási exportálások leképezése virtuális elérési utak. A fürt névteret biztosít az ügyfelek egy egységes és elérhető fájlrendszer struktúra még akkor is, ha váltson át a háttér-tárolást kínál. A névtér is megadhat egy felhasználóbarát virtuális tárolási hierarchiában, az Azure Blob-tárolók vagy más támogatott felhőalapú tárolás.

  Olvasási [konfigurálása a névtér](fxt-add-storage.md#configure-the-namespace) részleteiről. Ez a lépés tartalmazza:
  * Vservers létrehozása
  * Az ügyfél hálózati nézet és a háttér-tárolás között elhelyezni pontokra beállítása 
  * Meghatározása az ügyfél IP-címek által üzemeltetett mindegyik vserver

  > [!Note] 
  > Jelentős tervezési állíthatja be a fürt GNS megkezdése előtt ajánlott. Olvassa el a [használatával egy globális Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) és [létrehozásának és használatának VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) szakaszai a fürt beállítási útmutató a Súgó.

* [Hálózati beállítások módosítása](fxt-configure-network.md)

  Nincsenek több hálózattal kapcsolatos beállításokat kell ellenőrizni, vagy testre szabott egy új fürt számára. Olvasási [módosítsa a hálózati beállításokat](fxt-configure-network.md) ezek az elemek részleteit:

  * A DNS és az NTP-konfigurációjának ellenőrzése 
  * Címtárszolgáltatások, konfigurálása, ha szükséges 
  * Virtuális helyi hálózatok beállítása
  * Proxy-kiszolgálók konfigurálása
  * A fürt hálózati IP-címek hozzáadása
  * Titkosítási tanúsítványok tárolása

* [Támogatási figyelés beállítása](#enable-support)

  El kell fogadnia az adatvédelmi szabályzatot, az a konfigurációs eszközt, és a támogatási feltöltési beállításokat konfigurálnia kell egy időben.

  A fürt automatikusan feltöltheti a fürthöz, többek között a statisztikákat, és a hibakeresés fájlokat hibaelhárítási adatait. Ezek a feltöltések lehetővé teszik a Microsoft ügyfélszolgálatát, és adja meg a lehető legjobb szolgáltatás. Testre szabható, mit figyel, és opcionálisan engedélyezheti a proaktív támogatási és hibaelhárítási távoli szolgáltatás.  

### <a name="optional-configuration"></a>Választható konfiguráció

Ezeket a lépéseket minden fürtök esetében nem szükségesek. Ezek szükségesek a munkafolyamatok, vagy az egyes stílusok a fürt felügyeleti bizonyos típusú. 

* Csomópont-beállítások testreszabása

  Állítsa be a csomópont nevét, és a csomópont IPMI portok konfigurálása a fürtre kiterjedő szinttel, vagy külön-külön. Ha a fürt csomópontok hozzáadása előtt ezeket a beállításokat konfigurálja, az új csomópontok folytathatja a munkát a beállítások automatikusan, amikor azok csatlakoznak. A lehetőségek a régi fürt létrehozásának dokumentumban ismertetett [csomópont beállítások testreszabása](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > A termék egyes dokumentumokban még nem áll rendelkezésre a Microsoft Azure dokumentációs oldalon. Hivatkozásokat tartalmaz a [fürt beállítási útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) és a régi verziója a [fürt létrehozási útmutató](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) külön GitHub által üzemeltetett webhely léphet. 

* SMB konfigurálása

  Ha azt szeretné, SMB férhessenek hozzá a fürtöt, valamint az NFS, SMB konfigurálása kell és bekapcsolásához. Az SMB (más néven CIFS) általában használja a Microsoft Windows-ügyfelek támogatásához.

  Tervezéséről és konfigurálásáról az SMB magában foglalja a több, mint a Vezérlőpult néhány gombokra kattintva. A rendszer követelményeitől függően SMB befolyásolhatja, hogyan adhatja meg a core kiemelik, hogy hány vservers hoz létre, hogy hogyan konfigurálhat a elhelyezni pontokra és a névtér, a hozzáférési engedélyek és az egyéb beállításokat.

  További információkért olvassa el a fürt beállítási útmutató [SMB-hozzáférés konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) szakaszban.

* További licencek telepítése

  Ha szeretné használni a felhőalapú tárolás az Azure Blob eltérő, telepítenie kell egy további szolgáltatás-licencet. Egy FlashCloud megvásárlásával kapcsolatos részletekért forduljon a Microsoft helyi képviselőjéhez<sup>TM</sup> licenc. Részletes magyarázatához [háttér-tároló hozzáadása és konfigurálása virtuális névtér](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Támogatásának engedélyezése

Az Azure FXT Edge Filer fürt automatikusan feltöltheti a fürttel kapcsolatos támogatási adatok. Ezek a feltöltések lehetővé teszik, hogy a legjobb ügyfélszolgálati munkatársak.

Kövesse az alábbi lépéseket, állítsa be a támogatási feltöltések.

1. Keresse meg a **fürt** > **támogatási** beállítások lapon. Fogadja el az adatvédelmi nyilatkozatát. 

   ![Képernyőfelvétel: a Vezérlőpulton, és a megerősítés gombra az előugró ablakban fogadja el az adatvédelmi szabályzatot](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kattintson a háromszögre balra **Customer Info** a szakasz kibontásához.
1. Kattintson a **Revalidate feltöltési információk** gombra.
1. Állítsa be a fürt támogatási nevét **egyedi fürtnév** – ellenőrizze, hogy a fürt a támogató személyzet egyedileg azonosítja.
1. Jelölje be a **statisztikák figyelése**, **általános információkat feltöltése**, és **összeomlási adatokat feltölteni**.
1. Kattintson a **Submit** (Küldés) gombra.  

   ![Képernyőkép, amely tartalmazza a customer info szakaszban támogatási beállítások oldal befejeződött](media/fxt-cluster-create/fxt-support-info.png)

1. Kattintson a háromszögre balra **biztonságos proaktív támogatási (Szervizcsomagok)** a szakasz kibontásához.
1. Jelölje be a **Szervizcsomagok hivatkozás engedélyezése**.
1. Kattintson a **Submit** (Küldés) gombra.

   ![Befejezett proaktív támogatja biztonságos szakaszban támogatási beállítások oldalon tartalmazó képernyőképe](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>További lépések

Miután az alapszintű fürt létrehozása és elfogadta az adatvédelmi szabályzatot, a többi a fürtcsomópontok hozzáadása. 

> [!div class="nextstepaction"]
> [Fürtcsomópontok hozzáadása](fxt-add-nodes.md)
