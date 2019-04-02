---
title: Minta - ISO 27001, a megosztott szolgáltatások tervezet - vezérlő leképezés
description: Az ISO 27001, a megosztott szolgáltatások tervezet minta és az Azure Policy RBAC vezérlő leképezését.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 3ce755b62ce2ba0328852b551225ffa828a6c1bf
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804683"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001, a megosztott szolgáltatások tervezet minta vezérlő leképezés

A következő cikk részletezi, hogyan az Azure tervezetek ISO 27001 megosztott szolgáltatások tervezetet a minta az ISO 27001 vezérlők vannak leképezve. A vezérlőkkel kapcsolatos további információkért lásd: [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

A következő hozzárendeléseket a rendszer a **ISO 27001:2013** szabályozza. A jobb oldalon a navigációs segítségével közvetlenül egy adott vezérlőt leképezés ugorhat. Számos, a csatlakoztatott vezérlők implementálása egy [Azure Policy](../../../policy/overview.md) kezdeményezésére. Tekintse át a teljes kezdeményezés, nyissa meg a **házirend** az Azure Portalon, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a  **[Előzetes verzió] naplózási ISO 27001:2013 vezérli, és a naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése** beépített szabályzat-kezdeményezéshez.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 szétválasztására

Csak egy Azure-előfizetés tulajdonosa kellene nem engedélyezi a felügyeleti redundancia biztosítása érdekében. Ezzel szemben túl sok Azure-előfizetéssel rendelkező kellene növelheti a lehetséges egy biztonsági kockázatot jelentő tulajdonosok fiók keresztül megsértése. Ez a megoldás segít a megfelelő számú Azure-előfizetéssel rendelkező karbantartása két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózása az Azure-előfizetések tulajdonosainak száma definíciókat. Előfizetés tulajdonosi engedélyekkel kezelése segíthetnek a megfelelő feladatkörök megvalósításához.

- [Előzetes verzió]: Audit minimum number of owners for subscription
- [Előzetes verzió]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A hálózatok és hálózati szolgáltatások A.9.1.2 hozzáférés

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC) az Azure-erőforrásokhoz való hozzáférés kezeléséhez. Ez a megoldás segít az Azure-erőforrásokhoz való hozzáférés szabályozása hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat. Ezek a szabályzatok, erőforrás-típus használatának naplózása és konfigurációkat lehet megengedőbb elérhető erőforrások.
A feljogosított felhasználók ezek házirendeket is meg javítási műveleteket az Azure-erőforrásokhoz való hozzáférés biztosítása súgó korlátozódik, nem megfelelő erőforrások ismertetése.

- [Előzetes verzió]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Előzetes verzió]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Jelszavaks
- [Előzetes verzió]: Audit Linux VM accounts with no passwords
- [Előzetes verzió]: Audit Linux VM allowing remote connections from accounts with no passwords
- Klasszikus tárfiókok használatának naplózása
- Klasszikus virtuális gépek használatának naplózása
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="a922-user-access-provisioning"></a>A.9.2.2 felhasználói hozzáférési kiépítése

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC) az Azure-erőforrásokhoz való hozzáférés kezeléséhez. Ez a megoldás három rendeli [Azure Policy](../../../policy/overview.md) naplózása érdekében használja a [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) hitelesítés az SQL Server-kiszolgálók és [Service Fabric](../../../../service-fabric/service-fabric-overview.md). Az Azure Active Directory authentication lehetővé teszi, engedélykezelés egyszerűsített és központosított Identitáskezelés adatbázis-felhasználók és más Microsoft-szolgáltatások. Ez a megoldás is rendel egy Azure szabályzat-definíció egyéni RBAC-szabályok naplózni kell. Ismertetése, amelyeknél megvalósítása az egyéni RBAC-szabályok segítségével van szüksége, és megfelelő végrehajtásához, győződjön meg arról, egyéni RBAC-szabályok hibalehetőségeket rejt magában.

- Azure Active Directory-rendszergazda-fiók kiépítésének naplózása az SQL-kiszolgálón
- Az Azure Active Directory ügyfél-hitelesítésre való használatának naplózása a Service Fabricben
- Egyéni RBAC-szabályok használatának naplózása

## <a name="a923-management-of-privileged-access-rights"></a>Az emelt szintű A.9.2.3 felügyeleti hozzáférési jogosultságok

Ez a megoldás segít korlátozása, valamint szabályozhatja a privilegizált hozzáférési jogosultsága négy hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók külső fiókok naplózás a tulajdonos és/vagy írási engedélyek és a fiókok a tulajdonosa és/vagy írási engedélyekkel többtényezős hitelesítés engedélyezve van, amely nem rendelkezik.

- [Előzetes verzió]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Előzetes verzió]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Előzetes verzió]: Audit external accounts with owner permissions on a subscription
- [Előzetes verzió]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A felhasználók titkos hitelesítési adatok A.9.2.4 kezelése

