---
title: Az Azure Automation áttekintése
description: Útmutató az Azure Automation az infrastruktúra és az alkalmazások életciklusának automatizálásához történő használatához.
services: automation
ms.subservice: process-automation
keywords: Azure automation, DSC, powershell, állapotkonfiguráció, frissítéskezelés, változáskövetés, DSC, leltár, runbookok, python, grafikus
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010239"
---
# <a name="an-introduction-to-azure-automation"></a>Az Azure Automation bemutatása

Ez a cikk röviden áttekinti az Azure Automationt, valamint választ ad néhány gyakran felmerülő kérdésre. A különböző képességekkel kapcsolatos további információért kattintson a jelen áttekintésben található hivatkozásokra.

## <a name="about-azure-automation"></a>Az Azure Automation-ről

Az Azure Automation felhőalapú automatizálási és konfigurációs szolgáltatást nyújt, amely támogatja a konzisztens felügyeletet az Azure-ban és a nem Azure-környezetekben. Folyamatautomatizálást, konfigurációkezelést, frissítéskezelést, megosztott képességeket és heterogén funkciókat tartalmaz. Az automatizálás teljes körű vezérlést biztosít a telepítés, a műveletek és a számítási feladatok és erőforrások leszerelése során.

![Automatizálási lehetőségek](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Folyamatautomatizálás

Az Azure Automation folyamatautomatizálása lehetővé teszi a gyakori, időigényes és hibalehetőségeket rejtő felhőkezelési feladatok automatizálását. Ez a szolgáltatás segít az üzleti értéket növelő munkára összpontosítani. A hibák csökkentése és a hatékonyság növelése révén az üzemeltetési költségek is csökkennek. A folyamatautomatizálási működési környezetet az [Azure Automation Runbook-végrehajtása](automation-runbook-execution.md)részletezi.

A folyamatautomatizálás támogatja az Azure-szolgáltatások és más nyilvános rendszerek integrálását, amelyek a végpontok közötti folyamatok üzembe helyezéséhez, konfigurálásához és kezeléséhez szükségesek. A szolgáltatás lehetővé teszi [a runbookok](automation-runbook-types.md) grafikus, PowerShell vagy Python használatával történő futtatását. [Egy hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md)használatával egyesítheti a felügyelet a helyszíni környezetekben vezénylésével. [A webhookok](automation-webhooks.md) lehetővé teszik a kérések teljesítését, valamint a folyamatos kézbesítés és műveletek biztosítását az ITSM, a DevOps és a figyelési rendszerek automatizálásának elindításával. 

## <a name="configuration-management"></a>Konfigurációkezelés

Az Azure Automation [állapotkonfigurációegy](automation-dsc-overview.md) felhőalapú megoldás a PowerShell kívánt állapotkonfigurációjához (DSC), amely vállalati környezetek számára nyújt szolgáltatásokat. Ezzel a funkcióval kezelheti a DSC-erőforrásokat az Azure Automationben, és konfigurációkat alkalmazhat virtuális vagy fizikai gépekre egy DSC lekéréses kiszolgálóról az Azure-felhőben. Figyelheti és automatikusan frissítheti a gépkonfigurációkat fizikai és virtuális gépeken, Windows vagy Linux rendszeren, a felhőben vagy a helyszínen. A készlettámogatás lehetővé teszi a vendégerőforrások lekérdezését a telepített alkalmazások és más konfigurációs elemek láthatóságához.
 
Az Azure Automation állapotkonfigurációs szolgáltatás a részletes jelentéskészítési és keresési lehetőségeket biztosítja. Ezekkel a szolgáltatásokkal részletes információkat találhat arról, hogy mi van konfigurálva az operációs rendszeren belül. A szolgáltatás támogatja a változások nyomon követését a szolgáltatások, démonok, szoftverek, rendszerleíró adatbázis és fájlok a környezetben, hogy segítsen diagnosztizálni a nem kívánt változásokat, és riasztást. Fontos kapcsolódó funkció a főbb események jelentése, például olyan események jelentése, amelyeket akkor adnak ki, amikor a csomópontok eltérnek a hozzárendelt konfigurációktól. 

## <a name="update-management"></a>Frissítéskezelés

Az Azure Automation tartalmazza a windowsos és linuxos rendszerek [frissítéskezelési](automation-update-management.md) megoldását hibrid környezetekben. Ezzel a megoldással betekintést nyerhet a frissítési megfelelőségbe az Azure-ban és más felhőkben, valamint a helyszínen. Update management lehetővé teszi, hogy ütemezett központi telepítések, amelyek koordinálják a frissítések telepítését egy meghatározott karbantartási időszakon belül. Ha egy frissítést nem kell telepíteni a számítógépre, a frissítésfelügyeleti szolgáltatások segítségével kizárhatja azt a központi telepítésből.

## <a name="shared-capabilities"></a>Közös képességek

Az Azure Automation számos megosztott lehetőséget kínál, beleértve a megosztott erőforrásokat, a szerepköralapú hozzáférés-vezérlést, a rugalmas ütemezést, a forrásvezérlés integrációját, a naplózást és a címkézést.

### <a name="shared-resources"></a><a name="shared-resources"></a>Megosztott erőforrások

Az Azure Automation megosztott erőforrásainak segítségével könnyebben automatizálhatja és konfigurálhatja a nagy méretű környezeteket.

* **[Ütemezések](automation-schedules.md)** – Eseményindító műveletek előre meghatározott időpontokban.
* **[Modulok](automation-integration-modules.md)** – Az Azure és más rendszerek kezelése. Modulokat importálhat a Microsoft automation-fiókjába, a külső, közösségi és egyéni definiált parancsmagokat és dsc-erőforrásokat.
* **[Modulok galéria](automation-runbook-gallery.md)** – Támogatja a natív integráció a PowerShell-galériában, hogy a runbookok megtekintéséhez és importálja őket az Automation-fiókba. A galéria lehetővé teszi, hogy gyorsan elkezdjék integrálni és a folyamatok szerkesztését a PowerShell-galériából és a Microsoft Script Centerből.
* **[Python 2-csomagok](python-packages.md)** – A Python 2 runbookok támogatása az Automation-fiókhoz.
* **[Hitelesítő adatok](automation-credentials.md)** – Biztonságosan tárolja a bizalmas információkat, amelyekrunbookok és konfigurációk használhatják futásidőben.
* **[Kapcsolatok](automation-connections.md)** – A rendszerekkel való kapcsolatokhoz használt név-érték közös információpárok. A modul szerzője határozza meg a kapcsolatokat a runbookokban és konfigurációkban a futásidőben való használatra.
* **[Tanúsítványok](automation-certificates.md)** – Adja meg az üzembe helyezett erőforrások hitelesítéséhez és védelméhez használandó adatokat, ha runbookok vagy DSC-konfigurációk érik futásidőben. 
* **[Változók](automation-variables.md)** – Tartsa lenyomva a runbookok és konfigurációk között használható tartalmakat. A változóértékek et anélkül módosíthatja, hogy módosítania kellene az őket referencia runbookokat vagy konfigurációkat.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure Automation támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) az Automation-fiókhoz és annak erőforrásaihoz való hozzáférés szabályozásához. Ha többet szeretne tudni az RBAC automation-fiókon, runbookokon és feladatokon történő konfigurálásáról, olvassa el [az Azure Automation szerepköralapú hozzáférés-vezérléscímű témakört.](automation-role-based-access-control.md)

