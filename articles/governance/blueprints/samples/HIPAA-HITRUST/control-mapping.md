---
title: HIPAA HITRUST mintavezérlők
description: A HIPAA HITRUST tervminták vezérlőleképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472309"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>A HIPAA HITRUST tervezetminta vezérlőtérképezése

A következő cikk részletezi, hogy az Azure Blueprints HIPAA HITRUST minta minta leképezi a HIPAA HITRUST vezérlők. A vezérlőkkel kapcsolatos további információkért lásd: [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Az alábbi leképezések a **HIPAA HITRUST** vezérlőkhöz tartoznak. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[Előzetes\]verzió : A HIPAA HITRUST vezérlők** beépített házirend-kezdeményezésének naplózása.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md)

## <a name="control-against-malicious-code"></a>A rosszindulatú kód elleni védekezés

Ez a tervezet segít a végpontvédelem kezelésében, beleértve a rosszindulatú kódvédelmet is, azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó végpontvédelmet az Azure Security Center virtuális gépein, és kényszerítik a Microsoft kártevőirtó megoldást a Windows virtuális gépeken.

- Alapértelmezett Microsoft IaaS kártevőirtó bővítmény telepítése Windows Server rendszerhez
- A Batch-fiókok diagnosztikai naplóit engedélyezni kell
- A rendszerfrissítéseket telepíteni kell a gépeken


## <a name="management-of-removable-media"></a>Cserélhető adathordozók kezelése

A szervezet az adatbesorolási szint alapján a használat előtt regisztrálja az adathordozókat (beleértve a laptopokat is), ésszerű korlátozásokat vezet be az ilyen adathordozók használatára vonatkozóan, és megfelelő szintű fizikai és logikai védelmet (beleértve a titkosítást is) biztosít a a fedett információkat tartalmazó adathordozók, amíg megfelelően meg nem semmisülnek vagy fertőtlenítésre nem kerülnek.

- Titkosítás megkövetelése data lake store-fiókokon
- Az SQL felügyelt példány TDE-védőjét saját kulccsal kell titkosítani
- A lemeztitkosítást virtuális gépeken kell alkalmazni
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell


## <a name="information-exchange-policies-and-procedures"></a>Információcsere-szabályzatok és -eljárások

A felhőszolgáltatók az iparág által elismert virtualizációs platformot és szabványos virtualizációs formátumokat (pl. Open Virtualization Format, OVF) használják az interoperabilitás biztosításához, és dokumentálták a használatban lévő hipervizorok egyéni módosításait és az összes megoldás-specifikus virtualizációs horgok állnak rendelkezésre az ügyfél felülvizsgálata.

- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyeken nincs telepítve a megadott alkalmazás

## <a name="control-of-operational-software"></a>Az operatív szoftverek ellenőrzése 

A szervezet azonosítja a jogosulatlan szoftvereket az információs rendszeren, beleértve a szervereket, munkaállomásokat és laptopokat, minden engedélyezésre vonatkozó, kivételi házirendet alkalmaz, amely megtiltja az ismert, jogosulatlan szoftverek végrehajtását az információs rendszeren, és rendszeresen, de nem kevesebb, mint évente ellenőrzi és frissíti a jogosulatlan szoftverek listáját.

- \[Preview\] A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások naplózása" párbeszédpanelen
- \[Preview\] A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Rendszernaplózási házirendek– Fiókkezelés" szolgáltatásban

## <a name="change-control-procedures"></a>Változásellenőrzési eljárások

Az összes virtuálisgép-lemezkép integritását mindig biztosítják a virtuálisgép-lemezképek naplózásával és riasztásának kiemelésével, valamint a tulajdonos(ok) és/vagy ügyfél(ek) számára elektronikus módszerekkel (pl. portálok vagy riasztások) a változás vagy áthelyezés eredményeinek, valamint a lemezkép integritásának későbbi érvényesítésével történő hozzáférhetővé tétele révén.

- \[Preview\] A windows vms-konfigurációnaplózási eredményeinek megjelenítése a "Rendszernaplózási házirendek – részletes nyomon követés" című részében

## <a name="inventory-of-assets"></a>Eszközök leltára 

