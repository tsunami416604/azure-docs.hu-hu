---
title: HIPAA HITRUST terv minta vezérlők
description: A HIPAA HITRUST tervrajz-minták leképezésének vezérlése. Mindegyik vezérlő egy vagy több olyan Azure-szabályzatra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209412"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>A HIPAA HITRUST tervezetének vezérlési mintája

A következő cikk részletesen ismerteti, hogyan HIPAA az Azure-tervezetek HITRUST-tervezete a HIPAA HITRUST-vezérlőkbe. A vezérlőkkel kapcsolatos további információkért lásd: [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

A következő leképezések a **HIPAA HITRUST** vezérlői. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az ** \[ előnézetet \] : audit HIPAA HITRUST Controls** beépített Policy Initiative.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>A kártékony kódok elleni védekezés

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoft antimalware megoldást a Windows rendszerű virtuális gépeken.

- Az Azure-hoz készült Microsoft antimalware-t úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat
- Hiányzó Endpoint Protection figyelése Azure Security Center
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken


## <a name="management-of-removable-media"></a>Cserélhető adathordozók kezelése

Az adatbesorolási szint alapján a szervezet a használat előtt regisztrálja az adathordozót (beleértve a laptopokat is), megkeresi az adott adathordozó használatára vonatkozó ésszerű korlátozásokat, és megfelelő szintű fizikai és logikai védelmet biztosít (beleértve a titkosítást is) a kezelt adatokat tartalmazó adathordozók számára, a megfelelő megsemmisítés vagy fertőtlenítés céljából.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- A nem csatolt lemezeket titkosítani kell
- Titkosítás megkövetelése Data Lake Store fiókokon
- Az SQL Server TDE-védőt a saját kulccsal kell titkosítani
- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani

## <a name="control-of-operational-software"></a>Az operatív szoftverek felügyelete 

A szervezet azonosítja a jogosulatlan szoftvereket az információs rendszeren, beleértve a kiszolgálókat, a munkaállomásokat és a laptopokat, lehetővé teszi az összes ismert szoftver futtatását az információs rendszeren, valamint a jogosulatlan szoftverek listájának felülvizsgálatát és frissítését rendszeresen, de nem kevesebb, mint évente.

- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="change-control-procedures"></a>Vezérlési eljárások módosítása

Az összes virtuálisgép-lemezkép integritását a rendszer mindig a virtuális gépek rendszerképein végrehajtott módosítások naplózásával és riasztással biztosítja, valamint az üzleti tulajdonos (ok) és/vagy az ügyfél (ek) számára az elektronikus metódusok (például a portálok vagy a riasztások), valamint a kép integritásának későbbi érvényesítése során.

- \[Az előzetes verzió \] naplózási eredményeinek megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a rendszernaplózási házirendek – részletes követés menüpontban
- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a rendszernaplózási házirendek – részletes nyomkövetés

## <a name="control-of-technical-vulnerabilities"></a>A technikai sebezhetőségek szabályozása 

Az összes rendszer-és hálózati összetevő megerősített konfigurációs szabványt tartalmaz.

- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- \[Az előnézeti \] sebezhetőségek felmérését engedélyezni kell a Virtual machines
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- \[Előzetes verzió \] : a pod biztonsági szabályzatokat meg kell határozni a Kubernetes-szolgáltatásokban

## <a name="segregation-in-networks"></a>Elkülönítés a hálózatokban

A szervezet biztonsági átjárói (például tűzfalak) kikényszerítik a biztonsági házirendeket, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az Internet) közötti elkülönítést használják, beleértve a DMZ és az egyes tartományok hozzáférés-vezérlési szabályzatának érvényesítését.

