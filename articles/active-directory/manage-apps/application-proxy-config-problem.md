---
title: A probléma az alkalmazásproxy-alkalmazás létrehozása |} A Microsoft Docs
description: Az alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portálon hibáinak elhárítása
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825816"
---
# <a name="problem-creating-an-application-proxy-application"></a>A probléma az alkalmazásproxy-alkalmazás létrehozása 

Az alábbiakban néhány gyakori problémák személyek face egy új alkalmazásproxy-alkalmazás létrehozásakor.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

A felügyeleti portálon keresztül egy Application Proxy-alkalmazások létrehozásával kapcsolatos további tudnivalókért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-add-on-premises-application.md).

Ha a dokumentum lépéseit követi, és az alkalmazás létrehozása hibaüzenet, tekintse meg az információkat a hiba részleteit, és hogyan javíthatja az alkalmazás javaslatokat. A legtöbb hiba üzenetekben a javasolt javítás. 

## <a name="specific-things-to-check"></a>Adott tudnivaló a ellenőrzése

Gyakori hibák elkerülése érdekében győződjön meg:

-   Az Application Proxy-alkalmazások létrehozásához szükséges engedéllyel rendelkező rendszergazdáknak

-   A belső URL-cím egyediségét.

-   A külső URL-cím egyediségét.

-   Az URL-címeket http vagy https előtaggal kezdődhet és végződhet egy "/"

-   Az URL-cím tartománynév, és ne az IP-címet kell lennie.

A következő hibaüzenet jelenik meg a jobb felső sarokban az alkalmazás létrehozásakor. Választhatja az értesítési ikonra a hibaüzenetek megtekintéséhez.

   ![Értesítés kérése](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>További lépések
[Alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-add-on-premises-application.md)
