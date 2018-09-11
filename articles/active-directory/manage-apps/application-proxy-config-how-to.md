---
title: Az Application Proxy-alkalmazások konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy konfigurálása az APplication Proxy-alkalmazások néhány egyszerű lépésben
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
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: cf3e367dad528017a98e103962c57cb758da55cb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356620"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Az Application Proxy-alkalmazások konfigurálása

Ennek a cikknek a segítségével megismerheti a konfigurálása az Application Proxy-alkalmazások Azure AD-ben elérhetővé a helyszíni alkalmazásait a felhőbe.

## <a name="recommended-documents"></a>Ajánlott dokumentumok 

A kezdeti konfigurációt és a egy Application Proxy-alkalmazások, a felügyeleti portálon keresztül létrehozása kapcsolatos további információkért kövesse a [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-publish-azure-portal.md).

További összekötő konfigurálásával kapcsolatos további információkért lásd: [alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-enable.md).

Tanúsítványok feltöltése és egyéni tartományok használatával kapcsolatos tudnivalókat lásd: [egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Hozzon létre az Alkalmazásbeállítás URL-címek

Ha lépéseit követi a [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-publish-azure-portal.md) dokumentáció alatt, és az alkalmazás létrehozása hibaüzenet az információkat a hiba részleteit, és hogyan háríthatja el a javaslatokat tekintse meg a az alkalmazás. A legtöbb hiba üzenetekben a javasolt javítás. Gyakori hibák elkerülése érdekében győződjön meg:

-   Az Application Proxy-alkalmazások létrehozásához szükséges engedéllyel rendelkező rendszergazdáknak

-   A belső URL-cím egyediségét.

-   A külső URL-cím egyediségét.

-   Az URL-címeket http vagy https előtaggal kezdődhet és végződhet egy "/"

-   Az URL-cím tartománynév, ne az IP-címet kell lennie.

A következő hibaüzenet jelenik meg a jobb felső sarokban az alkalmazás létrehozásakor. Választhatja az értesítési ikonra a hibaüzenetek megtekintéséhez.

   ![Értesítés kérése](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Összekötők/összekötő csoportok konfigurálása

Ha problémái vannak az alkalmazás konfigurálása összekötőcsoportok és összekötők kapcsolatos figyelmeztetés miatt, a tudnivalókat alkalmazásproxy engedélyezése az összekötők letöltési részleteiről. Ha szeretne további információ az összekötőkről, tekintse meg a [összekötők dokumentációja](application-proxy-connectors.md).

Ha az összekötő inaktív, ez azt jelenti, hogy azok nem érhető el a szolgáltatást. Ez gyakran azért van, mert a szükséges portok nem nyílt. Szükséges portok listájának megtekintéséhez az alkalmazásproxy engedélyezése dokumentáció Előfeltételek című szakaszában talál.

## <a name="upload-certificates-for-custom-domains"></a>Az egyéni tartományokhoz tanúsítványok feltöltése

Egyéni tartományok lehetővé teszik a külső URL-címek tartományát adja meg. Egyéni tartományt használ, meg kell töltse fel a tanúsítványt a tartományhoz. Egyéni tartományok és tanúsítványok használatával kapcsolatos információkért lásd: [egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md). 

Ha problémái vannak a tanúsítvány feltöltését, keresse meg a hibaüzeneteket, a portálon további információkat a tanúsítvánnyal kapcsolatos probléma. Közös tanúsítvánnyal kapcsolatos problémák a következők:

-   A lejárt tanúsítvány

-   Tanúsítványa egy önaláírt

-   Tanúsítvány hiányzik a titkos kulcs

A hibaüzenetet jeleníti meg a jobb felső sarokban lévő megpróbálja feltölteni a tanúsítványt. Választhatja az értesítési ikonra a hibaüzenetek megtekintéséhez.

   ![Értesítés kérése](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>További lépések
[Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md)