- Az alhálózatokat hálózati biztonsági csoporttal kell társítani
- A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni
- A virtuális gépeket hálózati biztonsági csoporttal kell társítani
- Service Bus virtuális hálózati szolgáltatás végpontját kell használnia
- App Service virtuális hálózati szolgáltatás végpontját kell használnia
- SQL Server virtuális hálózati szolgáltatás végpontját kell használnia
- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia
- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia
- Key Vault virtuális hálózati szolgáltatás végpontját kell használnia
- Az átjáró-alhálózatokat nem szabad hálózati biztonsági csoporttal konfigurálni
- A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk
- \[Előzetes verzió \] : Container Registry virtuális hálózati szolgáltatás végpontját kell használnia
- A belső elérésű virtuális gépekre alkalmazni kell az adaptív hálózatokra vonatkozó kötési javaslatokat.

## <a name="network-connection-control"></a>Hálózati kapcsolatok vezérlése

A hálózati forgalmat a szervezet hozzáférés-vezérlési szabályzatának megfelelően a tűzfal és az egyes hálózati hozzáférési pontokhoz vagy a külső távközlési szolgáltatás felügyelt felületéhez kapcsolódó egyéb korlátozásokkal összhangban kell szabályozni.

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A legújabb TLS-verziót kell használni az API AP-ben
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Az alhálózatokat hálózati biztonsági csoporttal kell társítani
- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni
- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni
- A virtuális gépeket hálózati biztonsági csoporttal kell társítani

## <a name="network-controls"></a>Hálózati vezérlők

A szervezet biztonságos és titkosított kommunikációs csatornákat használ a fizikai kiszolgálók, alkalmazások vagy az alkalmazások virtualizált kiszolgálókra való áttelepítésekor.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A belső elérésű virtuális gépekre alkalmazni kell az adaptív hálózatokra vonatkozó kötési javaslatokat.
- Service Bus virtuális hálózati szolgáltatás végpontját kell használnia
- App Service virtuális hálózati szolgáltatás végpontját kell használnia
- SQL Server virtuális hálózati szolgáltatás végpontját kell használnia
- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia
- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia
- Key Vault virtuális hálózati szolgáltatás végpontját kell használnia
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk
- \[Előzetes verzió \] : Container Registry virtuális hálózati szolgáltatás végpontját kell használnia

## <a name="security-of-network-services"></a>Network Services biztonsága

A hálózati szolgáltató/kezelő által biztosított egyeztetett szolgáltatásokat a rendszer hivatalosan felügyeli és figyeli, hogy azok biztonságosan legyenek biztosítva.

- \[Előzetes \] verzió: a hálózati forgalom adatgyűjtési ügynökét telepíteni kell a Windows rendszerű virtuális gépekre
- \[Előzetes \] verzió: a hálózati adatgyűjtési ügynököt Linux rendszerű virtuális gépekre kell telepíteni, Network Watcher engedélyezni kell

## <a name="information-exchange-policies-and-procedures"></a>Információcsere-házirendek és eljárások

A szervezet korlátozza a szervezet által vezérelt hordozható tárolóeszközök használatát a külső információs rendszereken lévő, felhatalmazott személyek által.

- Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke
- A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését
- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz
- A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni API Apps

## <a name="on-line-transactions"></a>Online tranzakciók

