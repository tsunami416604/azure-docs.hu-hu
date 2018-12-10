---
title: A probléma az alkalmazásproxy-alkalmazás létrehozása |} A Microsoft Docs
description: Az alkalmazásproxy-alkalmazások létrehozása az Azure AD felügyeleti portálon hibáinak elhárítása
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
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: e75b04b3220347bdaa90dbc2f21d92475df3aa47
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136254"
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
