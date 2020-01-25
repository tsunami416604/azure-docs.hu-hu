---
title: A felhasználók üzembe helyezésének konfigurálása egy Azure AD Gallery-alkalmazásban
description: Hogyan állíthatja be gyorsan az Azure AD Application Galleryben már felsorolt alkalmazások részletes felhasználói fiókjának üzembe helyezését és megszüntetését
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 87818066a656aa4bcd6f45f1835ca1512a674a9e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712305"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>A felhasználók üzembe helyezésének konfigurálása Azure AD Gallery-alkalmazásokban

A *felhasználói fiókok üzembe* helyezése a felhasználói fiókok rekordjainak létrehozása, frissítése és/vagy letiltása az alkalmazás helyi felhasználói profiljának tárolójába. A legtöbb felhőalapú és SaaS-alkalmazás a felhasználók szerepkörét és engedélyeit tárolja a felhasználó helyi felhasználói profiljának tárolójában, és az egyszeri bejelentkezéshez és a munkához való hozzáféréshez *szükség* van egy ilyen felhasználói rekord meglétét a felhasználó helyi tárolójába.

A Azure Portal a vállalati alkalmazás bal oldali navigációs paneljének **kiépítés** lapja megjeleníti az adott alkalmazás által támogatott kiépítési módokat. Ez az alábbi két érték egyike lehet:

## <a name="configuring-an-application-for-manual-provisioning"></a>Alkalmazás konfigurálása kézi üzembe helyezéshez

A *manuális* kiépítés azt jelenti, hogy a felhasználói fiókokat manuálisan kell létrehozni az alkalmazás által biztosított metódusok használatával. Ez azt jelentheti, hogy be kell jelentkeznie egy felügyeleti portálra az alkalmazáshoz, és hozzá kell hozzáadni a felhasználókat egy webalapú felhasználói felület használatával. Vagy a felhasználói fiókkal részletesen feltölthet egy táblázatot az adott alkalmazás által biztosított mechanizmus használatával. Tekintse át az alkalmazás által biztosított dokumentációt, vagy lépjen kapcsolatba az alkalmazás fejlesztővel, és határozza meg, hogy milyen mechanizmusok érhetők el.

Ha az adott alkalmazáshoz csak a *manuális* mód látható, az azt jelenti, hogy még nincs automatikus Azure ad-létesítési összekötő az alkalmazáshoz. Ez azt jelenti, hogy az alkalmazás nem támogatja a Microsoft felhasználói Management API előfeltételeit, amelyekkel automatizált kiépítési összekötők hozhatók létre.

Ha szeretne támogatást kérni egy adott alkalmazás automatikus kiépítéséhez, kitöltheti a kérést a [Azure Active Directory alkalmazás-kérelmek](https://aka.ms/aadapprequest)használatával.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Alkalmazás konfigurálása automatikus kiépítés esetén

Az *automatikus* beállítás azt jelenti, hogy az alkalmazáshoz egy Azure ad-létesítési összekötő lett kifejlesztve. Az Azure AD kiépítési szolgáltatásával és működésével kapcsolatos további információkért lásd: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](user-provisioning.md).

Az egyes felhasználók és csoportok alkalmazásokhoz való kiépítésével kapcsolatos további információkért lásd: a [felhasználói fiókok üzembe helyezésének kezelése a vállalati](configure-automatic-user-provisioning-portal.md)alkalmazásokhoz.

Az automatikus kiépítés engedélyezéséhez és konfigurálásához szükséges tényleges lépések az alkalmazástól függően változnak.

> [!NOTE]
> Először meg kell keresnie a telepítési oktatóanyagot, amely az alkalmazás üzembe helyezésének beállítására vonatkozik, és ezeket a lépéseket követve konfigurálhatja az alkalmazást és az Azure AD-t is a létesítési kapcsolatok létrehozásához.

Az alkalmazás-oktatóanyagok az [SaaS-alkalmazások Azure Active Directory használatával történő integrálását ismertető oktatóanyagban](../saas-apps/tutorial-list.md)találhatók.

A kiépítés beállításakor megfontolandó szempont, hogy áttekintse és konfigurálja azokat az attribútum-hozzárendeléseket és munkafolyamatokat, amelyek meghatározzák, hogy mely felhasználói (vagy csoport-) tulajdonságokat kell az Azure AD-ből az alkalmazásba áthelyezni. Ide tartozik a "megfelelő tulajdonság" beállítása, amely a felhasználók/csoportok egyedi azonosítására és a két rendszer közötti egyeztetésére szolgál. Az attribútumok hozzárendelésével kapcsolatos további információkért tekintse meg a *következő lépések* hivatkozását.

## <a name="next-steps"></a>Következő lépések
[Az SaaS-alkalmazások felhasználói kiépítési attribútum-leképezésének testreszabása Azure Active Directory](customize-application-attributes.md)