Ez a megoldás három rendeli [Azure Policy](../../../policy/overview.md) definíciók naplózása, amelyek nem rendelkeznek a többtényezős hitelesítést engedélyező fiókokat. A multi-factor authentication teszi lehetővé, fiókok biztonságos akkor is, ha sérül a hitelesítési adatok egy részét. Figyelési fiók engedélyezve van a multi-factor authentication nélkül, a fiókok, amelyek nagyobb a valószínűsége, hogy sérülhet azonosíthatja. Ez a megoldás is rendel két Azure szabályzatdefiníció, amely Linux rendszerű virtuális gépek naplózása jelszó fájlengedélyek riasztást küldjön, ha azok nem megfelelően van-e állítva. Ez a beállítás lehetővé teszi annak hitelesítők nem sérült a biztonsága érdekében javítási intézkedéseket.

- [Előzetes verzió]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Előzetes verzió]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Előzetes verzió]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Előzetes verzió]: Deploy VM extension to audit Linux VM passwd file permissions
- [Előzetes verzió]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Felhasználói hozzáférési jogosultságokat A.9.2.5 áttekintése

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC) segítségével felügyelt ki férhet hozzá erőforrásokat az Azure-ban. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. A tervezet rendeli hozzá, négy [Azure Policy](../../../policy/overview.md) fiókokat, ellenőrzésre, beleértve a értékcsökkentett és emelt szintű engedélyekkel rendelkező külső fiókok élveznek naplózása érdekében.

- [Előzetes verzió]: Audit deprecated accounts on a subscription
- [Előzetes verzió]: Audit deprecated accounts with owner permissions on a subscription
- [Előzetes verzió]: Audit external accounts with owner permissions on a subscription
- [Előzetes verzió]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 eltávolítása és a hozzáférési jogosultságok beállítása

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC) segítségével felügyelt ki férhet hozzá erőforrásokat az Azure-ban. Használatával [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) és RBAC, felhasználói szerepkörök szervezeti változásainak frissítheti. Ha szükséges, fiókok bejelentkezés blokkolva (vagy eltávolítása), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogosultságok. Ez a megoldás két rendeli [Azure Policy](../../../policy/overview.md) definíciók naplózása értékcsökkentett fiók eltávolítása kell figyelembe venni.

- [Előzetes verzió]: Audit deprecated accounts on a subscription
- [Előzetes verzió]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 Ügyféljelszó-kezelő rendszert

Ez a megoldás segít kényszerítése az erős jelszónak 10 hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat, amelyeket nem minimális erősségét és más jelszókövetelményeinek Windows virtuális gépek naplózása. A jelszó erőssége szabályzat megsértése a virtuális gépek figyelése segítséget nyújt az összes virtuális gép felhasználói fiók jelszavát felelnek meg a házirend biztosítása érdekében javítási műveleteket.

- [Előzetes verzió]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Előzetes verzió]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Előzetes verzió]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Előzetes verzió]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Előzetes verzió]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Előzetes verzió]: Audit Windows VM enforces password complexity requirements
- [Előzetes verzió]: Audit Windows VM maximum password age 70 days
- [Előzetes verzió]: Audit Windows VM minimum password age 1 day
- [Előzetes verzió]: Audit Windows VM passwords must be at least 14 characters
- [Előzetes verzió]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Titkosítási funkciók használatakor A.10.1.1 házirend

Ez a megoldás segít cryptograph vezérlők használatakor a szabályzat kényszerítéséhez 13 hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyeket adott cryptograph vezérlők és naplózási gyenge kriptográfiai beállítások használatát.
Ismertetése, ahol az Azure-erőforrások rendelkezhetnek titkosítási konfiguráció nem optimális segíthet az információk biztonsági szabályzatának megfelelően konfigurált erőforrások biztosítása érdekében javítási műveleteket. Pontosabban a házirendek, ez a megoldás által hozzárendelt titkosítás használata a blob storage-fiókok és a data lake storage-fiókok; Transzparens adattitkosítás; SQL-adatbázis szükséges a storage-fiókok, SQL Database-adatbázisok, virtuális gépek lemezeinek és automation-fiók változók; hiányzó titkosítást naplózása a storage-fiókok, Function Apps, Web App, az API Apps és Redis Cache; nem biztonságos kapcsolatok naplózása gyenge virtuális gép titkosított; naplózása és a Service Fabric a titkosítatlan kommunikációnak.

- [Előzetes verzió]: Audit HTTPS only access for a Function App
- [Előzetes verzió]: Audit HTTPS only access for a Web Application
- [Előzetes verzió]: Audit HTTPS only access for an API App
- [Előzetes verzió]: Audit missing blob encryption for storage accounts
- [Előzetes verzió]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  titkosításn
- [Előzetes verzió]: Audit Windows VM should not store passwords using reversible encryption
- [Előzetes verzió]: Monitor unencrypted SQL database in Azure Security Center
- [Előzetes verzió]: Monitor unencrypted VM Disks in Azure Security Center
- Az Automation-fiókbeli változótitkosítás engedélyezésének naplózása
- Azon művelet naplózása, amely a Redis Cache kizárólag biztonságos kapcsolaton keresztül történő elérését engedélyezi
- Tárfiókokba történő biztonságos átvitel naplózása
- A ClusterProtectionLevel tulajdonság EncryptAndSign értékre való beállításának naplózása a Service Fabricben
- Transzparens adattitkosítás állapotának naplózása

