---
title: Az Azure Stack Edge Pro-eszközön futó proaktív naplók megismerése
description: Leírja, hogyan történik az proaktív naplók gyűjtése egy Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466436"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktív naplók gyűjtése az Azure Stack Edge-eszközön

A rendszerállapot-mutatók alapján engedélyezheti az előjelzéses naplózási gyűjteményt a Azure Stack Edge-eszközön, hogy az eszköz hibáinak hatékony hibaelhárítását segítse. Ez a cikk ismerteti, hogy mi az a proaktív naplók gyűjtése, hogyan engedélyezhető, és hogyan történik az adatkezelés, amikor a proaktív naplók gyűjtése engedélyezve van.
   
A cikkben található információk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkoznak.

## <a name="about-proactive-log-collection"></a>A proaktív naplók gyűjtésének ismertetése

A Microsoft ügyfélszolgálati és mérnöki csapata rendszernaplókat használ a Azure Stack peremhálózati eszközön a művelet során esetlegesen felmerülő problémák hatékony azonosításához és javításához. Az előjelzéses naplózási gyűjtemény olyan metódus, amely riasztást küld a Microsoftnak egy probléma/esemény miatt (lásd a nyomon követett események előjelzéses naplózási mérőszámai szakaszt) az ügyfél Azure Stack peremhálózati berendezése. A hibával kapcsolatos támogatási naplókat a rendszer automatikusan feltölti a Microsoft által kezelt és felügyelt Azure Storage-fiókba. A Microsoft ügyfélszolgálata és a Microsoft mérnökök ezeket a támogatási naplókat tekintik át a probléma megoldásához szükséges legjobb műveletek meghatározásához.    

> [!NOTE]
> Ezeket a naplókat csak hibakeresési célokra használják, és ha problémák merülnek fel, támogatást nyújtanak az ügyfeleknek. 


## <a name="enabling-proactive-log-collection"></a>Proaktív naplók gyűjtésének engedélyezése

Engedélyezheti a proaktív naplók gyűjtését, amikor az eszközt a helyi felhasználói felületen kísérli meg aktiválni. 

1. Az eszköz helyi webes FELÜLETén válassza az **első lépések** lapot.
2. Az **aktiválási** csempén válassza az **aktiválás** lehetőséget. 

    ![Helyi webes felhasználói felület "Cloud details" 1. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Az **aktiválás** ablaktáblán:
    1. Adja meg azt az **aktiválási kulcsot** , amelyet az [Azure stack Edge Pro R aktiválási kulcsának beszerzéséhez](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)kapott.

    1. Engedélyezheti a proaktív naplók gyűjtését, hogy a Microsoft az eszköz állapotának megfelelően Gyűjtse össze a naplókat. Az így összegyűjtött naplók egy Azure Storage-fiókba lesznek feltöltve.
    
    1. Kattintson az **Alkalmaz** gombra.

    ![Helyi webes felhasználói felület "Cloud details" 2. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Proaktív naplók gyűjtési mutatói

Az előjelzéses naplózási gyűjtemény engedélyezése után a rendszer automatikusan feltölti a naplókat, ha a következő események valamelyike észlelhető az eszközön:  


|Riasztás/hiba/feltétel  |Description  |
|---------|---------|
|AcsUnhealthyCondition     |Az Azure konzisztens szolgáltatások állapota nem kifogástalan.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge ügynök nem fut.         |
|UpdateInstallFailedEvent | Nem lehetett telepíteni a frissítést.        |

 
A Microsoft továbbra is új eseményeket ad az előző listához. Új eseményekhez nem szükséges további beleegyezni. Tekintse meg ezt az oldalt, és ismerkedjen meg a legnaprakészebb proaktív naplók gyűjtési mutatókkal.    
 

## <a name="other-log-collection-methods"></a>Egyéb naplók gyűjtési módszerei

Az előjelzéses naplózási gyűjteményen kívül az észlelt konkrét problémákra vonatkozó naplókat is gyűjt, vannak olyan naplók is, amelyek a rendszerállapot és a működés mélyebb megismerését teszik lehetővé. Ezek a többi naplózási gyűjtemény általában a támogatási kérelem során vagy a Microsoft által az eszköz által biztosított telemetria alapján indítható el.  

## <a name="handling-data"></a>Az adatkezelés

Ha egy ügyfél lehetővé teszi az előjelzéses naplók gyűjtését, elfogadják, hogy a Microsoft a Azure Stack Edge-eszközről gyűjt naplókat az itt leírtak szerint. Az ügyfél emellett tudomásul veszi és elfogadja a naplók feltöltését és megőrzését egy, a Microsoft által kezelt és felügyelt Azure Storage-fiókban.

A Microsoft az információkat csak a rendszerállapot és a problémák elhárításához használja fel. Az adatkezelési, reklámozási vagy egyéb kereskedelmi célra nem használhatók az ügyfelek beleegyezett engedélye nélkül. 

A Microsoft által gyűjtött adatok a szokásos adatvédelmi gyakorlatnak megfelelően kezelhetők. Ha az ügyfél úgy dönt, hogy visszavonja az előjelzéses naplókhoz való beleegyezését, a visszavonást megelőzően beleegyezéssel gyűjtött adatokat a rendszer nem érinti.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [gyűjthet egy támogatási csomagot](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).