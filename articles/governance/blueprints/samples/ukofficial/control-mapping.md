---
title: Egyesült Királyság hivatalos & uk NHS tervezet minta ellenőrzések
description: Az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintáinak ellenőrzési feltérképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851366"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintáinak ellenőrzési feltérképezése

A következő cikk részletezi, hogy az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-tervezetmintái hogyan térképezik le az Egyesült Királyság hivatalos és egyesült királyságbeli NHS-ellenőrzését. Az ellenőrzésekkel kapcsolatos további információkért lásd az [Egyesült Királyság hivatalos személyét.](https://www.gov.uk/government/publications/government-security-classifications)

Az alábbi leképezések az **Egyesült Királyság hivatalos** és az Egyesült Királyság NHS-ellenőrzései. **UK NHS** A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[Előzetes\] audit uk OFFICIAL és az Egyesült Királyság NHS vezérlők és üzembe helyezése konkrét virtuálisgép-bővítmények a naplózási követelmények** beépített házirend-kezdeményezés támogatása érdekében.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md)

## <a name="1-data-in-transit-protection"></a>1 Az árutovábbítási védelemben részt veőket

A tervezet segítségével biztosíthatja az adatok átvitelét az Azure-szolgáltatások biztonságos hozzárendelésével Azure Policy-definíciók, amelyek naplózják a nem biztonságos kapcsolatok tárfiókok és a Redis cache. [Azure Policy](../../../policy/overview.md)

- Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- Biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- Előfeltételek telepítése a nem biztonságos kommunikációs protokollokat használó Windows webkiszolgálók naplózásához
- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót a Web App ban kell használni
- A legújabb TLS verziót kell használni a Függvényalkalmazásban

## <a name="23-data-at-rest-protection"></a>2.3 Nyugalmi védelemre vonatkozó adatok

Ez a tervezet segít a kriptográfiai vezérlők használatára vonatkozó szabályzat kényszerítésében azáltal, hogy [olyan Azure-szabályzatdefiníciókat](../../../policy/overview.md) rendel hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket és a gyenge kriptográfiai beállítások naplózását.
Ha tisztában van azzal, hogy az Azure-erőforrások hol rendelkezhetnek nem optimális kriptográfiai konfigurációkkal, akkor korrekciós műveleteket végezhet annak érdekében, hogy az erőforrások az információbiztonsági szabályzatnak megfelelően legyenek konfigurálva. Pontosabban a tervezet által hozzárendelt házirendek titkosítást igényelnek az adattó-tárfiókokhoz; transzparens adattitkosítást igényel nek az SQL-adatbázisokon; a hiányzó titkosítás naplózása a tárfiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az automatizálási fiókváltozókon; nem biztonságos kapcsolatok naplózása a tárfiókokhoz és a Redis-gyorsítótárhoz; a virtuális gép gyenge jelszótitkosításának naplózása; és a nem titkosított Service Fabric kommunikáció naplózása.

