---
title: 'Azure AD Application Proxy: Verzió kiadási előzményei | Microsoft Docs'
description: Ez a cikk felsorolja az Azure AD Application Proxy összes kiadását, és ismerteti az új funkciókat és a rögzített problémákat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693901"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Application Proxy: Verziókiadások előzményei
Ez a cikk a kiadott Azure Active Directory (Azure AD) alkalmazásproxy verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti az alkalmazásproxy új szolgáltatásait és funkcióit. Az alkalmazásproxy-összekötők automatikusan frissülnek, amikor megjelent egy új verzió. 

Javasoljuk, hogy az automatikus frissítések engedélyezve legyenek az összekötők számára a legújabb funkciók és hibajavítások biztosításához. A Microsoft közvetlen támogatást biztosít a legújabb összekötő-verzióhoz és egy korábbi verzióhoz.

A kapcsolódó erőforrások listája:

Resource |  Részletek
--------- | --------- |
Alkalmazásproxy engedélyezése | Ebben az [oktatóanyagban](application-proxy-add-on-premises-application.md)az alkalmazásproxy engedélyezéséhez, valamint az összekötők telepítéséhez és regisztrálásához szükséges előfeltételeket ismertetjük.
Az Azure AD-alkalmazásproxy-összekötők ismertetése | További információ az [Összekötők felügyeletéről](application-proxy-connectors.md) és az összekötők [automatikus frissítéséről](application-proxy-connectors.md#automatic-updates).
Azure AD Application Proxy-összekötő letöltése |  [Töltse le a legújabb összekötőt](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Kiadás állapota

2018. szeptember 20.: Letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

- A WebSocket-támogatás hozzáadva a QlikSense alkalmazáshoz. A QlikSense és az alkalmazásproxy integrálásával kapcsolatos további tudnivalókért tekintse [meg ezt a](application-proxy-qlik.md)bemutatót. 
- Továbbfejlesztettük a telepítővarázsló segítségével, hogy könnyebben konfiguráljon egy kimenő proxyt. 
- A TLS 1,2 beállítása az összekötők alapértelmezett protokollja. 
- Új végfelhasználói licencszerződés (EULA) lett hozzáadva.  

### <a name="fixed-issues"></a>Hibák kijavítva:

- Kijavított egy hibát, amely némi memóriavesztés okozta az összekötőt.
- Frissült a Azure Service Bus verziója, amely tartalmazza az összekötő időtúllépési hibáinak elhárítását.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Kiadás állapota

2018. január 19.: Letöltésre kiadva

### <a name="fixed-issues"></a>Hibák kijavítva:

- A cookie-ban tartományi fordítást igénylő egyéni tartományok támogatása.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Kiadás állapota 

2017. május 25.: Letöltésre kiadva 

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 

Az összekötők kimenő kapcsolati korlátainak jobb szabályozása. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Kiadás állapota

2017. április 15.: Letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

- Egyszerűbb előkészítés és felügyelet kevesebb szükséges porttal. Az alkalmazásproxy mostantól csak két szabványos kimenő portot nyit meg: 443 és 80. Az alkalmazásproxy továbbra is csak a kimenő kapcsolatokat használja, így még nincs szüksége a DMZ egyik összetevőjére sem. Részletekért tekintse meg a [konfigurációs dokumentációt](application-proxy-add-on-premises-application.md).  
- Ha a külső proxy vagy tűzfal támogatja, a hálózatot az IP-címtartomány helyett DNS-sel nyithatja meg. Az alkalmazásproxy-szolgáltatások csak a *. msappproxy.net és a *. servicebus.windows.net kapcsolatait igénylik.


## <a name="earlier-versions"></a>Korábbi verziók

Ha a 1.5.36.0-nál korábbi verziójú alkalmazásproxy-összekötőt használ, frissítsen a legújabb verzióra, és győződjön meg arról, hogy a legújabb teljes körűen támogatott funkciókkal rendelkezik.

## <a name="next-steps"></a>További lépések
- További információ [a helyszíni alkalmazások távoli eléréséről az Azure ad Application Proxyon keresztül](application-proxy.md).
- Az alkalmazásproxy használatának megkezdéséhez tekintse [meg az oktatóanyag: Helyszíni alkalmazás hozzáadása a távoli eléréshez alkalmazásproxy](application-proxy-add-on-premises-application.md)használatával.
