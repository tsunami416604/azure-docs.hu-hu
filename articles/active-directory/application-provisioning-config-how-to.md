---
title: A felhasználók átadása egy Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs
description: Gazdag felhasználói fiók kiépítésének és megszüntetésének biztosítása már szerepel az Azure AD Application Gallery alkalmazások gyors konfigurálásához
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
ms.openlocfilehash: da7050edae282c90c4f92c588ce8fe501ccba50d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464425"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>A felhasználók átadása egy Azure AD-katalógusában alkalmazás konfigurálása

*Felhasználói fiók kiépítése* történő létrehozása, frissítése és/vagy letiltása a felhasználói fiókot rögzíti az alkalmazás helyi felhasználói profil tárolójában van. Legtöbb felhő- és SaaS-alkalmazásokhoz tárolja a felhasználói szerepköröket és engedélyeket a saját helyi felhasználói profil tárolójában, és ilyen felhasználói rekordban a helyi tárolóban levő jelenléte *szükséges* egyszeri bejelentkezést és a hozzáférés működéséhez.

Az Azure portálon a **kiépítési** egy vállalati alkalmazást jeleníti meg, milyen üzembe helyezési mód támogatja az alkalmazás a bal oldali navigációs panelen lapján. Ez a két értékek egyike lehet:

## <a name="configuring-an-application-for-manual-provisioning"></a>Alkalmazások konfigurálása a manuális üzembe helyezéséhez

*Manuális* kiépítés azt jelenti, hogy felhasználói fiókokat kell létrehozni az alkalmazás által biztosított módszerek segítségével manuálisan. Ez azt jelentheti, hogy az alkalmazáshoz egy felügyeleti portálra való bejelentkezéskor, és a webes felhasználói felülete segítségével a felhasználók hozzáadásával. Vagy az sikerült feltölteni egy táblázatot a felhasználói fiók részletes, egy adott alkalmazás által biztosított mechanizmus használatával. Az alkalmazás által biztosított dokumentációt, vagy lépjen kapcsolatba az alkalmazás fejlesztőjének annak meghatározásához, Nyugat-afrikai mechanizmusok érhetők el.

Ha manuális az egyetlen mód egy adott alkalmazás látható, az azt jelenti, hogy nincs automatikus az Azure AD összekötő kiépítése még létrehozva az alkalmazás. Vagy az azt jelenti, hogy az alkalmazás nem támogatja a működéséhez szükséges felhasználói API szerint az automatikus létesítési összekötő létrehozásához.

Ha azt szeretné, a megadott alkalmazások automatikus kiépítés támogatás kéréséhez, kitöltötte a kérelmek használatával az [Azure Active Directory Alkalmazáskérelmeinek](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Alkalmazások konfigurálása az Automatikus kiépítés

*Automatikus* azt jelenti, hogy egy összekötő kiépítése az Azure AD identitáskezelési ehhez az alkalmazáshoz. Az Azure ad-val kiépítése szolgáltatáshoz, és annak működéséről további információkért lásd: [Felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokhoz az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

A meghatározott felhasználókhoz és csoportokhoz alkalmazáshoz való kiépítése további információkért lásd: [kezelése a felhasználói fiók kiépítése vállalati alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Engedélyezze és konfigurálja az Automatikus kiépítés tényleges lépéseit az alkalmazástól függően eltérőek.

>[!NOTE]
>Először meg kell válaszolnia a telepítő az oktatóanyag az alkalmazás üzembe helyezési, és a következő azokat az alkalmazás és az üzembe helyezési kapcsolat létrehozása az Azure AD konfigurálása lépésekkel beállítására vonatkozó keresése. 
>
>

Alkalmazás oktatóprogramok találhatók [integrálhatja SaaS-alkalmazásokhoz az Azure Active Directoryval kapcsolatos lista a bemutatók](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Kiépítés beállítása során figyelembe kell venni egy fontos dolog lehet áttekintése és konfigurálása a attribútum-leképezésekhez és a munkafolyamatok, amelyek meghatározzák, milyen felhasználói (vagy a csoport) tulajdonságok folyamata az Azure AD az alkalmazásnak. Ez magában foglalja a "egyező property" beállítás használható egyedileg azonosíthatja és felel meg a felhasználókat/csoportokat a két rendszer között. További információ a fontos folyamatban.

## <a name="next-steps"></a>További lépések
[Attribútum-leképezésekhez kiépítés az SaaS-alkalmazásokhoz az Azure Active Directory felhasználói testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