- A lemeztitkosítást virtuális gépeken kell alkalmazni
- Az automatizálási fiókváltozókat titkosítani kell
- A Service Fabric-fürtöknek encryptAndSign-re kell állítaniuk a ClusterProtectionLevel tulajdonságot
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- Az SQL DB transzparens adattitkosításának telepítése
- Titkosítás megkövetelése data lake store-fiókokon
- Engedélyezett helyek (nehéz kódolni "UK SOUTH" és "UK WEST")
- Az erőforráscsoportok engedélyezett helyei (nehéz kódolni "UK SOUTH" és "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 A biztonsági rés kezelése

Ez a tervezet segít az információs rendszer biztonsági réseinek kezelésében azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó végpontvédelmet, a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek biztonsági réseit. Ezek az elemzések valós idejű információkat nyújtanak az üzembe helyezett erőforrások biztonsági állapotáról, és segítenek a javítási műveletek rangsorolásában.

- Hiányzó végpontvédelem figyelése az Azure Security Centerben
- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell
- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani
- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni
- A biztonsági rés felmérését engedélyezni kell az SQL-kiszolgálókon
- A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- A speciális adatbiztonságot engedélyezni kell az SQL által felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="53-protective-monitoring"></a>5.3 Védőellenőrzés

Ez a tervezet segít megvédeni az információs rendszer eszközeit az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek a korlátlan hozzáférés, a listatevékenységek és a fenyegetések védelmét biztosítják.

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken
- Virtuális gépek naplózása a vészhelyreállítás konfigurálása nélkül
- A DDoS Protection Standard-ot engedélyezni kell
- A komplex veszélyforrások elleni védelem típusait "All" típusúra kell állítani az SQL felügyelt példányok speciális adatbiztonsági beállításaiban.
- A komplex veszélyforrások elleni védelem típusait az SQL-kiszolgáló speciális adatbiztonsági beállításaiban "All" típusúra kell állítani.
- Fenyegetésészlelés telepítése SQL-kiszolgálókon
- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez

## <a name="9-secure-user-management"></a>9 Biztonságos felhasználókezelés 

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, hogy könnyebben kezelhesse, ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet segít korlátozni és szabályozni a hozzáférési jogokat azáltal, hogy [az Azure Policy-definíciók](../../../policy/overview.md) at a tulajdonosi és/vagy olvasási/írási engedélyekkel rendelkező külső fiókok és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókok naplózásához, amelyek nem rendelkeznek többtényezős hitelesítéssel.

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="10-identity-and-authentication"></a>10 Identitás és hitelesítés

Ez a tervezet segít korlátozni és szabályozni a hozzáférési jogokat azáltal, hogy [az Azure Policy-definíciók](../../../policy/overview.md) at a tulajdonosi és/vagy olvasási/írási engedélyekkel rendelkező külső fiókok és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókok naplózásához, amelyek nem rendelkeznek többtényezős hitelesítéssel.

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a tervezet hozzárendeli az Azure Policy-definíciók az Azure Active Directory-hitelesítés sql-kiszolgálók és service fabric használatának naplózásához. Az Azure Active Directory-hitelesítés lehetővé teszi az egyszerűbb engedélykezelést és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezelését.

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez

Ez a tervezet azure-szabályzat-definíciókat is hozzárendel olyan naplózási fiókokhoz, amelyeket felül kell vizsgálni, beleértve az amortizált fiókokat és a külső fiókokat is. Szükség esetén a fiókok letilthatók a bejelentkezésben (vagy az eltávolításban), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a tervezet két Azure Policy-definíciót rendel hozzá az elavult fiók naplózásához, amelyet meg kell fontolni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a tervezet is hozzárendel egy Azure Policy-definíciót, amely naplózza a Linux virtuális gép jelszófájl engedélyekriasztást, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi, hogy korrekciós intézkedéseket tegyen annak érdekében, hogy a hitelesítők ne kerüljenek veszélybe.

- \[Előzetes\]verzió : A 0644-es passwd fájlengedélyekkel nem rendelkező Linux-virtuális gépek naplózási eredményeinek megjelenítése

Ez a tervezet segít az erős jelszavak kényszerítésében az Azure Policy-definíciók hozzárendelésével, amelyek naplózják azokat a Windows virtuális gépeket, amelyek nem kényszerítik ki a minimális erősséget és az egyéb jelszókövetelményeket. A jelszóerősségre vonatkozó szabályzatot megsértő virtuális gépek ismertségét figyelembe véve korrekciós műveleteket is végrehajthatsz annak érdekében, hogy az összes virtuálisgép-felhasználói fiók jelszavai megfeleljenek a szabályzatnak.

- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyeknél nincs engedélyezve a jelszó-összetettségi beállítás
- \[Előzetes\]verzió : Előfeltételek telepítése a 70 napos maximális jelszóval nem rendelkező Windows virtuális gépek naplózásához
- \[Előzetes\]verzió : Előfeltételek telepítése az 1 napos minimális jelszóval nem rendelkező Windows-virtuális gépek naplózásához
- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek naplózásához, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyeknél nincs engedélyezve a jelszó-összetettségi beállítás
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek maximális jelszószáma nem 70 nap
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek minimális jelszóéletkora nem 1 nap
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : A Windows virtuális gépek ből származó olyan naplózási eredmények megjelenítése, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát

Ez a tervezet is segít az Azure-erőforrásokhoz való hozzáférés szabályozása az Azure-szabályzat definíciók hozzárendelésével. Ezek a házirendek naplózják az erőforrások típusainak és konfigurációinak használatát, amelyek lehetővé teszik az erőforrásokhoz való megengedhetőbb hozzáférést. A szabályzatokat megsértő erőforrások ismertetése segíthet a korrekciós műveletek elvégzésében annak biztosítása érdekében, hogy az Azure-erőforrások csak a jogosult felhasználók számára legyenek elérhetők.

- \[Előzetes\]verzió: A jelszavak nélküli fiókkal rendelkező Linux-virtuális gépek naplózására vonatkozó követelmények üzembe helyezése
- \[Előzetes\]verzió : A jelszavak nélküli fiókokból távoli kapcsolatokat engedélyező Linux-virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió : A jelszó nélküli fiókkal rendelkező Linux-virtuális gépek naplózása
- \[Előzetes\]verzió : A jelszavak nélküli fiókokból távoli kapcsolatokat engedélyező Linux-virtuális gépek naplózása
- A tárfiókokat át kell telepíteni az új Azure Resource Manager-erőforrásokba
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="11-external-interface-protection"></a>11 Külső illesztőfelület-védelem

A több mint 25 szabályzat használata a megfelelő biztonságos felhasználókezeléshez, ez a tervezet segít megvédeni a szolgáltatásfelületeket a jogosulatlan hozzáféréstől azáltal, hogy hozzárendel egy Azure Policy-definíciót, amely a korlátlan tárfiókokat figyeli. [Azure Policy](../../../policy/overview.md) A korlátlan hozzáféréssel rendelkező tárfiókok nem kívánt hozzáférést biztosíthatnak az információs rendszerben található információkhoz. Ez a tervezet is hozzárendel egy szabályzatot, amely lehetővé teszi az adaptív alkalmazásvezérlők a virtuális gépeken.

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken
- Az IaaS-alapú webes alkalmazásokra vonatkozó NSG-szabályokat
- Az internetfelé néző végponton keresztüli hozzáférést korlátozni kell
- A virtuális gépek internetre néző hálózati biztonsági csoportszabályait meg kell erősíteni
- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- A távoli hibakeresést ki kell kapcsolni a Függvényalkalmazásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A webalkalmazás csak HTTPS-en keresztül érhető el
- A Függvényalkalmazás csak HTTPS-en keresztül érhető el
- Az API-alkalmazás csak HTTPS-en keresztül érhető el

## <a name="12-secure-service-administration"></a>12 Biztonságos szolgáltatás felügyelete

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, hogy könnyebben kezelhesse, ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet segít korlátozni és szabályozni a kiemelt hozzáférési jogokat azáltal, hogy öt [Azure Policy-definíciót](../../../policy/overview.md) rendel a tulajdonosi és/vagy a tulajdonossal rendelkező külső fiókok és írási engedélyek és fiókok naplózásához, és/vagy írási engedélyeket, amelyek nem rendelkeznek engedélyezve a többtényezős hitelesítésengedélyezésével.

A felhőszolgáltatások felügyeletéhez használt rendszerek magas szintű jogosultsággal rendelkeznek a szolgáltatáshoz. A kompromisszumuk nak jelentős hatása lenne, beleértve a biztonsági ellenőrzések megkerülésének, valamint a nagy mennyiségű adat ellopására vagy manipulálására szolgáló eszközöket. A szolgáltató adminisztrátorai által az operatív szolgáltatás irányítására használt módszereket úgy kell kialakítani, hogy csökkentsék a szolgáltatás biztonságát aláásó hasznosítás kockázatát. Ha ez az elv nincs megvalósítva, a támadó rendelkezhet a biztonsági vezérlők megkerülésére és nagy mennyiségű adat ellopására vagy kezelésére szolgáló eszközökkel.

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a tervezet hozzárendeli az Azure Policy-definíciók az Azure Active Directory-hitelesítés sql-kiszolgálók és service fabric használatának naplózásához. Az Azure Active Directory-hitelesítés lehetővé teszi az egyszerűbb engedélykezelést és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezelését.

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez

Ez a tervezet is hozzárendeli az Azure Policy-definíciók a naplózási fiókok, amelyeket prioritást kell rendelni a felülvizsgálat, beleértve az amortizált fiókok és a külső fiókok emelt szintű engedélyekkel. Szükség esetén a fiókok letilthatók a bejelentkezésben (vagy az eltávolításban), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a tervezet két Azure Policy-definíciót rendel hozzá az elavult fiók naplózásához, amelyet meg kell fontolni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a tervezet is hozzárendel egy Azure Policy-definíciót, amely naplózza a Linux virtuális gép jelszófájl engedélyekriasztást, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi, hogy korrekciós intézkedéseket tegyen annak érdekében, hogy a hitelesítők ne kerüljenek veszélybe.

- \[Előzetes\]verzió : A Linux VM /etc/passwd fájlengedélyek naplózása 0644-re van állítva

## <a name="13-audit-information-for-users"></a>13 A felhasználók naplózási információi

Ez a tervezet segít biztosítani a rendszeresemények naplózása az [Azure-szabályzat](../../../policy/overview.md) definícióinak hozzárendelésével, amelyek naplóbeállításokat naplóznak az Azure-erőforrásokon. A hozzárendelt szabályzat is naplózza, ha a virtuális gépek nem küld naplókat egy adott log analytics munkaterületre.

- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban
- Diagnosztikai beállítás naplózása
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez
- Hálózati figyelő telepítése virtuális hálózatok létrehozásakor

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Egyesült Királyság hivatalos és egyesült királyságbeli NHS tervrajzainak ellenőrzési térképezését, látogasson el a következő cikkekbe, hogy megismerje az áttekintést és a minta telepítésének módját:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és az Egyesült Királyság NHS tervrajzok - Áttekintés](./index.md)
> [uk official és uk NHS blueprints - Deploy steps](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
