---
title: Probléma alkalmazásproxy-alkalmazás létrehozásakor | Microsoft dokumentumok
description: Alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portálon való létrehozásával kapcsolatos problémák elhárítása
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825816"
---
# <a name="problem-creating-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazás létrehozása probléma 

Az alábbiakban felsorolunk néhány gyakori problémát, amellyel az emberek szembesülnek egy új alkalmazás proxyalkalmazás létrehozásakor.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

Ha többet szeretne tudni arról, hogy miként hozhat létre alkalmazásproxy-alkalmazást a felügyeleti portálon keresztül, olvassa el az Alkalmazások közzététele az [Azure AD alkalmazásproxy használatával című témakört.](application-proxy-add-on-premises-application.md)

Ha a dokumentum lépéseit követi, és hibaüzenetet kap az alkalmazás létrehozásáról, tekintse meg a hiba részleteit az alkalmazás javításához szükséges információkért és javaslatokért. A legtöbb hibaüzenet tartalmaz egy javasolt javítást. 

## <a name="specific-things-to-check"></a>Konkrét ellenőrizési tudnivalók

A gyakori hibák elkerülése érdekében ellenőrizze a következőket:

-   Alkalmazásproxy-alkalmazás létrehozására jogosult rendszergazda

-   A belső URL egyedi

-   A külső URL egyedi

-   Az URL-címek http-vel vagy https-lel kezdődnek, és "/"

-   Az URL-címnek tartománynévnek és nem IP-címnek kell lennie.

A hibaüzenetnek a jobb felső sarokban kell megjelennie az alkalmazás létrehozásakor. Az értesítésikont is kiválaszthatja a hibaüzenetek megtekintéséhez.

   ![Értesítési üzenet](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>További lépések
[Alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-add-on-premises-application.md)
