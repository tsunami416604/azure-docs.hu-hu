---
title: A probléma az alkalmazásproxy-alkalmazás létrehozása |} Microsoft Docs
description: Alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portál kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 376925715e954904cfdadccb060d0ca242bbec4a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="problem-creating-an-application-proxy-application"></a>A probléma az alkalmazásproxy-alkalmazás létrehozása 

Az alábbiakban néhány gyakori problémákat személyek arcfelismerési egy új application proxy alkalmazás létrehozásakor.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

A felügyeleti portálon keresztül alkalmazásproxy alkalmazás létrehozásával kapcsolatos további tudnivalókért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](manage-apps/application-proxy-publish-azure-portal.md).

Ha a dokumentum lépéseit követi, és az alkalmazás létrehozásakor hiba lépett fel kap, tekintse meg információt a hiba részletes adatait, és javaslatokat arról, hogyan javítsa ki az alkalmazást. A legtöbb hiba üzenetekben javasolt javítás. 

## <a name="specific-things-to-check"></a>Adott ellenőrizze az alábbiakat

Gyakori hibák elkerülése érdekében győződjön meg arról:

-   Alkalmazásproxy alkalmazás létrehozása engedéllyel rendelkező rendszergazdáknak

-   A belső URL-címe egyedi:

-   A külső URL-címe egyedi:

-   Az URL-címének http vagy https kezdődnie, és végén a "/"

-   Az URL-cím a tartomány neve és IP-cím nem kell lennie.

A jobb felső sarokban a hibaüzenet a következő megjelenjen-e az alkalmazás létrehozásakor. Igény szerint kiválaszthatja az értesítés ikonra a hibaüzenetekben talál.

   ![Értesítési üzenet](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>További lépések
[Alkalmazásproxy engedélyezése az Azure-portálon](manage-apps/application-proxy-enable.md)
