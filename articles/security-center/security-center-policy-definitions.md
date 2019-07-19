---
title: Az Azure Policy-definíciók figyelése Azure Security Centerban | Microsoft Docs
description: A Azure Security Centerban figyelt Azure-házirend-definíciók.
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
ms.author: v-mohabe
ms.openlocfilehash: db7811a925846337487801a63e0f0c4584179c06
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295521"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Security Center által figyelt Azure biztonsági szabályzatok
Ez a cikk azokat a Azure Policy-definíciókat sorolja fel, amelyeket a Azure Security Center figyelheti. További információ a biztonsági házirendekről: [a biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Elérhető biztonsági szabályzatok

Ha többet szeretne megtudni a Security Center által figyelt beépített szabályzatokról, tekintse meg a következő táblázatot:

| Szabályzat | A szabályzat működése |
| --- | --- |
|A Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell|Azt javasoljuk, hogy engedélyezze a naplókat, hogy egy adott incidens vagy kompromisszum után egy tevékenység nyomvonala elérhető legyen a vizsgálathoz.|
|A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből|Az Azure Event Hubs-ügyfelek nem használhatnak olyan névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legkevesebb jogosultsággal rendelkező biztonsági modellhez való igazításhoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Az Event hub-entitás engedélyezési szabályait definiálni kell|Event Hubs entitásokra vonatkozó engedélyezési szabályok meglétének naplózása a legalacsonyabb jogosultsági szintű hozzáférés biztosításához.|
|A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell|Naplózza a nem korlátozott hálózati hozzáférést a Storage-fiók tűzfala beállításaiban. Konfigurálja a hálózati szabályokat úgy, hogy csak az engedélyezett hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. Az adott internetről vagy helyszíni ügyfelekről érkező kapcsolatok engedélyezéséhez adjon hozzáférést bizonyos Azure-beli virtuális hálózatokból vagy nyilvános internetes IP-címtartományok érkező forgalomhoz.|
|Egyéni RBAC-szabályok használatának naplózása|A beépített szerepkörök, például a "tulajdonos, közreműködő, olvasó" naplózása az egyéni szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök helyett, amely a hibákra hajlamos. Az egyéni szerepkörök használatát kivételként kezeli a rendszer, és szigorú felülvizsgálati és veszélyforrás-modellezést igényel.|
|A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell|A biztonságos átvitel naplózási követelményei a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között. Emellett védelmet biztosít a hálózati rétegbeli támadásoktól, például a személyes, a lehallgatási és a munkamenet-eltérített adatoktól.|
|Az SQL Server Azure AD-rendszergazdájának kiépítve kell lennie|Az Azure AD-hitelesítés engedélyezéséhez Azure Active Directory (Azure AD) rendszergazdájának kiépítés naplózása SQL Server számára. Az Azure AD-hitelesítés támogatja az adatbázis-felhasználók és más Microsoft-szolgáltatások egyszerűsített engedélyek kezelését és központi identitás-kezelését.|
|A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani Service Bus névtérből|Azure Service Bus ügyfelek nem használhatnak olyan névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtér összes várólistájához és témaköréhez. A legkevesebb jogosultsággal rendelkező biztonsági modellel való összehangoláshoz hozzon létre hozzáférési házirendeket az entitások szintjén a várólisták és témakörök számára, hogy csak az adott entitáshoz férhessenek hozzá.|
|A Service Bus diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és megtarthatja őket egy évig. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|Meg kell adni a ClusterProtectionLevel tulajdonságot, amelyet a Service Fabric EncryptAndSign kell beállítani|A Service Fabric három védelmi szintet biztosít a csomópontok közötti kommunikációhoz, amely elsődleges fürtöt használó tanúsítványt használ: Nincs, aláírás és EncryptAndSign. Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása.|
|Az ügyfél-hitelesítésnek Azure Active Directory kell használnia|Az ügyfél-hitelesítés használatának naplózása csak az Azure AD-n keresztül Service Fabric.|
|A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve|Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használatával biztosítható a kiszolgáló és a szolgáltatás közötti hitelesítés. Emellett védelmet biztosít a hálózati rétegbeli támadásoktól, például a személyes, a lehallgatási és a munkamenet-eltérített adatoktól.|
|A Logic Apps diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|A Key Vault diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|Az Event hub diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|A Azure Data Lake Store diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is tarthat. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|A Data Lake Analytics diagnosztikai naplóit engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|A Storage-fiókokat át kell telepíteni az új AzureRM-erőforrásokra|A biztonsági fejlesztéseket a Storage-fiókok Azure Resource Manager használatával biztosíthatja. Ezek a következők: <br>-Erősebb hozzáférés-vezérlés (RBAC)<br>– Jobb naplózás<br>-Azure Resource Manager-alapú üzembe helyezés és irányítás<br>-Hozzáférés a felügyelt identitásokhoz<br>– Hozzáférés Azure Key Vault a titkokhoz<br>– Azure AD-alapú hitelesítés<br>– Címkék és erőforráscsoportok támogatása a biztonsági felügyelet megkönnyítéséhez|
|A virtuális gépeket át kell telepíteni az új AzureRM-erőforrásokra|A virtuális gépek Azure Resource Manager használatával biztosíthatja a biztonsági fejlesztéseket.  Ezek a következők: <br>-Erősebb hozzáférés-vezérlés (RBAC)<br>– Jobb naplózás<br>-Azure Resource Manager-alapú üzembe helyezés és irányítás<br>-Hozzáférés a felügyelt identitásokhoz<br>– Hozzáférés Azure Key Vault a titkokhoz<br>– Azure AD-alapú hitelesítés<br>– Címkék és erőforráscsoportok támogatása a biztonsági felügyelet megkönnyítéséhez|
|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni|A metrikus riasztási szabályok konfigurációjának naplózása Azure Batch-fiókokon a szükséges metrika engedélyezéséhez.|
|A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell|Naplózhatja a naplók engedélyezését, és akár egy évig is megtarthatja őket. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|Az Automation-fiók változói esetében engedélyezni kell a titkosítást.|Fontos, hogy a bizalmas adatok tárolása során engedélyezze Azure Automation Account változó eszközök titkosítását.|
|A App Services diagnosztikai naplóit engedélyezni kell|Diagnosztikai naplók engedélyezésének naplózása az alkalmazásban. Ez olyan tevékenységi nyomvonalat hoz létre, amely a biztonsági incidensek bekövetkezésekor, vagy a hálózat biztonsága során jelentkezik.|
|Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás|Az SQL-adatbázisok transzparens adattitkosítási állapotának naplózása.|
|Az SQL Server naplózását engedélyezni kell|Ellenőrizze az SQL-naplózás meglétét a kiszolgálói szinten.|
|\[Előzetes verzió]: Titkosítatlan SQL-adatbázis figyelése Azure Security Center|Azure Security Center a nem titkosított SQL-kiszolgálókat vagy-adatbázisokat a javasolt módon figyeli.|
|\[Előzetes verzió]: Nem naplózott SQL-adatbázis figyelése Azure Security Center|Azure Security Center az SQL-naplózással nem rendelkező SQL-kiszolgálókat és-adatbázisokat figyeli a javasolt módon.|
|\[Előzetes verzió]: A számítógépekre telepíteni kell a rendszerfrissítéseket|Azure Security Center az ajánlott módon figyeli a kiszolgálókon hiányzó biztonsági rendszer-frissítéseket.|
|\[Előzetes verzió]: Hiányzó blob-titkosítás naplózása a Storage-fiókoknál|A blob titkosítást nem használó tárolási fiókok naplózása. Ez csak a Microsoft Storage-erőforrástípusok esetében érvényes, más szolgáltatóktól nem. Azure Security Center figyeli a lehetséges hálózati igény szerinti hozzáférést a javasolt módon.|
|\[Előzetes verzió]: Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken|Azure Security Center figyeli a lehetséges hálózati igény szerinti hozzáférést a javasolt módon.|
|\[Előzetes verzió]: Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken|Azure Security Center figyeli az alkalmazás engedélyezési listájának lehetséges konfigurációját.|
|\[Előzetes verzió]: A virtuális gépekhez hiányzó hálózati biztonsági csoportokat kell konfigurálni.|A Azure Security Center a túl megengedő szabályokkal rendelkező hálózati biztonsági csoportokat figyeli a javasolt módon.|
|\[Előzetes verzió]: A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell|Azure Security Center figyeli azokat a kiszolgálókat, amelyek nem felelnek meg a konfigurált alapkonfigurációnak.| 
|\[Előzetes verzió]: Az Endpoint Protection szolgáltatást virtuális gépekre kell telepíteni|Azure Security Center figyeli azokat a kiszolgálókat, amelyek nem rendelkeznek telepített Microsoft System Center Endpoint Protection-ügynökkel.|
|\[Előzetes verzió]: A lemezes titkosítást a virtuális gépeken kell alkalmazni|Azure Security Center figyeli azokat a virtuális gépeket, amelyeken nincs engedélyezve a lemez titkosítása.|
|\[Előzetes verzió]: A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Figyelje a sebezhetőségi felmérési megoldás által észlelt biztonsági réseket, valamint azokat a virtuális gépeket, amelyek nem rendelkeznek sebezhetőségi felmérési megoldással a Azure Security Center ajánlott módon.|
|\[Előzetes verzió]: Nem védett webalkalmazás figyelése Azure Security Center|Azure Security Center figyeli a webalkalmazás-tűzfalat a javasolt módon nem rendelkező webalkalmazásokat.|
|\[Előzetes verzió]: Az Endpoint Protection-megoldást virtuális gépekre kell telepíteni|Azure Security Center figyeli azokat a hálózati végpontokat, amelyek nem rendelkeznek a következő generációs tűzfal-védelemmel, ajánlott módon.|
|\[Előzetes verzió]: Az SQL-adatbázisok biztonsági réseit szervizelni kell|A sebezhetőségi felmérés vizsgálatának eredményeinek figyelése és az adatbázis-biztonsági rések elhárítása.|
|\[Előzetes verzió]: Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni|Javasoljuk, hogy legfeljebb három előfizetés-tulajdonost jelöljön ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét.|
|\[Előzetes verzió]: Az előfizetéshez egynél több tulajdonos rendelhető hozzá|Javasoljuk, hogy egynél több előfizetés-tulajdonost jelöljön ki a rendszergazdai hozzáférés redundancia biztosításához.|
|\[Előzetes verzió]: Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon |A többtényezős hitelesítést (MFA) engedélyezni kell minden olyan előfizetési fióknál, amely rendelkezik tulajdonosi engedélyekkel a fiókok vagy erőforrások megszegésének megakadályozásához.|
|\[Előzetes verzió]: Az MFA-t az írási engedélyekkel rendelkező előfizetési fiókokban kell engedélyezni|A többtényezős hitelesítést engedélyezni kell minden olyan előfizetési fióknál, amely rendelkezik írási engedéllyel a fiókok vagy erőforrások megszegésének megakadályozásához.|
|\[Előzetes verzió]: Az MFA-nak engedélyezve kell lennie az előfizetési fiókokban olvasási engedélyekkel|A többtényezős hitelesítést engedélyezni kell minden olyan előfizetési fióknál, amely olvasási engedéllyel rendelkezik a fiókok vagy erőforrások megszegésének megakadályozásához.|
|\[Előzetes verzió]: A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből. A rendszer letiltotta az elavult fiókokat a bejelentkezéshez.|
|\[Előzetes verzió]: Az elavult fiókokat el kell távolítani az előfizetésből|Az elavult fiókokat el kell távolítani az előfizetésből. A rendszer letiltotta az elavult fiókokat a bejelentkezéshez.|
|\[Előzetes verzió]: A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből az engedélyek elérésének megakadályozása érdekében.|
|\[Előzetes verzió]: Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Az írási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférés megakadályozása érdekében.|
|\[Előzetes verzió]: Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből a nem figyelt hozzáférés megakadályozása érdekében.|




## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő cikkeket.

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md): Megtudhatja, hogyan tervezheti meg és értelmezheti a tervezési szempontokat Azure Security Centerban.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása Azure Security Centerekkel](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

Ha többet szeretne megtudni a Azure Policyről, tekintse meg a [Mi az a Azure Policy?](../governance/policy/overview.md)című témakört.
