---
title: "Az alkalmazást az Azure Active Directory alkalmazáskatalógusában listázása"
description: "Hogyan listázhat egy alkalmazás, amely támogatja az egyszeri bejelentkezés az Azure Active Directory-katalógus |} A Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 0c324829469b9babe6608480204bd46691f84228
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazást az Azure Active Directory alkalmazáskatalógusában listázása
Egy alkalmazás, amely támogatja az egyszeri bejelentkezés az Azure Active Directoryban a listát a [az Azure AD-gyűjtemény](https://azure.microsoft.com/marketplace/active-directory/all/), az alkalmazás első kell megvalósítania, a következő integrációs módok egyikét:

* **OpenID Connect** -közvetlen integráció használja OpenID Connect hitelesítési és az Azure AD hozzájárulás API konfigurálása az Azure AD-val. Ha integrációs csak indításakor, és az alkalmazás nem támogatja az SAML-alapú, majd azt az ajánlott mód.
* **SAML** – az alkalmazás már rendelkezik az ügyfélgépek konfigurálására harmadik fél Identitásszolgáltatók az SAML protokoll használatával.

Listaelem követelmények az egyes módokban alatt van.

## <a name="openid-connect-integration"></a>OpenID Connect integráció
Az alkalmazás integrálása az Azure Active Directory, a következő a [fejlesztői útmutatás](active-directory-authentication-scenarios.md). Ezután végezze el az alábbi kérdésekre, és elküldje waadpartners@microsoft.com.

* Azokat a hitelesítő adatokat egy tesztelési bérlőn vagy a fiókot az alkalmazással, az integráció tesztelése az Azure AD-csoport által használható.  
* Hogyan az Azure AD-csoport is bejelentkezhet, és csatlakozzon az Azure AD példányát az alkalmazást a vonatkozó utasítások a [az Azure AD hozzájárulási keretrendszer](active-directory-integrating-applications.md#overview-of-the-consent-framework). 
* Adja meg az Azure AD csapatával az egyszeri bejelentkezés az alkalmazáshoz szükséges további utasításokat. 
* Adja meg az alábbi adatokat:

> Vállalat neve:
> 
> Vállalati webhely:
> 
> Alkalmazás neve:
> 
> Az alkalmazás leírása (200 karakter lehet):
> 
> Alkalmazás webhely (tájékoztató):
> 
> Alkalmazás technikai támogatási webhely vagy kapcsolattartási adatokat:
> 
> Az alkalmazás, ahogy az alkalmazás részletes adatainak https://portal.azure.com, az alkalmazás azonosítója:
> 
> Alkalmazás Sign-Up URL-Címének Regisztráljon a felhasználók hová és/vagy vásároljon az alkalmazást:
> 
> (A kategóriák lásd az Azure Active Directory Marketplace) alatt helyezkednek el az alkalmazás három kategóriák kiválasztása:
> 
> Csatolja az alkalmazás kis ikon (PNG-fájlt, 45px 45px, teli háttérszíne szerint):
> 
> Csatolja az alkalmazás nagy méretben (PNG-fájlt, 215px 215px, teli háttérszíne szerint):
> 
> Csatolja az alkalmazás embléma (PNG-fájlt, 122px, háttérszínt által 150px):
> 
> 

## <a name="saml-integration"></a>SAML-integráció
Bármely alkalmazás, amely támogatja az SAML 2.0 integrálható közvetlenül az Azure AD-bérlő segítségével [ezeket az utasításokat egy egyéni alkalmazás hozzáadása](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). Miután ellenőrizte, hogy működik-e az alkalmazások integrálása az Azure ad-vel, az alábbi információk küldése < mailto:waadpartners@microsoft.com >.

* Azokat a hitelesítő adatokat egy tesztelési bérlőn vagy a fiókot az alkalmazással, az integráció tesztelése az Azure AD-csoport által használható.  
* Adja meg a SAML bejelentkezési URL-címet, a kiállítói URL-címe (entitás azonosítója) és a válasz URL-CÍMEN (helyességi feltétel fogyasztói szolgáltatás) értékek az alkalmazás leírtak [Itt](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). Ha általában megadja ezeket az értékeket a SAML-metaadatfájl részeként, majd küldje, amely is.
* Az Azure AD konfigurálása az alkalmazás SAML 2.0 használatával az identitás-szolgáltatóként rövid leírását adhatja meg. Ha az alkalmazás támogatja az konfigurálása az Azure AD felügyeleti önkiszolgáló portál keresztül identitás-szolgáltatóként, majd ellenőrizze, hogy a fent megadott hitelesítő adatok közé tartozik a beállítására.
* Adja meg az alábbi adatokat:

> Vállalat neve:
> 
> Vállalati webhely:
> 
> Alkalmazás neve:
> 
> Az alkalmazás leírása (200 karakter lehet):
> 
> Alkalmazás webhely (tájékoztató):
> 
> Alkalmazás technikai támogatási webhely vagy kapcsolattartási adatokat:
> 
> Alkalmazás Sign-Up URL-Címének Regisztráljon a felhasználók hová és/vagy vásároljon az alkalmazást:
> 
> Válassza ki a alkalmazás alatt helyezkednek el legfeljebb három kategóriába (kategóriák lásd a [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Csatolja az alkalmazás kis ikon (PNG-fájlt, 45px 45px, teli háttérszíne szerint):
> 
> Csatolja az alkalmazás nagy méretben (PNG-fájlt, 215px 215px, teli háttérszíne szerint):
> 
> Csatolja az alkalmazás embléma (PNG-fájlt, 122px, háttérszínt által 150px):
> 
> 

