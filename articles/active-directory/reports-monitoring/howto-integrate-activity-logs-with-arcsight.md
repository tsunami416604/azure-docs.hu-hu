---
title: Naplók integrálása az ArcSight segítségével az Azure Monitor használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként integrálható az Azure Active Directory-naplók az ArcSight szolgáltatással az Azure Monitor használatával
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05002c1b11ef31b61fb4036f09dc8edcdafca767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75608380"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Az Azure Active Directory-naplók integrálása az ArcSight szolgáltatással az Azure Monitor használatával

[A Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) egy biztonsági információs és eseménykezelési (SIEM) megoldás, amely segít észlelni és reagálni a platformon lévő biztonsági fenyegetésekre. Most már átirányíthatja az Azure Active Directory (Azure AD) naplók at ArcSight az Azure Monitor segítségével az ArcSight-összekötő az Azure AD használatával. Ez a funkció lehetővé teszi, hogy figyelje a bérlő biztonsági biztonsági biztonsággal kapcsolatban az ArcSight használatával.  

Ebben a cikkben megtudhatja, hogyan irányíthatja az Azure AD-naplókat az ArcSight az Azure Monitor használatával. 

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Az Azure-eseményközpont, amely tartalmazza az Azure AD-tevékenységnaplók. Megtudhatja, hogyan [streamelheti a tevékenységnaplókat egy eseményközpontba.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 
* Az ArcSight Syslog NG Daemon SmartConnector (SmartConnector) vagy az ArcSight load Balancer konfigurált példánya. Ha az eseményeket az ArcSight terheléselosztónak küldi el, a rendszer ennek megfelelően a Load Balancer küldi el őket a SmartConnector-nak.

Töltse le és nyissa meg az [ArcSight SmartConnector for Azure Monitor Event Hub konfigurációs útmutatóját.](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) Ez az útmutató az ArcSight SmartConnector for Azure Monitor telepítéséhez és konfigurálásához szükséges lépéseket tartalmazza. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Az Azure AD-naplók integrálása az ArcSightsegítségével

1. Először **hajtsa** végre a konfigurációs útmutató Előfeltételek szakaszában leírt lépéseket. Ez a szakasz a következő lépéseket tartalmazza:
    * Állítsa be a felhasználói engedélyeket az Azure-ban, hogy megbizonyosodjon arról, hogy van egy felhasználó a **tulajdonosi** szerepkörrel az összekötő üzembe helyezéséhez és konfigurálásához.
    * Nyissa meg a portokat a kiszolgálón a Syslog NG Daemon SmartConnector segítségével, így az az Azure-ból érhető el. 
    * A központi telepítés egy Windows PowerShell-parancsfájlt futtat, ezért engedélyeznie kell a PowerShell számára a parancsfájlok futtatását azon a számítógépen, amelyen telepíteni szeretné az összekötőt.

2. Kövesse az **összekötő üzembe helyezéséhez** az összekötő szakaszban a konfigurációs útmutató lépéseit. Ez a szakasz bemutatja, hogyan töltheti le és bonthatja ki az összekötőt, konfigurálhatja az alkalmazástulajdonságait, és futtathatja a központi telepítési parancsfájlt a kibontott mappából. 

3. Az **Azure-beli üzembe helyezés ellenőrzése** című lépések segítségével ellenőrizze, hogy az összekötő be van-e állítva, és megfelelően működik-e. Ellenőrizze a következőket:
    * A szükséges Azure-függvények az Azure-előfizetésben jönnek létre.
    * Az Azure AD-naplók streamelése a megfelelő helyre. 
    * A központi telepítés alkalmazásbeállításai megmaradnak az Azure-függvényalkalmazások alkalmazásbeállításaiban. 
    * Az ArcSight új erőforráscsoportja jön létre az Azure-ban, az ArcSight-összekötőhöz és a CEF-formátumban leképezett fájlokat tartalmazó Tárfiókokhoz egy Azure AD-alkalmazással.

4. Végül hajtsa végre a telepítés utáni lépéseket a konfigurációs útmutató **üzembe helyezés utáni konfigurációk** ban. Ez a szakasz bemutatja, hogyan hajthat végre további konfigurációt, ha egy App Service-csomaggal rendelkezik, hogy megakadályozza, hogy a függvényalkalmazások egy időbeli elmaradási idő után tétlenül menjenek, konfigurálja a diagnosztikai naplók streamelését az eseményközpontból, és frissítse a SysLog NG démont SmartConnector keystore tanúsítvány társítani az újonnan létrehozott tárfiókhoz.

5. A konfigurációs útmutató azt is ismerteti, hogyan szabhatja testre az összekötő tulajdonságait az Azure-ban, és hogyan frissítheti és távolíthatja el az összekötőt. Van is egy szakasz a teljesítmény javítása, beleértve az [Azure-fogyasztási csomagra](https://azure.microsoft.com/pricing/details/functions) való frissítés és az ArcSight terheléselosztó konfigurálása, ha az esemény terhelés nagyobb, mint amit egy Syslog NG Daemon SmartConnector képes kezelni.

## <a name="next-steps"></a>További lépések

[Konfigurációs útmutató az ArcSight SmartConnector for Azure Monitor Event Hub alkalmazáshoz](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
