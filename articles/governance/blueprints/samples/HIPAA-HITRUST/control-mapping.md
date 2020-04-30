---
title: HIPAA HITRUST terv minta vezérlők
description: A HIPAA HITRUST tervrajz-minták leképezésének vezérlése. Mindegyik vezérlő egy vagy több olyan Azure-szabályzatra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472309"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>A HIPAA HITRUST tervezetének vezérlési mintája

A következő cikk részletesen ismerteti, hogyan HIPAA az Azure-tervezetek HITRUST-tervezete a HIPAA HITRUST-vezérlőkbe. A vezérlőkkel kapcsolatos további információkért lásd: [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

A következő leképezések a **HIPAA HITRUST** vezérlői. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki ** \[az\]előnézetet: audit HIPAA HITRUST Controls** beépített Policy Initiative.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>A kártékony kódok elleni védekezés

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoft antimalware megoldást a Windows rendszerű virtuális gépeken.

- Az alapértelmezett Microsoft IaaS antimalware-bővítmény telepítése a Windows Serverhez
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell
- A rendszerfrissítéseket telepíteni kell a gépeken


## <a name="management-of-removable-media"></a>Cserélhető adathordozók kezelése

Az adatbesorolási szint alapján a szervezet a használat előtt regisztrálja az adathordozót (beleértve a laptopokat is), megkeresi az adott adathordozó használatára vonatkozó ésszerű korlátozásokat, és megfelelő szintű fizikai és logikai védelmet biztosít (beleértve a titkosítást is) a kezelt adatokat tartalmazó adathordozók számára, a megfelelő megsemmisítés vagy fertőtlenítés céljából.

- Titkosítás megkövetelése Data Lake Store fiókokon
- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás


## <a name="information-exchange-policies-and-procedures"></a>Információcsere-házirendek és eljárások

A felhőalapú szolgáltatók egy iparág által elismert virtualizációs platformot és standard virtualizációs formátumokat (például Open Virtualization Format, OVF) használnak az együttműködési képesség biztosításához, és dokumentálták a használatban lévő hypervisorok egyéni módosításait, valamint az ügyfelek általi felülvizsgálathoz elérhető összes megoldás-specifikus virtualizálási hookot.

- A megadott alkalmazásokkal nem rendelkező Windows rendszerű virtuális gépek naplózásához szükséges előfeltételek központi telepítése

## <a name="control-of-operational-software"></a>Az operatív szoftverek felügyelete 

A szervezet azonosítja a jogosulatlan szoftvereket az információs rendszeren, beleértve a kiszolgálókat, a munkaállomásokat és a laptopokat, lehetővé teszi az összes ismert szoftver futtatását az információs rendszeren, valamint a jogosulatlan szoftverek listájának felülvizsgálatát és frissítését rendszeresen, de nem kevesebb, mint évente.

- \[A\] Windows rendszerű virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "biztonsági beállítások – naplózás" elemnél
- \[A\] Windows rendszerű virtuális gépek konfigurációinak naplózási eredményeinek megtekintése a "rendszernaplózási házirendek – Fiókkezelés" című dokumentumból

## <a name="change-control-procedures"></a>Vezérlési eljárások módosítása

Az összes virtuálisgép-lemezkép integritását a rendszer mindig a virtuális gépek rendszerképein végrehajtott módosítások naplózásával és riasztással biztosítja, valamint az üzleti tulajdonos (ok) és/vagy az ügyfél (ek) számára az elektronikus metódusok (például a portálok vagy a riasztások), valamint a kép integritásának későbbi érvényesítése során.

- \[Az\] előzetes verzió naplózási eredményeinek megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a rendszernaplózási házirendek – részletes követés menüpontban

## <a name="inventory-of-assets"></a>Eszközök leltára 

A rendszer karbantartja az eszközök és szolgáltatások leltárát

- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.
- \[A\] Windows rendszerű virtuális gépek konfigurációinak naplózása a "biztonsági beállítások – Microsoft hálózati kiszolgáló" című dokumentum előzetes verziójának központi telepítése
- \[A\] Windows rendszerű virtuális gépek konfigurációjának naplózása a "felügyeleti sablonok-Network" alkalmazásban – előzetes verzió

## <a name="control-of-technical-vulnerabilities"></a>A technikai sebezhetőségek szabályozása 

Az összes rendszer-és hálózati összetevő megerősített konfigurációs szabványt tartalmaz.

- Naplózás Virtual Machines a vész-helyreállítási beállítások nélkül
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A biztonsági rést a sebezhetőségi felmérési megoldásnak kell szervizelni

## <a name="segregation-in-networks"></a>Elkülönítés a hálózatokban

A szervezet biztonsági átjárói (például tűzfalak) kikényszerítik a biztonsági házirendeket, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az Internet) közötti elkülönítést használják, beleértve a DMZ és az egyes tartományok hozzáférés-vezérlési szabályzatának érvényesítését.

- A biztonsági figyelő ügynök automatikus kiépítés
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor

## <a name="input-data-validation"></a>Bemeneti adatok ellenőrzése

