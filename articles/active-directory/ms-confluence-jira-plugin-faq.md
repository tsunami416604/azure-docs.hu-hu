---
title: Gyakori kérdések az Azure Active Directory-alapú Egyszeri beépülő modul |} Microsoft Docs
description: Egyszeri bejelentkezés Azure Active Directory és Jira/való összefolyás felett beállításával kapcsolatos gyakori kérdésekre adott válaszok.
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
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699206"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Gyakori kérdések az Azure Active Directory-alapú Egyszeri beépülő modul

Tekintse meg alább gyakran ismételt kérdéseket, ha ez vonatkozó lekérdezés beépülő modult.

## <a name="what-does-the-plug-in-do"></a>A beépülő modul ne funkciója?

A beépülő modul lehetővé teszi az egyszeri bejelentkezés (SSO) (beleértve a Jira mag, Jira szoftver, Jira ügyfélszolgálatához) Atlassian Jira és való összefolyás felett helyszíni szoftver. Azure Active Directory (Azure AD), az identitásszolgáltató (IdP) beépülő modul együttműködik.

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Atlassian termékek használ a beépülő modul használata?

A beépülő modul működését Jira és való összefolyás felett helyszíni verzióival.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>A beépülő modul munkahelyi felhő verzióin működik?

Nem. A beépülő modul támogatja csak a helyszíni Jira és való összefolyás felett.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Mely verzióit Jira és való összefolyás felett a beépülő modul támogatási használ?

A beépülő modul alábbi verzióit támogatja:

* Jira Core és a szoftver: 6.0 való 7,8
* Jira ügyfélszolgálatához: 3.0 3.2
* Való összefolyás felett: 5.0 5.10

## <a name="is-the-plug-in-free-or-paid"></a>A beépülő modult szabad vagy fizetős?

Egy ingyenes bővítmény.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Kell Jira vagy való összefolyás felett történik meg a beépülő modul telepítése után indítsa újra?

Nincs szükség újraindításra. A beépülő modult használó azonnal elindíthatja.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Hogyan kérhet támogatást a beépülő modul?

Akkor is elérhetők a [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) bármely támogatásához szükséges a beépülő modult. A csapat változásokhoz 24-48 üzleti órában.

Az Azure portál csatornán keresztül a Microsoft támogatási jegy is is növelheti.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>A beépülő modul munkahelyi Jira és való összefolyás felett Mac vagy Ubuntu telepítésen volna?

A beépülő modul csak a 64 bites Windows Server-példányok Jira és való összefolyás felett tesztelése.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Az Azure AD eltérő IdPs a beépülő modul használata nem?

Nem. Csak az Azure ad szolgáltatással működik.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Az SAML milyen verziója nem a beépülő modul használata?

A SAML 2.0 működik.

## <a name="does-the-plug-in-do-user-provisioning"></a>A beépülő modul feladata a felhasználók átadása?

Nem. A beépülő modul csak SAML 2.0-alapú egyszeri Bejelentkezést biztosít. A felhasználó rendelkezik-e úgy kell létrehozni, az Egyszeri bejelentkezés előtt az alkalmazásban.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>A beépülő modul támogatási fürt Jira és verziói való összefolyás felett használ?

Nem. A beépülő modul működését Jira és való összefolyás felett helyszíni verzióival.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>A beépülő modul használata Jira és való összefolyás felett HTTP verziója nem?

Nem. A beépülő modul csak a HTTPS használatára konfigurált rendszerek együttműködik.