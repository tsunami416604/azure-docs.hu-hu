---
title: Az Azure Management áttekintése
description: A felügyeleti területeket, az Azure-alkalmazások és erőforrások az Azure felügyeleti eszközökhöz a tartalmakra mutató hivatkozásokat áttekintése.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: f058bf8195c56681be0419a0a30f3d814e5d1906
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960832"
---
# <a name="management-in-azure"></a>Felügyelet az Azure-ban

Irányítás az Azure-ban az Azure-felügyelet egyik formája. Ez a cikk röviden ismerteti a különböző felügyeleti területeket, amelyekre szükség van az Azure-ban az alkalmazások és erőforrások üzembe helyezéséhez és karbantartásához, továbbá a szükséges dokumentumokra mutató hivatkozásokat tartalmaz.

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez. Az Azure számos együttműködő szolgáltatást és eszközt kínál, amelyek teljes körű felügyeletet biztosítanak nem csupán az Azure-ban, hanem a felhőben és a helyszínen futó alkalmazások felett is. Egy teljes körű felügyeleti környezet kialakításának első lépése, hogy megismerkedjen az elérhető eszközökkel és azzal, hogyan használhatja ezeket együtt különböző felügyeleti forgatókönyvek során.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez. Ezekre a területekre egy életciklus szakaszaiként is gondolhatunk, mivel egymás után mindegyik sorra kerül egy erőforrás működtetése során. A folyamat a kezdeti üzembe helyezéssel kezdődik, az erőforrás működtetése során folyamatosan zajlik, és csak akkor zárul le végleg, amikor az erőforrást kivonják a működésből.

![Felügyeleti funkciók](../monitoring/media/management-overview/management-capabilities.png)

Nincsen olyan Azure-szolgáltatás, amely egymaga képes lenne ellátni az egyes felügyeleti területek összes feladatát, ehhez több szolgáltatás együttes használata szükséges. Egyes szolgáltatások célzott funkciókat tartalmaznak, az Application Insights például a webalkalmazások monitorozását végzi. Más szolgáltatások általános funkciókat nyújtanak, a Log Analytics például más szolgáltatások felügyeleti adatait tárolja, lehetővé téve a különböző szolgáltatások által küldött különböző típusú adatok elemzését.

A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Figyelés

A monitorozás adatok gyűjtését és elemzését jelenti azzal a céllal, hogy meg lehessen állapítani az üzleti alkalmazás és az általa használt erőforrások teljesítményét, állapotát és rendelkezésre állását. Egy hatékony monitorozási stratégia pontos információkat nyújt az alkalmazás egyes összetevőinek működéséről, és segít növelni az üzemidőt a kritikus problémákra vonatkozó előzetes értesítésekkel, amelyeknek köszönhetően még azelőtt fel lehet számolni őket, hogy tényleges problémát okoznának. Az [Azure-alkalmazások és -erőforrások monitorozásával](../monitoring/monitoring-overview.md) foglalkozó témakör áttekintést nyújt az Azure-beli monitorozásról, és bemutatja azokat a szolgáltatásokat, amelyek szerepet kapnak a monitorozási stratégiában.

## <a name="configure"></a>Konfigurálás

A konfigurálás az alkalmazások és erőforrások kezdeti üzembe helyezését, majd ezt követő, javításokkal és frissítésekkel történő karbantartását jelenti. Ha szkriptekkel vagy szabályzatokkal automatizálja ezeket a feladatokat, elkerülheti a redundanciákat, minimalizálhatja a feladatokra fordított időt és energiát, és pontosabban és hatékonyabban dolgozhat. Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek. A folyamatok automatizálására szolgáló runbookok mellett konfiguráció- és frissítésfelügyeletet is kínál, ezzel segítve a konfigurációk szabályzatokkal történő kezelését, valamint a frissítések azonosítását és telepítését.

## <a name="govern"></a>Irányítás

Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség. Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.
Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el. Az [Azure Policy](../azure-policy/azure-policy-introduction.md) lehetővé teszi olyan szabályzatdefiníciók létrehozását, hozzárendelését és kezelését, amelyek különböző szabályokat és műveleteket kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az [Azure Cost Management by Cloudyn](../cost-management/overview.md) segítségével követheti az Azure-erőforrások és egyéb felhőszolgáltatók, például az AWS vagy a Google felhőhasználatát és költségeit.

## <a name="secure"></a>Biztonságos

Az alkalmazások, erőforrások és adatok biztonságának beletartozik a fenyegetések értékelése, összegyűjtése és biztonsággal kapcsolatos adatok elemzése és annak biztosítása, hogy az alkalmazások és erőforrások kialakítása és konfigurációja biztonságos módon. Azure Security Center, amely tartalmazza a egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet a hibrid felhőbeli számítási feladatokban által biztosított biztonsági figyelési és kockázati elemzések. Az [Azure biztonsági megoldásait ismertető](../security/azure-security.md) témakör részletesen foglalkozik az Azure biztonsági kérdéseivel, és az Azure-erőforrások biztonságos konfigurálásához is útmutatást nyújt.

## <a name="protect"></a>Védelem

A védelem annak garantálását jelenti, hogy az alkalmazások és adatok mindig rendelkezésre álljanak, olyan leállások esetén is, amelyeknek a bekövetkeztére Ön nincsen hatással. Az Azure-ban erről a védelemről két szolgáltatás gondoskodik. [Az Azure Backup](../backup/backup-introduction-to-azure-backup.md)biztosít a biztonsági mentés és helyreállítás az adatok a felhőben vagy a helyszínen. Az [Azure Site Recovery](../site-recovery/site-recovery-overview.md) az alkalmazások magas rendelkezésre állásáról gondoskodik, garantálva az üzletmenet folytonosságát és az azonnali vészhelyreállítást.

## <a name="migrate"></a>Migrate (Áttelepítés)

A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.
Az [Azure Migrate](../migrate/migrate-overview.md) szolgáltatás segít felmérni, hogy a helyszíni virtuális gépek mennyire alkalmasak az Azure-felhőbe való migrálásra, és erről teljesítményalapú méretezési és költségbecsléseket is készít. Az Azure Site Recovery segít a [helyszíni](../site-recovery/migrate-tutorial-on-premises-azure.md) virtuális gépek vagy az [Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) virtuális gépeinek tényleges migrálásában. Az [Azure Database Migration](../dms/dms-overview.md) több adatbázis-erőforrás migrálását teszi lehetővé Azure-adatplatformokra.
