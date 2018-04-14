---
title: "A Windows Hello-jelszó nélkül identitások hitelesítése és az Azure AD |} Microsoft Docs"
description: "Áttekintést nyújt a Windows Hello üzleti és a vállalati Windows Hello telepítésével kapcsolatos további információkat."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>A vállalati Windows Hello jelszó nélkül identitások hitelesítése
Az aktuális önmagában jelszavakkal hitelesítési módszerek nem elegendőek felhasználók biztonságát. A felhasználók használja fel, és elfelejti a jelszavát. A jelszóban breachable, phishable, repedések hajlamos és kitalálható. Akkor is le a nehéz megjegyezni és vannak téve a támadásoknak, például a "[adja át a kivonat](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>A vállalati Windows Hello kapcsolatos
Vállalati Windows Hello a saját vagy nyilvános kulcs- vagy tanúsítványalapú hitelesítési módszert alkalmaz a szervezetek és a fogyasztók, amely jelszavak túllép. Az űrlap-hitelesítés kulcsból álló kulcspárt hitelesítő adatait, amely lecserélheti a jelszavak, illetve megszegése, thefts és adathalászat ellen védett támaszkodik.

 Vállalati Windows Hello lehetővé teszi, hogy egy felhasználó Microsoft-fiókkal, egy Windows Server Active Directory-fiókot, a Microsoft Azure Active Directory (Azure AD-) fiók vagy egy nem Microsoft-szolgáltatás, amely támogatja a gyors identitás Online (FIDO) hitelesítést hitelesítéséhez. Egy kezdeti kétlépéses ellenőrzés után során a Windows Hello üzleti regisztrálásra vállalati Windows Hello be van állítva a felhasználó eszközén, és a felhasználó állítja be a hitelesítési mód, amely Windows Hello vagy PIN-kódot. A felhasználó megadja a hitelesítési mód igazolniuk az identitásukat. A Windows a vállalati Windows Hello hitelesíteni a felhasználót és segítséget nyújthatnak a védett erőforrások és szolgáltatások eléréséhez.

A titkos kulcs keresztül teszik elérhetővé kizárólag a "felhasználói hitelesítési mód" például a PIN-kód, biometrikus vagy egy távoli eszköz, például egy intelligens kártya, amely a felhasználó jelentkezzen be az eszközt használja. Ez az információ a tanúsítvány vagy egy aszimmetrikus kulcspár van csatolva. A titkos kulcs hardver, ha az eszköz egy platformmegbízhatósági modul (TPM) csipje igazolt. A titkos kulcs soha ne hagyja el az eszközt.

A nyilvános kulcs regisztrálva van az Azure Active Directory és a Windows Server Active Directory (helyszíni). Identitás-szolgáltatóktól (IDPs) ellenőrzi, hogy a felhasználó által a nyilvános kulcsot a felhasználó leképezése a titkos kulcsot, és adjon meg egy alkalommal jelszót (OTP), a PhoneFactor vagy egy másik értesítési mechanizmus bejelentkezési adatait.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Miért vállalatok el kell fogadnia vállalati Windows Hello
Engedélyezésével vállalati Windows Hello, vállalatok is erőforrásaik még biztonságosabbá teszi szerint:

* Beállítása vállalati Windows Hello hardver előnyben részesített módszerrel. Ez azt jelenti, hogy kulcsokat generál a TPM 1.2-es vagy a TPM 2.0, ha elérhető. Ha nem érhető el TPM, a szoftver hoz létre a kulcsot.
* A összetettségét és a PIN-kód hossza és Hello használata engedélyezve van-e a szervezetében.
* Konfigurálás vállalati Windows Hello-alapú megbízhatósági használatával intelligenskártya-szerű forgatókönyvek támogatása céljából.

## <a name="how-windows-hello-for-business-works"></a>Vállalati Windows Hello működése
1. Kulcsok TPM vagy szoftver által előállított hardveren. Sok eszköz rendelkezik beépített TPM lapka biztonságossá tételére a hardver titkosítási kulcsok integrálása eszközök. A TPM 1.2-es vagy TPM 2.0 hoz létre kulcsokat vagy tanúsítványokat, a létrehozott kulcsokat a létrehozott.
2. A TPM igazolja a hardver adathoz kötött kulcsokat.
3. Egyetlen unlock kézmozdulatok feloldja az eszköz. A hitelesítési mód lehetővé teszi, hogy több erőforrásokhoz való hozzáférést, ha az eszköz nem tartományhoz csatlakoztatott vagy az Azure AD-tartományhoz.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>A Windows Hello-üzleti életciklus működése
![A Windows Hello-üzleti életciklusa](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

A fenti ábrán a saját vagy nyilvános kulcsból álló kulcspárt és az identitásszolgáltatótól érvényesítési mutatja be. A lépések van részletes magyarázatához lásd itt:

1. A felhasználó igazolja az identitásukat több beépített ellenőrző módszerek (kézmozdulatok, fizikai intelligens kártyával, a multi-factor authentication) keresztül, és elküldi az információkat a az Identity Provider (IDP) például az Azure Active Directory vagy a helyszíni Active Directory.
2. Az eszköz majd a kulcsot hoz létre, a kulcs igazolja, időt vesz igénybe a kulcs nyilvános részének, csatolja az állomás utasítások, jelentkezik be és elküldi az IDP regisztrálja a kulcsot.
3. Amint az IDP a kulcs nyilvános részének regisztrál, a kiállító terjesztési hely felkéri az eszköz a kulcs titkos része alá.
4. Az IDP majd érvényesíti, és a védett erőforrások állít ki a hitelesítési jogkivonat, amely lehetővé teszi, hogy a felhasználó és eszköz-hozzáférés. IDPs platformfüggetlen alkalmazások írhat vagy támogatott böngésző (keresztül JavaScript/Webcrypto API-k) segítségével hozza létre, és használja a Windows Hello a vállalati hitelesítő adatok a felhasználók számára.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>A központi telepítésére vonatkozó követelmények vállalati Windows Hello
### <a name="at-the-enterprise-level"></a>A vállalati szinten
* A vállalat az Azure-előfizetéssel rendelkezik.

### <a name="at-the-user-level"></a>A felhasználói szinten
* A felhasználó számítógépén fut a Windows 10 Professional és Enterprise.

Részletes telepítési utasításokért lásd: [engedélyezése vállalati Windows Hello a szervezet](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>További információ
* [Vállalati használatú Windows 10: Az eszközök munkahelyi célú használata](active-directory-azureadjoin-windows10-devices-overview.md)
* [A felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory Joinon keresztül](active-directory-azureadjoin-user-upgrade.md)
* [További információk az Azure AD Join használati forgatókönyveiről](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es környezetben](active-directory-azureadjoin-devices-group-policy.md)
* [Az Azure AD Join beállítása](active-directory-azureadjoin-setup.md)

