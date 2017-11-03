---
title: "SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése |} Microsoft Docs"
description: "Ismerje meg, SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 246709effcff1c38d14db3848fe2fad836ad90da
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése
Amikor egy SAML-alapú alkalmazások integrálása hibakeresés, akkor célszerű gyakran hasonló eszközök használata [Fiddler](http://www.telerik.com/fiddler) a SAML-kérelmet, SAML-válasz és az alkalmazás számára kiállított tényleges SAML-jogkivonatra. A SAML-jogkivonat vizsgálatával biztosítható, hogy a szükséges attribútumokat, a felhasználónevet a SAML-tulajdonos, és a kiállító URI mindegyikét érkeznek várt módon.

![][1]

Az Azure ad-, amely tartalmazza a SAML-jogkivonat esetében általában a egy másikat, amely egy HTTP 302 átirányítási után a https://login.windows.net következik be, majd továbbítja a konfigurált való **válasz URL-CÍMEN** az alkalmazás. 

Ehhez jelölje ki a sort, és jelölje be a SAML-jogkivonat megtekintheti a **ellenőrök > WebForms** fülre a jobb oldali panelen. Ott, kattintson a jobb gombbal a **SAMLResponse** értékét, és válassza ki **TextWizard küldeni**. Válassza ki **a Base64** a a **átalakítási** menü dekódolni a jogkivonatot, és tekintse meg annak tartalmát.

**Megjegyzés:**: a HTTP-kérelem tartalmának megtekintéséhez Fiddler késztethetik, konfigurálhatja a HTTPS forgalmat, amely ehhez szüksége lesz visszafejtése.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [A SAML-jogkivonat előzetesen beépített alkalmazások számára kiállított jogcímek testreszabása](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png