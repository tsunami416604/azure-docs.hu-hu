---
title: Minta – Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – vezérlési leképezés
description: Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 945898105aab7261ee494a86aeff10337599feb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226013"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása

A következő cikk azt ismerteti, hogyan jelennek meg az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei az Egyesült Királyság hivatalos és egyesült királysági NHS-vezérlőinek. További információ a vezérlőelemekről: [Egyesült Királyság hivatalos](https://www.gov.uk/government/publications/government-security-classifications).

Az alábbi hozzárendelések az **Egyesült Királyság hivatalos** és **Egyesült Királysági NHS** -vezérlőinek. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa  meg a szabályzatot a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki  **\[az\] előzetes naplózási Egyesült Királyság hivatalos és egyesült királysági NHS-vezérlőit, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

## <a name="1-data-in-transit-protection"></a>1 adatátviteli védelem

A terv segítségével biztosítható, hogy az Azure-szolgáltatásokkal való adatátvitel biztonságos legyen, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek naplózzák a nem biztonságos kapcsolatokat a Storage-fiókokhoz és Redis cache.

- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell

## <a name="23-data-at-rest-protection"></a>2,3 inaktív adatok védelme

Ez a terv segítséget nyújt a házirendnek a titkosítási vezérlők használatára való érvényesítéséhez, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát.
Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzatok titkosítást igényelnek a Lake Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; a Storage-fiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az Automation-fiók változójában a hiányzó titkosítás naplózása; nem biztonságos kapcsolatok naplózása a Storage-fiókokhoz és a Redis Cache; gyenge virtuális gép jelszavas titkosításának naplózása; és a titkosítatlan Service Fabric kommunikáció naplózása.

- Titkosítatlan SQL-adatbázisok figyelése Azure Security Center
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az Automation-fiók változóit titkosítani kell
- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- Titkosítás megkövetelése Data Lake Store fiókokon
- Engedélyezett helyszínek (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített)
- Engedélyezett telephelyek az erőforráscsoportok számára (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített kód)

## <a name="52-vulnerability-management"></a>5,2 biztonsági rések kezelése

Ebből a tervből megtudhatja, hogyan kezelheti az információs rendszer biztonsági réseit olyan [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével, amelyek figyelik a hiányzó Endpoint Protectiont, a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit a gép biztonsági rései. Ezek az információk valós idejű információkat biztosítanak az üzembe helyezett erőforrások biztonsági állapotáról, és segíthetnek a Szervizelési műveletek rangsorolásában.

- Hiányzó Endpoint Protection figyelése Azure Security Center
- A számítógépekre telepíteni kell a rendszerfrissítéseket
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="53-protective-monitoring"></a>5,3 védelmi monitorozás

Ez a terv segítséget nyújt az információs rendszer eszközeinek védelméhez olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek védelmet nyújtanak a korlátlan hozzáféréshez, a engedélyezési listán szereplő tevékenységekhez és a fenyegetésekhez.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- Az alapértelmezett Microsoft IaaS anti-malware bővítmény telepítése a Windows Serverhez

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 biztonságos felhasználói felügyelet/10 identitás és hitelesítés

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv segít a hozzáférési jogosultságok korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával a külső fiókok naplózásához tulajdonossal és/vagy olvasási/írási engedélyekkel, valamint tulajdonosi, olvasási és/vagy írási engedéllyel rendelkező fiókokkal, amelyek nem rendelkeznek több tényezővel hitelesítés engedélyezve.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv Azure Policy definíciókat rendel hozzá az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

Ez a terv Azure Policy definíciókat is hozzárendeli a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a külső fiókokat is. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két Azure Policy-definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv egy Azure Policy-definíciót is hozzárendel, amely a Linux rendszerű virtuális gép jelszavas engedélyeinek naplózására vonatkozó engedélyeket naplózza, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi a korrekciós műveletek elvégzését, hogy a hitelesítő adatok ne legyenek biztonságban.

- \[Előzetes\]verzió: A Linux rendszerű virtuális gép/etc/passwd engedélyeinek naplózása 0644

Ez a terv segít az erős jelszavak betartatásában olyan Azure Policy-definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem érvényesítő Windows-virtuális gépeket naplózzák. A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- \[Előzetes\]verzió: A jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózási követelményeinek központi telepítése
- \[Előzetes\]verzió: Követelmények telepítése a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózásához 70 nap
- \[Előzetes\]verzió: Követelmények telepítése olyan Windows rendszerű virtuális gépek naplózására, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes\]verzió: Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakternél
- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió: A jelszó-bonyolultsági beállítással nem rendelkező Windows-alapú virtuális gépek naplózása
- \[Előzetes\]verzió: A maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózása 70 nap
- \[Előzetes\]verzió: Olyan Windows rendszerű virtuális gépek naplózása, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes\]verzió: A jelszó minimális hosszát 14 karakterre nem korlátozó Windows-alapú virtuális gépek naplózása
- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózása

