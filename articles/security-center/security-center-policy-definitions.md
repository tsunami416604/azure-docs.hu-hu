---
title: Azure szabályzatdefiníciók figyeli az Azure Security Centerben |} A Microsoft Docs
description: Azure szabályzatdefiníciók az Azure Security Center figyeli.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: a4f9fc31f411d36e63775a3665b6dfe27eec7710
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319378"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Az Azure Security Center által figyelt Azure biztonsági szabályzatok
Ez a cikk az Azure szabályzat-definíciókat a Security Center által megfigyelt listáját.

## <a name="available-security-policies"></a>Biztonsági szabályzatok

Szeretné megtudni, a beépített szabályzatokat, a Security Center által figyelt, tekintse meg a következő táblázatban:

| Szabályzat | A szabályzat működése |
| --- | --- |
|Diagnosztikai naplózás engedélyezése a Service Fabricben és a virtuálisgép-méretezési csoportokban|A naplózást ajánlott engedélyezni, annak érdekében, hogy a tevékenységnaplót újra létre lehessen hozni, ha incidens vagy a biztonság veszélyeztetése esetén vizsgálatokra van szükség.|
|Event Hubs-névterek engedélyezési szabályainak naplózása|Event Hub-ügyfelek ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Event Hubs-entitásokra vonatkozó engedélyezési szabályok meglétének naplózása|Event Hub-entitások alacsonyabb szintű hozzáférést biztosítani az engedélyezési szabályok meglétének naplózása.|
|Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálja a hálózati szabályok, az engedélyezett hálózatok csak alkalmazások férhetnek hozzá a tárfiók. Az adott internetről fogadjon kapcsolatokat, vagy egy helyszíni ügyfelek, a hozzáférés adható adott Azure virtuális hálózatok forgalmát, vagy nyilvános internetes IP-címtartományok.|
|Egyéni RBAC-szabályok használatának naplózása|Naplózza a beépített szerepkörök, például "Tulajdonosi, közreműködői, olvasó" helyett egyéni RBAC-szerepkörök, amelyek hibalehetőségeket rejt magában. Egyéni szerepkörök használatával kivételként kezeli, és szigorú áttekintése és a fenyegetések modellezése szükséges.|
|Az Azure Stream Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Tárfiókokba történő biztonságos átvitel naplózása|Naplózási követelmény, biztonságos átvitel a storage-fiókban. Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. HTTPS használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – például a hálózati réteg támadások ellen védi a az átvitt adatokat.|
|Azure Active Directory-rendszergazda-fiók kiépítésének naplózása az SQL-kiszolgálón|Naplózási üzembe helyezése egy Azure Active Directory-rendszergazda az SQL Server az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés lehetővé teszi, hogy egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel.|
|Service Bus-névterek engedélyezési szabályainak naplózása|Service Bus-ügyfél ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|A Service Bus-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A ClusterProtectionLevel tulajdonság EncryptAndSign értékre való beállításának naplózása a Service Fabricben|Service Fabric egy elsődleges fürttanúsítvány csomópontok közötti kommunikációt három szintje (nincs, bejelentkezési és EncryptAndSign) védelmet biztosít. Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása.| 
|Az Azure Active Directory ügyfél-hitelesítésre való használatának naplózása a Service Fabricben|A kizárólag az Azure Active Directoryval való ügyfél-hitelesítés használatának naplózása a Service Fabricben| 
|A Search szolgáltatásbeli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Azon művelet naplózása, amely a Redis Cache kizárólag biztonságos kapcsolaton keresztül történő elérését engedélyezi|Naplózás engedélyezése csak SSL Redis Cache-en keresztül kapcsolatok. Biztonságos kapcsolatok használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és az átvitt adatokat védi a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés| 
|A Logic Apps-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|A Key Vault-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Az Event Hubs-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi a támadások hatékonyabb kivizsgálásához tevékenység nyomot hagyjanak maguk után újra létrehozhatja, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Az Azure Data Lake Store-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|A Data Lake Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Klasszikus tárfiókok használatának naplózása|A tárfiókok az Azure Resource Manager használatával adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), jobb naplózás, az Azure Resource Manager-alapú üzembe helyezési és irányítási elérését a felügyelt identitásokból, titkos kulcsok a key vaulthoz való hozzáférés , Az azure AD-alapú hitelesítés és a címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése.| 
|Klasszikus virtuális gépek használatának naplózása|A virtuális gépek Azure Resource Manager használatával adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), jobb naplózás, az Azure Resource Manager-alapú üzembe helyezési és irányítási elérését a felügyelt identitásokból, titkos kulcsok a key vaulthoz való hozzáférés , Az azure AD-alapú hitelesítés és a címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése.| 
|Batch-fiókok metrikaalapú riasztási szabályaira vonatkozó konfiguráció naplózása |Batch-fiókhoz a szükséges metrika engedélyezéséhez a metrikaalapú riasztási szabályok naplózás konfigurációja.| 
|Batch-fiókok metrikaalapú riasztási szabályaira vonatkozó konfiguráció naplózása |Batch-fiókhoz a szükséges metrika engedélyezéséhez a metrikaalapú riasztási szabályok naplózás konfigurációja.| 
|Az Azure Stream Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Az Automation-fiókbeli változótitkosítás engedélyezésének naplózása|Bizalmas adatok tárolásakor fontos engedélyezni az Automation-fiók változókészleteinek a titkosítását.| 
|Az App Services-beli diagnosztikai naplók engedélyezésének naplózása|Naplózza az alkalmazást a diagnosztikai naplók engedélyezése. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, ha egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Transzparens adattitkosítás állapotának naplózása|SQL-adatbázisok transzparens adattitkosítási állapotának naplózása.| 
|SQL-kiszolgálói szintű naplózási beállítások naplózása|Az SQL-naplózás a kiszolgáló szintjén megléte eseményeket.| 
|[Előzetes verzió]: Nem titkosított SQL-adatbázis figyelése az Azure Security Centerben|Titkosítatlan SQL-kiszolgálókat és adatbázisokat figyeli a rendszer az Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Nem naplózott SQL-adatbázis figyelése az Azure Security Centerben|SQL Serverek és adatbázisok nem rendelkezik SQL-naplózás engedélyezve van, amely felügyeli az Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Hiányzó rendszerfrissítések figyelése az Azure Security Centerben|Hiányzó biztonsági a kiszolgálókon által figyelt Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Tárfiókok hiányzó blobtitkosításának naplózása|Ez a szabályzat a blobtitkosítás nélküli tárfiókokat naplózza. Kizárólag a Microsoft.Storage erőforrástípusokra, más tárolási szolgáltatók nem vonatkozik. Lehetséges hálózati igény szerinti hozzáférést ajánlásként az Azure Security Center által figyelt.| 
|[Preview]: Igény szerinti hálózati hozzáférés figyelése az Azure Security Centerben|Lehetséges hálózati csak az idő szerinti (JIT) hozzáférési ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Alkalmazások engedélyezésének figyelése az Azure Security Centerben|Lehetséges Alkalmazásengedélyezési konfigurációkat az Azure Security Center figyeli.| 
|[Előzetes verzió]: A megengedő hálózati hozzáférés figyelése az Azure Security Centerben|A túlzottan megengedő hálózati biztonsági csoportokat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Operációs rendszer biztonsági réseinek figyelése az Azure Security Centerben|Kiszolgálók, amelyek nem a konfigurált alapértékeknek ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Hiányzó Endpoint Protection figyelése az Azure Security Centerben|Az Endpoint Protection telepített ügynök nélküli kiszolgálókat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Nem titkosított VM-lemezek figyelése az Azure Security Centerben|Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Virtuális gépek biztonsági réseinek figyelése az Azure Security Centerben|A Vulnerability Assessment megoldás által észlelt biztonsági rések és a Vulnerability Assessment megoldást nem használó virtuális gépek ajánlásként történő figyelése az Azure Security Centerben.| 
|[Előzetes verzió]: Nem védett webalkalmazás figyelése az Azure Security Centerben|Egy webalkalmazási tűzfallal nem védett webalkalmazásokat ajánlásként az Azure Security Center által figyelendő ki.| 
|[Előzetes verzió]: Nem védett hálózati végpontok figyelése az Azure Security Centerben|Egy az Újgenerációs tűzfallal nem védett hálózati végpontokat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: SQL – sebezhetőségi felmérés eredményeinek figyelése az Azure Security Centerben|A sebezhetőségi felmérés vizsgálati eredményeinek és az adatbázisok biztonsági réseinek megszüntetéséhez útmutatást nyújtó ajánlások figyelése.| 
|[Előzetes verzió]: Egy előfizetés tulajdonosai maximális számának naplózása|Ajánlott legfeljebb 3 előfizetés-tulajdonost kijelölni a biztonsági kockázatot jelentő tulajdonosok általi esetleges incidensek csökkentése céljából.| 
|[Előzetes verzió]: Egy előfizetés tulajdonosai minimális számának naplózása|Ajánlott legalább egy előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundanciájának biztosításához.| 
|[Előzetes verzió]: Egy előfizetés azon tulajdonosi engedélyekkel rendelkező fiókjainak naplózása, amelyek számára nincs engedélyezve az MFA|A Multi-Factor Authenticationt (MFA) a tulajdonosi engedéllyel rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Egy előfizetés azon írási engedélyekkel rendelkező fiókjainak naplózása, amelyek számára nincs engedélyezve az MFA|A Multi-Factor Authenticationt (MFA) az írási jogosultsággal rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Egy előfizetés azon olvasási engedélyekkel rendelkező fiókjainak naplózása, amelyek számára nincs engedélyezve az MFA|A Multi-Factor Authenticationt (MFA) az olvasási jogosultsággal rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Egy előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókjainak naplózása|Elavult fiókok tulajdonosi engedélyekkel rendelkező el kell távolítani az előfizetésből. Elavult fiókok olyan fiókok, a bejelentkezés le van tiltva.| 
|[Előzetes verzió]: Egy előfizetés elavult fiókjainak naplózása|Az elavult fiókokat el kell távolítani az előfizetésekből. Elavult fiókok olyan fiókok, a bejelentkezés le van tiltva.| 
|[Előzetes verzió]: Egy előfizetés tulajdonosi engedélyekkel rendelkező külső fiókjainak naplózása|A tulajdonosi engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférések megelőzése céljából.| 
|[Előzetes verzió]: Egy előfizetés írási engedélyekkel rendelkező elavult fiókjainak naplózása|Az írási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.| 
|[Előzetes verzió]: Egy előfizetés olvasási engedélyekkel rendelkező elavult fiókjainak naplózása|Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.| 




## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
