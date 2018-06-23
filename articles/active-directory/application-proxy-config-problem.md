---
title: A probléma az alkalmazásproxy-alkalmazás létrehozása |} Microsoft Docs
description: Alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portál kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 97795593ecc83a0b15f018861f5cd9139d26443b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335108"
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
