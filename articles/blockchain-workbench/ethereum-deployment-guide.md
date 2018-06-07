---
title: Ethereum igazolása a munkahelyi konzorcium megoldás sablon
description: A Etherereum igazolása-az-munkahelyi konzorcium megoldás sablon használatával telepíthet és konfigurálhat egy többtagú konzorcium Ethereum hálózati
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 0ea9bd2c7d23aa227e62858983e7170b771751da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655120"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Ethereum igazolása a munkahelyi konzorcium megoldás sablon

A Ethereum igazolása-az-munkahelyi konzorcium megoldássablonban célja, hogy egyszerűbbé és gyorsabb telepítésére és konfigurálására egy többtagú konzorcium Ethereum hálózati minimális Azure és Ethereum ismeretekkel.

A felhasználói bevitel és az Azure portálon keresztül egy kattintással indítható telepítési skálázhatja minden tag építhető ki a hálózat erőforrásigényét, segítségével a Microsoft Azure számítási, hálózati és tárolási szolgáltatások világszerte. A tranzakció elosztott terhelésű csomópontok áll minden tag hálózat erőforrásigényét, amely egy alkalmazás vagy felhasználó kommunikációját elküldeni a tranzakciók, rögzítése tranzakciók adatbányászati csomópontokat, és a VPN-átjáró. A következő kapcsolódási lépés hozhat létre teljesen konfigurált többtagú blockchain hálózatot átjáró csatlakozik.

## <a name="about-blockchain"></a>Blockchain kapcsolatos

Azok is, akik nem rendelkeznek a blockchain Közösség a kiadás az ilyen típusú megoldásra további információt a technológia Azure könnyen és konfigurálható módon nagyszerű lehetőséget. Első lépésként javasoljuk azonban központi telepítése a egyszerűbb önálló Ethereum hálózati topológia a részletes útmutató, mielőtt többtagú konzorcium hálózatok létrehozására.

### <a name="mining-node-details"></a>Adatbányászati csomópont részletei

A csomópontokra, amelyeket a tranzakciók enyém el legyen különítve a csomópontokat, amelyek fogadnak el tranzakciók győződjön meg arról, hogy a két műveletek nem verseng ugyanazokat az erőforrásokat.

Konzorcium tagja hozhat létre egy vagy több adatbányászati csomópontok, által felügyelt lemezes biztonsági másolatot tartalmazó legfeljebb öt régiók. Egy vagy több csomópontja régióban dinamikus felderíthetőség csomóponton a hálózat támogatásához rendszerindítás csomópontként van konfigurálva. Adatbányászati csomópontok kommunikálnak más adatbányászati csomópontok együttműködési tudomást az alapul szolgáló elosztott főkönyvi állapotának. Nincs szükség, vegye figyelembe, vagy ezek a csomópontok kommunikálnak az alkalmazáshoz. Előtérbe a magánhálózatokon, az adatbányászati csomópontok el különítve bejövő nyilvános internetes forgalmat egy másodlagos védelmi réteget. Kimenő forgalom engedélyezve van, de nem a Ethereum felderítési port.

A csomópontokat az Ugrás Ethereum (Geth) ügyfél stabil verzióját, és úgy van konfigurálva, hogy adatbányászati csomópontok. Ha nem adja meg egy egyéni képződés blokkot, minden csomópont használja, a címmel Ethereum és a kulcspár, amely védi a Ethereum fiók jelszavát. A megadott Ethereum jelszót használja az alapértelmezett fiókot (coinbase) készítéséhez minden adatbányászati csomóponthoz. Adatbányászati csomópontként enyém, ezt a fiókot hozzáadott díjak gyűjtenek.

Egy konzorcium tagnak adatbányászati csomópontok száma attól függ, hogy a szükséges hálózati mennyiségétől és a kivonatoló minden egyes tagjára dedikált power teljes méretét. Minél nagyobb ez a hálózat, a további csomópontokat, amely szükséges ahhoz, hogy a tisztességtelen előnyös megsértik. A sablon legfeljebb 15 adatbányászati csomópontok virtuálisgép-méretezési csoportok használó kiépítve régiónként támogatja.

### <a name="transaction-node-details"></a>Tranzakció csomópont részletei

Konzorcium tagja is rendelkezik egy elosztott terhelésű tranzakció csomópontok. Ezek a csomópontok érhetők el a a virtuális hálózaton kívül, hogy az alkalmazások használhatják ezeket a csomópontokat tranzakciók küldhetnek, vagy intelligens szerződések blockchain hálózaton belüli végrehajtását. Minden csomópont az Ugrás Ethereum (Geth) ügyfél stabil verzióját, valamint az elosztott főkönyvi teljes másolata karbantartása úgy vannak konfigurálva. Ha egy egyéni képződés blokk nem áll rendelkezésre, ezek a csomópontok ugyanazt Ethereum a fiókot használja, a Ethereum fiók jelszóval védett.

