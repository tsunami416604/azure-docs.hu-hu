---
title: Az Azure AD katalógusából származó alkalmazásba való véve óra vagy több |} A Microsoft Docs
description: Miért való az alkalmazás üzembe helyezést, hogyan lehet, hogy kell hosszabb időt vesz igénybe, a várt
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
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 5be6933c4cd9efa4b8decc4cba6298f18610266a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364697"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD katalógusából származó alkalmazásba való véve óra vagy több

Először az alkalmazás az Automatikus kiépítés engedélyezése, ha a kezdeti szinkronizálás is eltarthat, 20 percet vagy akár néhány órát, a kiépítés hatókörébe felhasználók száma és az Azure AD-címtár méretétől függően. 

A kezdeti szinkronizálást követően ezt követő szinkronizálások esetén gyorsabb, ahogy a kiépítési szolgáltatás tárolja, amely mindkét rendszer állapotát képviselik a ezt követő szinkronizálások teljesítményének növelése a kezdeti szinkronizálást követően a vízjelek.

## <a name="how-to-improve-provisioning-performance"></a>Üzembe helyezési teljesítményének növelése

Ha a kezdeti szinkronizálás egynél több óráig tart, van egy dolog, amit a teljesítmény javítása:

-   **Felhasználói Hatókörszűrő.** Hatókörszűrő lehetővé teszik pontosíthatja az adatokat, amelyek alapján szűri ki a felhasználók meghatározott attribútumértékek alapján az Azure ad-ből kinyeri a kiépítési szolgáltatás. A szűrők felmerülő további információkért lásd: [attribútum-alapú alkalmazások üzembe helyezése és hatókörének beállítása szűrőket](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](active-directory-saas-app-provisioning.md)

