---
title: A Windows rendszerű virtuális asztal előzetes verziójának figyelése – Azure
description: A Windows rendszerű virtuális asztalok Azure Monitorével kapcsolatos hibák elhárítása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466788"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>A Windows rendszerű virtuális asztali számítógép (előzetes verzió) Azure Monitor hibáinak megoldása

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali Azure Monitor jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Windows rendszerű virtuális asztalok (előzetes verzió) Azure Monitor gyakori problémáinak ismert problémáit és megoldásait ismerteti.

## <a name="the-configuration-workbook-isnt-working-properly"></a>A konfigurációs munkafüzet nem működik megfelelően

Ha a Azure Monitor konfigurációs munkafüzet nem működik, ezeket az erőforrásokat használhatja a részek manuális beállításához:

- A diagnosztika manuális engedélyezéséhez vagy a Log Analytics munkaterület eléréséhez lásd: [a Windows rendszerű virtuális asztali diagnosztika küldése log Analytics](diagnostics-log-analytics.md).
- Ha manuálisan szeretné telepíteni a Log Analytics-bővítményt egy gazdagépre, tekintse meg [log Analytics virtuálisgép-bővítményt a Windowshoz](../virtual-machines/extensions/oms-windows.md).
- Új Log Analytics munkaterület beállításához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/learn/quick-create-workspace.md)című témakört.
- Teljesítményszámlálók hozzáadásához vagy eltávolításához tekintse meg a [teljesítményszámlálók konfigurálása](../azure-monitor/platform/data-sources-performance-counters.md)című témakört.
- Log Analytics munkaterület eseményeinek konfigurálásához lásd: a [Windows Eseménynapló adatforrásainak összegyűjtése log Analytics ügynökkel](../azure-monitor/platform/data-sources-windows-events.md).

Azt is megteheti, hogy a problémát az erőforrások hiánya okozta, vagy nem rendelkezik a szükséges engedélyekkel.

Ha az előfizetés nem rendelkezik Windows rendszerű virtuális asztali erőforrásokkal, az *előfizetés* paraméter nem jelenik meg.

Ha nem rendelkezik olvasási hozzáféréssel a megfelelő előfizetésekhez, azok nem jelennek meg az *előfizetési* paraméterben, és nem fogja látni az adataikat az irányítópulton. A probléma megoldásához forduljon az előfizetés tulajdonosához, és kérjen olvasási hozzáférést.

## <a name="my-data-isnt-displaying-properly"></a>Az adataim nem megfelelően jelennek meg

Ha az adatai nem megfelelően jelennek meg, előfordulhat, hogy a Azure Monitor konfigurációs folyamat során valami történt. Először is győződjön meg róla, hogy kitöltötte az összes mezőt a konfigurációs munkafüzetben az [Azure monitor használata a Windows rendszerű virtuális asztalhoz](azure-monitor.md)című témakörben leírtak szerint az üzemelő példány figyeléséhez. Bármikor módosíthatja az új és a meglévő környezet beállításait is. Ha hiányoznak a számlálók vagy események, a hozzájuk társított adatai nem jelennek meg a Azure Portalban.

Ha nem ad meg semmilyen információt, de az adatai még nem jelennek meg megfelelően, előfordulhat, hogy probléma van a lekérdezésben vagy az adatforrásokban. 

Ha nem lát telepítési hibákat, és még mindig nem látja a várt adatmennyiséget, érdemes 15 percet várnia, és frissítenie kell a hírcsatornát. A Azure Monitor 15 perces késési időszakot biztosítanak a naplófájlok feltöltéséhez. További információ: [adatfeldolgozási idő naplózása Azure monitorban](../azure-monitor/platform/data-ingestion-time.md).

Végül, ha nem ad meg semmilyen információt, de az adatai még nem jelennek meg, előfordulhat, hogy probléma van a lekérdezésben vagy az adatforrásokban. Előfordulhat, hogy kapcsolatba kell lépnie a támogatási szolgálattal a probléma megoldásához, ha ez a helyzet.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Szeretném testreszabni a Windows rendszerű virtuális asztali Azure Monitor

A Windows rendszerű virtuális asztali Azure Monitor Azure Monitor munkafüzeteket használ. A munkafüzetek lehetővé teszik a Windows rendszerű virtuális asztali munkafüzet sablonjának másolatának mentését, és a saját testreszabását.

A testreszabott sablonok nem frissülnek, amikor a termékcsoport frissíti az eredeti sablont. Ez a munkafüzetek eszközben való kialakítás, a frissített sablon másolatát kell mentenie, és újra kell építenie a testreszabásokat a frissítések elfogadásához. További információ: a [munkafüzet-alapú információk hibaelhárítása](../azure-monitor/insights/troubleshoot-workbooks.md) és a [munkafüzetek áttekintése](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nem tudom értelmezni az adatforrást

További információ az adatkifejezésekről: [Azure monitor for Window Virtual Desktop Szószedet](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>A szükséges adatértékek nem érhetők el

Nem található olyan adatpont, amely segít a probléma diagnosztizálásában? Küldje el nekünk visszajelzését!

- A visszajelzések elküldésével kapcsolatos további információkért lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás a Windows rendszerű virtuális asztalokhoz](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali Windows Virtual Desktop [visszajelzési központ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) vagy [a UserVoice fórum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)visszajelzéseit is elhagyhatja.

## <a name="known-issues"></a>Ismert problémák

Ezek a problémák jelenleg is tisztában vannak, és a javításra dolgozunk:

- Jelenleg csak egy előfizetést, erőforráscsoportot és egy állomást lehet figyelni egyszerre. Emiatt, amikor a felhasználói jelentések oldalon megérti a felhasználó élményét, ellenőriznie kell, hogy rendelkezik-e a felhasználó által használt megfelelő gazdagép-készlettel, vagy az adataik nem töltik fel a vizualizációkat.

- A kedvenc beállítások nem menthetők a Azure Monitorban, ha nem menti a munkafüzet egyéni sablonját. Ez azt jelenti, hogy a rendszergazdáknak minden alkalommal meg kell adniuk az előfizetés nevét, az erőforráscsoport nevét és a gazdagép-készlet beállításait, amikor megnyitják Azure Monitor a Windows rendszerű virtuális asztali számítógépeken.

- Jelenleg nem lehet exportálni a Windows rendszerű virtuális asztali Azure Monitor adatait az Excel programba.

- A kijelölt előfizetésben található összes termékre vonatkozó összes súlyosság 1 Azure Monitor riasztás az Áttekintés oldalon jelenik meg. Ez a tervezés szerint történik, mivel az előfizetés más termékeitől származó riasztások hatással lehetnek a Windows virtuális asztalra. A lekérdezés csak az 1. súlyossági szintű riasztásokra korlátozódik, kivéve az Áttekintés oldal magas prioritású súlyosságú riasztásait.

- Bizonyos hibaüzenetek nem a felhasználóbarát módon jelennek meg, és nem minden hibaüzenetet ismertetnek a dokumentációban.

## <a name="next-steps"></a>További lépések

Ha nem tudja, hogyan értelmezze az adatait, vagy többet szeretne megtudni a gyakori feltételekről, tekintse meg a [Windows rendszerű virtuális asztali szószedet Azure monitorét](azure-monitor-glossary.md). Ha szeretné megismerni, hogyan kell beállítani és Azure Monitor használni a Windows rendszerű virtuális asztali gépeket, tekintse meg a Windows rendszerű virtuális asztali Azure Monitor használata az üzemelő [példány figyeléséhez](azure-monitor.md)című témakört.