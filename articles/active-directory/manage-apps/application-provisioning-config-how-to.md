---
title: Hogyan kell konfigurálnom a felhasználókiépítést az Azure AD katalógusából származó alkalmazásba való |} A Microsoft Docs
description: Gazdag felhasználói fiók kiépítésének és megszüntetésének biztosítása már szerepel az Azure AD Alkalmazásgyűjteményben alkalmazások gyors konfigurálásához
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 028c93e1d250606ccf1516e9393bf61140961831
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174792"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Hogyan kell konfigurálnom a felhasználókiépítést egy Azure AD katalógusából származó alkalmazásba

*Felhasználói fiók kiépítése* a törvény létrehozása, frissítése, illetve letiltása a felhasználóifiók-rekordok az alkalmazás helyi felhasználói profiljainak tárolójában van. A legtöbb felhő- és SaaS-alkalmazások tárolja a felhasználói szerepköröket és engedélyeket a saját helyi felhasználói profiljainak tárolójában, és ilyen egy felhasználórekordban tárolja a helyi tároló jelenléte *szükséges* egyszeri bejelentkezést és a hozzáférés működéséhez.

Az Azure Portalon a **kiépítési** lapra a bal oldali navigációs panelen, a vállalati alkalmazásokat jeleníti meg, milyen üzembe helyezési módok használata támogatott az adott alkalmazáshoz. Ez két értékek egyike lehet:

## <a name="configuring-an-application-for-manual-provisioning"></a>Alkalmazás manuális üzembe helyezés konfigurálása

*Manuális* kiépítés azt jelenti, hogy felhasználói fiókokat kell létrehozni, amelyet az alkalmazás által biztosított metódusokkal segítségével manuálisan. Ez azt jelentheti, hogy jelentkezik be egy felügyeleti portálon, az adott alkalmazáshoz és a egy webalapú felhasználói felület használatával a felhasználók hozzáadásával. Vagy, sikerült feltölteni egy táblázatot a felhasználói fiók részletes, egy adott alkalmazás által biztosított mechanizmus segítségével. Tekintse át a dokumentációt, az alkalmazás által biztosított, vagy forduljon az alkalmazás fejlesztőjének meghatározásához wat mechanizmus áll rendelkezésre.

Ha a manuális az egyetlen mód egy adott alkalmazás látható, az azt jelenti, hogy nem automatikus az Azure AD connector kiépítése még létrehozva az alkalmazás. Vagy az azt jelenti, hogy az alkalmazás nem támogatja az előfeltételként felhasználói felügyeleti API-t, amelyen egy automatizált üzembe helyezési összekötőt hozhat létre.

Ha szeretné egy adott alkalmazás automatikus üzembe helyezés támogatást kérhet, kitölti a kérelmet az a [Azure Active Directory alkalmazásokra irányuló kérések](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Alkalmazás automatikus üzembe helyezés konfigurálása

*Automatikus* azt jelenti, hogy egy összekötő kiépítése az Azure AD identitáskezelési ehhez az alkalmazáshoz. Az Azure AD regisztrációs szolgáltatást és annak működéséről további információért lásd: [automatizálhatja a Felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Adott felhasználók és csoportok alkalmazásokhoz való kiépítése további információkért lásd: [kezelése a felhasználói fiók kiépítése vállalati alkalmazásaihoz](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Engedélyezheti és konfigurálhatja az Automatikus kiépítés tényleges lépéseit az alkalmazástól függően eltérőek lehetnek.

>[!NOTE]
>A telepítő az oktatóanyag az alkalmazás üzembe helyezésével és azok lépések konfigurálhatja az alkalmazás és a kiépítési kapcsolat létrehozása az Azure AD alábbi beállítására vonatkozó felderítésével webalkalmazásokba. 
>
>

Alkalmazás oktatóanyagokat talál [oktatóanyagok listája SaaS-alkalmazások integrálása az Azure Active Directoryval való](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Egy lényeges tudnivaló, hogy fontolja meg a kiépítési beállítása során kell, hogy tekintse át, és az attribútumleképezések és a munkafolyamatok, amelyek meghatározzák, mely felhasználó (vagy csoport) tulajdonságai a folyamat az Azure ad-ből az alkalmazás konfigurálása. Ez magában foglalja a "egyező tulajdonság" beállítás, amely egyedileg azonosíthatja és felhasználók/csoportok a két rendszer közötti megfelelő használható. További információ a fontos folyamattal.

## <a name="next-steps"></a>További lépések
[Felhasználókiépítés az attribútum-leképezéshez az SaaS-alkalmazásokhoz az Azure Active Directoryban testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