A nyilvános webalkalmazások esetében az alkalmazás-szintű tűzfalak a forgalom szabályozásához vannak implementálva. A nem webalapú nyilvános alkalmazások esetében a szervezet az alkalmazás típusához tartozó, hálózati alapú tűzfalat implementált. Ha a nyilvános alkalmazás felé irányuló forgalom titkosítva van, az eszköz vagy a titkosítás mögött helyezkedik el, vagy képes a forgalom visszafejtésére az elemzés előtt.

- \[A\] Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "Windows tűzfal tulajdonságai" párbeszédpanelen


## <a name="network-connection-control"></a>Hálózati kapcsolatok vezérlése

A hálózati forgalmat a szervezet hozzáférés-vezérlési szabályzatának megfelelően a tűzfal és az egyes hálózati hozzáférési pontokhoz vagy a külső távközlési szolgáltatás felügyelt felületéhez kapcsolódó egyéb korlátozásokkal összhangban kell szabályozni.

- Korlátozni kell az internetre irányuló végpontok elérését
- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazások esetében

## <a name="network-controls"></a>Hálózati vezérlők

A szervezet biztonságos és titkosított kommunikációs csatornákat használ a fizikai kiszolgálók, alkalmazások vagy az alkalmazások virtualizált kiszolgálókra való áttelepítésekor.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az SQL Server TDE-védőt a saját kulccsal kell titkosítani
- \[A\] Windows virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – hálózati hozzáférés" elemnél
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- \[A\] Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "Windows tűzfal tulajdonságai" nézetben
- Diagnosztikai beállítások telepítése hálózati biztonsági csoportokból
- Korlátozni kell az internetre irányuló végpontok elérését

## <a name="sensitive-system-isolation"></a>Érzékeny rendszerek elkülönítése

A megosztott rendszererőforrások (például a regisztrációk, a fő memória, a másodlagos tárolók) visszakerülnek a rendszerbe, a más rendszerekre/alkalmazásokra/felhasználókra való illetéktelen hozzáféréstől, és a felhasználók nem tudnak szándékosan hozzáférni az adatokhoz.

- Virtual Machines át kell telepíteni az új Azure Resource Manager-erőforrásokra

## <a name="security-of-network-services"></a>Network Services biztonsága

A hálózati szolgáltató/kezelő által biztosított egyeztetett szolgáltatásokat a rendszer hivatalosan felügyeli és figyeli, hogy azok biztonságosan legyenek biztosítva.

- Virtual Machines át kell telepíteni az új Azure Resource Manager-erőforrásokra

## <a name="network-routing-control"></a>Hálózati útválasztási vezérlő

Az útválasztási vezérlők a belső és külső hálózatok (például az Internet és a harmadik féltől származó hálózatok) között használt biztonsági átjárókkal (például tűzfalakkal) valósíthatók meg.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="information-exchange-policies-and-procedures"></a>Információcsere-házirendek és eljárások

A szervezet korlátozza a szervezet által vezérelt hordozható tárolóeszközök használatát a külső információs rendszereken lévő, felhatalmazott személyek által.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz
- Az APi-alkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="electronic-messaging"></a>Elektronikus üzenetküldés

A jóváhagyások a külső nyilvános szolgáltatások, például az azonnali üzenetküldés vagy a fájlmegosztás használata előtt szerezhetők be.

- \[Az\] előnézet olyan Linux rendszerű virtuális gépek naplózási eredményeit jeleníti meg, amelyek nem rendelkeznek az 0644-as jelszavas engedélyekkel

## <a name="on-line-transactions"></a>Online tranzakciók