A terv az Azure-erőforrásokhoz való hozzáférés szabályozását is lehetővé teszi Azure Policy definíciók hozzárendelésével. Ezek a házirendek olyan erőforrástípusok és konfigurációk használatát naplózzák, amelyek lehetővé tehetik az erőforrásokhoz való hozzáférést. A szabályzatok megsértése miatti erőforrások megismerése segíthet az Azure-erőforrások elérését engedélyező, a jogosult felhasználókra korlátozódó kijavítási műveletek elvégzésében.

- \[Előzetes\]verzió: Követelmények telepítése a jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózására
- \[Előzetes\]verzió: Követelmények telepítése a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózásához
- \[Előzetes\]verzió: Linux rendszerű virtuális gépek naplózása jelszó nélküli fiókkal
- \[Előzetes\]verzió: A jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózása
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="11-external-interface-protection"></a>11 külső felület védelme

Ha több mint 25 házirendet használ a megfelelő biztonságos felhasználói felügyelethez, ez a terv a nem korlátozott tárolási fiókokat figyelő [Azure Policy](../../../policy/overview.md) -definíció hozzárendelésével segíti a szolgáltatási felületek jogosulatlan hozzáférés elleni védelmét. A korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz. Ez a terv egy olyan szabályzatot is hozzárendel, amely lehetővé teszi az adaptív alkalmazások vezérlését a virtuális gépeken.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="12-secure-service-administration"></a>12 biztonságos szolgáltatás felügyelete

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv segít az emelt szintű hozzáférési jogosultságok korlátozásában és szabályozásában úgy, hogy öt [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a tulajdonossal és/vagy írási engedélyekkel rendelkező külső fiókok, valamint a tulajdonossal és/vagy írási engedélyekkel rendelkező fiókokhoz. a többtényezős hitelesítés engedélyezve van.

A felhőalapú szolgáltatás felügyeletéhez használt rendszerek magas jogosultsági szintű hozzáférést kapnak a szolgáltatáshoz. A kompromisszum jelentős hatással lenne, beleértve a biztonsági ellenőrzés megkerülését és a nagy adatmennyiségek ellopását és kezelését. A szolgáltató rendszergazdája által az operatív szolgáltatás kezeléséhez használt módszereket úgy kell kialakítani, hogy enyhítse a szolgáltatás biztonságát veszélyeztető kiaknázás kockázatát. Ha ez az elv nem valósul meg, a támadók megkerülhetik a biztonsági ellenőrzéseket, vagy nagy mennyiségű adatmennyiséget ellopnak vagy kezelhetnek.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv Azure Policy definíciókat rendel hozzá az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

Ez a terv Azure Policy-definíciókat is hozzárendeli a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a emelt szintű engedélyekkel rendelkező külső fiókokat is. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két Azure Policy-definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv egy Azure Policy-definíciót is hozzárendel, amely a Linux rendszerű virtuális gép jelszavas engedélyeinek naplózására vonatkozó engedélyeket naplózza, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi a korrekciós műveletek elvégzését, hogy a hitelesítő adatok ne legyenek biztonságban.

- \[Előzetes\]verzió: A Linux rendszerű virtuális gép/etc/passwd engedélyeinek naplózása 0644

## <a name="13-audit-information-for-users"></a>13 naplózási információ a felhasználók számára

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével. A hozzárendelt szabályzat azt is naplózza, hogy a virtuális gépek nem küldenek naplókat egy adott log Analytics-munkaterületre.

- Nem naplózott SQL-kiszolgálók figyelése Azure Security Center
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- \[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetének vezérlési leképezését, az alábbi cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – áttekintés](./index.md)
> [az Egyesült Királyság hivatalos és az Egyesült királyságbeli NHS-tervezetekről – lépések telepítése](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
