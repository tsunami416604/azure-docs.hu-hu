---
title: Azure szabályzatdefiníciók figyeli az Azure Security Centerben |} A Microsoft Docs
description: Azure szabályzatdefiníciók az Azure Security Center figyeli.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428330"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>A Security Center által figyelt Azure biztonsági szabályzatok
Ez a cikk az Azure Security Center segítségével figyelheti az Azure szabályzatdefiníciók listáját tartalmazza. Biztonsági szabályzatok kapcsolatos további információkért lásd: [biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Biztonsági szabályzatok

A beépített szabályzatokat, a Security Center által figyelt kapcsolatos további információkért lásd az alábbi táblázatot:

| Szabályzat | A szabályzat működése |
| --- | --- |
|Engedélyezni kell a Virtual Machine Scale Sets-diagnosztikai naplók|Azt javasoljuk, hogy egy tevékenység beállításainak érhető el egy incidens- vagy biztonsági sérülés utáni vizsgálati naplók engedélyezése.|
|RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Eseményközpont-névtér|Az Azure Event Hubs-ügyfelek ne használja egy névtérszintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságon alapuló biztonsági modellel igazításához kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Az Event Hubs entitás engedélyezési szabályok meg kell határozni.|Az Event Hubs entitásokon jogosultságon alapuló hozzáférést engedélyezési szabályok meglétének naplózása.|
|Tűzfal és a virtuális hálózati konfiguráció a storage-fiókok hozzáférést korlátozva kell lennie.|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálhatja a hálózati szabályait, hogy csak az engedélyezett hálózatok alkalmazások férhetnek hozzá a tárfiók. Ahhoz, hogy a kapcsolatok meghatározott internetről, vagy olyan helyszíni ügyfelek, hozzáférést biztosít a forgalmat az adott Azure virtuális hálózatok vagy a nyilvános interneten IP-cím-tartományait.|
|Az egyéni RBAC-szabályok használatának naplózása|Naplózza a beépített szerepkörök, például a "Tulajdonos, közreműködő, olvasó" helyett egyéni, szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör, amely a rendszer hibalehetőségeket rejt magában. Egyéni szerepkörök használata kivételként kezeli, és megköveteli a szigorú áttekintése és a fenyegetések modellezése.|
|Engedélyezni kell a diagnosztikai naplók az Azure Stream Analytics szolgáltatásban|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Engedélyezni kell a biztonságos átvitelt a storage-fiókok|Naplózási tárfiókban található biztonságos átvitel követelményeinek. Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. A HTTPS használatával biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez. A hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – az átvitt adatokat is védi.|
|Üzembe helyezi az SQL server az Azure AD-rendszergazda|Naplózási kiépítése az Azure Active Directory (Azure AD) az Azure AD-hitelesítés engedélyezése az SQL Server rendszergazdájának. Az Azure AD-hitelesítés egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel támogatja.|
|RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani a Service Bus-névtér|Az Azure Service Bus-ügyfél ne használja egy névtérszintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. Igazítás a minimális jogosultságon alapuló biztonsági modellel, hozzon létre hozzáférési szabályzatok az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Engedélyezni kell a Service Bus-diagnosztikai naplók|Audit naplók engedélyezése és az őket az egy év. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A Service Fabric EncryptAndSign ClusterProtectionLevel tulajdonság kell állítani.|A Service Fabric biztosít az elsődleges fürt tanúsítványt használó csomópontok közötti kommunikáció védelmét három szintje: Nincs, bejelentkezési és EncryptAndSign. Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása.|
|Ügyfél-hitelesítést használjon az Azure Active Directory|Ügyfél-hitelesítés csak az Azure AD-t a Service Fabric használatával naplózza.|
|Diagnosztikai naplók keresési szolgáltatások engedélyezni kell az|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|A Redis gyorsítótárhoz kizárólag biztonságos kapcsolatot engedélyezni kell|Naplózás engedélyezése csak a redis Azure Cache SSL-en keresztül kapcsolatok. Biztonságos kapcsolat használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez. A hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – az átvitt adatokat is védi.|
|Engedélyezni kell a Logic Apps-diagnosztikai naplók|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Engedélyezni kell a Key Vault-diagnosztikai naplók|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Engedélyezni kell a diagnosztikai naplók az Event Hubs|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Az Azure Data Lake Store diagnosztikai naplóinak engedélyezni kell|Naplók engedélyezése naplózása, és láthatóan tartja őket legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Engedélyezni kell a Data Lake Analytics-diagnosztikai naplók|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Storage-fiókok kell áttelepíteni az új AzureRM-erőforrásokhoz|A tárfiókok az Azure Resource Manager használatával biztonsági fejlesztések. Ezek a következők: <br>– Erősebb hozzáférés-vezérlés (RBAC)<br>-Jobban naplózása<br>– Az azure Resource Manager-alapú üzembe helyezési és irányítás<br>-Felügyelt identitások hozzáférést<br>-A hozzáférést az Azure Key Vault titkos kulcsok<br>– Az azure AD-alapú hitelesítés<br>-Támogatása a címkék és -erőforráscsoportok egyszerűbb biztonság kezelése|
|Virtuális gépek kell áttelepíteni az új AzureRM-erőforrásokhoz|A virtuális gépek Azure Resource Manager használatával biztonsági fejlesztések.  Ezek a következők: <br>– Erősebb hozzáférés-vezérlés (RBAC)<br>-Jobban naplózása<br>– Az azure Resource Manager-alapú üzembe helyezési és irányítás<br>-Felügyelt identitások hozzáférést<br>-A hozzáférést az Azure Key Vault titkos kulcsok<br>– Az azure AD-alapú hitelesítés<br>-Támogatása a címkék és -erőforráscsoportok egyszerűbb biztonság kezelése|
|Metrikaalapú riasztási szabályok kell konfigurálni a Batch-fiókok|Az Azure Batch-fiókok ahhoz, hogy a szükséges metrikát a metrikaalapú riasztási szabályok naplózási konfigurációja.|
|Engedélyezni kell a Batch-fiókok a diagnosztikai naplók|Audit naplók engedélyezése és láthatóan tartja őket a legfeljebb egy évig. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Az Automation-fiók változók titkosítási engedélyezni kell|Fontos, ha bizalmas adatok titkosítása az Azure Automation-fiók változó adategységek tételéhez.|
|Engedélyezni kell a diagnosztikai naplók az App Servicesben|Naplózza az alkalmazást a diagnosztikai naplók engedélyezése. Ez létrehoz tevékenység nyomot hagyjanak maguk után a vizsgálat egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Engedélyezni kell az SQL Database-adatbázisok transzparens adattitkosítás|SQL-adatbázisok transzparens adattitkosítási állapotának naplózása.|
|SQL server-naplózás engedélyezni kell|Az SQL-naplózás a kiszolgáló szintjén meglétének naplózása.|
|\[Előzetes verzió]: Az Azure Security Centerben a titkosítatlan SQL database monitorozása|Az Azure Security Center figyeli a titkosítatlan SQL-kiszolgálók és adatbázisok javasolt módon.|
|\[Előzetes verzió]: Az Azure Security Center nem naplózott SQL database monitorozása|Az Azure Security Center figyeli az SQL Server-kiszolgálók és adatbázisok, amelyek nem rendelkeznek az SQL-naplózás engedélyezve van, ajánlott.|
|\[Előzetes verzió]: Rendszerfrissítések, telepítenie kell a gépek|Az Azure Security Center figyeli a kiszolgálók hiányzó biztonsági Rendszerfrissítések, javasolt módon.|
|\[Előzetes verzió]: Tárfiókok hiányzó blobtitkosításának naplózása|Storage-fiókok, amelyek nem használják a blobtitkosítás naplózása. Ez csak a Microsoft storage erőforrástípus esetében nem más szolgáltatóktól származó tárolási vonatkozik. Az Azure Security Center figyeli a lehetséges hálózati just-in-time hozzáférés javasolt módon.|
|\[Előzetes verzió]: Just-In-Time-hálózati hozzáférés-vezérlést alkalmazni kell a virtuális gépek|Az Azure Security Center figyeli a lehetséges hálózati just-in-time hozzáférés javasolt módon.|
|\[Előzetes verzió]: Az adaptív Alkalmazásvezérlők a virtuális gépek engedélyezve kell lennie|Az Azure Security Center figyeli a lehetséges Alkalmazáskonfiguráció engedélyezési listáján.|
|\[Előzetes verzió]: Hiányzó hálózati biztonsági csoportok a virtuális gépek kell konfigurálni|Az Azure Security Center figyeli a hálózati biztonsági csoportokat, amelyek túl megengedő szabály, javasolt módon.|
|\[Előzetes verzió]: Biztonsági beállítások a gépek biztonsági réseinek kell kijavítható.|Az Azure Security Center figyeli a kiszolgálókat, amelyek nem a konfigurált alapértékeknek javasolt módon.| 
|\[Előzetes verzió]: Az Endpoint protection, telepítenie kell a virtuális gépek|Az Azure Security Center figyeli a kiszolgálók, amelyek nem rendelkeznek telepített Microsoft System Center Endpoint Protection-ügynök, javasolt módon.|
|\[Előzetes verzió]: Lemeztitkosítás alkalmazni kell a virtuális gépek|Az Azure Security Center figyeli a virtuális gépek, amelyekhez nincs lemeztitkosítás engedélyezve van a javasolt módon.|
|\[Előzetes verzió]: Biztonsági rések kell kijavítható biztonságirés-értékelési megoldás által|A biztonságirés-felmérési megoldást, és nem rendelkező virtuális gépeken sebezhetőség-felmérési megoldás az Azure Security Center által közölt által észlelt biztonsági rések figyelésére.|
|\[Előzetes verzió]: Az Azure Security Center nem védett webalkalmazás figyelése|Az Azure Security Center figyeli webes alkalmazások, amelyek nem webes alkalmazás tűzfal védelmének javasolt módon.|
|\[Előzetes verzió]: Végpontvédelmi megoldás, telepítenie kell a virtuális gépek|Az Azure Security Center figyeli a hálózati végpontokat, amelyek nem rendelkeznek a következő generációs tűzfal védelmének, javasolt módon.|
|\[Előzetes verzió]: Az SQL-adatbázisok biztonsági rések kell kijavítható.|A figyelő a biztonságirés-értékelési a keresési eredményeket, és javasolja az adatbázis biztonsági rések javítása.|
|\[Előzetes verzió]: Legfeljebb 3 tulajdonosok az előfizetéshez kell kijelölni|Azt javasoljuk, hogy jelöljön ki legfeljebb három előfizetések tulajdonosai az esetleges incidensek feltört tulajdonosa.|
|\[Előzetes verzió]: Meg kell adni az előfizetéshez hozzárendelt egynél több tulajdonosa|Azt javasoljuk, hogy Ön által kijelölt egynél több tulajdonossal, és győződjön meg, hogy a rendszergazdai hozzáférés redundanciájának.|
|\[Előzetes verzió]: MFA engedélyezni kell a fiókok az előfizetésben tulajdonosi engedélyekkel rendelkező |Többtényezős hitelesítés (MFA) engedélyezni kell az összes előfizetési fiókban, amely a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése tulajdonosi engedélyekkel rendelkeznek.|
|\[Előzetes verzió]: MFA engedélyezni kell a írási engedéllyel rendelkező előfizetés fiókjának|A multi-factor authentication engedélyezni kell az összes előfizetési fiókban, amely rendelkezik írási engedéllyel a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése érdekében.|
|\[Előzetes verzió]: MFA a az előfizetés olvasási engedélyekkel rendelkező fiókok engedélyezve kell lennie|A multi-factor authentication engedélyezni kell az összes előfizetési fiókban, amely rendelkezik olvasási engedéllyel a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése érdekében.|
|\[Előzetes verzió]: Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|Elavult fiókok tulajdonosi engedélyekkel rendelkező el kell távolítani az előfizetésből. Elavult fiókokat a rendszer letiltotta a bejelentkezés.|
|\[Előzetes verzió]: Elavult fiókokat el kell távolítani az előfizetésből|Elavult fiókokat el kell távolítani az előfizetésekből. Elavult fiókokat a rendszer letiltotta a bejelentkezés.|
|\[Előzetes verzió]: Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani, hogy megkezdhesse az engedélyek hozzáférések megelőzése céljából.|
|\[Előzetes verzió]: Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Rendelkező külső fiókok írási engedélyek el kell távolítani az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.|
|\[Előzetes verzió]: Olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből|Amely rendelkezik olvasási engedéllyel a külső fiókokat el kell távolítani az előfizetést nem monitorozott hozzáférések megelőzése céljából.|




## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket.

* [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Ismerje meg, hogyan tervezhet és az Azure Security Center tervezési szempontokat.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

Az Azure Policyvel kapcsolatos további információkért lásd: [Mi az Azure Policy?](../governance/policy/overview.md).
