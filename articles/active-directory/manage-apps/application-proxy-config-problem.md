---
title: Probléma az alkalmazásproxy-alkalmazás létrehozásakor | Microsoft Docs
description: Alkalmazásproxy-alkalmazások létrehozásával kapcsolatos problémák elhárítása az Azure AD felügyeleti portálon
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
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "65825816"
---
# <a name="problem-creating-an-application-proxy-application"></a>Probléma az alkalmazásproxy-alkalmazás létrehozásakor 

Az alábbiakban felsorolunk néhányat az új alkalmazásproxy-alkalmazás létrehozásakor előforduló gyakori problémákról.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

Az alkalmazásproxy-alkalmazások felügyeleti portálon keresztüli létrehozásával kapcsolatos további tudnivalókért tekintse meg az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)című témakört.

Ha a dokumentumban leírt lépéseket követve hibaüzenetet kap az alkalmazás létrehozásakor, tekintse meg a hiba részleteit, és javaslatokat az alkalmazás kijavításához. A legtöbb hibaüzenet tartalmaz egy javasolt javítást. 

## <a name="specific-things-to-check"></a>Konkrétan ellenőrizhető dolgok

A gyakori hibák elkerüléséhez ellenőrizze az alábbiakat:

-   Rendszergazdai jogosultsággal rendelkezik alkalmazásproxy-alkalmazás létrehozásához

-   A belső URL-cím egyedi

-   A külső URL-cím egyedi

-   Az URL-címek http vagy HTTPS protokollal kezdődnek, és a végén egy "/"

-   Az URL-címnek tartománynévnek és nem IP-címnek kell lennie

A hibaüzenetnek a jobb felső sarokban kell megjelennie az alkalmazás létrehozásakor. Az értesítés ikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

   ![Értesítési kérés](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>További lépések
[Alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-add-on-premises-application.md)
