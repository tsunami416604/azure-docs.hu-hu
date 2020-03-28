---
title: PCI-DSS v3.2.1 mintavezérlők
description: A Payment Card Industry Data Security Standard 3.2.1-es számú tervezetminta leképezése az Azure Policy és az RBAC rendszerhez.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905637"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v3.2.1-es tervezetminta vezérlőtérképezése

A következő cikk bemutatja, hogy az Azure Blueprints PCI-DSS v3.2.1 minta minta leképezi a PCI-DSS v3.2.1 vezérlők. A vezérlőkről további információt a [PCI-DSS 3.2.1-es v3.1-es című témakörben talál.](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)

Az alábbi leképezések a **PCI-DSS v3.2.1:2018** vezérlők. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[előzetes\] naplózási PCI v3.2.1:2018 vezérlőket, és telepítsen konkrét virtuálisgép-bővítményeket** a beépített házirend-kezdeményezés naplózási követelményeinek támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md)

## <a name="132-and-134-boundary-protection"></a>1.3.2. és 1.3.4.

Ez a tervezet segít a hálózatok kezeléséhez és vezérléséhez az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek megengedő szabályokkal figyelik a hálózati biztonsági csoportokat. A túl megengedő szabályok nem kívánt hálózati hozzáférést engedélyezhetnek, ezért azát felül kell vizsgálni. Ez a tervezet hozzárendel egy Azure-szabályzat-definíciók, amelyek a nem védett végpontok, alkalmazások és tárfiókok figyelése. Végpontok és alkalmazások, amelyek nem védi a tűzfal, és a tárfiókok korlátlan hozzáféréssel lehetővé teheti a nem kívánt hozzáférést az információs rendszer ben található információkhoz.

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- Az internetfelé néző végponton keresztüli hozzáférést korlátozni kell

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1., 4.1.g., 4.1.h.

Ez a tervezet segít a szabályzat kényszerítésében a kriptográfiai vezérlők használatával az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek kikényszerítik az adott titkosítási vezérlőket és a gyenge kriptográfiai beállítások naplózását. Ha tisztában van azzal, hogy az Azure-erőforrások hol rendelkezhetnek nem optimális kriptográfiai konfigurációkkal, akkor korrekciós műveleteket végezhet annak érdekében, hogy az erőforrások az információbiztonsági szabályzatnak megfelelően legyenek konfigurálva. Pontosabban a tervezet által hozzárendelt házirendek transzparens adattitkosítást igényelnek az SQL-adatbázisokon; naplózhatja a hiányzó titkosítást a tárfiókokon és az automatizálási fiókváltozókon. Vannak olyan szabályzatok is, amelyek a tárfiókokhoz, a függvényalkalmazásokhoz, a webappokhoz, az API-alkalmazásokhoz és a Redis-gyorsítótárhoz való nem biztonságos kapcsolatok naplózását, valamint a titkosítatlan Service Fabric-kommunikáció naplózását kezelik.

- A Függvényalkalmazás csak HTTPS-en keresztül érhető el
- A webalkalmazás csak HTTPS-en keresztül érhető el
- Az API-alkalmazás csak HTTPS-en keresztül érhető el
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- A lemeztitkosítást virtuális gépeken kell alkalmazni
- Az automatizálási fiókváltozókat titkosítani kell
- Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A Service Fabric-fürtöknek encryptAndSign-re kell állítaniuk a ClusterProtectionLevel tulajdonságot
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- Az SQL DB transzparens adattitkosításának telepítése

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 és 11.2.1 Biztonsági rés vizsgálata és rendszerfrissítések

Ez a tervezet segít az információs rendszer biztonsági réseinek kezelésében azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek biztonsági réseit az Azure Security Centerben. Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába.

- Hiányzó végpontvédelem figyelése az Azure Security Centerben
- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez
- Fenyegetésészlelés telepítése SQL-kiszolgálókon
- A rendszerfrissítéseket telepíteni kell a gépeken
- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani
- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2. és 7.1.3.

Ha csak egy Azure-előfizetés-tulajdonossal rendelkezik, az nem teszi lehetővé a felügyeleti redundanciát. Ezzel szemben a túl sok Azure-előfizetés-tulajdonos növelheti a feltört tulajdonosi fiókon keresztül a feltört fiók megsértése lehetőségét. Ez a tervezet segít fenntartani a megfelelő számú Azure-előfizetés-tulajdonosok hozzárendelésével Azure Policy-definíciók, amelyek naplózza a tulajdonosok száma az Azure-előfizetések. [Azure Policy](../../../policy/overview.md) Az előfizetés tulajdonosi engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1., 8.3.1.a.

