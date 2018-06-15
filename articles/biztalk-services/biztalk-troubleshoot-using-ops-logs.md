---
title: BizTalk szolgáltatások használatával műveletnaplók hibaelhárítása |} Microsoft Docs
description: Végezzen hibaelhárítást a BizTalk szolgáltatások műveletnaplók használatával. MABS, WABS
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
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102018"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk szolgáltatások: Műveletnaplók használata – hibaelhárítás

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Mik azok a műveleti naplói
A műveletnaplók szolgáltatások funkció lehetővé teszi az Azure-szolgáltatásokkal, beleértve a BizTalk szolgáltatások végre műveletek korábbi naplók. Ez lehetővé teszi, hogy megtekintheti az előzményadatokat a BizTalk szolgáltatás előfizetésének kötött 180 nap alatt a felügyeleti műveleteihez kapcsolódik.

> [!NOTE]
> Ez a funkció csak rögzíti a naplókat, ha a szolgáltatás indítása, például a BizTalk szolgáltatások felügyeleti műveleteihez biztonsági fel, és így tovább. Ilyen műveletek segítségével nyomon követi a [BizTalk szolgáltatás REST API-k](http://msdn.microsoft.com/library/azure/dn232347.aspx). Szolgáltatások használatával nyomon követett műveletek teljes listáját lásd: [műveletek nyomon követett Azure felügyeleti szolgáltatások használatával](#bizops).<br/><br/>
> Ez a nem rögzíthető lemezkép a naplók BizTalk szolgáltatás futásideje (például hidak, és így tovább által feldolgozott üzenet.) kapcsolódó tevékenységekhez. Ezek a naplók megtekintéséhez használja a nyomon követési a BizTalk szolgáltatások portálról. További információkért lásd: [üzenetek nyomon követése](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>BizTalk szolgáltatások műveletnaplók megtekintése
1. Válassza a portál **szolgáltatások**, majd válassza ki a **műveletnaplók** fülre.
2. A naplók előfizetés, a dátumtartományt, a szolgáltatás típusa (pl. BizTalk szolgáltatások), a szolgáltatás neve vagy a (sikeres, sikertelen) a művelet állapotának például különböző paraméterek alapján szűrhetők.
3. Válassza ki a jelet a szűrt listájának megtekintése. A következő kép bemutatja testbiztalkservice kapcsolódó tevékenységek: ![műveletnaplók megtekintése][ViewLogs] 
4. Egy adott műveletekre vonatkozó megtekintéséhez több, válassza ki a, és kattintson **részletek** alján a tálcán.

## <a name="bizops"></a>Az Azure szolgáltatások segítségével nyomon műveletek
A következő táblázat felsorolja a nyomon követett az Azure szolgáltatások használatával műveletek:

| A művelet neve | Tevékenység |
| --- | --- |
| CreateBizTalkService |Új BizTalk szolgáltatás létrehozása művelet |
| DeleteBizTalkService |A művelet egy BizTalk szolgáltatás törlése |
| RestartBizTalkService |A művelet egy BizTalk szolgáltatás újraindítása |
| StartBizTalkService |A BizTalk szolgáltatás indítási művelete |
| StopBizTalkService |A művelet egy BizTalk szolgáltatás leállítása |
| DisableBizTalkService |A művelet egy BizTalk szolgáltatás letiltása |
| EnableBizTalkService |A művelet egy BizTalk szolgáltatás engedélyezése |
| BackupBizTalkService |Készítsen biztonsági másolatot a BizTalk szolgáltatás művelet |
| RestoreBizTalkService |A BizTalk szolgáltatás biztonsági másolatból történő visszaállítását megadott művelet |
| SuspendBizTalkService |A művelet felfüggeszti a BizTalk szolgáltatás |
| ResumeBizTalkService |BizTalk szolgáltatás folytatásához a művelet |
| ScaleBizTalkService |A BizTalk szolgáltatások méretezéséhez felfelé vagy lefelé művelet |
| ConfigUpdateBizTalkService |A BizTalk szolgáltatás konfigurációját frissítési művelete |
| ServiceUpdateBizTalkService |A BizTalk szolgáltatás egy másik verzió visszaminősítését vagy frissítéséhez művelet |
| PurgeBackupBizTalkService |Biztonsági mentés a megőrzési időn kívül a BizTalk szolgáltatás kiürítése művelet |

## <a name="see-also"></a>Lásd még:
* [Biztonsági mentési BizTalk szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Állítsa vissza biztonsági másolatból BizTalk szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk szolgáltatások: Fejlesztői, Basic, Standard és prémium kiadás diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk szolgáltatások: kiépítés](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Kiépítési állapot diagramja](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Szabályozás](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Kiállító neve és kiállító kulcsa](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

