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
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877496"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>A Security Center által figyelt Azure biztonsági szabályzatok
Ez a cikk az Azure Security Center segítségével figyelheti az Azure szabályzatdefiníciók listáját tartalmazza. Biztonsági szabályzatok kapcsolatos további információkért lásd: [biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Biztonsági szabályzatok

A beépített szabályzatokat, a Security Center által figyelt kapcsolatos további információkért lásd az alábbi táblázatot:

| Szabályzat | A szabályzat működése |
| --- | --- |
|Naplózás engedélyezése a diagnosztikai naplók az Azure Service Fabric és a virtual machine scale sets|Azt javasoljuk, hogy egy tevékenység beállításainak érhető el egy incidens- vagy biztonsági sérülés utáni vizsgálati naplók engedélyezése.|
|Az Event Hubs-névterek naplózási engedélyezési szabályok|Az Azure Event Hubs-ügyfelek ne használja egy névtérszintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságon alapuló biztonsági modellel igazításához kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Az Event Hubs entitásokon engedélyezési szabályok meglétének naplózása|Az Event Hubs entitásokon jogosultságon alapuló hozzáférést engedélyezési szabályok meglétének naplózása.|
|Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálhatja a hálózati szabályait, hogy csak az engedélyezett hálózatok alkalmazások férhetnek hozzá a tárfiók. Ahhoz, hogy a kapcsolatok meghatározott internetről, vagy olyan helyszíni ügyfelek, hozzáférést biztosít a forgalmat az adott Azure virtuális hálózatok vagy a nyilvános interneten IP-cím-tartományait.|
|Egyéni RBAC-szabályok használatának naplózása|Naplózza a beépített szerepkörök, például a "Tulajdonos, közreműködő, olvasó" helyett egyéni, szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör, amely a rendszer hibalehetőségeket rejt magában. Egyéni szerepkörök használata kivételként kezeli, és megköveteli a szigorú áttekintése és a fenyegetések modellezése.|
|Az Azure Stream Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Tárfiókokba történő biztonságos átvitel naplózása|Naplózási tárfiókban található biztonságos átvitel követelményeinek. Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. A HTTPS használatával biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez. A hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – az átvitt adatokat is védi.|
|Az Azure Active Directory-rendszergazda, az SQL Server üzembe helyezése naplózása|Naplózási kiépítése az Azure Active Directory (Azure AD) az Azure AD-hitelesítés engedélyezése az SQL Server rendszergazdájának. Az Azure AD-hitelesítés egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel támogatja.|
|Service Bus-névterek engedélyezési szabályainak naplózása|Az Azure Service Bus-ügyfél ne használja egy névtérszintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. Igazítás a minimális jogosultságon alapuló biztonsági modellel, hozzon létre hozzáférési szabályzatok az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|A Service Bus-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és az őket az egy év. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A ClusterProtectionLevel tulajdonság EncryptAndSign értékre való beállításának naplózása a Service Fabricben|A Service Fabric biztosít az elsődleges fürt tanúsítványt használó csomópontok közötti kommunikáció védelmét három szintje: Nincs, bejelentkezési és EncryptAndSign. Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása.|
|Az Azure Active Directory ügyfél-hitelesítésre való használatának naplózása a Service Fabricben|Ügyfél-hitelesítés csak az Azure AD-t a Service Fabric használatával naplózza.|
|A Search szolgáltatásbeli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A redis Azure Cache csak biztonságos kapcsolatok engedélyezése naplózása|Naplózás engedélyezése csak a redis Azure Cache SSL-en keresztül kapcsolatok. Biztonságos kapcsolat használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez. A hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – az átvitt adatokat is védi.|
|A Logic Apps-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A Key Vault-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Audit Event hubs-diagnosztikai naplók engedélyezése|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Az Azure Data Lake Store-beli diagnosztikai naplók engedélyezésének naplózása|Naplók engedélyezése naplózása, és láthatóan tartja őket legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A Data Lake Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Klasszikus tárfiókok használatának naplózása|A tárfiókok az Azure Resource Manager használatával biztonsági fejlesztések. Ezek a következők: <br>– Erősebb hozzáférés-vezérlés (RBAC)<br>-Jobban naplózása<br>– Az azure Resource Manager-alapú üzembe helyezési és irányítás<br>-Felügyelt identitások hozzáférést<br>-A hozzáférést az Azure Key Vault titkos kulcsok<br>– Az azure AD-alapú hitelesítés<br>-Támogatása a címkék és -erőforráscsoportok egyszerűbb biztonság kezelése|
|Klasszikus virtuális gépek használatának naplózása|A virtuális gépek Azure Resource Manager használatával biztonsági fejlesztések.  Ezek a következők: <br>– Erősebb hozzáférés-vezérlés (RBAC)<br>-Jobban naplózása<br>– Az azure Resource Manager-alapú üzembe helyezési és irányítás<br>-Felügyelt identitások hozzáférést<br>-A hozzáférést az Azure Key Vault titkos kulcsok<br>– Az azure AD-alapú hitelesítés<br>-Támogatása a címkék és -erőforráscsoportok egyszerűbb biztonság kezelése|
|Batch-fiókok metrikaalapú riasztási szabályaira vonatkozó konfiguráció naplózása |Az Azure Batch-fiókok ahhoz, hogy a szükséges metrikát a metrikaalapú riasztási szabályok naplózási konfigurációja.|
|Az Azure Stream Analytics-beli diagnosztikai naplók engedélyezésének naplózása|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Automation-fiók változók titkosítási engedélyezése naplózása|Fontos, ha bizalmas adatok titkosítása az Azure Automation-fiók változó adategységek tételéhez.|
|Az App Service-diagnosztikai naplók engedélyezése naplózása|Naplózza az alkalmazást a diagnosztikai naplók engedélyezése. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Transzparens adattitkosítás állapotának naplózása|SQL-adatbázisok transzparens adattitkosítási állapotának naplózása.|
|Naplózási beállítások SQL kiszolgálószintű naplózás|Az SQL-naplózás a kiszolgáló szintjén meglétének naplózása.|
|\[Előzetes verzió]: Az Azure Security Centerben a titkosítatlan SQL database monitorozása|Az Azure Security Center figyeli a titkosítatlan SQL-kiszolgálók és adatbázisok javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Center nem naplózott SQL database monitorozása|Az Azure Security Center figyeli az SQL Server-kiszolgálók és adatbázisok, amelyek nem rendelkeznek az SQL-naplózás engedélyezve van, ajánlott.|
|\[Előzetes verzió]: Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése|Az Azure Security Center figyeli a kiszolgálók hiányzó biztonsági Rendszerfrissítések, javasolt módon.|
|\[Előzetes verzió]: Tárfiókok hiányzó blobtitkosításának naplózása|Storage-fiókok, amelyek nem használják a blobtitkosítás naplózása. Ez csak a Microsoft storage erőforrástípus esetében nem más szolgáltatóktól származó tárolási vonatkozik. Az Azure Security Center figyeli a lehetséges hálózati just-in-time hozzáférés javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Centerben a lehetséges hálózati just-in-time hozzáférés figyelése|Az Azure Security Center figyeli a lehetséges hálózati just-in-time hozzáférés javasolt módon.|
|\[Előzetes verzió]: Alkalmazások az Azure Security Center engedélyezésének figyelése|Az Azure Security Center figyeli a lehetséges Alkalmazáskonfiguráció engedélyezési listáján.|
|\[Előzetes verzió]: Az Azure Security Center megengedő hálózati hozzáférés figyelése|Az Azure Security Center figyeli a hálózati biztonsági csoportokat, amelyek túl megengedő szabály, javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése|Az Azure Security Center figyeli a kiszolgálókat, amelyek nem a konfigurált alapértékeknek javasolt módon.| 
|\[Előzetes verzió]: Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása|Az Azure Security Center figyeli a kiszolgálók, amelyek nem rendelkeznek telepített Microsoft System Center Endpoint Protection-ügynök, javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása|Az Azure Security Center figyeli a virtuális gépek, amelyekhez nincs lemeztitkosítás engedélyezve van a javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Centerben a virtuális gépek biztonsági réseinek figyelése|A biztonságirés-felmérési megoldást, és nem rendelkező virtuális gépeken sebezhetőség-felmérési megoldás az Azure Security Center által közölt által észlelt biztonsági rések figyelésére.|
|\[Előzetes verzió]: Az Azure Security Center nem védett webalkalmazás figyelése|Az Azure Security Center figyeli webes alkalmazások, amelyek nem webes alkalmazás tűzfal védelmének javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Center nem védett hálózati végpontok figyelése|Az Azure Security Center figyeli a hálózati végpontokat, amelyek nem rendelkeznek a következő generációs tűzfal védelmének, javasolt módon.|
|\[Előzetes verzió]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben|A figyelő a biztonságirés-értékelési a keresési eredményeket, és javasolja az adatbázis biztonsági rések javítása.|
|\[Előzetes verzió]: Naplózni tulajdonosok egy előfizetéshez tartozó maximális száma|Azt javasoljuk, hogy jelöljön ki legfeljebb három előfizetések tulajdonosai az esetleges incidensek feltört tulajdonosa.|
|\[Előzetes verzió]: Naplózási előfizetés tulajdonosok minimális száma|Azt javasoljuk, hogy Ön által kijelölt egynél több tulajdonossal, és győződjön meg, hogy a rendszergazdai hozzáférés redundanciájának.|
|\[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok|Többtényezős hitelesítés (MFA) engedélyezni kell az összes előfizetési fiókban, amely a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése tulajdonosi engedélyekkel rendelkeznek.|
|\[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok|A multi-factor authentication engedélyezni kell az összes előfizetési fiókban, amely rendelkezik írási engedéllyel a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése érdekében.|
|\[Előzetes verzió]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés olvasási jogosultsággal rendelkező fiókok|A multi-factor authentication engedélyezni kell az összes előfizetési fiókban, amely rendelkezik olvasási engedéllyel a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése érdekében.|
|\[Előzetes verzió]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása|Elavult fiókok tulajdonosi engedélyekkel rendelkező el kell távolítani az előfizetésből. Elavult fiókokat a rendszer letiltotta a bejelentkezés.|
|\[Előzetes verzió]: Elavult fiókokat egy előfizetésben naplózása|Az elavult fiókokat el kell távolítani az előfizetésekből. Elavult fiókokat a rendszer letiltotta a bejelentkezés.|
|\[Előzetes verzió]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása|Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani, hogy megkezdhesse az engedélyek hozzáférések megelőzése céljából.|
|\[Előzetes verzió]: Naplózási rendelkező külső fiókok írási engedélyekkel egy adott előfizetés|Rendelkező külső fiókok írási engedélyek el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.|
|\[Előzetes verzió]: Egy adott előfizetés olvasási jogosultsággal rendelkező külső fiókok naplózása|Amely rendelkezik olvasási engedéllyel a külső fiókokat el kell távolítani az előfizetést nem monitorozott hozzáférések megelőzése céljából.|




## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket.

* [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Ismerje meg, hogyan tervezhet és az Azure Security Center tervezési szempontokat.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

Az Azure Policyvel kapcsolatos további információkért lásd: [Mi az Azure Policy?](../governance/policy/overview.md).