Az eszközök és szolgáltatások leltárát

- A keresési szolgáltatások diagnosztikai naplóit engedélyezni kell.
- \[Előzetes\] telepítési követelmények a Windows virtuális gépek konfigurációinak naplózásához a "Security Options- Microsoft Network Server" (Biztonsági beállítások – Microsoft Network Server) szolgáltatásban
- \[Előzetes\] üzembe helyezési követelmények a Windows virtuális gépek konfigurációinak naplózásához a "Felügyeleti sablonok- Hálózat"

## <a name="control-of-technical-vulnerabilities"></a>A technikai biztonsági rések ellenőrzése 

Minden rendszer- és hálózati összetevőhöz létezik megerősített konfigurációs szabvány.

- Virtuális gépek naplózása a vészhelyreállítás konfigurálása nélkül
- A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon
- A biztonsági rést a biztonsági rés felmérésével kell orvosolni

## <a name="segregation-in-networks"></a>Szegregáció a hálózatokban

A szervezet biztonsági átjárói (pl. tűzfalak) biztonsági házirendeket kényszerítenek, és úgy vannak beállítva, hogy szűrjék a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hozzáférés közötti szegregáció fenntartására szolgálnak hálózati szegmensek (pl. az internet), beleértve a DMZ-ket, és minden tartományhoz betartatják a hozzáférés-vezérlési házirendeket.

- A biztonsági figyelési ügynök automatikus kiépítése
- Hálózati figyelő telepítése virtuális hálózatok létrehozásakor

## <a name="input-data-validation"></a>Bemeneti adatok érvényesítése

A nyilvános elérésű webalkalmazások esetében alkalmazásszintű tűzfalak vannak megvalósítva a forgalom szabályozására. A nem webalapú nyilvános alkalmazások esetében a szervezet az alkalmazástípusra jellemző hálózati tűzfalat valósított meg. Ha a nyilvános fekvésű alkalmazásra irányuló forgalom titkosítva van, az eszköz vagy a titkosítás mögött ül, vagy az elemzés előtt képes visszafejteni a forgalmat.

- \[Preview\] A Windows virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "Windows tűzfal tulajdonságai" párbeszédpanelen


## <a name="network-connection-control"></a>Hálózati kapcsolat vezérlése

A hálózati forgalmat a szervezet hozzáférés-vezérlési házirendjének megfelelően szabályozzák tűzfalon és más hálózati korlátozásokon keresztül minden egyes hálózati hozzáférési pont vagy külső távközlési szolgáltatás felügyelt felületére vonatkozóan.

- Az interneten keresztüli hozzáférést korlátozni kell
- Az SQL felügyelt példány TDE-védőjét saját kulccsal kell titkosítani
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazásokesetében

## <a name="network-controls"></a>Hálózati vezérlők

A szervezet biztonságos és titkosított kommunikációs csatornákat használ fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra történő áttelepítésekor.

- A lemeztitkosítást virtuális gépeken kell alkalmazni
- AZ SQL server TDE protector-t saját kulccsal kell titkosítani
- \[Preview\] A Windows virtuális gépek konfigurációinak naplózási eredményei a "Biztonsági beállítások- Hálózati hozzáférés" menüben
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- \[Preview:\] A Windows-verzió kontinói beállításainak eredményei a "Windows tűzfal tulajdonságai" párbeszédpanelen
- Diagnosztikai beállítások telepítése hálózati biztonsági csoportokból
- Az interneten keresztüli hozzáférést korlátozni kell

## <a name="sensitive-system-isolation"></a>Érzékeny rendszerelkülönítés

A megosztott rendszererőforrások (pl. regiszterek, fő memória, másodlagos tároló) visszakerülnek a rendszerbe, védve vannak a más rendszerek/alkalmazások/felhasználók számára történő közzétételtől, és a felhasználók szándékosan vagy akaratlanul nem férhetnek hozzá az információ maradványokhoz.

- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba

## <a name="security-of-network-services"></a>A hálózati szolgáltatások biztonsága