### <a name="source-control-integration"></a>Verziókövetés integrálása

Az Azure Automation lehetővé teszi [a forrásvezérlés integrációját.](source-control-integration.md) Ez a szolgáltatás olyan kódként támogatja a konfigurációt, ahol a runbookok vagy konfigurációk beadhatók a forrásvezérlő rendszerbe.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogén támogatás (Windows és Linux)

Az automatizálás takarásban van a hibrid felhőkörnyezetben, valamint a Windows és Linux rendszerekben is. Egységes módot biztosít a telepített számítási feladatok és az azokat futtató operációs rendszerek automatizálására és konfigurálására.

## <a name="common-scenarios-for-automation"></a>Általános példák az Automation használatára

Az Azure Automation az infrastruktúra és az alkalmazások teljes életciklusa során támogatja a felügyeletet. A gyakori forgatókönyvek a következők:

* **Runbookok írása** – PowerShell- és PowerShell-munkafolyamat, grafikus, Python 2- és DSC-runbookok közös nyelveken. 
* **Erőforrások létrehozása és üzembe helyezése** – Virtuális gépek üzembe helyezése hibrid környezetben runbookok és Az Azure Resource Manager-sablonok használatával. Integrálható a fejlesztői eszközökbe, például a Jenkins beépülésébe és az Azure DevOps-ba.
* **Virtuális gépek konfigurálása** – A Windows- és Linux-gépek felmasszák és konfigurálják az infrastruktúra és az alkalmazás konfigurációit.
* **Tudás megosztása** – Tudás átvitele a rendszerbe arról, hogy a szervezet hogyan biztosítja és karbantartja a munkaterheléseket. 
* **Készlet lekérése** – Teljes leltárt kaphat az üzembe helyezett erőforrásokról a célzáshoz, a jelentéskészítéshez és a megfelelőséghez. 
* **Módosítások keresése** – Azonosítsa azokat a módosításokat, amelyek helytelen konfigurációt okozhatnak, és javíthatja a működési megfelelőséget.
* **Figyelő** – Elkülönítheti a problémákat okozó gépmódosításokat, és kiigazítja vagy felügyeleti rendszerekre bővíti azokat.
* **Védelem** – Karanténgépek, ha biztonsági riasztások jelennek meg. Megadhatja a vendégen futtatott rendszerekre vonatkozó követelményeket.
* **Irányítás** - Az RBAC beállítása csapatokszámára. Visszanyerheti a nem használt erőforrásokat.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Az Automation szolgáltatás díjszabása

Tekintse át az Azure Automation-hez társított árakat a [díjszabási](https://azure.microsoft.com/pricing/details/automation/) oldalon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Automatizálási fiók létrehozása](automation-quickstart-create-account.md)

