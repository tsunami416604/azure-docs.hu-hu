---
title: SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése |} Microsoft Docs
description: 'Ismerje meg, SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése '
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
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
ms.openlocfilehash: 55ff6b7a70bcdcceacb1484f9969337f9853ce50
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése

Amikor egy SAML-alapú alkalmazások integrálása hibakeresés, akkor célszerű gyakran hasonló eszközök használata [Fiddler](http://www.telerik.com/fiddler) , a SAML-kérelmet, és a SAML-válasz, amelyek tartalmazzák az alkalmazás számára korábban kiadott SAML-jogkivonatra. 

![Fiddler][1]

Gyakori problémák kapcsolódó helytelen beállítása az Azure Active Directoryval vagy az alkalmazás oldalára. Attól függően, hogy ha a hibát látja tekintse át a SAML-kérés vagy válasz rendelkezik:

- Hiba látható a vállalat bejelentkezési oldal [hivatkozások szakaszához]
- Látható hiba az alkalmazás lapon [hivatkozások szakaszához] bejelentkezés után

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Hiba a saját vállalati bejelentkezési oldala látható.

Egy az egyhez típusú leképezést eredményez a hibakód és a megoldási lépései között található [probléma jelentkezik be egy gyűjtemény alkalmazáshoz konfigurált összevont egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Ha hiba történt a vállalati bejelentkezési oldal jelenik meg, a hibaüzenet és szükséges az a probléma hibakeresését SAML-kérelmet.

### <a name="how-can-i-get-the-error--message"></a>Hogyan férhetnek a hibaüzenet a következő?

Ahhoz, hogy a hibaüzenet, tekintse meg a lap jobb alsó sarkában. Amely tartalmazza a hiba jelenik meg:

- A CorrelationID
- Időbélyeg
- Egy üzenet

![Hiba][2] 

 
A korrelációs azonosító és a Timestamp típusú alkotnak egy egyedi azonosítót, használhatja a háttér-naplókat a bejelentkezés sikertelen társított kereséséhez. Ezeket az értékeket hoz létre egy támogatási esetet a Microsofttal, mivel a mérnökök gyorsabb a probléma megoldását, így segítségükkel esetén fontos.

Az üzenet a bejelentkezési probléma okozza. 


### <a name="how-can-i-review-the-saml-request"></a>Hogyan tudja ellenőrizni a SAML-kérelmet?

Az alkalmazás Azure Active Directory által küldött SAML-kérelmet az általában a legutóbbi 200-as HTTP-válasz a [ https://login.microsoftonline.com ](https://login.microsoftonline.com).
 
Fiddler segítségével megtekintheti a SAML-kérelmet a sor kiválasztásával, és jelölje be a **ellenőrök > WebForms** fülre a jobb oldali panelen. Kattintson a jobb gombbal a **SAMLRequest** értékét, majd válassza ki **TextWizard küldeni**. Válassza ki **a Base64** a a **átalakítási** menü dekódolni a jogkivonatot, és tekintse meg annak tartalmát. 

Is is értékét a SAMLrequest másolása és egy másik Base64 dekóder használja.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

A dekódolt SAML-kérelmet, és tekintse meg a következőket:

1. **Cél** a SAML-kérelmet a felel meg a SAML egyetlen bejelentkezési URL-címe az Azure Active Directory kapott.
 
2. **Kibocsátó** a SAML a kérelem megegyezik **azonosító** konfigurálta, az alkalmazás Azure Active Directoryban. Az Azure AD a kibocsátó használja a címtárban található alkalmazás.

3. **AssertionConsumerServiceURL** van, ahol az alkalmazás fogadni az SAML-jogkivonat az Azure Active Directory vár. Ez az érték konfigurálhatja az Azure Active Directoryban, de már nem kötelező, ha a SAML-kérelmet része.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Látható hiba az alkalmazás lapon a bejelentkezés után

Ebben a forgatókönyvben az alkalmazás az Azure AD által kibocsátott válasz nem fogad el. Fontos, hogy ellenőrizze az Azure AD, amely tartalmazza az alkalmazás gyártójánál számára a hiányzó vagy hibás a SAML-jogkivonat válaszát. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Hogyan beolvasása és tekintse át a SAML-válasz?

Az Azure ad-, amely tartalmazza a SAML-jogkivonat esetében általában az egyik egy HTTP 302 irányítja át a felhasználókat utáni https://login.microsoftonline.com és küldi el a beállított való **válasz URL-CÍMEN** az alkalmazás. 

Ehhez jelölje ki a sort, és jelölje be a SAML-jogkivonat megtekintheti a **ellenőrök > WebForms** fülre a jobb oldali panelen. Ott, kattintson a jobb gombbal a **SAMLResponse** értékét, és válassza ki **TextWizard küldeni**. Válassza ki **a Base64** a a **átalakítási** dekódolni a jogkivonatot, és tekintse meg annak tartalmát használja egy másik Base64 dekóder menü. 

Az értéket is másolhatja a **SAMLrequest** és egy másik Base64 dekóder használja.

Látogasson el [hiba történt a bejelentkezés után az alkalmazás oldalon](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) hibaelhárítási útmutatót olvashat Mi hiányzik vagy helytelen a SAML-válasz lehet.

Tekintse át a SAML olvashat válasz látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [A SAML-jogkivonat előzetesen beépített alkalmazások számára kiállított jogcímek testreszabása](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
