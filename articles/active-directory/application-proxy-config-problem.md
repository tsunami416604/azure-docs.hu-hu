---
title: "A probléma az alkalmazásproxy-alkalmazás létrehozása |} Microsoft Docs"
description: "Alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portál kapcsolatos problémák elhárítása"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fe56f56162ba7186f1b660a5b44fcef38f1dee9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="problem-creating-an-application-proxy-application"></a>A probléma az alkalmazásproxy-alkalmazás létrehozása 

Az alábbiakban néhány gyakori problémákat személyek arcfelismerési egy új application proxy alkalmazás létrehozásakor.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

A felügyeleti portálon keresztül alkalmazásproxy alkalmazás létrehozásával kapcsolatos további tudnivalókért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

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

## <a name="next-steps"></a>Következő lépések
[Alkalmazásproxy engedélyezése az Azure-portálon](active-directory-application-proxy-enable.md)
