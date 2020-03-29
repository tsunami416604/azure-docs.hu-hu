---
title: Alkalmazásproxy-alkalmazás konfigurálása | Microsoft dokumentumok
description: Az APplication Proxy alkalmazások néhány egyszerű lépésben történő létrehozásáról és konfigurálásáról
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807851"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazás konfigurálása

Ez a cikk segít megérteni, hogyan konfigurálhat egy alkalmazásproxy-alkalmazást az Azure AD-n belül a helyszíni alkalmazások felhőben való elérhetővé tételéhez.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

Ha többet szeretne megtudni a kezdeti konfigurációkról és egy alkalmazásproxy-alkalmazás létrehozásáról a felügyeleti portálon keresztül, kövesse az Alkalmazások közzététele az [Azure AD alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)című területet.

Az összekötők konfigurálásával kapcsolatos további részletekért olvassa [el az Alkalmazásproxy engedélyezése az Azure Portalon című témakört.](application-proxy-add-on-premises-application.md)

A tanúsítványok feltöltésével és az egyéni tartományok használatával kapcsolatos további tudnivalókért olvassa el [az Egyéni tartományok használata az Azure AD alkalmazásproxyban című témakört.](application-proxy-configure-custom-domain.md)

## <a name="create-the-applicationsetting-the-urls"></a>Az alkalmazás létrehozása/AZ URL-címek beállítása

Ha az [Azure AD alkalmazásproxy dokumentációval az alkalmazások közzététele](application-proxy-add-on-premises-application.md) című témakör lépéseit követi, és hibaüzenetet kap az alkalmazás létrehozásáról, tekintse meg a hiba részleteit és javaslatokat az alkalmazás javításához. A legtöbb hibaüzenet tartalmaz egy javasolt javítást. A gyakori hibák elkerülése érdekében ellenőrizze a következőket:

- Alkalmazásproxy-alkalmazás létrehozására jogosult rendszergazda
- A belső URL egyedi
- A külső URL egyedi
- Az URL-címek http-vel vagy https-lel kezdődnek, és "/"
- Az URL-címnek tartománynévnek, nem IP-címnek kell lennie.

A hibaüzenetnek a jobb felső sarokban kell megjelennie az alkalmazás létrehozásakor. Az értesítésikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

![Megmutatja, hogy hol található az értesítési parancs az Azure Portalon](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Összekötők/összekötőcsoportok konfigurálása

Ha az összekötők és az összekötőcsoportok ra vonatkozó figyelmeztetés miatt nehézségekbe ütközik az alkalmazás konfigurálása, az összekötők letöltésével kapcsolatos részleteket az Alkalmazásproxy engedélyezésére vonatkozó utasításokban találja. Ha többet szeretne megtudni az összekötőkről, olvassa el az [összekötők dokumentációját.](application-proxy-connectors.md)

Ha az összekötők inaktívak, ez azt jelenti, hogy nem tudják elérni a szolgáltatást. Ennek gyakran az az oka, hogy az összes szükséges port nincs megnyitva. A szükséges portok listáját az alkalmazásproxy-engedélyezés dokumentációjának előfeltételei szakaszában találja.

## <a name="upload-certificates-for-custom-domains"></a>Tanúsítványok feltöltése egyéni tartományokhoz

Az egyéni tartományok lehetővé teszik a külső URL-címek tartományának megadását. Az egyéni tartományok használatához fel kell töltenie az adott tartomány tanúsítványát. Az egyéni tartományok és tanúsítványok használatáról az [Egyéni tartományok használata az Azure AD alkalmazásproxyban című témakörben](application-proxy-configure-custom-domain.md)talál.

Ha problémákat tapasztal a tanúsítvány feltöltésével kapcsolatban, keresse meg a hibaüzeneteket a portálon, ahol további információt talál a tanúsítvánnyal kapcsolatos problémáról. A tanúsítványokkal kapcsolatos gyakori problémák a következők:

- Lejárt tanúsítvány
- A tanúsítvány önaláírt
- A tanúsítványból hiányzik a személyes kulcs

A hibaüzenet a jobb felső sarokban jelenik meg, amikor megpróbálja feltölteni a tanúsítványt. Az értesítésikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

## <a name="next-steps"></a>További lépések

[Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md)
