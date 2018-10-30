---
title: Ethereum proof of work consortium megoldássablon
description: A Etherereum Proof-az-munkahelyi Consortium megoldás sablon segítségével telepítheti és konfigurálhatja egy többtagú consortium Ethereum hálózati
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: fa58ecf4607efc1d212e40b98d199756d4b987f8
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231797"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Ethereum proof of work consortium megoldássablon

Az Ethereum Proof-az-munkahelyi Consortium megoldássablon célja, hogy egyszerűbb és gyorsabb üzembe helyezése és a egy többtagú consortium Ethereum hálózati beállításával minimális Azure és az Ethereum kapcsolatos.

Felhasználói adatok és a egy kattintással üzembe helyezés az Azure Portalon néhány minden tagjának helyezhet üzembe a hálózati jelenlét, a Microsoft Azure Compute-tal, hálózati és tárolási szolgáltatások szerte a világon. Minden tag hálózati erőforrás-igényű áll kiegyenlített terhelésű tranzakció csomópontok egy készletét az, amely egy alkalmazás vagy a felhasználó használhatja a tranzakciók, a tranzakciókat. adatbányászati csomópontok egy készletét és a egy VPN-átjárót nyújt. A későbbi csatlakozási lépés az átjárók teljes konfigurációjú többtagú blockchain-hálózat létrehozása kapcsolódik.

## <a name="about-blockchain"></a>Információ a blockchainről

Azoknak, akik nem ismeri a blockchain-Közösséggel Ez a megoldás megjelenésével nagyszerű lehetőséget biztosít a technológia megismerheti az Azure-ban könnyen és konfigurálható módon. Javasoljuk azonban, első lépésként a egyszerűbb önálló Ethereum hálózati topológiát a az interaktív bemutató, mielőtt többtagú consortium hálózatokhoz való kiépítésének üzembe helyezéséhez.

### <a name="mining-node-details"></a>Adatbányászati csomópont részletei

Tranzakciók adatbányászatot csomópontok elkülönülnek a csomópontokat, hogy fogadja el a tranzakciók, győződjön meg arról, hogy a két művelet nem versenytárs ugyanazokat az erőforrásokat.

Consortium tag tartalmazó egy vagy több adatbányászati csomópont, egy felügyelt lemez által támogatott legfeljebb öt régióban helyezhet üzembe. A régióban egy vagy több csomópontja úgy van konfigurálva, a csomópontok dinamikus zjistitelnost támogatásához a hálózaton lévő rendszerindító csomópontja. Adatbányászati csomópontok kommunikálni más adatbányászati csomópontok hozására való konszenzus az alapul szolgáló elosztott Főkönyv állapotáról. Hiba esetén nem kell, hogy az alkalmazás vegye figyelembe, vagy ezek a csomópontok kommunikálni. Magánhálózatokat való összpontosítással a adatbányászati csomópontok elkülönülnek a bejövő nyilvános internetes forgalmat a másodlagos védelmi réteg hozzáadása. Kimenő forgalom engedélyezett, azonban nem az Ethereum-felderítési port.

A csomópontokat a Go Ethereum (Geth) ügyfél stabil verziójával rendelkezik, és adatbányászati csomóponttal vannak konfigurálva. Ha nem ad meg egy egyéni képződés letiltása, a összes csomópontja használja ugyanazt az Ethereum-címet és kulcspár, amelyet az Ethereum-fiókot jelszó védi. A megadott Ethereum jelszót minden adatbányászati csomópont esetében az alapértelmezett fiók (coinbase) létrehozására szolgál. Adatbányászati csomópontként adatbányászatot, ehhez a fiókhoz hozzáadott díjak gyűjtenek.

Egy tagnak consortium adatbányászati csomópontok számát a kívánt hálózathoz és a kivonatoló power minden tagja számára kijelölt teljes méretének függ. Minél nagyobb ez a hálózat, a tisztességtelen előnyhöz próbál a jeggyel sérülhet igénylő több csomópont. A sablon a virtual machine scale sets használatával kiépül régiónként legfeljebb 15 adatbányászati csomópontok támogatja.

### <a name="transaction-node-details"></a>Tranzakció csomópont részletei

Consortium tagja is rendelkezik egy elosztott terhelésű tranzakció csomópontok. Ezek a csomópontok érhetők el, a virtuális hálózaton kívül, hogy az alkalmazások használhatják ezek a csomópontok tranzakciók elküldéséhez, vagy hajtsa végre az intelligens szerződések a blockchain-hálózaton belül. Minden csomópont a Go Ethereum (Geth) ügyfél stabil verziójával rendelkezik, és teljes másolata az elosztott főkönyv vezetésére vannak konfigurálva. Ha egy egyéni képződés letiltása nem áll rendelkezésre, ezek a csomópontok ugyanazt Ethereum a fiókot használja, az Ethereum-fiókot jelszó védi.