A szervezetnek a tranzakcióban részt vevő felek mindegyike számára szükséges a titkosítás és az elektronikus aláírások használata. A szervezet biztosítja, hogy a tranzakció részletei a nyilvánosan elérhető környezeteken kívül helyezkednek el (például a szervezet intranetén meglévő tárolási platformon, és nem őrzi meg és nem teszi közzé az internetről közvetlenül elérhető adathordozóról. ha megbízható szolgáltatót használnak (például digitális aláírások és/vagy digitális tanúsítványok kiállítása és kezelése céljából), a biztonság integrálva van és beágyazva van a teljes végpontok közötti tanúsítvány-és aláírás-kezelési folyamat során.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- \[Az\] előnézet olyan Windows rendszerű virtuális gépek naplózási eredményeit jeleníti meg, amelyek nem tartalmazzák a megbízható gyökérben lévő megadott tanúsítványokat.

## <a name="password-management"></a>Jelszókezelés

A rendszer a jelszavakat az átvitel és a tárolás során titkosítja az összes rendszerösszetevőn.

- \[Az\] előnézet olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás

## <a name="user-authentication-for-external-connections"></a>A külső kapcsolatok felhasználói hitelesítése

Az erős hitelesítési módszerek, például a többtényezős, a RADIUS vagy a Kerberos (privilegizált hozzáférés esetén) és a CHAP (a telefonos módszerekhez tartozó hitelesítő adatok titkosításához) a szervezet hálózatához tartozó összes külső kapcsolat esetében implementálva vannak.

- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="user-identification-and-authentication"></a>Felhasználói azonosító és hitelesítés

A Kiemelt funkciókat (például a rendszerfelügyeletet) végző felhasználók külön fiókokat használnak az emelt szintű függvények végrehajtásakor. A többtényezős hitelesítési módszereket a szervezeti szabályzatoknak (például távoli hálózati hozzáférés esetén) megfelelően használják.

- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="privilege-management"></a>Jogosultságok kezelése

A virtualizált rendszereket működtető rendszerek felügyeleti funkcióihoz vagy rendszergazdai konzolokhoz való hozzáférése a legalacsonyabb jogosultsági szint elve alapján, a technikai szabályozások által támogatottak szerint korlátozódik a személyzetre.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- \[A\] szerepkör-alapú Access Control (RBAC) előzetes verziójának használata a Kubernetes-szolgáltatásokban

## <a name="review-of-user-access-rights"></a>Felhasználói hozzáférési jogosultságok áttekintése

A szervezet megtartja az adategységek felhatalmazott felhasználóinak dokumentált listáját.

- \[A\] Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – fiókok" nézetben

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Távoli diagnosztikai és konfigurációs portok védelme

A számítógépekre vagy hálózati rendszerekre telepített portok, szolgáltatások és hasonló alkalmazások le vannak tiltva vagy eltávolíthatók.

- A felügyeleti portokat be kell zárni a virtuális gépeken
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell

## <a name="audit-logging"></a>Naplózás naplózása

A küldött és fogadott üzenetek naplói megmaradnak, beleértve az üzenet dátumát, időpontját, eredetét és célját, de nem annak tartalmát. A naplózás mindig elérhető, amíg a rendszer aktív, és nyomon követi a legfontosabb eseményeket, a sikeres/sikertelen adatelérést, a rendszerbiztonsági konfiguráció módosításait, A jogosultsági szintű vagy a segédprogram-használatot, az összes riasztást, a védelmi rendszerek (például A/V és az AZONOSÍTÓk) aktiválását és inaktiválását, valamint a rendszerszintű objektumok létrehozását és törlését.

- Az Event hub diagnosztikai naplóit engedélyezni kell
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell

## <a name="monitoring-system-use"></a>A rendszer használatának figyelése

A szervezet környezetében üzembe helyezett automatizált rendszerek a kulcsfontosságú események és a rendellenes tevékenységek figyelésére, valamint a rendszernaplók elemzésére szolgálnak, amelyek eredményei rendszeresen felül vannak értékelve. A figyelés magában foglalja az emelt szintű műveleteket, a hitelesített hozzáférési vagy jogosulatlan hozzáférési kísérleteket, beleértve a deaktivált fiókok elérésére tett kísérleteket, valamint a rendszerriasztásokat és a hibákat.

- A virtuálisgép-méretezési csoportokban lévő diagnosztikai naplókat engedélyezni kell

## <a name="segregation-of-duties"></a>A feladatok elkülönítése

A feladatok elkülönítése arra szolgál, hogy korlátozza az információk és rendszerek jogosulatlan vagy véletlen módosításának kockázatát. Egyetlen személy sem férhet hozzá, nem módosíthatja és nem használhatja az információs rendszereket hitelesítés vagy észlelés nélkül. A felügyeleti és hozzáférés-vezérlésért felelős személyek hozzáférése az egyes felhasználók szerepköreitől és kötelezettségeitől függ, és ezek a személyek nem férhetnek hozzá ezekhez a vezérlőkhöz kapcsolódó naplózási funkciókhoz.

- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak

## <a name="administrator-and-operator-logs"></a>Rendszergazdai és operátori naplók

A szervezet biztosítja, hogy a megfelelő naplózás engedélyezve legyen a rendszergazdai tevékenységek naplózása érdekében; a rendszergazda és a kezelő rendszeresen ellenőrzi a rendszergazdákat és a naplókat.

- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak

## <a name="identification-of-risks-related-to-external-parties"></a>Külső felekkel kapcsolatos kockázatok azonosítása

A szervezet és a külső felek közötti távelérési kapcsolatok titkosítva vannak

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="business-continuity-and-risk-assessment"></a>Üzletmenet-folytonosság és kockázatértékelés

A szervezet a kritikus fontosságú üzleti folyamatokat azonosítja, és integrálja az üzleti folytonossággal kapcsolatos, a tevékenységek, a munkatársak, az anyagok, a szállítás és a létesítmények vonatkozásában felmerülő egyéb folytonossági követelményekkel kapcsolatos adatvédelmi követelményeket.

- \[A\] Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megtekintése a "biztonsági beállítások – helyreállítási konzolban"

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>További lépések

Áttekintette a HIPAA HITRUST Blueprint minta vezérlési leképezését. A következő cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="next step action"]
> [HIPAA HITRUST Blueprint – áttekintés](./control-mapping.md)
> [HIPAA HITRUST Blueprint – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
