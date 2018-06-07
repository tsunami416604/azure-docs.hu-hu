---
title: Az alkalmazásproxy alkalmazás konfigurálása |} Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy konfigurálása az alkalmazásproxy alkalmazást néhány egyszerű lépésben
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
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: b297aab75212070aa435c58bf9024bf90e8ffec3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590154"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Az alkalmazásproxy alkalmazás konfigurálása

Ez a cikk segít megérteni, hogyan teszi közzé a felhőbe a helyszíni alkalmazások belül az Azure AD alkalmazásproxy alkalmazást konfigurálhatja úgy nyújt segítséget.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

További tudnivalók a kezdeti konfigurációt és a felügyeleti portálon keresztül alkalmazásproxy alkalmazás létrehozását, hajtsa végre a [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](manage-apps/application-proxy-publish-azure-portal.md).

Az összekötők konfigurálása, lásd: [alkalmazásproxy engedélyezése az Azure-portálon](manage-apps/application-proxy-enable.md).

Tanúsítványok feltöltése és egyéni tartomány használatával kapcsolatos tudnivalókat lásd: [egyéni tartományok az Azure AD alkalmazásproxy használata](manage-apps/application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Az Alkalmazásbeállítás URL-címek létrehozása

Ha a következő lépéseket a [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](manage-apps/application-proxy-publish-azure-portal.md) és a dokumentáció első hiba történt az alkalmazás létrehozásakor tekintse meg a hiba részletes adatait információk és javaslatok megoldásával a az alkalmazás. A legtöbb hiba üzenetekben javasolt javítás. Gyakori hibák elkerülése érdekében győződjön meg arról:

-   Alkalmazásproxy alkalmazás létrehozása engedéllyel rendelkező rendszergazdáknak

-   A belső URL-címe egyedi:

-   A külső URL-címe egyedi:

-   Az URL-címének http vagy https kezdődnie, és végén a "/"

-   Az URL-cím a tartomány nevét, IP-cím nem kell lennie.

A jobb felső sarokban a hibaüzenet a következő megjelenjen-e az alkalmazás létrehozásakor. Igény szerint kiválaszthatja az értesítés ikonra a hibaüzenetekben talál.

   ![Értesítési üzenet](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Az összekötők/összekötő csoportok beállítása

Ha a rendelkezésre álló összekötők és összekötő csoportok figyelmeztetés miatt az alkalmazás konfigurálása, lásd: utasításokat a letöltési összekötők részletekért alkalmazásproxy engedélyezése. Ha azt szeretné, további információt az összekötők, tekintse meg a [összekötők dokumentációja](manage-apps/application-proxy-connectors.md).

Ha az összekötők nem működnek, ez azt jelenti, hogy azok nem érhetők el a szolgáltatást. Ez gyakran azért van, mivel nincsenek nyissa meg a szükséges portok. Szükséges portok listájának megtekintéséhez lásd: a szükséges Előfeltételek című részben engedélyezése alkalmazásproxy.

## <a name="upload-certificates-for-custom-domains"></a>Az egyéni tartományok tanúsítványok feltöltése

Egyéni tartományok engedélyezi, hogy a külső URL-címek tartományát adja meg. Egyéni tartományok használatához szüksége töltse fel az ehhez a tartományhoz. Egyéni tartományok és tanúsítványok használatával kapcsolatos információkért lásd: [egyéni tartományok az Azure AD alkalmazásproxy használata](manage-apps/application-proxy-configure-custom-domain.md). 

Ha a tanúsítvány feltöltése problémák adódtak, keresse meg a hibaüzeneteket, a portálon található további információ a tanúsítvánnyal kapcsolatos problémát. Közös tanúsítvánnyal kapcsolatos problémák a következők:

-   A lejárt tanúsítvány

-   Önaláírt tanúsítvány

-   Tanúsítvány hiányzik a titkos kulcs

A hibaüzenetet jeleníti meg a jobb felső sarokban található megpróbálja feltölteni a tanúsítványt. Igény szerint kiválaszthatja az értesítés ikonra a hibaüzenetekben talál.

   ![Értesítési üzenet](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](manage-apps/application-proxy-publish-azure-portal.md)