## <a name="a1241-event-logging"></a>A.12.4.1 események naplózása

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózási beállításai az Azure-erőforrás naplózása definíciókat. Szabályzat akkor is naplózza, ha a virtuális gépek nem naplókat küld a megadott log analytics-munkaterület.

- [Előzetes verzió]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- [Előzetes verzió]: Függőségi ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- [Előzetes verzió]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- [Előzetes verzió]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- [Előzetes verzió]: Monitor unaudited SQL database in Azure Security Center
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálói szintű naplózási beállítások naplózása

## <a name="a121-management-of-technical-vulnerabilities"></a>Műszaki biztonsági rések A.12.1 kezelése

Ez a megoldás segít információt rendszer biztonsági rések kezelése öt hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek a hiányzó Rendszerfrissítések, operációs rendszer biztonsági rései, az SQL biztonsági réseket és a virtuális gép figyelése biztonsági rések. Ezen elemzési valós idejű ismertetik a telepített erőforrások biztonsági állapotát, és segíthet a szervizelési műveletek rangsorolására.

- [Előzetes verzió]: Monitor missing Endpoint Protection in Azure Security Center
- [Előzetes verzió]: Monitor missing system updates in Azure Security Center
- [Előzetes verzió]: Monitor OS vulnerabilities in Azure Security Center
- [Előzetes verzió]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Előzetes verzió]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>Szoftvertelepítés A.12.6.2 korlátozásai

Adaptív Alkalmazásvezérlés a megoldás az Azure Security Centerben, amelyek segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépek. Ez a megoldás egy Azure szabályzat-definíció, amely figyeli a módosításokat az engedélyezett alkalmazások a csoporthoz rendeli hozzá. Korlátozások a Szoftvertelepítés segítségével csökkentheti annak lehetőségét, hogy a szoftver biztonsági rések bemutatása.

- [Előzetes verzió]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 hálózati vezérlők

Ez a megoldás segítségével kezelheti és vezérelheti hálózatok hozzárendelése egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a megengedő hálózati biztonsági csoportok. Előfordulhat, hogy nem kívánt hálózati hozzáférés engedélyezése, amelyek túl megengedő szabályok, és át kell tekinteni.

- [Előzetes verzió]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 információk átviteli szabályok és eljárások

A tervezet segítségével győződjön meg, hogy információ átvitele az Azure-szolgáltatásokkal biztonságos két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózását, tárfiókok és a Redis Cache biztonságos kapcsolatok definícióit.

- Azon művelet naplózása, amely a Redis Cache kizárólag biztonságos kapcsolaton keresztül történő elérését engedélyezi
- Tárfiókokba történő biztonságos átvitel naplózása

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 védelme alkalmazásszolgáltatások tranzakciók

Ez a megoldás védheti meg információs rendszerek eszközeit teheti három [Azure Policy](../../../policy/overview.md) figyelheti a védelem nélküli végpontok, alkalmazások és storage-fiókok definíciókat. Végpontok és a egy tűzfal és a korlátlan hozzáférést a storage-fiókok által nem védett alkalmazások lehetővé tehetik a információs rendszerben található információkhoz való nem szándékos hozzáférés.

- [Előzetes verzió]: Monitor unprotected network endpoints in Azure Security Center
- [Előzetes verzió]: Monitor unprotected web application in Azure Security Center
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 jelentéskészítési információk biztonsági hiányosságok

Ez a megoldás segít a figyelése, a rendszer biztonsági rések kezelése öt hozzárendelésével [Azure Policy](../../../policy/overview.md) figyelheti a biztonsági réseket, javítási állapota és az Azure Security Centerben kártevőszoftver-figyelmeztetések definíciókat. Az Azure Security Center jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy üzembe helyezett Azure-erőforrások biztonsági állapotának valós idejű betekintést biztosít.

- [Előzetes verzió]: Monitor missing Endpoint Protection in Azure Security Center
- [Előzetes verzió]: Monitor missing system updates in Azure Security Center
- [Előzetes verzió]: Monitor OS vulnerabilities in Azure Security Center
- [Előzetes verzió]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Előzetes verzió]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001, a megosztott szolgáltatások tervezet vezérlő leképezése, látogasson el a további információ az architektúra és üzembe helyezése a minta az alábbi cikkeket:

> [!div class="nextstepaction"]
> [ISO 27001, a megosztott szolgáltatások tervezet - áttekintés](./index.md)
> [ISO 27001, a megosztott szolgáltatások tervezetet – a telepítés lépéseit](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.