---
title: "RADIUS-hitelesítés és Azure MFA-kiszolgáló | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt a RADIUS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017, it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: e696b95c9db86b062440f0c4fd788bf97223317a
ms.contentlocale: hu-hu
ms.lasthandoff: 02/28/2017

---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS-hitelesítés integrálása az Azure Multi-Factor Authentication-kiszolgálóval
Az Azure MFA-kiszolgáló RADIUS-hitelesítés szakaszában engedélyezheti és konfigurálhatja a RADIUS-hitelesítést. A RADIUS egy szabványos protokoll hitelesítési kérések fogadására és feldolgozására. Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-kiszolgálóként működik. Az Azure Multi-Factor Authentication hozzáadásához szúrja be a kiszolgálót a RADIUS-ügyfél (VPN-készülék) és a hitelesítési cél közé, amely Active Directory (AD), LDAP-címtár vagy egy másik RADIUS-kiszolgáló is lehet. Az Azure Multi-Factor Authentication (MFA) működéséhez az Azure MFA-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és a hitelesítési céllal is tudjon kommunikálni. Az Azure MFA-kiszolgáló fogadja a RADIUS-ügyfél kéréseit, ellenőrzi a hitelesítő adatokat a hitelesítési célon, Azure Multi-Factor Authenticationt biztosít és választ küld a RADIUS-ügyfélnek. A hitelesítés csak akkor sikeres, ha az elsődleges hitelesítés és az Azure Multi-Factor Authentication is sikeres.

> [!NOTE]
> Az MFA-kiszolgáló csak a PAP (Password Authentication Protocol) és az MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protokollt támogatja, ha RADIUS-kiszolgálóként működik.  Egyéb protokollok, például az EAP (Extensible Authentication Protocol), akkor használhatók, ha az MFA-kiszolgáló egy másik olyan RADIUS-kiszolgáló RADIUS-proxyjaként működik, amely támogatja az adott protokollt.
>
> Ebben a konfigurációban az egyirányú SMS és OATH tokenek nem működnek, mivel az MFA-kiszolgáló nem tud sikeres RADIUS kérdés-választ kezdeményezni más protokollok használatával.

![Radius-hitelesítés](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>RADIUS-ügyfél hozzáadása
A RADIUS-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálóra. Ha Active Directory-környezetet használ, a kiszolgálót a hálózaton belüli tartományhoz kell csatlakoztatni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő RADIUS-hitelesítés ikonra.
2. Jelölje be a **RADIUS-hitelesítés engedélyezése** jelölőnégyzetet.
3. Az Ügyfelek lapon módosítsa a hitelesítési és a nyilvántartási portokat, ha az Azure MFA RADIUS szolgáltatásnak nem szabványos portokon kell figyelnie a RADIUS-kéréseket.
4. Kattintson az **Add** (Hozzáadás) parancsra.
5. Adja meg az Azure Multi-Factor Authentication-kiszolgálón hitelesítendő készülék/kiszolgáló IP-címét, egy alkalmazásnevet (nem kötelező) és egy közös titkos kulcsot.

  Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.

  A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a készüléken/kiszolgálón.

6. Jelölje be a **Multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
7. Jelölje be a **Tartalék OATH token engedélyezése** jelölőnégyzetet, ha mobil hitelesítő alkalmazásokból származó OATH PIN-kódokat szeretné tartalékként használni a sávon kívüli telefonhívások, SMS-ek vagy leküldéses értesítések mellett.
8. Kattintson az **OK** gombra.

Szükség esetén a 4–8. lépés megismétlésével további RADIUS-ügyfeleket adhat hozzá.

## <a name="configure-your-radius-client"></a>RADIUS-ügyfél konfigurálása

1. Kattintson a **Cél** fülre.
2. Ha az Azure MFA-kiszolgáló tartományhoz csatlakoztatott kiszolgálón van telepítve Active Directory-környezetben, válasszon egy Windows-tartományt.
3. Ha a felhasználókat egy LDAP-címtár alapján kell hitelesíteni, válassza az **LDAP-kötést**.

  Az LDAP-kötés használatához kattintson a Címtár-integráció ikonra, és szerkessze úgy az LDAP-konfigurációt a Beállítások lapon, hogy a kiszolgáló kötést hozhasson létre a címtárával. Az LDAP konfigurálásával kapcsolatos utasításokat az [LDAP-proxy konfigurációs útmutatójában](multi-factor-authentication-get-started-server-ldap.md) talál.

4. Ha a felhasználókat egy másik RADIUS-kiszolgálón kell hitelesíteni, válassza a RADIUS-kiszolgáló(k) lehetőséget.
5. Kattintson a **Hozzáadás** gombra, és konfigurálja azt a kiszolgálót, amelynek a kiszolgáló proxykapcsolaton keresztül átadja a RADIUS-kéréseket.
6. A RADIUS-kiszolgáló hozzáadása párbeszédpanelen adja meg a RADIUS-kiszolgáló IP-címét és egy közös titkos kulcsot.

  A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication- és a RADIUS-kiszolgálón. Módosítsa a hitelesítési portot és a nyilvántartási portot, ha a RADIUS-kiszolgáló eltérő portokat használ.

7. Kattintson az **OK** gombra.
8. Adja hozzá az Azure MFA-kiszolgálót egy RADIUS-ügyfélként a másik RADIUS-kiszolgálón, hogy fel tudja dolgozni az Azure MFA-kiszolgálóról érkező hozzáférési kéréseket. Ugyanazt a közös titkos kulcsot használja, amelyet az Azure Multi-Factor Authentication-kiszolgálón konfigurált.

Ezt a lépést megismételve több RADIUS-kiszolgálót is hozzáadhat, illetve a **Feljebb** és **Lejjebb** gombokkal konfigurálhatja a sorrendet, amely alapján az Azure MFA-kiszolgáló meghívja őket.

Ezzel befejeződött az Azure Multi-Factor Authentication-kiszolgáló konfigurálása. A kiszolgáló mostantól fogadja a konfigurált ügyfelek RADIUS hozzáférési kéréseit a beállított portokon.   

## <a name="radius-client-configuration"></a>A RADIUS-ügyfél konfigurálása
A RADIUS-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a RADIUS-on keresztül hitelesítendő készüléket/kiszolgálót azon Azure Multi-Factor Authentication-kiszolgáló IP-címével, amely a RADIUS-kiszolgálóként fog működni.
* Használja a korábban konfigurált közös titkos kulcsot.
* A RADIUS-időtúllépést állítsa 30–60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére, a kétlépéses hitelesítés végrehajtására, a válasz fogadására és a RADIUS hozzáférési kérés megválaszolására.

