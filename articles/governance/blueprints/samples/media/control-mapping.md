---
title: Médiamintamint-vezérlők
description: A Média tervezetminták vezérlőleképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201905"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>A Médiatervezet minta vezérlőtérképezése

A következő cikk részletezi, hogy az Azure Blueprints Media minta leképezi a Media-vezérlők. A vezérlőkről további információt az Adathordozó című témakörben [talál.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

Az alábbi leképezések a **Média** vezérlőkhöz tartoznak. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és jelölje ki az ** \[Előzetes\]verzió : Naplózási vezérlők** beépített házirend-kezdeményezést.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)

## <a name="access-control"></a>Hozzáférés-vezérlés

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Győződjön meg arról, hogy nem létezik legfelső szintű hozzáférési kulcs

- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyek nem tartalmazzák a megadott tanúsítványokat a Megbízható gyökér ben

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 – A jelszavakat, PIN-kódokat és tokeneket védeni kell

- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - A megosztott fiókhoz való hozzáférés tilos

- A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani a Service Bus névteréből

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -A rendszernek korlátoznia kell a hozzáférést a jogosult felhasználók számára.

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 -A rendszernek érvényesítenie kell a hozzáférési jogokat.

- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Felhasználói jogok hozzárendelése" című részben

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 - A biztonsággal kapcsolatos információkhoz vagy funkciókhoz való jogosulatlan hozzáférés megakadályozása.

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Rendszerbeállítások" párbeszédpanelen

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - A feladatok elkülönítését a szerepkör megfelelő feladatával kell végrehajtani.

- [Előzetes\]verzió: A szerepköralapú hozzáférés-vezérlést (RBAC) a Kubernetes-szolgáltatásokon kell használni

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40- Győződjön meg arról, hogy a rendszerek nem csatlakoznak egyszerre megbízható hálózathoz és nem megbízható hálózatokhoz.

- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Hálózati hozzáférés" szolgáltatásban

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 - A nem alkalmazottak távoli hozzáférését korlátozni kell, hogy csak a kifejezetten jóváhagyott információs rendszerekhez lehessen hozzáférni

- \[Előzetes\]verzió : A linuxos virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a jelszavak nélküli fiókok távoli kapcsolatait

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - A biztonsággal kapcsolatos események naplózása az információs rendszer összes összetevőjéhez.

- A Logic Apps diagnosztikai naplóit engedélyezni kell

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 – Győződjön meg arról, hogy a többtényezős hitelesítés (MFA) engedélyezve van minden felhőkonzol-felhasználó számára.

- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- A többtényezős hitelesítést (MFA) engedélyezni kell az írási jogosultságokkal rendelkező összes előfizetési fiókhoz a fiókok vagy erőforrások megsértésének megelőzése érdekében.

## <a name="auditing--logging"></a>Naplózás naplózása &

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 - A sikeres és sikertelen eseményeket naplózni kell.

- A keresési szolgáltatások diagnosztikai naplóit engedélyezni kell

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - A hálózati eszközöknek/példányoknak naplózniuk kell minden olyan eseményt, amelyet az adott hálózati eszköz/példány kritikus biztonsági eseménynek minősít (ELB-k, webalkalmazás-tűzfalak stb.)

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Fiókok" párbeszédpanelen

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- A kiszolgálóknak/példányoknak naplózniuk kell minden olyan eseményt, amelyet az adott kiszolgáló/példány kritikus biztonsági eseménynek minősített

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Fiókok" párbeszédpanelen

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - A tartományi eseményeknek naplózniuk kell minden olyan eseményt, amelyet a tartománykezelő szoftver kritikus vagy magas biztonsági eseménynek minősít

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Fiókok" párbeszédpanelen
- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Microsoft hálózati ügyfél" párbeszédpanelen

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - A tartományi eseményeknek naplózniuk kell minden olyan eseményt, amelyet a tartomány biztonsági vezérlői kritikus biztonsági eseménynek minősítenek

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Fiókok" párbeszédpanelen

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 - A tartományi eseményeknek naplózniuk kell a tartományi naplóhoz való hozzáférést és módosításokat

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Helyreállítási konzol" párbeszédpanelen

## <a name="cryptographic-controls"></a>Kriptográfiai vezérlők

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2- Az alkalmazásoknak és rendszereknek a jelenlegi kriptográfiai megoldásokat kell használniuk az adatok védelmére.

- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- Az adattitkosítást engedélyezni kell a nyugalmi adatok védelme és a megfelelőségi követelmények teljesítése érdekében

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- A digitális tanúsítványokat jóváhagyott hitelesítésszolgáltatónak kell aláírnia.

- \[Előzetes\]verzió : A megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows virtuális gépek naplózási eredményeinek megjelenítése

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 - A digitális tanúsítványokat egyedileg kell hozzárendelni egy felhasználóhoz vagy eszközhöz.

- \[Előzetes\]verzió : Előfeltételek telepítése a megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows virtuális gépek naplózásához

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - A kriptográfiai anyagokat úgy kell tárolni, hogy a rekordok visszafejtése a rekordok megőrzésének időtartamára lehetővé váljon.

- A lemeztitkosítást virtuális gépeken kell alkalmazni
- Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket az Azure Security Center javaslatokként figyeli

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- A titkos és a személyes kulcsokat biztonságosan kell tárolni.

- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- Az adattitkosítást engedélyezni kell a nyugalmi adatok védelme és a megfelelőségi követelmények teljesítése érdekében