Tranzakció-csomópontok használata kiegyenlített terhelésű belül egy rendelkezésre állási csoportot a magas rendelkezésre állás fenntartása érdekében. A sablon támogatja a legfeljebb öt tranzakció-csomópontokat a virtual machine scale sets használatával építettek ki.

### <a name="log-analytics-details"></a>Log analytics részletei

Minden üzembe helyezés is létrehoz egy új Log Analytics-példányt, vagy csatlakozhat meglévő példányt. Ez lehetővé teszi, hogy az egyes virtuális gépek az üzembe helyezett hálózati alkotó különböző teljesítmény-mérőszámait figyelését.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

### <a name="description"></a>Leírás

Ez a megoldássablon telepíthet egy vagy több rétegből régió-alapú több tag Ethereum consortium network. A virtuális hálózat minden egyes régió egy lánc topológiában a virtuális hálózati átjárók és a kapcsolati erőforrás más régióban csatlakozik. A regisztráló, amely tartalmazza a szükséges adatokat az egyes régiókban telepített összes Miner és a tranzakciós csomópont is helyezi üzembe.

### <a name="standalone-and-consortium-leader-overview"></a>Önálló és consortium vezető áttekintése

![Önálló és Consortium vezető – áttekintés](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Csatlakozás consortium tag áttekintése

![Csatlakozás Consortium tag áttekintése](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Első lépések

Ez a folyamat az Azure-előfizetés, amely támogathatja több virtuálisgép-méretezési csoportok üzembe helyezése és a managed disks szükséges. Ha szükséges, hozzon létre egy ingyenes Azure-fiókot a kezdéshez.

Miután egy előfizetést védett, nyissa meg az Azure Portalon. Válassza ki **+ erőforrás létrehozása**, Marketplace-en (összes megjelenítése), és keressen rá a **Ethereum Proof-az-munkahelyi Consortium**.

A sablon üzembe helyezéséhez végigvezeti az első tagtól erőforrás-igényű konfigurálása a hálózatban. Az üzembe helyezési folyamat öt lépést oszlik: alapjait, az Operations Management Suite, üzembe helyezési régiók, a hálózat méretétől és teljesítményt, Ethereum-beállítások.

### <a name="basics"></a>Alapvető beállítások

A **alapjai**, megadhatja a környezethez, például az előfizetés, erőforráscsoport és az alapszintű virtuális gép tulajdonságainak standard paramétereinek értékét.

![Alapvető beállítások](./media/ethereum-deployment/sample-deployment.png)

Paraméter neve|Leírás| Megengedett értékek|Alapértelmezett értékek
---|---|---|---
Hozzon létre egy új vagy meglévő hálózaton való csatlakozás?|Hozzon létre egy új hálózatot, vagy egy már meglévő consortium network csatlakoztatása|Hozzon létre új való csatlakozás meglévő|Új létrehozása
Konzorcium része lesz a hálózat üzembe helyezése|Egy consortium network lehetővé teszi, hogy a későbbiekben ehhez a hálózathoz csatlakozni (látható, ha *hozzon létre új* felett van kiválasztva)|Önálló Consortium|Különálló
Erőforrás-előtag |A karakterlánc elnevezéséhez alapként erőforrások (2 – 4 alfanumerikus karakter). Egyedi kivonattal karakterláncot az egyes erőforrásokat, kiegészített, amíg a rendszer hozzáfűzi erőforrás-specifikus információkat.|Hossza 2 – 4 alfanumerikus karakterek|NA
Virtuális gép felhasználóneve| Az egyes üzembe helyezett virtuális gépek (csak alfanumerikus karaktereket) a rendszergazdai jogosultságú felhasználónevet|1 – 64 karakter |gethadmin
Hitelesítés típusa|A módszert a virtuális géphez. |Jelszó vagy SSH nyilvános kulcs|Jelszó
Jelszó (hitelesítési típus = jelszó)|Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava. A jelszónak tartalmaznia kell a 3, a következő követelményeknek: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter. <br />Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|12 – 72 karakter|NA
SSH-kulcs (hitelesítési típus = a nyilvános kulcs)|A secure shell-kulcsot a távoli bejelentkezéshez használt.|| NA
Előfizetés| Az előfizetés, melyben szeretné üzembe helyezni a consortium network||NA
Erőforráscsoport| Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network.||NA
Hely| Az Azure-régió erőforráscsoport. ||NA



### <a name="operations-management-suite"></a>Operations Management Suite

Az Operations Management Suite (OMS) panelen egy OMS-erőforrást, a hálózat konfigurálását teszi lehetővé. OMS gyűjt, és a surface hasznos metrikákat és naplókat a hálózatról, így gyorsan ellenőrizheti, hogy a hálózati állapotfigyelő vagy hibakeresési lehetővé teszi problémák. Az ingyenesen elérhető OMS szabályosan meghiúsul, kapacitás elérésekor.

![Új OMS létrehozása](./media/ethereum-deployment/new-oms.png)

Paraméter neve|Leírás| Megengedett értékek|Alapértelmezett értékek
---|---|---|---
Meglévő OMS-csatlakozás|Hozzon létre egy új Log Analytics-példányt, vagy egy meglévő példányát csatlakoztatása|Hozzon létre új való csatlakozás meglévő|Új Log Analytics-hely létrehozása|A régió, ahol szeretné telepíteni az új Log Analytics (látható if *új létrehozása* van kiválasztva)
Meglévő OMS-munkaterület azonosítója|Munkaterület-Azonosítót a meglévő példány (látható if *csatlakozás meglévő* van kiválasztva) OMS szolgáltatásszint|Válassza ki a tarifacsomagot az új példány. További információ: https://azure.microsoft.com/pricing/details/log-analytics/ (látható if *csatlakozás meglévő* van kiválasztva)|Ingyenes önálló csomópontonként|Ingyenes
Meglévő OMS elsődleges kulcs|A meglévő OMS-példányhoz való csatlakozáshoz használt elsődleges kulcs (látható if *csatlakozás meglévő* van kiválasztva)

### <a name="deployment-regions"></a>Üzembe helyezési régiók

A következő **üzembe helyezési régiók**, adja meg a bemenetek **régió(k) száma** üzembe helyezéséhez a consortium network és Azure-régió alapján adott régiók számának kiválasztása. Felhasználó legfeljebb öt régióban is üzembe.

![Üzembe helyezési régiók](./media/ethereum-deployment/deployment-regions.png)

Paraméter neve| Leírás| Megengedett értékek |Alapértelmezett értékek
---|---|---|---
Régió(k) száma| Több régióban való üzembe helyezése a consortium network|1, 2, 3, 4, 5| 2
Első régióban| Első régióban való üzembe helyezése a consortium network|Az összes engedélyezett az Azure-régiók| USA nyugati régiója
Második régióban |Második régióban való üzembe helyezése a consortium network (csak akkor, ha több régióban beállítás van megadva 2 látható)|Az összes engedélyezett az Azure-régiók| USA keleti régiója
Harmadik régiót| Harmadik régiót üzembe helyezéséhez a consortium network (Visible csak akkor, ha több régióban 3 van kiválasztva)|Az összes engedélyezett az Azure-régiók| USA középső régiója
Negyedik régió| Negyedik régióban való üzembe helyezése a consortium network (Visible csak akkor, ha több régióban 4 van kiválasztva)|Az összes engedélyezett az Azure-régiók| USA 2. keleti régiója
Ötödik régió| Ötödik régióban való üzembe helyezése a consortium network (Visible csak akkor, ha több régióban van jelölve, mint 5)|Az összes engedélyezett az Azure-régiók| USA nyugati régiója, 2.

### <a name="network-size-and-performance"></a>A hálózat méretétől és a teljesítmény

A következő **méretének és teljesítményének hálózati** méretét, a consortium network bemenetek megadása. Például számát és méretét, adatbányászati és tranzakciós csomópontok.

![A hálózat méretétől és a teljesítmény](./media/ethereum-deployment/network-size-performance.png)

Paraméter neve |Leírás |Megengedett értékek| Alapértelmezett értékek
---|---|---|---
Adatbányászati csomópontok száma|Egy régióban üzembe helyezett adatbányászati csomópontok száma|2. 15.| 2
Adatbányászati csomópont tároló-teljesítményre|A telepített adatbányászati csomópontok biztonsági felügyelt lemez típusa.|Standard vagy prémium szintű|Standard
Adatbányászati csomópont virtuális gépének mérete|A virtuális gép mérete adatbányászati csomópontok használt.|Standard szintű, és <br />Standard D <br />Standard D v2 <br />Standard F sorozat <br />Standard DS-ben <br />és a Standard FS|Standard D1v2
Elosztott terhelésű tranzakció csomópontok száma|A hálózat részét képező kiépítése tranzakció csomópontok számát.|1 – 5| 2
Tranzakció csomópont tároló-teljesítményre|A telepített tranzakció csomópontok biztonsági felügyelt lemez típusa.|Standard vagy prémium szintű|Standard
Tranzakció csomópont virtuális gépének mérete|A tranzakció csomópontok használt virtuális gép méretét.|Standard szintű, és <br />Standard D <br />Standard D v2 <br />Standard F sorozat <br />Standard DS-ben <br />és a Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Ethereum-beállítások

A következő **Ethereum-beállítások**, Ethereum-konfigurációs beállítások, például a hálózati azonosító és az Ethereum fiók jelszavát vagy képződés blokk adja meg.

![Ethereum-beállítások](./media/ethereum-deployment/standalone-leader-deployment.png)

Paraméter neve |Leírás |Megengedett értékek|Alapértelmezett értékek
---|---|---|---
ConsortiumMember azonosítója|Minden tagja részt vesz a consortium network konfigurálhatók az IP-címterek ütközések elkerülése érdekében társított azonosítója. <br /><br />Tagazonosító egyedinek kell lennie a különböző szervezetek ugyanazon a hálózaton belül. Egy egyedi Tagazonosító van szükség, akkor is, ha több régióban telepít ugyanazon a szervezeten belül.<br /><br />Jegyezze meg a paraméter értékét óta kell megoszthatja más csatlakozó tagokat.|0 - 255
Ethereum hálózati azonosító|A hálózati Azonosítót a consortium Ethereum hálózati üzembe helyezve. Minden egyes Ethereum hálózati rendelkezik a saját hálózati azonosító, az 1 folyamatban van a nyilvános hálózat azonosítója. Az adatbányászati csomópont korlátozott hálózati hozzáférést pedig továbbra is javasoljuk sok ütközések elkerülése érdekében.|5 – 999,999,999| 10101010
Egyéni képződés letiltása|Lehetőség van automatikusan létrehozni egy képződés letiltása vagy adjon meg egy egyéni besorolás.|Igen/nem| Nem
Ethereum-fiók jelszavát (egyéni képződés letiltása = nem)|A rendszergazda jelszavát, az egyes importált Ethereum-fiókhoz használt. A jelszónak tartalmaznia kell a következőket: 1 nagybetűt, 1 kisbetűt és 1 számot.|12 vagy több karaktert|NA
Titkos kulcs jelszava Ethereum (egyéni képződés letiltása = nem)|Az ECC létrehozott alapértelmezett Ethereum-fiókhoz tartozó titkos kulcs létrehozásához használt jelszót. Egy előre létrehozott titkos kulcs nem kell explicit módon átadni.<br /><br />Fontolja meg egy jelszót az erős titkos kulcs és a másik consortium tagjaival nincs átfedés elegendő véletlenszerűségre azért. A hozzáférési kódot tartalmaznia kell legalább a következőket: 1 nagybetűt, 1 kisbetűt és 1 számot.<br /><br />Megjegyzés: Ha a két tagjára használja ugyanazt a jelszót a fiókokhoz létrehozott azonos lesz. Ugyanazt a jelszót akkor hasznos, ha egyetlen szervezet próbál régióban üzembe helyezheti, és szeretné osztani egy olyan fiók (alap érme) az összes csomópont.|12 vagy több karaktert|NA
Képződés letiltása (egyéni képződés letiltása = Yes)|Egyéni képződés blokk jelölő JSON-karakterlánc. Egyéni hálózatok találhat további információt itt a képződés blokk formátumát.<br /><br />Ethereum fiók továbbra is jön létre egy egyéni képződés blokk megadásakor. Vegye figyelembe, hogy a képződés blokk, nem várja meg, adatbányászati egy prefunded Ethereum-fiók megadása.|Érvényes JSON |NA
Megosztott kapcsolat kulcsa|Egy kapcsolat a virtuális hálózati átjáró között megosztott kulcs.| 12 vagy több karaktert|NA
Consortium adatok URL-címe|A tag egy másik telepítés által biztosított megfelelő consortium adatok mutató URL-címe. <br /><br />Ezeket az adatokat egy már csatlakoztatott tag, aki rendelkezik a központi telepítés által biztosított. Ha a hálózat többi részétől helyezte üzembe, az URL-cím a sablon üzembe helyezési kimeneti nevű CONSORTIUM-adatokat.||NA
Csatlakozás a virtuális hálózati átjáró|Az erőforrás elérési útja a VNet-átjáró, amelyhez kapcsolódni szeretne.<br />Ezeket az adatokat egy már csatlakoztatott tag, aki rendelkezik a központi telepítés által biztosított. Ha a hálózat többi részétől helyezte üzembe, az URL-cím sablon, üzembe helyezési CONSORTIUM_MEMBER_GATEWAY_ID nevű kimeneti van. Megjegyzés: Ugyanabban a tagban consortium adatok URL-CÍMÉT és a virtuális hálózati átjáró erőforrás kell használni.||NA
Társ információk regisztráló végpontja|A megadott tag egy másik központi telepítési társ info végpont|Érvénytelen végpont consortium az első tag|NA
Kulcs a társ információk regisztráló|Társ adatok elsődleges kulcs egy másik tag központi telepítés által biztosított|Érvényes elsődleges kulcs consortium az első tag|NA

### <a name="summary"></a>Összegzés

Kattintson az összefoglalás panel a megadott bemeneti adatok és a központi telepítés előtti Alapszintű ellenőrzés futtatásához.

![Összegzés](./media/ethereum-deployment/summary.png)

Tekintse át a jogi és adatvédelmi szempontból, és kattintson a **beszerzési** üzembe helyezéséhez. Ha az üzembe helyezés több régióban rendelkezik, vagy egy consortium network szól, majd a sablon előre telepíti a szükséges VPN-átjárók támogatják a hálózati kapcsolat más tagjaival. Az átjáró üzembe helyezési legfeljebb 45 50 percig is eltarthat.

## <a name="post-deployment-sanity-checks"></a>Üzembe helyezés megerősítések közzététele

### <a name="administrator-page"></a>Rendszergazda lap

A telepítés sikeresen befejeződött, és a kiépített összes erőforrást, a blockchain-hálózat képet kaphat a rendszergazda lapra léphet, és megerősítést a központi telepítési állapotának ellenőrzése. A rendszergazda lap URL-címe a terheléselosztó; DNS-neve Ez a sablon üzembe helyezéséhez, mivel ADMIN_SITE kimeneti szakaszában megtalálható.

Is megkeresheti, válassza ki a telepített erőforráscsoportot. Majd válassza ki **áttekintése**, kattintson a hivatkozásra, és azonnal alatt **központi telepítések** , amely a sikeresen telepített számát jeleníti meg.

![Erőforrás-csoport – áttekintés](./media/ethereum-deployment/resource-overview.png)

Az új képernyőn látható az üzembe helyezési előzmények. Válassza ki az első üzembe helyezés erőforrás (például a microsoft-azure-blockchain.azure-blockchain-szerviz...), és keresse meg a **kimenetek** szakasz a lap alsó felében. Látni fogja a felügyelet lapon, a sablon üzembe helyezési kimeneti paraméter ADMIN_SITE, URL-CÍMÉT.

![Erőforrás-telepítések](./media/ethereum-deployment/resource-deployments.png)

![Üzembe helyezés kimenet](./media/ethereum-deployment/deployment-output.png)

A rendszergazda lap beolvasásához, másolja a **rendszergazda – hely** kimeneti és a egy másik lapon nyissa meg.

A rendszergazda lapon kérheti le a topológia a Ethereum-csomópont állapota szakasz áttekintésével telepített magas szintű áttekintése. Tartalmazza az összes csomópont állomásnevek társ számukat és a legújabb blokkblob látható. A társ száma az egyes csomópontok között legalább az egyik van kisebb, mint a *csomópontok száma összesen* és a beállított maximális társ száma. Vegye figyelembe, hogy a társ száma nem korlátozza a csomópontok, a hálózaton belüli telepíthető.
Néha előfordul látni fogja a társ száma ingadozik, és lehet kisebb, mint a (teljes csomópontok száma - 1). A szám a különbség nem mindig a jele, hogy a csomópontok nem kifogástalan, mivel, főkönyvi elágazásoknak hatására kisebb módosításokat végez a társ száma. Végül ellenőrizheti a legújabb blokkblob elágazásoknak vagy késedelmes jelentések meghatározni a rendszer a hálózaton lévő mindegyik csomópont által látott.

![Csomópont állapota](./media/ethereum-deployment/node-status.png)

A csomópont állapota 10 másodpercenként frissül. Frissítse az oldalt a böngészőből vagy **Újrabetöltés** gombra kattintva frissítse a nézetet.

### <a name="oms-portal"></a>OMS-portál

Megkeresheti az OMS-portálon vagy az alábbi üzembe helyezési kimenet (OMSPORTALURL) hivatkozásra, vagy az erőforráscsoportban üzembe helyezett OMS erőforrás kijelölésével.

![OMS URL-CÍME](./media/ethereum-deployment/oms-url.png)

![OMS-erőforrás](./media/ethereum-deployment/oms-resource.png)

A portál első jelenik meg a magas szintű hálózati statisztika áttekintést nyújt.

![OMS áttekintése](./media/ethereum-deployment/oms-overview.png)

Kattintson az Áttekintés a közvetlen, a csomópontonkénti statisztika megtekintése a portálon.

![Csomópont stats](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Csomópontok elérése

Távolról csatlakozhat a virtuális gépek, a tranzakciós csomópontok SSH-n keresztül a megadott rendszergazdai felhasználónév és jelszó vagy SSH-kulccsal. A tranzakció csomópont virtuális gépek nem rendelkeznek saját nyilvános IP-címek, mert szüksége lesz a terheléselosztón keresztül, és adja meg a portszámot. Az SSH-parancs futtatása az első tranzakció csomópontjának eléréséhez szerepel, mint a sablon üzembe helyezési kimeneti paraméter **SSH_TO_FIRST_TX_NODE** (az üzembe helyezési minta: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). További tranzakciós csomópontok lekéréséhez egyesével a portszám (például tranzakciós első csomópontjára a 4000-es porton).

Mivel a virtuális gépek, amelyen futtatja a adatbányászati csomópontok nem kívülről elérhető-e, haladjon végig a tranzakció csomópontok egyikét kell. Ha már rendelkezik egy tranzakció csomóponthoz SSH-munkamenetből, telepítse a titkos kulcsot a tranzakció csomóponton, vagy a jelszó egy SSH-munkamenet indítása a adatbányászati csomópontokon történő használata.

**Megjegyzés**

A gazdanév felügyeleti hely vagy az Azure Portalról kérhető. Az Azure Portalon, a csomópontok a virtuális gép méretezési csoport (VMSS) erőforrás szerepel a gazdanév részen **példányok**, amely eltér a tényleges állomásnevét. Például az Azure Portalon állomásnév nézhet **mn-asdfmv-reg1_0** , de a tényleges állomásnév lehet például **mn-asdfmv-reg**.

Példa:

Az Azure portal állomásnév| Tényleges állomásnév
---|---
sloupec-ethwvu-reg1_0| sloupec-ethwvu-reg1000000
sloupec-ethwvu-reg1_1 |sloupec-ethwvu-reg1000001
sloupec-ethwvu-reg1_2 |sloupec-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Új consortium tag hozzáadása

### <a name="sharing-data"></a>Adatok megosztása

Az első olyan tag (vagy egy csatlakoztatott tag) konzorcium meg kell adnia a néhány információt más tagok ahhoz, hogy csatlakozzon, illetve a kapcsolatot. Konkrétan:

1. **Megosztott Consortium konfigurációs adatok**: egy adatkészletet, amellyel előkészíthető a Ethereum-kapcsolat között két tagja van. A szükséges információkat, beleértve a képződés letiltása, consortium hálózati azonosító és rendszerindító csomópontokat, a tranzakció-csomópontokon a vezető vagy egy másik üzembe helyezett tag fájl íródik. Ez a fájl helyét a sablon üzembe helyezési nevű kimeneti paraméter szerepel **CONSORTIUM-adatok**.
2. **Adatok végpont társviszonyt**: Ethereum hálózati a társ info regisztráló végpont lekérése az összes csomópont már csatlakozott a vezetői vagy egy másik tag központi telepítés. Egy sor minden csomópont kapcsolatos információkat a DB-tárolók kapcsolódik a hálózathoz, például csomópont állomásnév, privát IP-cím stb. Ez az a sablon üzembe helyezési kimeneti paraméter nevű **PEER_INFO_ENDPOINT**.
3. **Elsődleges kulcs adatai társviszonyt**: A társ info regisztráló elsődleges kulccsal kéri le a vezető vagy más tag társ elsődleges info-kulcshoz való hozzáférés. Ez az a sablon üzembe helyezési kimeneti paraméter nevű **PEER_INFO_PRIMARY_KEY**.


4. **Virtuális hálózati átjáró**: minden tagjának kapcsolatot hoz létre a teljes blockchain-hálózaton keresztüli meglévő tagjára. Csatlakoztassa a virtuális Hálózatot, a VNET-átjáró, amely kapcsolódik a tag erőforrás elérési útja kell. Ez az a sablon üzembe helyezési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Megosztott kulcs**: egy előre meghatározott titkos consortium hálózati kapcsolatot létesít két tagja között. Ez az alfanumerikus karakterláncnak (1 és 128 karakter között), amely a központi telepítés keretében kívül egyeztetett. (Például **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Új tag elfogadása

Ebben a lépésben kell elvégezni, a csatlakozó tag van sikeres üzembe helyezését követően a hálózaton. Mielőtt tag csatlakozzon a hálózathoz és a tranzakciós forgalom jelenik meg, meglévő tagjára végső konfiguráció kell elvégeznie a VPN-átjáró fogadja el a kapcsolatot. Ez azt jelenti, hogy a csatlakozó tag Ethereum-csomópontok nem fog futni, amíg létrejön a kapcsolat. Ez a konfiguráció PowerShell és xPlat CLI-n keresztül teheti meg. A PowerShell modul, és az xPlat CLI-példaszkript a tranzakció csomóponton consortium adatok mellett is tárolt. A parancsprogram helye az üzembe helyezés a kimeneti paraméterek nevű **PÁR-ÁTJÁRÓ-PS-modul** és **PÁR-ÁTJÁRÓ – AZURE-CLISCRIPT**, illetve.

**A telepítő PowerShell vagy a parancssori felületen**

További információt az első lépések az Azure PowerShell-parancsmagok és Azure xPlat CLI Azure dokumentációjában találhatók.

Szüksége lesz a legújabb Azure-parancsmagjaival helyileg telepített és a egy munkamenet megnyitása. Ellenőrizze, hogy jelentkezzen be a munkamenet az Azure-előfizetés hitelesítő adataival.

**PowerShell: Kapcsolat létrehozása**

Töltse le a PowerShell-modult, és helyileg tárolja. A PowerShell-modul helye van megadva a **PÁR-ÁTJÁRÓ-PS-modul** sablontelepítés kimeneti paraméterként.

Ha még nincs engedélyezve, használja a **Set-ExecutionPolicy** parancsmagot, hogy az aláírás nélküli modul fut a helyi munkamenet.

**Set-ExecutionPolicy Unrestricted CurrentUser tárolóba**

Ezután jelentkezzen be az Azure-előfizetés, amelyben a vezető központi telepítés segítségével telepítve

**Login-AzureRmAccount**

Ezután importálja a modult:

**Import-Module <filepath to downloaded file>**

Végül futtassa a függvényt a megfelelő bemenet:

- **MyGatewayResourceId:** a átjáró erőforrás elérési útja. Ez az a sablon üzembe helyezési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** a csatlakozó tag átjáró erőforrás elérési útja. Ez a csatlakozó tagja által biztosított, és a sablon üzembe helyezési kimeneti paraméter a is neve **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Kapcsolat neve:** azonosíthatja a Gateway-kapcsolat nevét.
- **Megosztott kulcs:** két tagjai között a consortium network, amely kapcsolatot létesít az előre meghatározott titkos kulcsot.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < a csatlakozó tag átjáró erőforrás elérési útja > - ConnectionName Sajátkapcsolat - SharedKey "MySharedKeyAbc123"

**az xPlat CLI: kapcsolat létrehozása**

Az Azure CLI-szkript letöltése, és helyileg tárolja. A sablon üzembe helyezési nevű paraméter megadott helyét, az Azure CLI-példaszkript **PÁR-ÁTJÁRÓ – AZURE-CLI-SZKRIPT**.

Futtassa a parancsfájlt a megfelelő bemenet:

- **MyGatewayResourceId:** a átjáró erőforrás elérési útja. Ez az a sablon üzembe helyezési kimeneti paraméter nevű **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** a csatlakozó tag átjáró erőforrás elérési útja. Ez a csatlakozó tagja által biztosított, és a központi telepítés is nevű sablonparaméter üzembe helyezési **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Kapcsolat neve:** azonosíthatja a Gateway-kapcsolat nevét.
- **Megosztott kulcs:** két tagjai között a consortium network, amely kapcsolatot létesít az előre meghatározott titkos kulcsot.
- **Hely:** az Azure-régióban, az átjáró erőforrás üzembe van helyezve.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Az architektúra a következők közötti kapcsolat sikeres konfigurálása után **vezető** és **tag** központi telepítések.

![Vezető és tag architektúra](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Alap új tag fiók

### <a name="generated-genesis-block"></a>Generált képződés letiltása

Az első tagként Ethereum-fiókja van finanszírozott egy trillió távolítsuk a Ha a központi telepítést hoz létre a képződés blokk (egyéni képződés letiltása = nem). Más tagok egy fiókot, amely előre nem támogatott, és meg kell várnia a távolítsuk gyűlnek, mivel azok adatbányászati csomópontok veszik át a blokkok adatbányászatot lesz. Ne kelljen új tagok távolítsuk várja, szüksége lesz csatlakozó tagokat Ethereum-fiókok kifejezetten alap.

Ehhez való csatlakozás tagok meg kell adni azt az Ethereum-fiókkal, amely a felügyeleti webhely jelenik meg. Ezután használhatja a felügyeleti webhely át távolítsuk-fiókjából a fiók a megadott fiók megadásával.

### <a name="custom-genesis-block"></a>Egyéni képződés letiltása

Ha egy egyéni képződés blokk megadott Ethereum-fiókkal van megadva, MetaMask is használhatja, vagy távolítsuk átvitele, amely egy másik eszköz a megadott fiók a felügyeleti webhely látható előre létrehozott Ethereum-fiókjába. MetaMask használati útmutatásért lásd: [Ethereum-fiók létrehozása](#create-ethereum-account).

Ha egy egyéni képződés blokk-fiókkal biztosított, vagy nem rendelkezik olyan előre lefoglalt fiókok hozzáférést, szüksége lesz a Várjon, amíg az adatbányászati csomópontok veszik át adatbányászatot távolítsuk létrehozni a fiók (érme alap). Adja meg, ha az egyéni képződés blokkban nehézségekbe szintjétől függ, hogy milyen gyorsan az alapok jönnek létre.

## <a name="create-ethereum-account"></a>Ethereum-fiók létrehozása

További fiókok létrehozásához, használhatja a különböző megoldásokat. Egy ilyen megoldás, MetaMask, a Chrome-bővítményt, amely egy identitás-tároló és a egy Ethereum hálózathoz, a nyilvános, tesztelési vagy egyéni biztosít. MetaMask formulates egy tranzakciónak a fiók regisztrálásához a hálózatban.

A tranzakció, bármely egyéb tranzakció, például halad át az egyik tranzakció csomópontot, és végül kell célú adatbányászatra sem használja az alábbi képen szemléltetett módon egy letiltása.

![Tranzakció-csomópont](./media/ethereum-deployment/transaction-node.png)

A bővítmény telepítéséhez a Chrome-ban, Ugrás a testreszabás, és szabályozhatja a Google Chrome (Túlcsordulás gomb) > További eszközök > bővítmények > További bővítmények beszerzése és MetaMask keresése.

![MetaMask bővítmény](./media/ethereum-deployment/metamask-extension.png)

A telepítést követően nyissa meg a MetaMask, és hozzon létre egy új tárolót. Alapértelmezés szerint a tároló csatlakoznak a Morden tesztelési célú hálózat. Módosítsa az kifejezetten a terheléselosztó tartománynévcímkéje a tranzakció csomópontok, az üzembe helyezett privát consortium hálózathoz való csatlakozáshoz. A sablon kimenetből lekérni a közzétett Ethereum RPC végpont a következő port 8545, mivel `ETHEREUM-RPC-ENDPOINT`, és adja meg az egyéni RPC alább látható módon.

![MetaMask beállításai](./media/ethereum-deployment/metamask-settings.png)

A tároló létrehozásával egy wallet-fiókot tartalmazó hoz létre. További fiókok létrehozásához válassza **kapcsoló fiókok** , majd a **+** gombra.

![MetaMask fiók létrehozása](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Kezdeti távolítsuk foglalási kezdeményezése

A rendszergazda lapon állítson össze egy tranzakció távolítsuk előre lefoglalt fiókból át egy másik Ethereum-fiókot. Ez távolítsuk átadása a tranzakciót, amelynek a tranzakció csomópont fogadja és célú adatbányászatra sem használja a blokkolás az alábbi képen szemléltetett módon.

![Tranzakció-csomópont](./media/ethereum-deployment/transaction-node-mined.png)

A vágólapra a MetaMask wallet ikonra, keresztül másolja a cím, amelyhez hozzá szeretné távolítsuk átvitelét, és térjen vissza a rendszergazda lapon Ethereum-fiók. Illessze be a másolt fiók a bemeneti mező 1000 távolítsuk előre lefoglalt Ethereum-fiókból át az újonnan létrehozott fiókhoz. Kattintson a **küldés** és várjon, amíg a tranzakció egy blokk be célú adatbányászatra sem használja.

![Csomópont állapota](./media/ethereum-deployment/node-status2.png)

A tranzakció egy mined blokkba előjegyzett, ha a fiókja egyenlege MetaMask fiókja 1000 távolítsuk átvitelét fogja tartalmazni.

![MetaMask átvitel](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Távolítsuk-fiókok közötti átvitele

Ezen a ponton készen áll a saját consortium hálózaton belüli tranzakciók végrehajtásához. A legegyszerűbb tranzakció távolítsuk átvitelét az egyik fiókból a másikba. Állítson össze egy ilyen tranzakció, használhatja MetaMask ismét pénzt visz át egy másik fiókot fent használt első fiókot.

A **Wallet 1** MetaMask, kattintson a küldés. Másolja ki a létrehozott a második wallet címét **címzett címe** beviteli mező, és az adatforgalom távolítsuk mennyisége a **összeg** beviteli mezőben. Kattintson a **küldése** , és fogadja el a tranzakciót.

![MetaMask küldési tranzakció](./media/ethereum-deployment/metamask-send.png)

Ismét a tranzakciós célú adatbányászatra sem használja, és a egy blokk véglegesíteni, a számla balances jelennek meg megfelelően. Vegye figyelembe, hogy **Wallet 1**a egyenleg levonása több mint 15 távolítsuk, mivel már a tranzakció feldolgozása adatbányászati díjat kell fizetnie.

![A Wallet 1](./media/ethereum-deployment/wallet1.png)

![A Wallet 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>További lépések

Most már készen áll az alkalmazás és az intelligens szerződés fejlesztési ellen a privát consortium blockchain-hálózat.
