---
title: PowerShell-minták az Azure AD alkalmazásproxyhoz
description: Ezekkel a PowerShell-minták az Azure AD alkalmazásproxyhoz segítségével információkat kaphat a címtárban lévő alkalmazásproxy-alkalmazásokról és -összekötőkről, felhasználókat és csoportokat rendelhet hozzá az alkalmazásokhoz, és tanúsítványinformációkat kaphat.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481262"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Példák az Azure AD PowerShell-alkalmazások proxyjának azure AD-alkalmazásproxyjával

Az alábbi táblázat az Azure AD-alkalmazásproxy powershell-parancsfájl-példákra mutató hivatkozásokat tartalmazza. Ezek a minták az [AzureAD V2 PowerShell graph modulvagy](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)igényel, ha nincs másképp.


A mintákban használt parancsmagokról további információt az [Alkalmazásproxy-alkalmazáskezelés](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) és [az alkalmazásproxy-összekötő kezelése című](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)témakörben talál.

| | |
|---|---|
|**Alkalmazásproxy-alkalmazások**||
| [Az összes alkalmazásproxy-alkalmazás alapvető adatainak listázása](scripts/powershell-get-all-app-proxy-apps-basic.md) | A címtárban található összes alkalmazásproxy-alkalmazással kapcsolatos alapvető információkat (AppId, DisplayName, ObjId) sorolja fel. |
| [Az összes alkalmazásproxy-alkalmazásra vonatkozó bővített információk listázása](scripts/powershell-get-all-app-proxy-apps-extended.md) | Felsorolja a könyvtárban lévő összes alkalmazásproxy-alkalmazásra vonatkozó bővített információkat (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType).  |
| [Az összes alkalmazásproxy-alkalmazás listája összekötőcsoport szerint](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | A címtárban lévő összes alkalmazásproxy-alkalmazással kapcsolatos információkat, valamint azt, hogy az alkalmazások mely összekötőcsoportokhoz vannak hozzárendelve. |
| [Az összes alkalmazásproxy-alkalmazás beszereznie egy jogkivonat-élettartam-szabályzattal](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Felsorolja az összes alkalmazásproxy-alkalmazást a címtárban egy jogkivonat élettartamra vonatkozó házirenddel és annak részleteivel. Ehhez a mintához az [AzureAD V2 PowerShell a Graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)szükséges. |
|**Összekötőcsoportok**||
| [Az összes összekötőcsoport és -összekötő beszereznie a címtárban](scripts/powershell-get-all-connectors.md) | A címtárban lévő összes összekötőcsoport és-összekötő felsorakozó. |
| [Az összekötőcsoporthoz rendelt összes alkalmazás áthelyezése egy másik összekötőcsoportba](scripts/powershell-move-all-apps-to-connector-group.md) | Áthelyezi az összekötőcsoporthoz jelenleg hozzárendelt összes alkalmazást egy másik összekötőcsoportba. |
|**Hozzárendelt felhasználók és csoportok**||
| [Alkalmazásproxy-alkalmazáshoz rendelt felhasználók és csoportok megjelenítése](scripts/powershell-display-users-group-of-app.md) | Egy adott alkalmazásproxy-alkalmazáshoz rendelt felhasználók és csoportok listája. |
| [Felhasználó hozzárendelése alkalmazáshoz](scripts/powershell-assign-user-to-app.md) | Egy adott felhasználóhozzárendelése egy alkalmazáshoz. |
| [Csoport hozzárendelése alkalmazáshoz](scripts/powershell-assign-group-to-app.md) | Egy adott csoportot rendel egy alkalmazáshoz. |
|**Külső URL-konfiguráció**||
| [Az összes alkalmazásproxy-alkalmazás beszereznie az alapértelmezett tartományokhasználatával (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Az alapértelmezett tartományokat használó alkalmazásproxy-alkalmazások (.msappproxy.net) listája. |
| [Az összes alkalmazásproxy-alkalmazás beszereznihelyettesítő közzététellel](scripts/powershell-get-all-wildcard-apps.md) | A helyettesítő házirend-közzétételt használó alkalmazásproxy-alkalmazások listája. |
|**Egyéni tartomány konfigurációja**||
| [Az összes alkalmazásproxy-alkalmazás beszerezniegyéni tartományok és tanúsítványadatok használatával](scripts/powershell-get-all-custom-domains-and-certs.md) | Megjeleníti az egyéni tartományokat használó alkalmazásproxy-alkalmazásokat és az egyéni tartományokhoz társított tanúsítványadatokat. |
| [Az összes Azure AD Proxy alkalmazásalkalmazás közzététele tanúsítvány feltöltés nélkül](scripts/powershell-get-all-custom-domain-no-cert.md) | Felsorolja az összes olyan alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, de nem rendelkezik érvényes Feltöltött TLS/SSL tanúsítvánnyal. |
| [Az azonos tanúsítvánnyal közzétett összes Azure AD Proxy alkalmazásalkalmazás beszerezni](scripts/powershell-get-custom-domain-identical-cert.md) | Az azonos tanúsítvánnyal közzétett összes Azure AD Proxy alkalmazásalkalmazás-alkalmazás listázása. |
| [Az összes Azure AD Proxy alkalmazásalkalmazás közzététele az azonos tanúsítvánnyal, és cserélje le](scripts/powershell-get-custom-domain-replace-cert.md) | Az Azure AD proxy alkalmazás alkalmazások, amelyek közzé egy azonos tanúsítvánnyal, lehetővé teszi, hogy cserélje ki a tanúsítványt ömlesztve. |
