---
title: Hibaelhárítás a műveletnaplók használatával a BizTalk Services |} A Microsoft Docs
description: Hibaelhárítás a műveletnaplók használatával a BizTalk Services. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: f69035202a3358af38ebaf8e94abdd3b030e633f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256068"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Hibaelhárítás a műveletnaplók használatával

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Mik azok a műveleti naplói
A műveletnaplók szolgáltatások funkciója lehetővé teszi, hogy az Azure-szolgáltatások, többek között a BizTalk Services a végrehajtott műveletek korábbi naplók megtekintéséhez. Ez lehetővé teszi, hogy a műveletek a legfeljebb 180 nappal a BizTalk-szolgáltatás előfizetésével kapcsolatos előzményadatok megtekintése.

> [!NOTE]
> Ez a funkció csak rögzíti a műveletek a BizTalk-szolgáltatások, például ha a szolgáltatás elindult, a Naplók biztonsági fel, és így tovább. Az ilyen műveletek használatával nyomon követi a [BizTalk-szolgáltatás REST API-k](https://msdn.microsoft.com/library/azure/dn232347.aspx). Az operations Management-szolgáltatások használatával nyomon követett teljes listáját lásd: [műveletek nyomon követett Azure Management szolgáltatások segítségével](#bizops).<br/><br/>
> Ez nem rögzíti a BizTalk szolgáltatások modul (például hidat, és így tovább által feldolgozott üzenetet.) kapcsolatos tevékenységeket a naplókat. Ezek a naplók megtekintéséhez használja a dokumentumkövetési nézet a BizTalk Services portálon. További információkért lásd: [üzenetek nyomon követése](https://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>A BizTalk Services műveletnaplók megtekintése
1. Válassza a portál **szolgáltatások**, majd válassza ki a **műveletnaplók** fülre.
2. A naplók előfizetés, dátumtartomány, szolgáltatás típusa (pl. a BizTalk Services), szolgáltatásnév vagy a művelet (sikeres, sikertelen) állapotának különböző paraméterek alapján szűrhetők.
3. Kattintson a pipa, megtekintheti a szűrt lista. Az alábbi képen látható testbiztalkservice kapcsolódó tevékenységek: ![művelet naplóinak megtekintése][ViewLogs] 
4. Tovább egy adott művelet, válassza ki a, és kattintson a **részletek** alján a tálcán.

## <a name="bizops"></a>Az Azure Management Services segítségével nyomon műveletek
A következő táblázat felsorolja a műveleteket, a rendszer nyomon követi az Azure Management Services használatával:

| Művelet neve | Tevékenység |
| --- | --- |
| CreateBizTalkService |A művelet új BizTalk-szolgáltatás létrehozása |
| DeleteBizTalkService |A művelet törli a BizTalk-szolgáltatás |
| RestartBizTalkService |A művelet egy BizTalk szolgáltatás újraindítása |
| StartBizTalkService |BizTalk-szolgáltatás indítási művelete |
| StopBizTalkService |A művelet egy BizTalk szolgáltatás leállítása |
| DisableBizTalkService |A művelet letiltja a BizTalk-szolgáltatás |
| EnableBizTalkService |A művelet egy BizTalk szolgáltatás engedélyezése |
| BackupBizTalkService |A művelet biztonsági mentése a BizTalk-szolgáltatás |
| RestoreBizTalkService |A művelet a BizTalk-szolgáltatás a megadott biztonsági másolatból |
| SuspendBizTalkService |A művelet felfüggeszti a BizTalk-szolgáltatás |
| ResumeBizTalkService |A művelet folytatásához a BizTalk-szolgáltatás |
| ScaleBizTalkService |A művelet egy BizTalk szolgáltatás vertikális felfelé vagy lefelé |
| ConfigUpdateBizTalkService |BizTalk-szolgáltatás konfigurációjának frissítésére szolgáló művelet |
| ServiceUpdateBizTalkService |A művelet- és Visszaléptetés a BizTalk-szolgáltatás, más verzióra |
| PurgeBackupBizTalkService |A művelet véglegesen törölni a biztonsági mentés a megőrzési időszak kívül a BizTalk szolgáltatás |

## <a name="see-also"></a>Lásd még:
* [Biztonsági mentési BizTalk-szolgáltatás](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [BizTalk-szolgáltatás a biztonsági másolatból](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [A BizTalk Services: Fejlesztői, alapszintű, Standard és prémium kiadások diagramja](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [A BizTalk Services: kiépítés](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Kiépítési állapot diagramja](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Szabályozás](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Kiállító neve és kiállító kulcsa](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

