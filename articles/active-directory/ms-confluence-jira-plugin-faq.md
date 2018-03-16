---
title: "A Microsoft Azure Active Directory egyszeri bejelentkezés beépülő modul – gyakori kérdések |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>A Microsoft Azure Active Directory egyszeri bejelentkezés beépülő modul – gyakori kérdések 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Újdonságok a Microsoft SSO bővítményt?

Ez a bővítmény lehetővé teszi az egyszeri bejelentkezés Atlassian meg (beleértve a JIRA mag, JIRA szoftver, JIRA ügyfélszolgálatához) JIRA és való összefolyás felett helyi szoftverek. Azure AD szolgáltatásba IdP bővítmény működik.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Bővítmény Atlassian termékek működik?

Mostantól a bővítmény JIRA és való összefolyás felett helyi változatának működik.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Ez a bővítmény felhő verziójával működik?

Nem. Csak JIRA és való összefolyás felett helyi verziói támogatottak.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. JIRA és való összefolyás felett mely verziói támogatottak?

Az alábbiakban a támogatott verziók listáját a következő:

* JIRA Core és a szoftver: 6.0 való 7.2.2. 
* JIRA Service Desk: 3.0 to 3.2 
* Való összefolyás felett: 5.0 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. A bővítmény ingyenes vagy fizetve van?

Ez egy szabad bővítményt, és Atlassian piactéren is telepíthető.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. Kell JIRA/való összefolyás felett újraindítása után történik meg a bővítmény telepítése

Újraindítás nem szükséges post bővítmény telepítése. A bővítmény használatával a telepítés után azonnal elindíthatja.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Hogyan kérhet támogatást a bővítményhez?

Kapcsolatba velünk a: <email> . A Microsoft <> órán belül válaszol. Az Azure portál csatornán keresztül a Microsoft támogatási jegy is is növelheti. Ön is meghívhatja nekünk a: <Number> közötti <> vagyok a <> létrehozását-kor.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Ez a bővítmény JIRA és való összefolyás felett Mac vagy Ubuntu telepítése a csatlakoztatás működik?

Ez a bővítmény csak a 64 bites Windows server-telepítéseket JIRA és való összefolyás felett tesztelése.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Nem használható a bővítmény egyéb IdPs, mint az Azure AD?

Nem. Bővítmény csak az Azure ad szolgáltatással működik.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. SAML verziójának a bővítmény működik?

Bővítmény SAML 2.0 működik.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. A bővítmény is kiosztás használata biztosítja?

Nem. Mostantól bővítményt biztosít, csak a SAML 2.0 SSO alapján. Felhasználó rendelkezik-e úgy kell létrehozni, az Egyszeri bejelentkezés előtt az alkalmazásban.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Fürt verziója JIRA és a bővítmény által támogatott való összefolyás felett van?

Nem. A bővítmény JIRA és való összefolyás felett helyi változatának működik.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. A beépülő modul JIRA és való összefolyás felett HTTP verziójával csatlakoztatás működik?

Nem. A bővítmény működik, a HTTPS csak telepítés engedélyezve van.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. Kell vásárolnia a licencet a bővítmény?

Egy ingyenes bővítmény.