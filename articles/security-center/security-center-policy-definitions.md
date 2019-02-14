---
title: Azure szabályzatdefiníciók figyeli az Azure Security Centerben |} A Microsoft Docs
description: Azure szabályzatdefiníciók az Azure Security Center figyeli.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: cbc9fa5586b848de79c52142f75b22b744fdae38
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105950"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Az Azure Security Center által figyelt Azure biztonsági szabályzatok
Ez a cikk az Azure szabályzat-definíciókat a Security Center által megfigyelt listáját. Biztonsági szabályzatok kapcsolatos további információkért lásd: [biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Biztonsági szabályzatok

Szeretné megtudni, a beépített szabályzatokat, a Security Center által figyelt, tekintse meg a következő táblázatban:

| Szabályzat | A szabályzat működése |
| --- | --- |
|Diagnosztikai naplózás engedélyezése a Service Fabricben és a virtuálisgép-méretezési csoportokban|A naplózást ajánlott engedélyezni, annak érdekében, hogy a tevékenységnaplót újra létre lehessen hozni, ha incidens vagy a biztonság veszélyeztetése esetén vizsgálatokra van szükség.|
|Event Hubs-névterek engedélyezési szabályainak naplózása|Event Hub-ügyfelek ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Event Hubs-entitásokra vonatkozó engedélyezési szabályok meglétének naplózása|Event Hub-entitások alacsonyabb szintű hozzáférést biztosítani az engedélyezési szabályok meglétének naplózása.|
|Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálja a hálózati szabályok, az engedélyezett hálózatok csak alkalmazások férhetnek hozzá a tárfiók. Az adott internetről fogadjon kapcsolatokat, vagy egy helyszíni ügyfelek, a hozzáférés adható adott Azure virtuális hálózatok forgalmát, vagy nyilvános internetes IP-címtartományok.|
|Egyéni RBAC-szabályok használatának naplózása|Naplózza a beépített szerepkörök, például a "Tulajdonos, közreműködő, olvasó" helyett egyéni RBAC-szerepkörök, amelyek hibalehetőségeket rejt magában. Egyéni szerepkörök használatával kivételként kezeli, és szigorú áttekintése és a fenyegetések modellezése szükséges.|
|Az Azure Stream Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Tárfiókokba történő biztonságos átvitel naplózása|Naplózási követelmény, biztonságos átvitel a storage-fiókban. Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. HTTPS használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – például a hálózati réteg támadások ellen védi a az átvitt adatokat.|
|Azure Active Directory-rendszergazda-fiók kiépítésének naplózása az SQL-kiszolgálón|Naplózási üzembe helyezése egy Azure Active Directory-rendszergazda az SQL Server az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés lehetővé teszi, hogy egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel.|
|Service Bus-névterek engedélyezési szabályainak naplózása|Service Bus-ügyfél ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|A Service Bus-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A ClusterProtectionLevel tulajdonság EncryptAndSign értékre való beállításának naplózása a Service Fabricben|Service Fabric egy elsődleges fürttanúsítvány csomópontok közötti kommunikációt három szintje (nincs, bejelentkezési és EncryptAndSign) védelmet biztosít. Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása.| 
|Az Azure Active Directory ügyfél-hitelesítésre való használatának naplózása a Service Fabricben|A kizárólag az Azure Active Directoryval való ügyfél-hitelesítés használatának naplózása a Service Fabricben| 
|A Search szolgáltatásbeli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.| 
|Naplózás engedélyezése a redis az Azure Cache csak biztonságos kapcsolatok|Naplózás engedélyezése az Azure gyorsítótárhoz SSL-en keresztül redis csak kapcsolatot. Biztonságos kapcsolatok használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és az átvitt adatokat védi a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés| 
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
|[Előzetes verzió]: Az Azure Security Centerben a titkosítatlan SQL database monitorozása|Titkosítatlan SQL-kiszolgálókat és adatbázisokat figyeli a rendszer az Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Az Azure Security Center nem naplózott SQL database monitorozása|SQL Serverek és adatbázisok nem rendelkezik SQL-naplózás engedélyezve van, amely felügyeli az Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése|Hiányzó biztonsági a kiszolgálókon által figyelt Azure Security Center ajánlásként.| 
|[Előzetes verzió]: Tárfiókok hiányzó blobtitkosításának naplózása|Ez a szabályzat a blobtitkosítás nélküli tárfiókokat naplózza. Kizárólag a Microsoft.Storage erőforrástípusokra, más tárolási szolgáltatók nem vonatkozik. Lehetséges hálózati igény szerinti hozzáférést ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése|Lehetséges hálózati csak az idő szerinti (JIT) hozzáférési ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Alkalmazások az Azure Security Center engedélyezésének figyelése|Lehetséges Alkalmazásengedélyezési konfigurációkat az Azure Security Center figyeli.| 
|[Előzetes verzió]: Az Azure Security Center megengedő hálózati hozzáférés figyelése|A túlzottan megengedő hálózati biztonsági csoportokat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése|Kiszolgálók, amelyek nem a konfigurált alapértékeknek ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása|Az Endpoint Protection telepített ügynök nélküli kiszolgálókat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása|Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: Virtuális gépek biztonsági réseinek figyelése az Azure Security Centerben|A Vulnerability Assessment megoldás által észlelt biztonsági rések és a Vulnerability Assessment megoldást nem használó virtuális gépek ajánlásként történő figyelése az Azure Security Centerben.| 
|[Előzetes verzió]: Az Azure Security Center nem védett webalkalmazás figyelése|Egy webalkalmazási tűzfallal nem védett webalkalmazásokat ajánlásként az Azure Security Center által figyelendő ki.| 
|[Előzetes verzió]: Az Azure Security Center nem védett hálózati végpontok figyelése|Egy az Újgenerációs tűzfallal nem védett hálózati végpontokat ajánlásként az Azure Security Center által figyelt.| 
|[Előzetes verzió]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben|A sebezhetőségi felmérés vizsgálati eredményeinek és az adatbázisok biztonsági réseinek megszüntetéséhez útmutatást nyújtó ajánlások figyelése.| 
|[Előzetes verzió]: Naplózni tulajdonosok egy előfizetéshez tartozó maximális száma|Ajánlott legfeljebb 3 előfizetés-tulajdonost kijelölni a biztonsági kockázatot jelentő tulajdonosok általi esetleges incidensek csökkentése céljából.| 
|[Előzetes verzió]: Naplózási előfizetés tulajdonosok minimális száma|Ajánlott legalább egy előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundanciájának biztosításához.| 
|[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok|A Multi-Factor Authenticationt (MFA) a tulajdonosi engedéllyel rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok|A Multi-Factor Authenticationt (MFA) az írási jogosultsággal rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés olvasási jogosultsággal rendelkező fiókok|A Multi-Factor Authenticationt (MFA) az olvasási jogosultsággal rendelkező összes előfizetési fiókban engedélyezni kell a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése céljából.| 
|[Előzetes verzió]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása|Elavult fiókok tulajdonosi engedélyekkel rendelkező el kell távolítani az előfizetésből. Elavult fiókok olyan fiókok, a bejelentkezés le van tiltva.| 
|[Előzetes verzió]: Elavult fiókokat egy előfizetésben naplózása|Az elavult fiókokat el kell távolítani az előfizetésekből. Elavult fiókok olyan fiókok, a bejelentkezés le van tiltva.| 
|[Előzetes verzió]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása|A tulajdonosi engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférések megelőzése céljából.| 
|[Előzetes verzió]: Naplózási rendelkező külső fiókok írási engedélyekkel egy adott előfizetés|Az írási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.| 
|[Előzetes verzió]: Egy adott előfizetés olvasási jogosultsággal rendelkező külső fiókok naplózása|Az olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.| 




## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Ismerje meg, hogyan tervezhet és az Azure Security Centerrel kapcsolatos tervezési szempontokat.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
