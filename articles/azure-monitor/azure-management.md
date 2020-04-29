---
title: Azure-felügyelet
description: Az Azure-alkalmazások és-erőforrások kezelésével kapcsolatos területek áttekintése, amelyek az Azure felügyeleti eszközein található tartalmakra mutató hivatkozásokat tartalmaznak.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/07/2018
ms.openlocfilehash: 93180f088935531f83da785bb7a490f80909a6cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669200"
---
# <a name="azure-management---monitoring"></a>Az Azure felügyelete – Monitorozás

Az Azure-ban a monitorozás a felügyelet egyik formája.  Ez a cikk röviden ismerteti a különböző felügyeleti területeket, amelyekre szükség van az Azure-ban az alkalmazások és erőforrások üzembe helyezéséhez és karbantartásához, továbbá a szükséges dokumentumokra mutató hivatkozásokat tartalmaz.

## <a name="management-in-azure"></a>Felügyelet az Azure-ban

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez.  Az Azure számos együttműködő szolgáltatást és eszközt kínál, amelyek teljes körű felügyeletet biztosítanak nem csupán az Azure-ban, hanem a felhőben és a helyszínen futó alkalmazások felett is.  Egy teljes körű felügyeleti környezet kialakításának első lépése, hogy megismerkedjen az elérhető eszközökkel és azzal, hogyan használhatja ezeket együtt különböző felügyeleti forgatókönyvek során.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez.  Ezekre a területekre egy életciklus szakaszaiként is gondolhatunk, mivel egymás után mindegyik sorra kerül egy erőforrás működtetése során.  A folyamat a kezdeti üzembe helyezéssel kezdődik, az erőforrás működtetése során folyamatosan zajlik, és csak akkor zárul le végleg, amikor az erőforrást kivonják a működésből.

![Felügyeleti funkciók](media/management-overview/management-capabilities.png)


A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Figyelés
A monitorozás adatok gyűjtését és elemzését jelenti azzal a céllal, hogy meg lehessen állapítani az üzleti alkalmazás és az általa használt erőforrások teljesítményét, állapotát és rendelkezésre állását. Egy hatékony monitorozási stratégia pontos információkat nyújt az alkalmazás egyes összetevőinek működéséről, és segít növelni az üzemidőt a kritikus problémákra vonatkozó előzetes értesítésekkel, amelyeknek köszönhetően még azelőtt fel lehet számolni őket, hogy tényleges problémát okoznának. Az Azure környezetben a monitorozást elsősorban az [Azure Monitor](../azure-monitor/overview.md) biztosítja, amely közös tárakat kínál a monitorozási adatok tárolására, több adatforrást biztosít az alkalmazást támogató különböző szintekhez, és különböző szolgáltatásokkal biztosítja a begyűjtött adatok elemzését és a reagálást az eseményekre.

## <a name="configure"></a>Konfigurálás
A konfigurálás az alkalmazások és erőforrások kezdeti üzembe helyezését, majd ezt követő, javításokkal és frissítésekkel történő karbantartását jelenti.  Ha szkriptekkel vagy szabályzatokkal automatizálja ezeket a feladatokat, elkerülheti a redundanciákat, minimalizálhatja a feladatokra fordított időt és energiát, és pontosabban és hatékonyabban dolgozhat.  Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek.  A folyamatok automatizálására szolgáló runbookok mellett konfiguráció- és frissítésfelügyeletet is kínál, ezzel segítve a konfigurációk szabályzatokkal történő kezelését, valamint a frissítések azonosítását és telepítését.

## <a name="govern"></a>Irányítás
Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség.  Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.  Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el.  Az [Azure Policy](../governance/policy/overview.md) lehetővé teszi olyan szabályzatdefiníciók létrehozását, hozzárendelését és kezelését, amelyek különböző szabályokat és műveleteket kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. A [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) segítségével nyomon követheti az Azure-erőforrások és más felhőalapú szolgáltatók (például az AWS és a Google) Felhőbeli használatát és kiadásait.

## <a name="secure"></a>Biztonságos
Az alkalmazások, erőforrások és adatok biztonságának felügyeletébe beletartozik a fenyegetések értékelése, a biztonsági adatok gyűjtése és elemzése, valamint annak biztosítása, hogy az alkalmazások és erőforrások kialakítása és konfigurációja biztonságos legyen.  A biztonsági monitorozással és a fenyegetések elemzésével az [Azure Security Center](../security-center/security-center-intro.md) foglalkozik, amely egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz.  Az [Azure biztonsági megoldásait ismertető](../security/fundamentals/overview.md) témakör részletesen foglalkozik az Azure biztonsági kérdéseivel, és az Azure-erőforrások biztonságos konfigurálásához is útmutatást nyújt.


## <a name="protect"></a>Védelem
A védelem annak garantálását jelenti, hogy az alkalmazások és adatok mindig rendelkezésre álljanak, olyan leállások esetén is, amelyeknek a bekövetkeztére Ön nincsen hatással.  Az Azure-ban erről a védelemről két szolgáltatás gondoskodik.  Az [Azure Backup](../backup/backup-introduction-to-azure-backup.md) helyreállítható biztonsági másolatokat készít az adatokról, és azokat a felhőben vagy helyszíni gépeken tárolja.    Az [Azure Site Recovery](../site-recovery/site-recovery-overview.md) az alkalmazások magas rendelkezésre állásáról gondoskodik, garantálva az üzletmenet folytonosságát és az azonnali vészhelyreállítást.

## <a name="migrate"></a>Migrate (Áttelepítés) 
A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.  Az [Azure Migrate](../migrate/migrate-overview.md) szolgáltatás segít felmérni, hogy a helyszíni virtuális gépek mennyire alkalmasak az Azure-felhőbe való migrálásra, és erről teljesítményalapú méretezési és költségbecsléseket is készít.  Az Azure Site Recovery segít a [helyszíni](../site-recovery/migrate-tutorial-on-premises-azure.md) virtuális gépek vagy az [Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) virtuális gépeinek tényleges migrálásában.  Az [Azure Database Migration](../dms/dms-overview.md) több adatbázis-erőforrás migrálását teszi lehetővé Azure-adatplatformokra.

