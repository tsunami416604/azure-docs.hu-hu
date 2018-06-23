---
title: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatásokban |} Microsoft Docs
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
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: a5345722005cc22ed7f89480c5aba51fd68cbf61
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335655"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Biztonságos LDAP (LDAPS) használatos az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz tartozó konfigurálása
Ez a cikk bemutatja, hogyan engedélyezheti biztonságos Lightweight Directory Access Protocol (LDAPS) vonatkozóan az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Biztonságos LDAP más néven az "Lightweight Directory Access Protocol (LDAP) Secure Sockets Layer (SSL) rétegen keresztül / Transport Layer Security (TLS)".

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. A **biztonságos LDAP engedélyezéséhez használni kívánt tanúsítványt**.

   * **Ajánlott** -megbízható, nyilvános hitelesítésszolgáltatótól származó tanúsítvány beszerzése. Ez a beállítás értéke nagyobb biztonságot nyújt.
   * Alternatív megoldásként is választhatja [hozzon létre egy önaláírt tanúsítványt](#task-1---obtain-a-certificate-for-secure-ldap) a cikk későbbi részében látható módon.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>A biztonságos LDAP-tanúsítványra vonatkozó követelményekről
Szerezzen be egy érvényes tanúsítványt az alábbi útmutatást, /, biztonságos LDAP engedélyezése előtt. Ha megpróbálja engedélyezni a felügyelt tartományok egy érvénytelen vagy helytelen tanúsítvánnyal biztonságos LDAP előforduló hibákat.

1. **A megbízható kiállítók** -a tanúsítványt egy szervezet biztonságos LDAP a felügyelt tartományra kapcsolódó számítógépek megbízhatónak kell kiállítani. A szolgáltató egy nyilvános hitelesítésszolgáltató (CA) vagy egy vállalati hitelesítésszolgáltató, ezek a számítógépek megbízhatónak lehet.
2. **Élettartam** -a tanúsítványnak érvényesnek kell lennie legalább a következő 3-6 hónapig. Biztonságos LDAP hozzáférést a felügyelt tartományra megszakad, ha a tanúsítvány lejár.
3. **Tulajdonos neve** – a tanúsítvány tulajdonos neve a felügyelt tartományok helyettesítő karakter lehet. Például, ha a tartomány neve "contoso100.com", a tanúsítvány tulajdonosának neve lehet "*. contoso100.com". A DNS-nevét (tulajdonos alternatív neve) értéke a helyettesítő karakteres neve.
4. **Kulcshasználat** -a következő használ - digitális aláírásokra és kulcstitkosítás kell beállítani a tanúsítványt.
5. **Tanúsítvány célja** -lehet, hogy a tanúsítvány érvényes SSL-kiszolgáló hitelesítéséhez.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>1. feladat – biztonságos LDAP tanúsítvány beszerzése
Az első tevékenységet foglal magában, beszerzett egy tanúsítványt, a felügyelt tartományra biztonságos LDAP eléréséhez használható. Erre két lehetősége van:

* A nyilvános Hitelesítésszolgáltatótól vagy vállalati hitelesítésszolgáltató tanúsítványának beszerzése.
* Hozzon létre egy önaláírt tanúsítványt.

> [!NOTE]
> Biztonságos LDAP segítségével a felügyelt tartományra kapcsolódó ügyfélszámítógépek számára megbízhatónak kell lennie a biztonságos LDAP tanúsítvány kiállítója.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Lehetőség (ajánlott) - biztonságos LDAP tanúsítvány beszerzése hitelesítésszolgáltatótól
Ha a szervezet beszerzi a tanúsítványokat nyilvános Hitelesítésszolgáltatótól származó, a biztonságos LDAP-tanúsítvány beszerzése a nyilvános Hitelesítésszolgáltatótól származó. Ha vállalati Hitelesítésszolgáltatót telepít, a biztonságos LDAP-tanúsítvány beszerzése a vállalati Hitelesítésszolgáltatótól.

> [!TIP]
> **Önaláírt tanúsítványokat használnak a felügyelt tartományok ". onmicrosoft.com" tartományi utótagokat.**
> Ha a DNS-tartománynév, a felügyelt tartomány fejeződik be a ". onmicrosoft.com", nem egy biztonságos LDAP-tanúsítvány beszerzése egy nyilvános hitelesítésszolgáltatót. Mivel a Microsoft az "onmicrosoft.com" tartománnyal rendelkezik, nyilvános hitelesítésszolgáltató egy biztonságos LDAP-tanúsítvány kiállításához Önnek egy tartományhoz a utótaggal rendelkező elutasítja. Ebben a forgatókönyvben hozzon létre egy önaláírt tanúsítványt, és használja, amely biztonságos LDAP konfigurálásához.
>

Győződjön meg arról, a nyilvános hitelesítésszolgáltatótól származó tanúsítvány megfelel a leírt [a biztonságos LDAP-tanúsítványra vonatkozó követelményekről](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>B lehetőség – biztonságos LDAP önaláírt tanúsítvány létrehozása
Ha nem tervezi, nyilvános hitelesítésszolgáltatótól származó tanúsítvány használatára, választhatja, hogy biztonságos LDAP egy önaláírt tanúsítvány létrehozásához. Válassza ki ezt a beállítást, ha a DNS-tartománynév, a felügyelt tartomány fejeződik be a ". onmicrosoft.com".

**Hozzon létre egy önaláírt tanúsítványt PowerShell használatával**

A Windows számítógépen nyisson meg egy új PowerShell-ablakot, **rendszergazda** és írja be a következő parancsokat egy új önaláírt tanúsítvány létrehozásához.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject *.contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

A fenti példában cserélje le a "*. contoso100.com" a DNS-tartománynévvel a felügyelt tartomány. For example, ha létrehozott egy "contoso100.onmicrosoft.com" nevű felügyelt tartomány, cserélje le a(z)*. contoso100.com "előző parancsfájlt a" *. contoso100.onmicrosoft.com ").

![Azure AD címtár kiválasztása](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Az újonnan létrehozott önaláírt tanúsítvány bekerül a helyi számítógép tanúsítványtárolójába.


## <a name="next-step"></a>Következő lépés
[2. feladat – a biztonságos LDAP tanúsítvány exportálása a. PFX-fájlból](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