## <a name="change--config-management"></a>A & a konfigurációkezelés módosítása

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Csak a jogosult felhasználók hajthatnak végre jóváhagyott módosításokat a rendszeren.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket az Azure Security Center javaslatok alapján figyeli.

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - Az információs rendszer naprakész, teljes, pontos és könnyen elérhető alapkonfigurációjának karbantartása.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket az Azure Security Center javaslatok alapján figyeli.

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - Az információs rendszer alapkonfigurációjának fenntartásához automatizált eszközöket alkalmazzon.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket az Azure Security Center javaslatok alapján figyeli.

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - A szükségtelen és/vagy nem biztonságos funkciók, portok, protokollok és szolgáltatások azonosítása és letiltása.

- A hálózati adaptereknek le kell tiltaniuk az IP-továbbítást
- \[Előzetes\]verzió: Az IP-továbbítást le kell tiltani a virtuális gépen

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - A biztonsági beállítások figyelése.

- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22- Győződjön meg arról, hogy csak engedélyezett szoftverek és frissítések vannak telepítve a vállalati rendszereken.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket az Azure Security Center javaslatok alapján figyeli.

## <a name="identity--authentication"></a>Identitás & hitelesítés

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- A felhasználói fiókokat egyedileg kell hozzárendelni az egyénekhez, hogy hozzáférjenek a nyilvánosnak nem minősített információkhoz. A fiókazonosítókat szabványos logikai formátumban kell létrehozni.

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférés megakadályozása érdekében.

## <a name="network-security"></a>Hálózati biztonság

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - A hálózati eszközkezelési funkciókhoz való hozzáférés csak a jogosult felhasználók számára érhető el.

- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Hálózati hozzáférés" szolgáltatásban

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Minden hálózati eszközt a legbiztonságosabb konfigurációkkal kell konfigurálni.

- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Hálózati hozzáférés" szolgáltatásban

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5- A tűzfalon keresztül a rendszerhez való összes hálózati kapcsolatot rendszeresen jóvá kell hagyni és naplózni kell.

- \[Előzetes\]verzió : A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Windows tűzfal tulajdonságai" párbeszédpanelen

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- A megfelelő vezérlőknek a megbízható hálózat és a nem megbízható vagy nyilvános hálózatok közötti bármely határon jelen kell lenniük.

- \[Előzetes\]verzió : A Windows-virtuális gépek konfigurációinak naplózásához szükséges előfeltételek központi telepítése a "Windows tűzfal tulajdonságai" párbeszédpanelen

## <a name="security-planning"></a>A biztonság tervezése

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - Olyan fenyegetéseket kell azonosítani, amelyek negatívan befolyásolhatják a vállalati információk és tartalmak titkosságát, integritását vagy elérhetőségét, valamint előfordulásuk valószínűségét.

- A komplex veszélyforrások elleni védelem típusait "All" típusúra kell állítani az SQL felügyelt példányok speciális adatbiztonsági beállításaiban.

### <a name="security-continuity"></a>Biztonsági folytonosság

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5- A hosszú távú tárolás során tárolt adatoknak a megőrzési időszak során hozzáférhetőnek kell lenniük, és védeniük kell őket a média károsodásától és a technológiai változásoktól.

- Az SQL-kiszolgálókat 90 napnál hosszabb naplózási megőrzési nappal kell konfigurálni.
- 90 napnál rövidebb naplózási megőrzési idővel konfigurált SQL-kiszolgálók naplózása.

## <a name="system-integrity"></a>Rendszer integritása

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3- Csak arra jogosult személyek figyelhetik a hálózati és felhasználói tevékenységeket.

- Az SQL-adatbázisok biztonsági réseit ki kell újítani
- A Biztonsági rés felmérésének ellenőrzése az adatbázis biztonsági rései rekretire vonatkozó eredmények és javaslatok.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - Az internetre néző rendszereknek behatolásérzékelővel kell rendelkezniük.

- Fenyegetésészlelés telepítése SQL-kiszolgálókon

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13- Szabványosított központilag kezelt anti-malware szoftver kell végrehajtani az egész cég.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- A kártevőirtó szoftvereknek legalább hetente be kell szaniuk a számítógépeket és a médiát.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 – Győződjön meg arról, hogy az alkalmazások havonta ellenőrzik a biztonsági réseket.

- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- Ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokon, hogy megvédje őket a támadásoktól.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 – Győződjön meg arról, hogy a biztonsági rések azonosítása, a fenyegetések és a kockázatok értékelése.

- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- Ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokon, hogy megvédje őket a támadásoktól.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 – Győződjön meg arról, hogy az azonosított biztonsági réseket egy kölcsönösen elfogadott idővonalon belül orvosolták.

- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- Ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokon, hogy megvédje őket a támadásoktól.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - A biztonsági réskezelő rendszerekhez való hozzáférést és azok használatát az arra jogosult személyekre kell korlátozni.

- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- Ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokon, hogy megvédje őket a támadásoktól.

> [!NOTE]
> Bizonyos Azure-szabályzat-definíciók elérhetősége az Azure Government és más nemzeti felhők ben eltérő lehet. 

## <a name="next-steps"></a>További lépések

Áttekintette a Média tervezetminta vezérlőleképezését. Ezután látogasson el az alábbi cikkekbe, amelyek ből megtudhatja az áttekintést és a minta üzembe helyezését:

> [!div class="next step action"]
> [Médiatervezet - Áttekintő](./control-mapping.md)
> [médiatervezet - Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