A hálózati szolgáltató/menedzser által nyújtott, egyeztetett szolgáltatásokat hivatalosan kezelik és ellenőrzik annak biztosítása érdekében, hogy azok biztonságosan legyenek biztosítva.

- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba

## <a name="network-routing-control"></a>Hálózati útválasztási vezérlő

Az útválasztási vezérlők a belső és külső hálózatok (pl. az internet és a külső hálózatok) közötti biztonsági átjárókon (pl. tűzfalakon) keresztül valósulnak meg.

- Az adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="information-exchange-policies-and-procedures"></a>Információcsere-szabályzatok és -eljárások

A szervezet korlátozza a szervezet által ellenőrzött hordozható adathordozók használatát a külső információs rendszereken engedélyezett személyek számára.

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokesetében
- Az APi alkalmazás csak HTTPS-en keresztül érhető el

## <a name="electronic-messaging"></a>Elektronikus üzenetküldés

A jóváhagyások a külső közszolgáltatások – többek között az azonnali üzenetküldés vagy a fájlmegosztás – használata előtt kerülnek beszerzésre.

- \[Preview\] A 0644-re nem beállított jelszófájl-engedélyekkel nem rendelkező Linux-virtuális gépek naplózási eredményeinek megjelenítése

## <a name="on-line-transactions"></a>On-Line tranzakciók

A szervezet megköveteli a tranzakcióban részt vevő felek közötti titkosítást és elektronikus aláírások használatát. A szervezet biztosítja, hogy a tranzakció részleteinek tárolása a nyilvánosan elérhető környezeteken kívül (pl. a szervezet intranetjén található tárolóplatformon) kívül helyezkedjen el, és ne maradjon meg és ne legyen elérhető az internetről közvetlenül elérhető adathordozón. Folyamat.

- A lemeztitkosítást virtuális gépeken kell alkalmazni
- \[Preview:\] A megbízható legfelső szintű tanúsítványban nem a megadott tanúsítványokat nem tartalmazó Windows-virtuális gépek naplózási eredményeinek megjelenítése

## <a name="password-management"></a>Jelszókezelés

A jelszavak titkosítása az összes rendszerösszetevő átvitele és tárolása során történik.

- \[Preview:\] A windows virtuális gépek naplózási eredményeinek megjelenítése, amelyeknél nincs engedélyezve a jelszó-összetettségi beállítás

## <a name="user-authentication-for-external-connections"></a>Külső kapcsolatok felhasználói hitelesítése

Az erős hitelesítési módszerek, például a többtényezős, a radius vagy a kerberos (kiemelt hozzáféréshez) és a CHAP (a telefonos módszerek hitelesítő adatainak titkosításához) a szervezeti hálózattal létesített összes külső kapcsolathoz vannak megvalósítva.

- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Just In Time hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="user-identification-and-authentication"></a>Felhasználó azonosítása és hitelesítése

Azok a felhasználók, akik kiemelt funkciókat (pl. rendszerfelügyeletet) hajtottak végre, külön fiókokat használnak a kiemelt funkciók végrehajtásakor. A többtényezős hitelesítési módszerek et a szervezeti házirendnek megfelelően (pl. távoli hálózati hozzáférésesetén) használják.

- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Just In Time hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="privilege-management"></a>Jogosultságkezelése

A virtualizált rendszereket üzemeltető rendszerek felügyeleti funkcióihoz vagy felügyeleti konzoljaihoz való hozzáférés a legkisebb jogosultság elvén alapuló személyzetre korlátozódik, és technikai ellenőrzéssel támogatott.

- Just In Time hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- \[Preview\] szerepköralapú hozzáférés-vezérlés (RBAC) kell használni a Kubernetes szolgáltatások

## <a name="review-of-user-access-rights"></a>A felhasználói hozzáférési jogok áttekintése

A szervezet az információs eszközök jogosult felhasználóinak dokumentált listáját tartja nyilván.

- \[Preview\] A Windows virtuális gépek konfigurációiból származó naplózási eredmények megjelenítése a "Biztonsági beállítások– fiókok" párbeszédpanelen

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Távoli diagnosztikai és konfigurációs portvédelem