Ez a tervezet segít korlátozni és szabályozni a kiemelt hozzáférési jogokat azáltal, hogy [az Azure Policy-definíciók](../../../policy/overview.md) at a tulajdonossal, írási és/vagy olvasási engedélyekkel rendelkező külső fiókok naplózásához, valamint olyan tulajdonosi és/vagy írási engedélyekkel rendelkező alkalmazotti fiókok naplózásához, amelyeknél nincs engedélyezve a többtényezős hitelesítés. Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg annak kezeléséhez, hogy ki férhet hozzá az Azure-erőforrásokhoz. Az egyéni RBAC-szabályok megvalósításának ismertetése segíthet a szükséges és megfelelő megvalósítás ellenőrzésében, mivel az egyéni RBAC-szabályok hibalehetőségeket rejtenek. Ez a tervezet is hozzárendeli az [Azure Policy-definíciók](../../../policy/overview.md) az Azure Active Directory-hitelesítés SQL-kiszolgálók hoz történő használatának naplózásához. Az Azure Active Directory-hitelesítés használata leegyszerűsíti az engedélyek kezelését, és központosítja az adatbázis-felhasználók és más Microsoft identitáskezelését  
máris használni tudják azt.
 
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon
- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- Egyéni RBAC-szabályok használatának naplózása

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2. és 8.1.5 A felhasználói hozzáférési jogok legkisebb kiváltsága és felülvizsgálata

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, hogy könnyebben kezelhesse, ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet [hozzárendeli az Azure Policy-definíciók](../../../policy/overview.md) at a naplózási fiókok, amelyeket prioritásként kell kezelni a felülvizsgálat, beleértve az amortizált fiókok és a külső fiókok emelt szintű engedélyekkel.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 A hozzáférési jogok megszüntetése vagy kiigazítása

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Active Directory és az RBAC használatával frissítheti a felhasználói szerepköröket, hogy tükrözze a szervezeti változásokat. Szükség esetén a fiókok letilthatók a bejelentkezésben (vagy az eltávolításban), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a tervezet hozzárendeli az [Azure Policy-definíciók](../../../policy/overview.md) naplózása amortizálódik fiók, amely et meg kell fontolni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b,8.2.4.a,b és 8.2.5 Jelszóalapú hitelesítés

Ez a tervezet segít az erős jelszavak kényszerítésében az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózják azokat a Windows virtuális gépeket, amelyek nem kényszerítik ki a minimális erősséget és az egyéb jelszókövetelményeket. A jelszóerősségre vonatkozó szabályzatot megsértő virtuális gépek ismertségét figyelembe véve korrekciós műveleteket is végrehajthatsz annak érdekében, hogy az összes virtuálisgép-felhasználói fiók jelszavai megfeleljenek a szabályzatnak.

- \[Előzetes\]verzió : A 70 napos maximális jelszóval nem rendelkező Windows-virtuális gépek naplózása
- \[Előzetes\]verzió: A 70 napos maximális jelszóval nem rendelkező Windows-virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió : A jelszó minimális hosszát nem tartalmazó Windows-virtuális gépek naplózása 14 karakterre
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózására vonatkozó követelmények telepítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : A korábbi 24 jelszó újbóli használatát lehetővé tévő Windows-virtuális gépek naplózása
- \[Előzetes\]verzió : A korábbi 24 jelszó újbóli használatát lehetővé tévő Windows virtuális gépek naplózására vonatkozó követelmények telepítése

## <a name="103-and-1054-audit-generation"></a>10.3 és 10.5.4 Audit generáció

Ez a tervezet segít biztosítani a rendszeresemények naplózása az [Azure-szabályzat](../../../policy/overview.md) definícióinak hozzárendelésével, amelyek naplóbeállításokat naplóznak az Azure-erőforrásokon.
A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekbe. Az Azure-naplók a szinkronizált belső órák segítségével időbeli korrelált rekord az erőforrások közötti események létrehozása.

- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- Naplózás telepítése SQL-kiszolgálókon
- A tárfiókokat át kell telepíteni az új Azure Resource Manager-erőforrásokba
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba

## <a name="1236-and-1237-information-security"></a>12.3.6. és 12.3.7 Információbiztonság

Ez a tervezet segít a hálózat kezeléséhez és vezérléséhez az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózják az elfogadható hálózati helyeket és a környezetszámára engedélyezett vállalati termékeket. Ezek az egyes vállalatok az egyes házirendek házirend-paraméterein keresztül testreszabhatók.

- Engedélyezett helyek
- Erőforráscsoportok engedélyezett helyei

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a PCI-DSS 3.2.1-es számú tervrajz vezérlőleképezését, az alábbi cikkekben megtudhatja az áttekintést és a minta üzembe helyezését:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 tervezet - Áttekintés](./index.md)
> [PCI-DSS v3.2.1 terv - Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)