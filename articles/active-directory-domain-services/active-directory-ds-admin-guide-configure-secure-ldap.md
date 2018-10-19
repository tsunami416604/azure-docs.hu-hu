---
title: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatások |} A Microsoft Docs
description: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 42c27df658ae810ac31813ffb94f397a0070933f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429133"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Biztonságos LDAP (LDAPS-t) az Azure AD Domain Services felügyelt tartomány konfigurálása
Ez a cikk bemutatja, hogyan engedélyezheti a biztonságos Lightweight Directory Access Protocol (LDAPS) számára az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. A Secure LDAP van más néven "Lightweight Directory Access Protocol (LDAP) Secure Sockets Layer (SSL) rétegen keresztül / Transport Layer Security (TLS)".

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. A **biztonságos LDAP engedélyezése a használt tanúsítvány**.

   * **Ajánlott** – szerezzen be egy tanúsítványt megbízható nyilvános hitelesítésszolgáltatótól származó. Ez az opció használata biztonságosabb.
   * Másik lehetőségként is dönthet úgy, hogy [hozzon létre egy önaláírt tanúsítványt](#task-1---obtain-a-certificate-for-secure-ldap) a cikk későbbi részében látható módon.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>A biztonságos LDAP-tanúsítvány követelményei
A következő irányelveket kiszolgálónként érvényes tanúsítványt beszerezni, a secure LDAP engedélyezése előtt. Ha megpróbálja érvénytelenek vagy helytelenek tanúsítvánnyal a felügyelt tartomány secure LDAP engedélyezése hibák tapasztal.

1. **Megbízható kiállítók** – a tanúsítvány egy szolgáltató által a felügyelt tartomány secure LDAP használatával kapcsolódó számítógépek megbízhatónak kell kiállítaniuk. Ez a szolgáltató egy nyilvános hitelesítésszolgáltató (CA) vagy egy vállalati hitelesítésszolgáltató, ezek a számítógépek megbízhatónak lehet.
2. **Élettartam** – a tanúsítványnak érvényesnek kell lennie legalább a következő 3 – 6 hónapra. A felügyelt tartomány Secure LDAP-hozzáférését megszakad, ha a tanúsítvány lejár.
3. **Tulajdonos neve** – a tanúsítványon a tulajdonos nevét a felügyelt tartomány helyettesítő karaktert kell lennie. Például ha a tartomány neve a "contoso100.com", a tanúsítvány tulajdonos neve kell lennie ' *. contoso100.com ". A helyettesítő karaktert tartalmazó nevet a DNS-név (tulajdonos alternatív neve) értékre.
4. **Kulcshasználat** -tanúsítvány esetében a következő használ – digitális aláírások és kulcstitkosítás kell konfigurálni.
5. **Tanúsítvány célja** – a tanúsítványnak kell lennie az SSL-kiszolgálói hitelesítés esetén érvényes.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>1. feladat – tanúsítvány beszerzése biztonságos LDAP-hoz
Az első feladat magában foglalja a felügyelt tartomány secure LDAP-hozzáférését használt tanúsítvány beszerzésével. Erre két lehetősége van:

* Szerezzen be egy tanúsítványt nyilvános Hitelesítésszolgáltatótól vagy vállalati hitelesítésszolgáltató.
* Hozzon létre egy önaláírt tanúsítványt.

> [!NOTE]
> Ügyfélszámítógépek, amelyek a felügyelt tartomány secure LDAP használatával csatlakoztatni kell a biztonságos LDAP-tanúsítvány kiállítója meg kell bíznia.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>. Lehetőség (ajánlott) – egy biztonságos LDAP-tanúsítvány beszerzése hitelesítésszolgáltatótól
Ha a szervezet a tanúsítványokat nyilvános hitelesítésszolgáltatótól szerzi be, szerezze be a secure LDAP-tanúsítványt a nyilvános hitelesítésszolgáltatótól. Ha vállalati Hitelesítésszolgáltatót telepít, szerezze be a secure LDAP-tanúsítványt a vállalati Hitelesítésszolgáltatótól.

> [!TIP]
> **Önaláírt tanúsítványokat használnak a felügyelt tartományok '. onmicrosoft.com "tartományi utótagokat.**
> Ha a felügyelt tartomány DNS-tartománynév végződő '. onmicrosoft.com ", nem szerezheti be a secure LDAP-tanúsítványt nyilvános hitelesítésszolgáltatótól származó. A Microsoft az "onmicrosoft.com" tartománnyal rendelkezik, mivel nyilvános hitelesítésszolgáltató tagadnia secure LDAP-tanúsítványt Önnek egy tartomány ennek az utótagnak a. Ebben a forgatókönyvben hozzon létre egy önaláírt tanúsítványt, és használja, amely a secure LDAP konfigurálása.
>

Győződjön meg arról, Ön a nyilvános hitelesítésszolgáltatótól szerez be a tanúsítvány megfelel a rendszerkövetelményeknek [a biztonságos LDAP-tanúsítvány követelményei](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>B. lehetőség: hozzon létre egy önaláírt tanúsítványt a secure LDAP-
Ha nem a nyilvános hitelesítésszolgáltatótól származó tanúsítványt használjon, dönthet úgy egy önaláírt tanúsítványt létrehozni biztonságos LDAP-hoz. Válassza ki ezt a beállítást, ha a felügyelt tartomány DNS-tartománynév ér véget, a ". onmicrosoft.com".

**Hozzon létre egy önaláírt tanúsítványt, PowerShell-lel**

A Windows számítógépen nyisson meg egy új PowerShell-ablakot, **rendszergazda** , és írja be a következő parancsokat egy új önaláírt tanúsítvány létrehozása.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

Az előző példában cserélje le a felügyelt tartomány DNS-tartománynév "contoso100.com". Például, ha létrehozott egy "contoso100.onmicrosoft.com" nevű felügyelt tartományt, cserélje le a tulajdonos attribútum a "contoso100.com" "contoso100.onmicrosoft.com" és "*. contoso100.com"-DnsName attribútumban "*. contoso100.onmicrosoft.com ").

![Azure AD címtár kiválasztása](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Az újonnan létrehozott önaláírt tanúsítványt a helyi gép tanúsítványtárolójába kerül.


## <a name="next-step"></a>Következő lépés
[2. feladat – exportálja a secure LDAP-tanúsítványt egy. PFX-fájl](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
