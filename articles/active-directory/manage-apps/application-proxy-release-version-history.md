---
title: 'Azure AD alkalmazásproxy: Verziókiadási előzmények | Microsoft dokumentumok'
description: Ez a cikk felsorolja az Azure AD alkalmazásproxy összes kiadását, és ismerteti az új funkciókat és a javított problémákat
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
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983891"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD alkalmazásproxy: Verziókiadási előzmények
Ez a cikk az Azure Active Directory (Azure AD) alkalmazásproxy kiadott verzióit és szolgáltatásait sorolja fel. Az Azure AD csapata rendszeresen frissíti az alkalmazásproxyt új funkciókkal és funkciókkal. Az alkalmazásproxy-összekötők automatikusan frissülnek, amikor új verzió jelenik meg. 

Javasoljuk, hogy győződjön meg arról, hogy az összekötők automatikus frissítéseinek engedélyezése biztosítva, hogy a legújabb funkciókkal és hibajavításokkal rendelkezzen. A Microsoft közvetlen támogatást nyújt a legutolsó összekötő verzióhoz és egy verzióhoz.

Itt van egy lista a kapcsolódó források:

Erőforrás |  Részletek
--------- | --------- |
Az alkalmazásproxy engedélyezése | Az alkalmazásproxy engedélyezésének, valamint az összekötő telepítésének és regisztrálásának előfeltételeit ez az [oktatóanyag](application-proxy-add-on-premises-application.md)ismerteti.
Az Azure AD alkalmazásproxy-összekötők megismerése | További információ az [összekötők kezeléséről](application-proxy-connectors.md) és arról, hogy az összekötők [hogyan frissíthetők.](application-proxy-connectors.md#automatic-updates)
Azure AD alkalmazásproxy-összekötő letöltése |  [Töltse le a legújabb csatlakozót.](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Kiadás állapota

Április 07, 2020: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
-   Az összekötők csak a TLS 1.2-t használják az összes kapcsolathoz. További részletekért lásd [az Összekötő előfeltételeit.](application-proxy-add-on-premises-application.md#before-you-begin)
- Továbbfejlesztett jelzés az Összekötő és az Azure-szolgáltatások között. Ez magában foglalja az összekötő és az Azure-szolgáltatások közötti WCF-kommunikáció megbízható munkameneteinek támogatását, valamint a WebSocket-kommunikáció DNS-gyorsítótárazási fejlesztéseit.
- Proxy konfigurálásának támogatása az összekötő és a háttéralkalmazás között. További információt a [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)című témakörben talál.

### <a name="fixed-issues"></a>Hibák kijavítva:
- Eltávolította a 8080-as portra való visszaesést az Összekötő és az Azure-szolgáltatások közötti kommunikációhoz.
- Hibakeresési nyomkövetések hozzáadva a WebSocket-kommunikációhoz. 
- A SameSite attribútum megőrzője megoldva, amikor háttéralkalmazás-cookie-kra van beállítva.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Kiadás állapota

2018. szeptember 20.: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

- Hozzáadott WebSocket támogatja a QlikSense alkalmazás. Ha többet szeretne megtudni arról, hogyan integrálható a QlikSense az alkalmazásproxyval, olvassa el ezt [a forgatókönyvet.](application-proxy-qlik.md) 
- Továbbfejlesztettük a telepítővarázslót, hogy könnyebblegyen a kimenő proxy konfigurálása. 
- Állítsa be a TLS 1.2 protokollt az összekötők alapértelmezett protokolljaként. 
- Új végfelhasználói licencszerződés (EULA) került hozzáadásra.  

### <a name="fixed-issues"></a>Hibák kijavítva:

- Javítva egy hiba, amely memóriavesztést okozott az összekötőben.
- Frissítette az Azure Service Bus verzióját, amely tartalmazza az összekötő időmegfordítási problémáinak hibajavítását.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Kiadás állapota

2018. január 19.: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva:

- Hozzáadott támogatás egyéni tartományok, amelyek a szükséges domain fordítás a cookie-ban.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Kiadás állapota 

2017. május 25.: Letölthető 

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések 

Továbbfejlesztett vezérlés az összekötők kimenő csatlakozási korlátai felett. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Kiadás állapota

2017. április 15.: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

- Egyszerűsített bevezetés és felügyelet kevesebb szükséges porttal. Az alkalmazásproxy mostantól csak két szabványos kimenő portot kell megnyitni: a 443-as és a 80-as portokat. Az alkalmazásproxy továbbra is csak kimenő kapcsolatokat használ, így továbbra sem kell a DMZ összetevőinek. További részletek a [konfigurációs dokumentációban](application-proxy-add-on-premises-application.md)találhatók.  
- Ha a külső proxy vagy tűzfal támogatja, most már megnyithatja a hálózatot a DNS-sel az IP-tartomány helyett. Az alkalmazásproxy-szolgáltatások csak a *.msappproxy.net és a *.servicebus.windows.net csatlakozást igénylik.


## <a name="earlier-versions"></a>Korábbi verziók

Ha az 1.5.36.0-nál korábbi alkalmazásproxy-összekötőverziót használ, frissítsen a legújabb verzióra, és győződjön meg arról, hogy a legújabb, teljes mértékben támogatott funkciókkal rendelkezik.

## <a name="next-steps"></a>További lépések
- További információ a [helyszíni alkalmazások hoz való távoli hozzáférésről az Azure AD alkalmazásproxyn keresztül.](application-proxy.md)
- Az alkalmazásproxy használatának megkezdéséhez olvassa el [az Oktatóanyag: Helyszíni alkalmazás hozzáadása távoli elérést az alkalmazásproxyn keresztül című témakörben.](application-proxy-add-on-premises-application.md)