Tranzakció csomópontja a rendelkezésre állási készlet magas rendelkezésre állás fenntartása belül terhelésű. A sablon támogatja a virtuálisgép-méretezési csoportok használó kiépítve legfeljebb öt tranzakció csomópontok.

### <a name="log-analytics-details"></a>Elemzés részletes adatainak naplózása

Minden központi telepítési is létrehoz egy új Naplóelemzési példányt, vagy csatlakozhat egy meglévő példányát. Ez lehetővé teszi, hogy az egyes virtuális gépek, amelyek a telepített hálózati teljesítménymutatók megfigyelését.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

### <a name="description"></a>Leírás

Ez a megoldás sablon telepíthet egy vagy több régió-alapú több tag Ethereum konzorcium hálózati. A virtuális hálózat minden egyes régió csatlakozik-e a virtuális hálózat átjárók és a kapcsolat erőforrások lánc topológiában más régióban. Azt is látja el a regisztráló, amely minden régióban telepített összes bányászati és a tranzakciós csomópontok szükséges információkat tartalmazza.

### <a name="standalone-and-consortium-leader-overview"></a>Önálló és konzorcium vezető – áttekintés

![Önálló és konzorcium vezető – áttekintés](./media/ethereum-deployment-guide/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Csatlakozás konzorcium tag – áttekintés

![Csatlakozás konzorcium tag – áttekintés](./media/ethereum-deployment-guide/member-overview.png)

## <a name="getting-started"></a>Első lépések

Ehhez a folyamathoz is támogatja, több virtuálisgép-méretezési csoportok telepítését és a lemezek felügyelt Azure-előfizetés szükséges. Szükség esetén hozzon létre egy ingyenes Azure-fiók megkezdéséhez.

Előfizetés védelmének biztosítása után nyissa meg az Azure-portálhoz. Válassza ki **+ hozzon létre egy erőforrást**, piactér (lásd az összes), keresse meg a **Ethereum igazolása-az-munkahelyi konzorcium**.

A sablon-üzembehelyezés végigvezeti a hálózat az első tagtól erőforrásigényét konfigurálása. Az üzembe helyezési folyamat öt lépése oszlik: alapjai, az Operations Management Suite, a telepítési régiók, a hálózat mérete és a teljesítményt, Ethereum beállítások.

### <a name="basics"></a>Alapvető beállítások

A **alapjai**, adja meg a telepítést, például az előfizetés, erőforráscsoport és alapszintű virtuálisgép-tulajdonságokat a szabványos paraméter értékét.

![Alapvető beállítások](./media/ethereum-deployment-guide/sample-deployment.png)

Paraméter neve|Leírás| Megengedett értékek|Alapértelmezett értékek
---|---|---|---
Hozzon létre egy új vagy csatlakozás meglévő hálózatra?|Hozzon létre egy új hálózatot, vagy egy már meglévő konzorcium hálózati csatlakozás|Hozzon létre új illesztési meglévő|Új létrehozása
Konzorciuma része lesz a hálózat központi telepítésére?|Egy konzorcium hálózati lehetővé teszi, hogy a jövőben a központi telepítésekre ehhez a hálózathoz csatlakozik (látható, ha *hozzon létre új* fent kijelölt)|Önálló konzorcium|Különálló
Erőforrás-előtag |Karakterlánc alapjaként elnevezési erőforrások (2 – 4 alfanumerikus karakter). Egyedi kivonattal $a az egyes erőforrások karakterlánc, míg az erőforrás-specifikus adatait. a rendszer hozzáfűzi.|Alfanumerikus karakterből állhat, és hossza 2 – 4|NA
Virtuális gép felhasználónév| Rendszergazdai jogosultságú felhasználónevet, minden központilag telepített virtuális gép (csak alfanumerikus karaktereket)|1 – 64 karakter hosszúságú lehet |gethadmin
Hitelesítés típusa|A módszer a virtuális gép felé történő hitelesítésre. |Jelszó vagy SSH nyilvános kulcs|Jelszó
Jelszó (hitelesítési típus = jelszó)|A telepített virtuális gépek mindegyikének a rendszergazdai fiók jelszavát. A jelszónak tartalmaznia kell a 3. a következő követelményeknek: 1 nagybetűt, 1 kisbetűt, 1 számot vagy 1 különleges karaktert. <br />Bár minden virtuális gép kezdetben rendelkezik ugyanazzal a jelszóval, módosíthatja a jelszó kiépítése után.|12 - 72 karakter|NA
Az SSH-kulcsot (hitelesítési típus = nyilvános kulcs)|A biztonságos rendszerhéj kulcs távoli bejelentkezés.|| NA
Előfizetés| Az előfizetést, amelyhez a konzorcium hálózati telepítése||NA
Erőforráscsoport| Az erőforráscsoport, amelyre a konzorcium hálózati telepíteni szeretné.||NA
Hely| Az Azure-régió az erőforráscsoport. ||NA



### <a name="operations-management-suite"></a>Operations Management Suite

Az Operations Management Suite (OMS) panelen az OMS-erőforrás a hálózat konfigurálását teszi lehetővé. OMS gyűjt, és felület hasznos metrikákat és naplókat a hálózatról, így képes gyorsan ellenőrizheti, hogy a hálózat állapotát, vagy a hibakeresési problémák. Az OMS ingyenesen elérhető szabályosan meghiúsul, kapacitás elérésekor.

![Új OMS létrehozása](./media/ethereum-deployment-guide/new-oms.png)

Paraméter neve|Leírás| Megengedett értékek|Alapértelmezett értékek
---|---|---|---
Csatlakozás meglévő OMS|Új Naplóelemzési példány létrehozása vagy csatlakozás meglévő példány|Hozzon létre új illesztési meglévő|Hozzon létre új napló Analytics helyét|A régió, hova szeretné telepíteni az új Naplóelemzési (látható if *hozzon létre új* van kiválasztva)
Meglévő OMS-munkaterület azonosítója|A meglévő példány munkaterület azonosítója (látható if *csatlakozás meglévő* van kiválasztva) OMS szolgáltatási rétegben|Válassza ki a tarifacsomagot az új példány. További információ: https://azure.microsoft.com/pricing/details/log-analytics/ (látható if *csatlakozás meglévő* van kiválasztva)|Szabad önálló csomópontonként|Ingyenes
Meglévő OMS elsődleges kulcs|A meglévő OMS-példányhoz való csatlakozáshoz használt elsődleges kulcs (látható if *csatlakozás meglévő* van kiválasztva)

### <a name="deployment-regions"></a>Központi telepítés régiók

A következő **telepítési régiók**, adja meg a bemeneti adatok **régió(k) száma** központi telepítése a konzorcium hálózati és kiválasztása az Azure-régiók régiók megadott száma alapján. Felhasználó legfeljebb öt régiók helyezhet üzembe.

![Központi telepítés régiók](./media/ethereum-deployment-guide/deployment-regions.png)

Paraméter neve| Leírás| Megengedett értékek |Alapértelmezett értékek
---|---|---|---
Régió(k) száma| A konzorcium hálózati telepítendő régiók száma|1, 2, 3, 4, 5| 2
Első terület| A konzorcium hálózati telepítendő első terület|Az összes Azure-régiók engedélyezett| USA nyugati régiója
Második régió |Második régiót, ahová telepíteni a konzorcium hálózati (csak akkor, ha van kiválasztva régiók száma 2 látható)|Az összes Azure-régiók engedélyezett| USA keleti régiója
Harmadik régió| Harmadik régiót, ahová telepíteni a konzorcium hálózati (csak akkor, ha van kiválasztva régiók száma 3 látható)|Az összes Azure-régiók engedélyezett| USA középső régiója
Negyedik régió| Negyedik régiót, ahová telepíteni a konzorcium hálózati (csak akkor, ha több régióban van kiválasztva, mint 4 látható)|Az összes Azure-régiók engedélyezett| USA 2. keleti régiója
Ötödik régió| Ötödik régiót, ahová telepíteni a konzorcium hálózati (Visible csak akkor, ha több régióban van kiválasztva, mint 5)|Az összes Azure-régiók engedélyezett| USA nyugati régiója, 2.

### <a name="network-size-and-performance"></a>A hálózat méretétől és teljesítmény

A következő **hálózati mérete és a teljesítmény** adja meg a bemeneti adatokat a konzorcium hálózatokon, például száma és mérete adatbányászati csomópontok és a tranzakció csomópontok méretének.

![A hálózat méretétől és teljesítmény](./media/ethereum-deployment-guide/network-size-performance.png)

Paraméter neve |Leírás |Megengedett értékek| Alapértelmezett értékek
---|---|---|---
Adatbányászati csomópontok száma|A telepített régiónként adatbányászati csomópontok száma|2 - 15| 2
Adatbányászati csomópont tárolási teljesítménye|A telepített adatbányászati csomópontok biztonsági felügyelt lemezes típusa.|Standard vagy prémium|Standard
Adatbányászati csomópont virtuálisgép-méret|A virtuálisgép-méret adatbányászati csomópontok használatos.|Standard A <br />Standard D <br />Standard D-2-es <br />Standard F sorozat <br />Standard DS-ben <br />és a szabványos FS|Standard D1v2
Elosztott terhelésű tranzakció csomópontok száma|A hálózati részeként kiépítését tranzakció csomópontok száma.|1 - 5| 2
Tranzakció csomópont tárolási teljesítménye|A telepített tranzakció csomópontok biztonsági felügyelt lemezes típusa.|Standard vagy prémium|Standard
Tranzakció csomópont virtuálisgép-méret|A tranzakció csomópontok használt virtuális gép méretét.|Standard A <br />Standard D <br />Standard D-2-es <br />Standard F sorozat <br />Standard DS-ben <br />és a szabványos FS|Standard D1v2

### <a name="ethereum-settings"></a>Ethereum beállítások

A következő **Ethereum beállítások**, adja meg a Ethereum konfigurációs beállításokat, például a hálózati azonosító és Ethereum fiók jelszó vagy képződés blokkot.

![Ethereum beállítások](./media/ethereum-deployment-guide/standalone-leader-deployment.png)

Paraméter neve |Leírás |Megengedett értékek|Alapértelmezett értékek
---|---|---|---
ConsortiumMember azonosítója|Minden tag a segítségével konfigurálhatók az IP-címterek konzorcium hálózati ütközés elkerülése érdekében az azonosítója. <br /><br />Tagazonosító különböző szervezetek ugyanazon a hálózaton belül egyedinek kell lennie. Egy egyedi azonosító van szükség, akkor is, ha több régióba ugyanazon a szervezeten belül telepíti.<br /><br />Jegyezze fel a paraméter értékének óta azt megosztása más csatlakozó tagok szüksége lesz.|0 - 255
Ethereum hálózati azonosító|A hálózati Azonosítót a konzorcium Ethereum hálózati éppen telepítés alatt áll. Minden Ethereum hálózathoz a saját hálózati azonosító, a nyilvános hálózat azonosítója 1 folyamatban van. Adatbányászati csomópontok korlátozott hálózati hozzáférést pedig továbbra is ajánlott sok ütközések megelőzése érdekében.|5 - 999,999,999| 10101010
Egyéni képződés letiltása|Vagy automatikusan létrehozni egy képződés blokk, vagy adjon meg egy egyéni beállítás.|Igen/nem| Nem
Ethereum fiók jelszavát (egyéni képződés block = No)|A rendszergazdai jelszó védi a Ethereum fiók minden csomópont importálni. A jelszónak tartalmaznia kell a következőket: 1 nagybetűt, 1 kisbetűt, valamint 1 számát.|legalább 12 karaktert|NA
Titkos kulcs jelszava Ethereum (egyéni képződés block = No)|Az ECC létrehozott alapértelmezett Ethereum fiókhoz tartozó titkos kulcs létrehozásához használt jelszót. Egy előre létrehozott titkos kulcs nem kell explicit módon átadni.<br /><br />Fontolja meg egy hozzáférési kódot a megfelelő annak véletlenszerű erős titkos kulcs és egyéb konzorcium tagok nem átfedi biztosításához. A jelszó tartalmaznia kell legalább a következőket: 1 nagybetűt, 1 kisbetűt, valamint 1 számát.<br /><br />Vegye figyelembe, hogy két tagja használja ugyanazt a jelszót a fiókokhoz létrehozott ugyanaz lesz. Ugyanazt a jelszót akkor hasznos, ha egy szervezet különböző régiókban telepítése próbál, és szeretné osztani ugyanazt a fiókot (érme alap) az összes csomópont.|legalább 12 karaktert|NA
Képződés blokk (egyéni képződés block = Yes)|Egyéni képződés blokk megfelelő JSON-karakterláncot. A formátum a képződés blokk itt olvashat az egyéni hálózatok találja.<br /><br />Ethereum fiók továbbra is létrejön egy egyéni képződés blokk biztosítása. Vegye figyelembe, hogy egy prefunded Ethereum fiókot megadó a képződés blokk egyik változóját adatbányászati nem vár.|Érvényes JSON-ban |NA
Megosztott kapcsolat kulcsa|A virtuális hálózat átjárók közötti kapcsolat megosztott kulcsa.| legalább 12 karaktert|NA
Konzorcium adatok URL-címe|A megadott tag egy másik telepítés által érintett konzorcium adatok mutató URL-címet. <br /><br />Ezeket az információkat egy már csatlakoztatott tag, aki rendelkezik a központi telepítés biztosítja. Ha a hálózat többi részétől telepítette, az URL-cím a központi telepítés kimenete, KONZORCIUM-adatok.||NA
Virtuális hálózat átjáró való kapcsolódáshoz|Az erőforrás elérési útja, amelyhez csatlakozni kíván a virtuális hálózat átjáró.<br />Ezeket az információkat egy már csatlakoztatott tag, aki rendelkezik a központi telepítés biztosítja. Ha a hálózat többi részétől telepítette, az URL-cím van központi telepítési sablon kimeneti, CONSORTIUM_MEMBER_GATEWAY_ID nevű. Megjegyzés: Ugyanaz a tag konzorcium adatok URL-cím és a virtuális hálózat átjáró erőforrás kell használni.||NA
Társ információk regisztráló végpontja|Egy másik tag központi telepítés által megadott társ info végpont|A konzorcium első tag érvényes végpontot|NA
Kulcs a társ információk nyilvántartója|Társ elsődleges kulcsnak tag egy másik telepítés által biztosított|A konzorcium első tag érvényes elsődleges kulcs|NA

### <a name="summary"></a>Összegzés

Kattintson a összefoglaló panelt, tekintse át a megadott bemeneti adatok és alapvető központi telepítés előtti érvényesítési futtatásához.

![Összegzés](./media/ethereum-deployment-guide/summary.png)

Olvassa el a jogi tudnivalók és adatvédelem feltételeket és a **beszerzési** központi telepítéséhez. Ha a telepítés csak egy régió tartozik, vagy konzorcium hálózatok, majd a sablon előre telepíti a szükséges VPN-átjárót támogatja a hálózati kapcsolat más tagjaival. Az átjáró telepítési legfeljebb 45 50 percig is eltarthat.

## <a name="post-deployment-sanity-checks"></a>Feladás egy vagy több központi telepítési megerősítések

### <a name="administrator-page"></a>Rendszergazdai lap

Miután a telepítés sikeresen befejeződött, és minden erőforrás kiépített, lépjen a felügyeleti lapot a blockchain hálózatot, és megerősítést ellenőrizze a központi telepítés állapotát. A rendszergazda lap URL-címe a terheléselosztó; DNS-neve a sablon-üzembehelyezés ADMIN_SITE megnevezett kimeneti szakaszában telepítve.

Találja, válassza ki a telepített erőforrás-csoportot. Ezt követően válassza **áttekintése**, és kattintson a hivatkozásra alapján azonnal **központi telepítések** , amely a sikeresen telepített számát mutatja.

![Erőforrás csoport – áttekintés](./media/ethereum-deployment-guide/resource-overview.png)

Az új képernyőn látható a telepítési előzményeket. Válassza ki az első központi telepítési erőforrás (például a microsoft-azure-blockchain.azure-blockchain-szerviz...), és keresse meg a **kimenetek** című szakaszában találhat az oldal alján. Látni fogja, a felügyelet lapon a sablon telepítési kimeneti paraméter, ADMIN_SITE URL-CÍMÉT.

![Erőforrás központi telepítések](./media/ethereum-deployment-guide/resource-deployments.png)

![Központi telepítés kimeneti](./media/ethereum-deployment-guide/deployment-output.png)

Ahhoz, hogy az admin oldalra, másolja a **ADMIN-hely** kimeneti, majd nyissa meg egy másik lapon.

A felügyelet lapon kaphat a topológia a Ethereum csomópont állapot szakasz megtekintésével telepített magas szintű áttekintését. Ez magában foglalja az összes csomópont állomásnevek, az egyenrangú száma és a legújabb blokk látható. A társ száma az egyes csomópontok között a legalább egy van kisebb, mint a *csomópontok száma összesen* és a beállított maximális társ száma. Vegye figyelembe, hogy a társ száma nem korlátozza, amely telepíthető a hálózaton belüli csomópontok számát.
Alkalmanként jelenik meg a társ száma ingadozik, és lehet kisebb, mint a (teljes csomópontok száma - 1). Az a szám különbség nem mindig a jele, hogy a csomópontok nem kifogástalan,-e, mivel a főkönyvi elágazásokon okozhat a másodlagos változtatások társ-számot. Végezetül vizsgálhatja meg a elágazásokon vagy késedelmes jelentések meghatározni a rendszer a hálózat minden egyes csomópontja által látott legutóbbi blokkot.

![Csomópont állapota](./media/ethereum-deployment-guide/node-status.png)

A csomópont állapota frissül minden 10 másodperc. Frissítse az oldalt, a böngésző keresztül vagy **Újrabetöltés** gombra kattintva frissítse a nézetet.

### <a name="oms-portal"></a>OMS-portál

A központi telepítés kimenet (OMSPORTALURL) hivatkozásra vagy az OMS-erőforrás kiválasztja a telepített erőforráscsoport megkeresheti az OMS-portálon.

![OMS URL-CÍME](./media/ethereum-deployment-guide/oms-url.png)

![OMS-erőforrás](./media/ethereum-deployment-guide/oms-resource.png)

A portál először leírásaként magas szintű hálózati statisztikákat áttekintése.

![OMS áttekintése](./media/ethereum-deployment-guide/oms-overview.png)

Kattintson az Áttekintés a elvezeti Önt csomópontonként statisztika megjelenítése a portálra.

![Csomópont statisztikák](./media/ethereum-deployment-guide/per-node-stats.png)

### <a name="accessing-nodes"></a>Csomópontok elérése

Távolról csatlakozhat a virtuális gépek pedig a tranzakció csomópontok SSH-kapcsolaton keresztül a megadott rendszergazdai felhasználónév és jelszó vagy SSH-kulccsal. A tranzakció csomópont virtuális gépek nem rendelkeznek a saját nyilvános IP-címeket, mivel szüksége lesz a terheléselosztó keresztül halad, és adja meg a portszámot. Az SSH-parancs futtatása az első tranzakció csomópont eléréséhez szerepel, mint a sablon telepítési kimeneti paraméter **SSH_TO_FIRST_TX_NODE** (az üzembe helyezési minta: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Ahhoz, hogy további tranzakciós csomópontokra, egyesével a portszám (például az első tranzakció csomópont van 4000 portra).

Mivel a virtuális gépek, amelyen fut az adatbányászati csomópontok nem kívülről elérhető, halad át a tranzakció csomópontok egyikét szeretné. Ha már van egy tranzakció csomópont SSH-munkamenetet, telepítse a titkos kulcsot a tranzakció csomóponton, vagy a jelszó segítségével indítsa el a adatbányászati csomópontokon SSH-munkamenetet.

**Megjegyzés**

A gazdagép neve szerezhetők felügyeleti helyéről vagy az Azure-portálon. Azure-portálon, a csomópontok a virtuálisgép-méretezési készlet (VMSS) erőforrás szerepel a gazdagép neve megtalálható-e **példányok**, amely különbözik a tényleges gazdagép neve. Például az Azure-portálon állomásnév hasonlíthat **mn-asdfmv-reg1_0** , de a tényleges állomásnevét lenne például **mn-asdfmv-reg**.

Példa:

Az Azure portál állomásnév| Tényleges állomásnevét
---|---
Mn-ethwvu-reg1_0| Mn-ethwvu-reg1000000
Mn-ethwvu-reg1_1 |Mn-ethwvu-reg1000001
Mn-ethwvu-reg1_2 |Mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Egy új konzorcium tag hozzáadása

### <a name="sharing-data"></a>Adatok megosztása

Az első tagtól (vagy csatlakoztatott) konzorcium meg kell adnia a néhány adatra más tagjaival, hogy csatlakozzon és a kapcsolat létrehozásához. Konkrétan:

1. **Megosztott konzorcium konfigurációs adatok**: a adatkészletet, amellyel levezényelni a Ethereum kapcsolat két tagok között van. A szükséges információkat, beleértve a képződés blokkot, a konzorcium hálózati azonosító és a rendszerindító csomópontokat, a tranzakció csomópontokon a vezető vagy egy másik telepített tag fájlba írása. Ez a fájl helyét, a sablon telepítési kimeneti paraméter nevű szerepel **KONZORCIUM-adatok**.
2. **Információ végpont partnert**: A partner info regisztráló végpont lekérése az összes csomópont már a vezetők vagy egy másik tag telepítési kapcsolódik a Ethereum hálózathoz. Egy sor minden csomópont információhoz DB tárolókat kapcsolódik a hálózatra, például a csomópont állomásnév, privát IP-cím stb. Ez az a sablon telepítési kimeneti paraméter nevű **PEER_INFO_ENDPOINT**.
3. **Elsődleges kulcsnak partnert**: A partner regisztráló elsődleges kulcsnak a vezető vagy más tag társ elsődleges kulcsnak eléréséhez használatos. Ez az a sablon telepítési kimeneti paraméter nevű **PEER_INFO_PRIMARY_KEY**.


4. **Virtuális hálózat átjáró**: minden tag kapcsolatot hoz létre a teljes blockchain hálózaton keresztül meglévő tagjára. Szeretne csatlakozni a virtuális hálózat, le kell a virtuális hálózat átjáró, amely kapcsolódik a tag az erőforrás elérési útja. Ez az a sablon telepítési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Megosztott kulcs**: egy előre meghatározott titkos a konzorcium hálózati kapcsolatot létesít két tagok között. Ez az alfanumerikus karakterlánc (1 és 128 karakter), amely a központi telepítés kívül egyeztetett. (Például **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Új tag elfogadása

Ezzel a lépéssel kell hajtható végre, miután a csatlakozó tag sikeresen telepítette a hálózaton. Mielőtt tagja a hálózathoz, és tranzakciós hálózati forgalom, meglévő tagjára elfogadja a kapcsolatot a VPN-átjáró a végső konfigurációs kell végezni. Ez azt jelenti, hogy a csatlakozó tag Ethereum csomópontja nem fog futni, amíg létrejön a kapcsolat. Ez a konfiguráció PowerShell vagy xPlat parancssori felületen keresztül végezhető. PowerShell modul és xPlat CLI-parancsfájlt is tárolják a tranzakció csomóponton a konzorcium adatok mellett. A parancsfájl elérési útja a központi telepítés a kimeneti paraméterek nevű **PÁR-ÁTJÁRÓ – PS-modul** és **PÁR-ÁTJÁRÓ-AZURE-CLISCRIPT**, illetve.

**PowerShell/CLI beállítása**

További információt Ismerkedés az Azure PowerShell-parancsmagok és Azure xPlat parancssori felület Azure dokumentációjában találhatók.

Szüksége lesz a legújabb verzióját a helyileg telepített Azure parancsmagok és a munkamenet megnyitása. Ügyeljen arra, hogy jelentkezzen be a munkamenet a Azure-előfizetés hitelesítő adataival.

**PowerShell: Kapcsolat**

A PowerShell-modul letöltése és helyileg tárolja. A PowerShell-modul a hely van megadva a **PÁR-ÁTJÁRÓ – PS-modul** sablon-üzembehelyezés kimeneti paraméterként.

Ha még nincs engedélyezve, használja a **Set-ExecutionPolicy** egy aláíratlan modul engedélyezése a helyi munkamenet-parancsmaggal.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Ezután jelentkezzen be az Azure-előfizetés, amelyben a vezető központi telepítés használatával telepített

**Login-AzureRmAccount**

Ezután importálja a modult:

**Import-Module <filepath to downloaded file>**

Végül futtassa a függvény a megfelelő bemeneti:

- **MyGatewayResourceId** : erőforrás elérési útja az átjárót. Ez az a sablon telepítési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : a csatlakozó tag átjáró erőforrás elérési útja. Ez biztosítja a csatlakozó tagot, és a sablon telepítési kimenő paraméter is nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Kapcsolat neve** : ahhoz, hogy a átjáró kapcsolat azonosítására nevét.
- **Megosztott kulcs** : két tagjai között a konzorcium hálózat, amely egy kapcsolatot létesít a előre meghatározott titkos kulcsot.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < csatlakozó tag átjáró erőforrás elérési útja > - ConnectionName Sajátkapcsolat - SharedKey "MySharedKeyAbc123"

**xPlat parancssori felület: kapcsolat**

Töltse le az Azure CLI-parancsfájlt, és helyileg tárolja. A hely az Azure CLI parancsfájl meg van adva a központi telepítés sablonparaméter nevű **PÁR-ÁTJÁRÓ-AZURE-CLI-PARANCSFÁJLT**.

Futtassa a parancsfájlt a megfelelő bevitellel:

- **MyGatewayResourceId** : erőforrás elérési útja az átjárót. Ez az a sablon telepítési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : a csatlakozó tag átjáró erőforrás elérési útja. Ez biztosítja a csatlakozó tagot, és a központi telepítés is nevű központi telepítés sablonparaméter **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Kapcsolat neve** : ahhoz, hogy a átjáró kapcsolat azonosítására nevét.
- **Megosztott kulcs** : két tagjai között a konzorcium hálózat, amely egy kapcsolatot létesít a előre meghatározott titkos kulcsot.
- **Hely** : az Azure-régió, amelyen az átjáró erőforrás üzembe van helyezve.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Az architektúra a következők közötti kapcsolat sikeres konfigurálása után **vezető** és **tag** központi telepítéseket.

![Tag és vezető architektúrája](./media/ethereum-deployment-guide/leader-member.png)

## <a name="fund-new-member-account"></a>Alap új tagsági fiók

### <a name="generated-genesis-block"></a>Generált képződés letiltása

Az első tagtól, mint a Ethereum fiók működését egy trillió éterrel Ha a központi telepítést hoz létre a képződés blokk (egyéni képződés Block = nem). Más tagjaival, amely már nem támogatott, és meg kell várnia, hogy éter gyűlik össze, mert az adatbányászati csomópontok veszik át a blokkok enyém lesz. Kerülje az új tagok éter várja meg, hogy szüksége lesz explicit módon alap csatlakozó tagok Ethereum fiókok.

Ehhez való csatlakozás tagok biztosítania kell, a Ethereum fiókkal, amely a felügyeleti webhely jelenik meg. A felügyeleti webhely átvitele éter fiókja fiókjuk írja be a fiókját használhatja.

### <a name="custom-genesis-block"></a>Egyéni képződés letiltása

Ha egy egyéni képződés blokk megadott Ethereum-fiókkal biztosított, MetaMask is használhatja, vagy éter átvitele, amely egy másik eszköz fiók a felügyeleti webhely látható előre generált Ethereum fiókhoz megadott. MetaMask használatával, lásd: [Ethereum-fiók létrehozása](#create-ethereum-account).

Ha egy egyéni képződés blokk csak-fiókkal biztosított, vagy Ön nem rendelkezik egyetlen előre lefoglalt fiókot a hozzáférést, akkor várjon, amíg a adatbányászati csomópontok veszik át az Ön fiókjába (érme alap) éter létrehozásához enyém. A nehezen szint megadja az egyéni képződés blokk függ, hogy milyen gyorsan az alapok jönnek létre.

## <a name="create-ethereum-account"></a>Ethereum-fiók létrehozása

További fiók létrehozásához használhatja a megoldások különböző. Egy ilyen megoldás, MetaMask, a Chrome-bővítménnyel, amely egy identitás-tároló és a egy Ethereum hálózathoz, a nyilvános, a teszt vagy az egyéni biztosít. MetaMask formulates egy tranzakciót a fiók regisztrálása a hálózaton.

A tranzakció, például fel másik tranzakcióban halad át a tranzakció csomópontok egyikét, és végül kell bányászott egy, az alábbi ábra szemlélteti a blokkba.

![Tranzakció csomópont](./media/ethereum-deployment-guide/transaction-node.png)

A bővítmény telepítéséhez a Chrome-ban, nyissa meg a testreszabás, és szabályozhatja a Google Chrome (túlcsordulási gombon) > További eszközök > bővítmények > Get több kiegészítő mezők és MetaMask keresése.

![MetaMask bővítmény](./media/ethereum-deployment-guide/metamask-extension.png)

Telepítése után nyissa meg a MetaMask, és hozzon létre egy új tárolót. Alapértelmezés szerint a tároló csatlakoznak-e a Morden tesztet hálózathoz. Szüksége lesz ezen a telepített privát konzorcium hálózathoz, a terheléselosztó a tranzakció csomópontok előtt kifejezetten az való csatlakozáshoz. A sablon kimeneti beolvasása kitett Ethereum RPC helyen található végpont port 8545, mint nevű `ETHEREUM-RPC-ENDPOINT`, és írja be az egyéni RPC alább látható módon.

![MetaMask beállítások](./media/ethereum-deployment-guide/metamask-settings.png)

Hozzon létre a tárolót, hozzon létre egy olyan fiókot tartalmazó személyespénztárca. További fiókok létrehozásához válassza **kapcsoló fiókok** , majd a **+** gombra.

![MetaMask-fiók létrehozása](./media/ethereum-deployment-guide/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Kezdeti éter foglalási kezdeményezése

A rendszergazda lapján állítson össze egy tranzakció éter átvitele a előre lefoglalt fiók Ethereum egy másik fiókot. Az éter átvitel egy tranzakciót, amely a tranzakció csomópont küldött és az alább bemutatott egy egy blokkba bányásszák.

![Tranzakció csomópont](./media/ethereum-deployment-guide/transaction-node-mined.png)

A vágólap ikonra a MetaMask személyespénztárca, keresztül másolja át a kívánt helyezi át az éter, és térjen vissza a rendszergazdai lap Ethereum fiók címet. Illessze be a másolt fiók 1000 éter átvitele a előre lefoglalt Ethereum fiók az újonnan létrehozott fiókhoz tartozó beviteli mezőt. Kattintson a **Submit** , és várja meg a tranzakció egy egy blokkba bányásszák lehet.

![Csomópont állapota](./media/ethereum-deployment-guide/node-status2.png)

A tranzakciót a szokott egy mined blokkba, ha a fiók egyenlege a fiók MetaMask 1000 éter átviteli fogja tartalmazni.

![MetaMask átvitel](./media/ethereum-deployment-guide/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Átviteli éter fiókok között

Ezen a ponton készen áll a titkos konzorcium hálózaton belüli tranzakciók végrehajtására. A legegyszerűbb tranzakció éter átvitele egy fiókot a másikra. Állítson össze egy ilyen tranzakció, használhatja MetaMask ismét pénz visz át az első fiók használt fent egy másik fiókot.

A **Személyespénztárca 1** MetaMask, kattintson a küldés. Másolja át a második személyespénztárca létrehozott történő címet **címzett címe** bemeneti mező és éter történő átvitel mennyisége a **összeg** beviteli mezőt. Kattintson a **küldése** , és fogadja el a tranzakciót.

![MetaMask küldési tranzakció](./media/ethereum-deployment-guide/metamask-send.png)

Ebben az esetben amikor a tranzakció bányásszák és egy olyan blokkba véglegesítve lett, a fiók kiegyensúlyozza tükröződnek ennek megfelelően. Vegye figyelembe, **Személyespénztárca 1**tartozó egyenleg le van-e vonni több mint 15 éter, mert a tranzakció feldolgozása adatbányászati díjat kell fizetnie volt.

![Személyespénztárca 1](./media/ethereum-deployment-guide/wallet1.png)

![Személyespénztárca 2](./media/ethereum-deployment-guide/wallet2.png)

## <a name="next-steps"></a>További lépések

Most már készen áll az alkalmazás és a titkos konzorcium blockchain hálózati elleni intelligens szerződés fejlesztési összpontosítani.
