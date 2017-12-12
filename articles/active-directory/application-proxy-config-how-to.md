---
title: "Az alkalmazásproxy alkalmazás konfigurálása |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre egy konfigurálása az alkalmazásproxy alkalmazást néhány egyszerű lépésben"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fafd7c03141f67f4fc9ffbedb7a27632754b8d59
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-configure-an-application-proxy-application"></a>Az alkalmazásproxy alkalmazás konfigurálása

Ez a cikk segít megérteni, hogyan teszi közzé a felhőbe a helyszíni alkalmazások belül az Azure AD alkalmazásproxy alkalmazást konfigurálhatja úgy nyújt segítséget.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

További tudnivalók a kezdeti konfigurációt és a felügyeleti portálon keresztül alkalmazásproxy alkalmazás létrehozását, hajtsa végre a [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

Az összekötők konfigurálása, lásd: [alkalmazásproxy engedélyezése az Azure-portálon](active-directory-application-proxy-enable.md).

Tanúsítványok feltöltése és egyéni tartomány használatával kapcsolatos tudnivalókat lásd: [egyéni tartományok az Azure AD alkalmazásproxy használata](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains).

## <a name="create-the-applicationsetting-the-urls"></a>Az Alkalmazásbeállítás URL-címek létrehozása

Ha a következő lépéseket a [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) és a dokumentáció első hiba történt az alkalmazás létrehozásakor tekintse meg a hiba részletes adatait információk és javaslatok megoldásával a az alkalmazás. A legtöbb hiba üzenetekben javasolt javítás. Gyakori hibák elkerülése érdekében győződjön meg arról:

-   Alkalmazásproxy alkalmazás létrehozása engedéllyel rendelkező rendszergazdáknak

-   A belső URL-címe egyedi:

-   A külső URL-címe egyedi:

-   Az URL-címének http vagy https kezdődnie, és végén a "/"

-   Az URL-cím a tartomány nevét, IP-cím nem kell lennie.

A jobb felső sarokban a hibaüzenet a következő megjelenjen-e az alkalmazás létrehozásakor. Igény szerint kiválaszthatja az értesítés ikonra a hibaüzenetekben talál.

   ![Értesítési üzenet](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Az összekötők/összekötő csoportok beállítása

Ha a rendelkezésre álló összekötők és összekötő csoportok figyelmeztetés miatt az alkalmazás konfigurálása, lásd: utasításokat a letöltési összekötők részletekért alkalmazásproxy engedélyezése. Ha azt szeretné, további információt az összekötők, tekintse meg a [összekötők dokumentációja](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

Ha az összekötők nem működnek, ez azt jelenti, hogy azok nem érhetők el a szolgáltatást. Ez gyakran azért van, mivel nincsenek nyissa meg a szükséges portok. Szükséges portok listájának megtekintéséhez lásd: a szükséges Előfeltételek című részben engedélyezése alkalmazásproxy.

## <a name="upload-certificates-for-custom-domains"></a>Az egyéni tartományok tanúsítványok feltöltése

Egyéni tartományok engedélyezi, hogy a külső URL-címek tartományát adja meg. Egyéni tartományok használatához szüksége töltse fel az ehhez a tartományhoz. Egyéni tartományok és tanúsítványok használatával kapcsolatos információkért lásd: [egyéni tartományok az Azure AD alkalmazásproxy használata](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains). 

Ha a tanúsítvány feltöltése problémák adódtak, keresse meg a hibaüzeneteket, a portálon található további információ a tanúsítvánnyal kapcsolatos problémát. Közös tanúsítvánnyal kapcsolatos problémák a következők:

-   A lejárt tanúsítvány

-   Önaláírt tanúsítvány

-   Tanúsítvány hiányzik a titkos kulcs

A hibaüzenetet jeleníti meg a jobb felső sarokban található megpróbálja feltölteni a tanúsítványt. Igény szerint kiválaszthatja az értesítés ikonra a hibaüzenetekben talál.

   ![Értesítési üzenet](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Következő lépések
[Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](application-proxy-publish-azure-portal.md)