A szervezetnek a tranzakcióban részt vevő felek mindegyike számára szükséges a titkosítás és az elektronikus aláírások használata. A szervezet biztosítja, hogy a tranzakció részletei a nyilvánosan elérhető környezeteken kívül helyezkednek el (például a szervezet intranetén meglévő tárolási platformon, és nem őrzi meg és nem teszi közzé az internetről közvetlenül elérhető adathordozóról. ha megbízható szolgáltatót használnak (például digitális aláírások és/vagy digitális tanúsítványok kiállítása és kezelése céljából), a biztonság integrálva van és beágyazva van a teljes végpontok közötti tanúsítvány-és aláírás-kezelési folyamat során.

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A legújabb TLS-verziót kell használni az API AP-ben
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- \[Előzetes verzió \] : előfeltételek központi telepítése a megadott tanúsítványokat nem tartalmazó Windows-alapú virtuális gépek naplózásához a megbízható gyökérben
- \[Előzetes verzió \] : a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem tartalmazzák a megadott tanúsítványokat a megbízható gyökérben

## <a name="user-password-management"></a>Felhasználói jelszavas kezelés

A rendszer a jelszavakat az átvitel és a tárolás során titkosítja az összes rendszerösszetevőn.

- \[\]Virtuális gépek naplózásának előnézete nem biztonságos jelszó-biztonsági beállításokkal

## <a name="user-authentication-for-external-connections"></a>A külső kapcsolatok felhasználói hitelesítése

Az erős hitelesítési módszerek, például a többtényezős, a RADIUS vagy a Kerberos (privilegizált hozzáférés esetén) és a CHAP (a telefonos módszerekhez tartozó hitelesítő adatok titkosításához) a szervezet hálózatához tartozó összes külső kapcsolat esetében implementálva vannak.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="user-identification-and-authentication"></a>Felhasználói azonosító és hitelesítés

A Kiemelt funkciókat (például a rendszerfelügyeletet) végző felhasználók külön fiókokat használnak az emelt szintű függvények végrehajtásakor. A többtényezős hitelesítési módszereket a szervezeti szabályzatoknak (például távoli hálózati hozzáférés esetén) megfelelően használják.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmaz csak a megadott tagokat.
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmaz csak a megadott tagokat

## <a name="privilege-management"></a>Jogosultságok kezelése

A virtualizált rendszereket működtető rendszerek felügyeleti funkcióihoz vagy rendszergazdai konzolokhoz való hozzáférése a legalacsonyabb jogosultsági szint elve alapján, a technikai szabályozások által támogatottak szerint korlátozódik a személyzetre.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A felügyeleti portokat be kell zárni a virtuális gépeken
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Egyéni RBAC-szabályok használatának naplózása
- Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban

## <a name="review-of-user-access-rights"></a>Felhasználói hozzáférési jogosultságok áttekintése

A szervezet megtartja az adategységek felhatalmazott felhasználóinak dokumentált listáját.

- Egyéni RBAC-szabályok használatának naplózása

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Távoli diagnosztikai és konfigurációs portok védelme

A számítógépekre vagy hálózati rendszerekre telepített portok, szolgáltatások és hasonló alkalmazások le vannak tiltva vagy eltávolíthatók.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A felügyeleti portokat be kell zárni a virtuális gépeken
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni API Apps
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="audit-logging"></a>Naplózás naplózása

A küldött és fogadott üzenetek naplói megmaradnak, beleértve az üzenet dátumát, időpontját, eredetét és célját, de nem annak tartalmát. A naplózás mindig elérhető, amíg a rendszer aktív, és nyomon követi a legfontosabb eseményeket, a sikeres/sikertelen adatelérést, a rendszerbiztonsági konfiguráció módosításait, A jogosultsági szintű vagy a segédprogram-használatot, az összes riasztást, a védelmi rendszerek (például A/V és az AZONOSÍTÓk) aktiválását és inaktiválását, valamint a rendszerszintű objektumok létrehozását és törlését.

- A Azure Data Lake Store lévő diagnosztikai naplókat engedélyezni kell
- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell 
- A IoT Hub lévő diagnosztikai naplókat engedélyezni kell 
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell 
- A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell 
- Az Event hub diagnosztikai naplóit engedélyezni kell 
- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat. 
- A App Services lévő diagnosztikai naplókat engedélyezni kell 
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell 
- A Key Vault lévő diagnosztikai naplókat engedélyezni kell 
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- Az SQL Server naplózását engedélyezni kell
- Diagnosztikai beállítás naplózása
- Azure Monitor az összes régióból gyűjti a tevékenység naplóit

## <a name="monitoring-system-use"></a>A rendszer használatának figyelése

A szervezet környezetében üzembe helyezett automatizált rendszerek a kulcsfontosságú események és a rendellenes tevékenységek figyelésére, valamint a rendszernaplók elemzésére szolgálnak, amelyek eredményei rendszeresen felül vannak értékelve. A figyelés magában foglalja az emelt szintű műveleteket, a hitelesített hozzáférési vagy jogosulatlan hozzáférési kísérleteket, beleértve a deaktivált fiókok elérésére tett kísérleteket, valamint a rendszerriasztásokat és a hibákat.

- Azure Monitor az összes régióból gyűjti a tevékenység naplóit
- A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken
- A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets
- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek naplózásához, amelyeken a log Analytics ügynök nem a várt módon van csatlakoztatva
- \[Előzetes verzió \] : a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken a log Analytics ügynök nem a várt módon van csatlakoztatva
- Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.
- Az előfizetéshez engedélyezni kell a Log Analytics figyelő ügynök automatikus üzembe helyezését

## <a name="segregation-of-duties"></a>A feladatok elkülönítése

A feladatok elkülönítése arra szolgál, hogy korlátozza az információk és rendszerek jogosulatlan vagy véletlen módosításának kockázatát. Egyetlen személy sem férhet hozzá, nem módosíthatja és nem használhatja az információs rendszereket hitelesítés vagy észlelés nélkül. A felügyeleti és hozzáférés-vezérlésért felelős személyek hozzáférése az egyes felhasználók szerepköreitől és kötelezettségeitől függ, és ezek a személyek nem férhetnek hozzá ezekhez a vezérlőkhöz kapcsolódó naplózási funkciókhoz.

- Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban
- Egyéni RBAC-szabályok használatának naplózása
- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "felhasználói jogok kiosztása" alkalmazásban
- \[Előzetes verzió \] : a Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "felhasználói jogok kiosztása" elemnél
- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – felhasználói fiókok felügyelete"
- \[Előzetes verzió \] : a Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a biztonsági beállítások – felhasználói fiókok felügyelete menüpontban
- Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek

## <a name="administrator-and-operator-logs"></a>Rendszergazdai és operátori naplók

A szervezet biztosítja, hogy a megfelelő naplózás engedélyezve legyen a rendszergazdai tevékenységek naplózása érdekében; a rendszergazda és a kezelő rendszeresen ellenőrzi a rendszergazdákat és a naplókat.

- A konkrét felügyeleti műveletekre vonatkozóan léteznie kell egy műveletnapló-riasztásnak.

## <a name="identification-of-risks-related-to-external-parties"></a>Külső felekkel kapcsolatos kockázatok azonosítása

A szervezet és a külső felek közötti távelérési kapcsolatok titkosítva vannak

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

## <a name="business-continuity-and-risk-assessment"></a>Üzletmenet-folytonosság és kockázatértékelés

A szervezet a kritikus fontosságú üzleti folyamatokat azonosítja, és integrálja az üzleti folytonossággal kapcsolatos, a tevékenységek, a munkatársak, az anyagok, a szállítás és a létesítmények vonatkozásában felmerülő egyéb folytonossági követelményekkel kapcsolatos adatvédelmi követelményeket.

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva
- Key Vault objektumoknak helyreállítható kell lenniük
- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózása a biztonsági beállítások – helyreállítási konzolban
- \[\]A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megtekintése a "biztonsági beállítások – helyreállítási konzolban"

## <a name="back-up"></a>Biztonsági mentés

Ez a terv olyan Azure Policy-definíciókat rendel hozzá, amelyek a szervezet rendszerbiztonsági mentési információit elektronikus úton naplózzák a másodlagos tárolóhelyre. A tárolási metaadatok fizikai szállítása esetén érdemes Azure Data Box használni.

- A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB
- A Azure Backup engedélyezni kell a Virtual Machines

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

Áttekintette a HIPAA HITRUST Blueprint minta vezérlési leképezését. A következő cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="next step action"]
> [HIPAA HITRUST terv – áttekintés](./control-mapping.md) 
>  [HIPAA HITRUST terv – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