A számítógépre vagy hálózati rendszerekre telepített portok, szolgáltatások és hasonló alkalmazások, amelyek nem kifejezetten szükségesek az üzleti funkciókhoz, le vannak tiltva vagy eltávolítva.

- A felügyeleti portokat be kell zárni a virtuális gépeken
- A virtuális gépek méretezési csoportjainak biztonsági biztonsági réseit ki kell újítani

## <a name="audit-logging"></a>Naplózás naplózása

Az elküldött és fogadott üzenetek naplói megmaradnak, beleértve az üzenet dátumát, időpontját, eredetét és célját, de annak tartalmát nem. A naplózás mindig elérhető, amíg a rendszer aktív, és nyomon követi a kulcsfontosságú eseményeket, a sikeres/sikertelen adathozzáférést, a rendszerbiztonsági konfigurációváltozásait, a kiemelt vagy segédprogram-használatot, a riasztást, a riasztást, a védelmi rendszerek (pl. A/V és IDS) aktiválását és deaktiválását, az azonosítási és hitelesítési mechanizmusok aktiválását és kikapcsolását, valamint a rendszerszintű objektumok létrehozását és törlését.

- Az Event Hub diagnosztikai naplóit engedélyezni kell
- A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell

## <a name="monitoring-system-use"></a>Felügyeleti rendszer használata

A szervezet környezetében telepített automatizált rendszerek a legfontosabb események és rendellenes tevékenységek figyelésére, valamint a rendszernaplók elemzésére szolgálnak, amelyek eredményeit rendszeresen felülvizsgálják. A figyelés magában foglalja a kiemelt műveleteket, az engedélyezett hozzáférést vagy a jogosulatlan hozzáférési kísérleteket, beleértve az inaktivált fiókok elérésére tett kísérleteket, valamint a rendszerriasztásokat vagy hibákat.

- A virtuálisgép-méretezési csoportok diagnosztikai naplóit engedélyezni kell

## <a name="segregation-of-duties"></a>A feladatok elkülönítése

A vámok elkülönítése az információk és rendszerek jogosulatlan vagy nem szándékos módosításának kockázatának korlátozására szolgál. Egyetlen személy sem férhet hozzá, nem módosíthat vagy használhat információs rendszereket engedély vagy észlelés nélkül. A hozzáférés az egyes felhasználók szerepköre és felelőssége alapján a szükséges minimumra korlátozódik, és ezek a személyek nem férhetnek hozzá az ilyen vezérlőkkel kapcsolatos ellenőrzési funkciókhoz.

- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat

## <a name="administrator-and-operator-logs"></a>Rendszergazdai és operátori naplók

A szervezet biztosítja a megfelelő naplózás engedélyezését a rendszergazdai tevékenységek naplózásához; és rendszeresen áttekinti a rendszergazdai és üzemeltetői naplókat.

- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat

## <a name="identification-of-risks-related-to-external-parties"></a>A külső felekkel kapcsolatos kockázatok azonosítása

A szervezet és a külső felek közötti távelérési kapcsolatok titkosítva vannak

- A lemeztitkosítást virtuális gépeken kell alkalmazni

## <a name="business-continuity-and-risk-assessment"></a>Üzletmenet-folytonosság és kockázatértékelés

A szervezet azonosítja kritikus üzleti folyamatait, és integrálja az üzletmenet-folytonosság információbiztonsági követelményeit az olyan szempontokra vonatkozó egyéb folytonossági követelményekkel, mint a működés, a személyzet, az anyagok, a közlekedés és létesítmények.

- \[Preview\] A Windows virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "Biztonsági beállítások – helyreállítási konzol" párbeszédpanelen

> [!NOTE]
> Bizonyos Azure-szabályzat-definíciók elérhetősége az Azure Government és más nemzeti felhők ben eltérő lehet. 

## <a name="next-steps"></a>További lépések

Áttekintette a HIPAA HITRUST tervezetminta vezérlőleképezését. Ezután látogasson el az alábbi cikkekbe, amelyek ből megtudhatja az áttekintést és a minta üzembe helyezését:

> [!div class="next step action"]
> [HIPAA HITRUST tervezet - Áttekintés](./control-mapping.md)
> [HIPAA HITRUST terv - Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
