---
title: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával ArcSight |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával ArcSight'
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/03/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 13a5f0fa46b801535e28868b20eea8642f351277
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810618"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával ArcSight

[Micro fókusz ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) van (egy biztonsági biztonságiadat- és eseménykezelés SIEM) megoldást, amely segítséget nyújt, és reagálhassanak azokra a platform biztonsági fenyegetések ellen. Az Azure Active Directory (Azure AD) naplókban most átirányítása ArcSight a ArcSight connector használata az Azure ad-hez készült Azure Monitor használatával. Ez a funkció lehetővé teszi, hogy a bérlő, a biztonsági sérülés ArcSight használatával figyelheti.  

Ebből a cikkből elsajátíthatja az Azure AD-naplók átirányítása az Azure Monitor használatával ArcSight. 

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Azure event hub, amely tartalmazza az Azure Active Directory-naplók. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* ArcSight Syslog-NG démont-SmartConnector (SmartConnector) vagy ArcSight terheléselosztó konfigurált példánya. Az események ArcSight Load Balancer küldenek, ha azok ebből következően érkeznek a SmartConnector a terheléselosztó által.

Töltse le és nyissa meg a [beállítási útmutató a Azure Monitor eseményközpont ArcSight SmartConnector](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Ez az útmutató tartalmazza a lépéseket, telepítheti és konfigurálhatja az Azure monitor ArcSight SmartConnector kell. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Az Azure AD-naplók integrálása ArcSight

1. Első lépésként hajtsa végre a a **Előfeltételek** konfigurációs útmutató szakasza. Ez a szakasz a következő lépésekből áll:
    * Az Azure-ban, annak érdekében, hogy egy felhasználó a felhasználói engedélyek beállítása a **tulajdonosa** szerepkör telepítését és konfigurálását az összekötő.
    * Nyissa meg a kiszolgáló portjait Syslog-NG démont SmartConnector, így az Azure-ból elérhető. 
    * Az üzembe helyezés egy Windows PowerShell-parancsprogram fut, így a engedélyeznie kell a PowerShell-parancsfájlok futtatása a számítógépen, ahol szeretné-e az összekötő üzembe helyezése.

2. Kövesse a **az összekötő üzembe helyezése** üzembe helyezéséhez az összekötő konfigurációs útmutató szakasza. Ez a szakasz végigvezeti töltse le és csomagolja ki az összekötő, alkalmazás tulajdonságainak konfigurálása és futtassa a telepítési parancsfájl a kibontott mappát. 

3. Kövesse a **az Azure-ban a telepítés ellenőrzésének folyamatát** , hogy az összekötő be van állítva, és megfelelően működik-e. Ellenőrizze a következőket:
    * Az előfeltételként szükséges az Azure functions az Azure-előfizetésben jönnek létre.
    * Az Azure AD naplókat a rendszer streamként továbbítja a megfelelő helyre. 
    * Az alkalmazásbeállítások az üzemelő példányra megmaradnak, az Azure-Függvényalkalmazás alkalmazás beállításaiban. 
    * Egy új erőforráscsoportot ArcSight ArcSight összekötő és a CEF-formátumban a csatlakoztatott fájljait tartalmazó storage-fiókok Azure AD-alkalmazást az Azure-ban jön létre.

4. Végül befejeződött az üzembe helyezés utáni lépéseket a **üzembe helyezés utáni konfigurációk** a konfigurációs útmutató. Ez a szakasz azt ismerteti, hogyan további konfigurációra, ha egy App Service-csomag megakadályozza, hogy a függvényalkalmazások fog üresjárati időkorlátja időszak után, konfigurálja a streamelés az event hubs-diagnosztikai naplók és a SysLog-NG démont frissítése SmartConnector keystore tanúsítvány társítsa az újonnan létrehozott tárfiókra.

5. A beállítási útmutató is bemutatja, hogyan szabhatja testre az összekötő-tulajdonságok, az Azure-ban, és hogyan frissítését és eltávolítását az összekötőt. Van még egy szakasz a teljesítménnyel kapcsolatos fejlesztések, beleértve a verzióra való egy [Azure Használatalapú csomag](https://azure.microsoft.com/pricing/details/functions) és a egy ArcSight Load Balancer konfigurálása, ha az esemény terhelés nagyobb, mint egyetlen Syslog-NG démont SmartConnector is kezelni.

## <a name="next-steps"></a>További lépések

* [Beállítási útmutató a Azure Monitor eseményközpont ArcSight SmartConnector](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
