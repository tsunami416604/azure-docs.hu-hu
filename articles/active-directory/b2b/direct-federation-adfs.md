---
title: Közvetlen összevonás beállítása B2B-hez tervezett AD FS-sel – Azure AD
description: Megtudhatja, hogyan állíthatja be az AD FS-t identitásszolgáltatóként a közvetlen összevonáshoz, hogy a vendégek bejelentkezhessenek az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272831"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Példa: Közvetlen összevonás az Active Directory összevonási szolgáltatásokkal (AD FS) (előzetes verzió)
|     |
| --- |
| A közvetlen összevonás az Azure Active Directory nyilvános előzetes verziójú szolgáltatása. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk azt ismerteti, hogy miként állítható konkreti [az](direct-federation.md) Active Directory összevonási szolgáltatások (AD FS) használatával SAML 2.0 vagy WS-Fed identitásszolgáltatóként. A közvetlen összevonás támogatásához bizonyos attribútumokat és jogcímeket az identitásszolgáltatónál kell konfigurálni. Annak szemléltetésére, hogyan konfigurálhat identitásszolgáltatót a közvetlen összevonáshoz, példaként az Active Directory összevonási szolgáltatásokat (AD FS) használjuk. Bemutatjuk, hogyan állíthat be AD FS-t SAML-identitásszolgáltatóként és WS-Fed identitásszolgáltatóként is.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan állíthatja be az AD FS-t mind az SAML, mind a WS-Fed számára illusztrációként. Közvetlen összevonási integrációk, ahol az identitásszolgáltató AD FS, javasoljuk, hogy a WS-Fed protokollként használja. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>AD FS konfigurálása SAML 2.0 közvetlen összevonáshoz
Az Azure AD B2B konfigurálható úgy, hogy az SAML protokollt az alábbiakban felsorolt speciális követelményekkel rendelkező identitásszolgáltatókkal egytelenítsen. Az SAML konfigurációs lépések szemléltetéséhez ez a szakasz bemutatja, hogyan állítható be az AD FS az SAML 2.0-hoz. 

A közvetlen összevonás beállításához a következő attribútumokat kell fogadni az SAML 2.0 válaszban az identitásszolgáltatótól. Ezek az attribútumok az online biztonsági tokenszolgáltatás XML-fájljára való hivatkozással vagy manuális beírással konfigurálhatók. 12. lépés: [Teszt létrehozása AD FS példány](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) leírja, hogyan lehet megtalálni az AD FS `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`végpontok, vagy hogyan kell létrehozni a metaadat URL-címet, például . 

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner IdP kibocsátóuri-ja, például`http://www.example.com/exk10l6w90DHM0yi...`         |

Az identitásszolgáltató által kiadott SAML 2.0-s jogkivonatban a következő jogcímeket kell konfigurálni:


|Attribútum  |Érték  |
|---------|---------|
|NameID formátum     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A következő szakasz bemutatja, hogyan konfigurálhatja a szükséges attribútumokat és jogcímeket az AD FS használatával az SAML 2.0 identitásszolgáltató példájaként.

### <a name="before-you-begin"></a>Előkészületek

Az eljárás megkezdése előtt az AD FS-kiszolgálót már be kell állítani és működni kell. Az AD FS-kiszolgáló beállításával kapcsolatban további segítséget az [AD FS 3.0-s példány létrehozása Azure-beli virtuális gépen](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)című témakörben talál.

### <a name="add-the-claim-description"></a>A jogcím leírásának hozzáadása

1. Az AD FS-kiszolgálón válassza **az Eszközök** > **AD FS-kezelés lehetőséget.**
2. A navigációs ablakban válassza a > **Szervizjogcím leírása lehetőséget.** **Service**
3. A **Műveletek csoportban**válassza **a Jogcím leírásának hozzáadása lehetőséget.**
4. A **Jogcím leírásának hozzáadása** ablakban adja meg a következő értékeket:

   - **Megjelenítendő név**: Állandó azonosító
   - **Jogcímazonosító**:`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Jelölje be a **Jogcím leírásának közzététele az összevonási metaadatokban olyan jogcímtípusként jelölőnégyzetet, amelyet ez az összevonási szolgáltatás el tud fogadni.**
   - Jelölje be a **Jogcím leírásának közzététele az összevonási metaadatokban az összevonási szolgáltatás által küldhető jogcímtípusként jelölőnégyzetet.**

5. Kattintson az **OK** gombra.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatósági és jogcímszabályának hozzáadása

1. Az AD FS-kiszolgálón nyissa meg az **Eszközök** > **AD FS-kezelés című**területet.
2. A navigációs ablakban válassza a **Bizalmi** > kapcsolatok**függő entitásmegbízhatóságok**lehetőséget.
3. A **Műveletek csoportban**válassza **a Függő entitás megbízhatóságának hozzáadása lehetőséget.** 
4. Az **Adatforrás kiválasztása**entitás hoz a Függő entitás megbízhatóságának hozzáadása varázslóban használja az **Adatok importálása a függő entitásról az interneten vagy a helyi hálózaton közzétett adatok importálása jelölőnégyzetbe**. Adja meg ezt az https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlösszevonási metaadat-URL-címet. Hagyja meg a többi alapértelmezett beállítást. Kattintson a **Bezárás** gombra.
5. Megnyílik **a Jogcímszabályok szerkesztése** varázsló.
6. A **Jogcímszabályok szerkesztése** varázslóban válassza **a Szabály hozzáadása**lehetőséget. A **Szabálytípus kiválasztása**csoportban válassza **az LDAP-attribútumok küldése jogcímként**lehetőséget. Válassza a **Tovább lehetőséget.**
7. A **Jogcímszabály konfigurálása**csoportban adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mail jogcímszabály 
   - **Attribútumtároló**: Active Directory 
   - **LDAP attribútum**: E-mail címek 
   - **Kimenő jogcím típusa:** E-mail cím

8. Válassza a **Finish** (Befejezés) elemet.
9. A **Jogcímszabályok szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra. 
10. Kattintson az **OK** gombra.  

### <a name="create-an-email-transform-rule"></a>E-mail-átalakítási szabály létrehozása
1. Nyissa meg a **Jogcímszabályok szerkesztése menüt,** és kattintson **a Szabály hozzáadása gombra.** A **Szabálytípus kiválasztása**csoportban válassza **a Bejövő jogcím átalakítása** lehetőséget, majd kattintson a **Tovább**gombra. 
2. A **Jogcímszabály konfigurálása**csoportban adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mail-átalakítási szabály 
   - **Bejövő jogcím típusa**: E-mail cím 
   - **Kimenő jogcím típusa**: Névazonosító 
   - **Kimenő névazonosító formátuma**: Állandó azonosító 
   - Válassza **Az összes jogcímérték továbbítása** lehetőséget.

3. Kattintson a **Befejezés** gombra. 
4. A **Jogcímszabályok szerkesztése** ablakban megjelennek az új szabályok. Kattintson az **Alkalmaz** gombra. 
5. Kattintson az **OK** gombra. Az AD FS-kiszolgáló most már az SAML 2.0 protokoll használatával van konfigurálva a közvetlen összevonáshoz.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Az AD FS konfigurálása a WS-Fed közvetlen összevonására 
Az Azure AD B2B konfigurálható úgy, hogy a WS-Fed protokollt az alább felsorolt követelményekkel rendelkező identitásszolgáltatókkal egytelenítsen. Jelenleg a két WS-Fed szolgáltatót tesztelték az Azure AD-vel való kompatibilitás szempontjából, beleértve az AD FS-t és a Shibboleth-et. Itt az Active Directory összevonási szolgáltatásokat (AD FS) használjuk példaként a WS-Fed identitásszolgáltatóra. Ha további információra van haa a WS-Fed-kompatibilis szolgáltatók és az Azure AD közötti függő entitás megbízhatóságának létrehozásáról, töltse le az Azure AD Identity Provider kompatibilitási dokumentumait.

A közvetlen összevonás beállításához a következő attribútumokat kell fogadni a WS-Fed üzenetben az identitásszolgáltatótól. Ezek az attribútumok az online biztonsági tokenszolgáltatás XML-fájljára való hivatkozással vagy manuális beírással konfigurálhatók. 12. lépés: [Teszt létrehozása AD FS példány](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) leírja, hogyan lehet megtalálni az AD FS `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`végpontok, vagy hogyan kell létrehozni a metaadat URL-címet, például .
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner IdP kibocsátóuri-ja, például`http://www.example.com/exk10l6w90DHM0yi...`         |

Az IdP által kiadott WS-Fed tokenhez szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|Nem módosítható azonosító     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A következő szakasz bemutatja, hogyan konfigurálhatja a szükséges attribútumokat és jogcímeket az AD FS használatával a WS-Fed identitásszolgáltató példájaként.

### <a name="before-you-begin"></a>Előkészületek
Az eljárás megkezdése előtt az AD FS-kiszolgálót már be kell állítani és működni kell. Az AD FS-kiszolgáló beállításával kapcsolatban további segítséget az [AD FS 3.0-s példány létrehozása Azure-beli virtuális gépen](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)című témakörben talál.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatósági és jogcímszabályának hozzáadása 
1. Az AD FS-kiszolgálón nyissa meg az **Eszközök** > **AD FS-kezelés című**területet. 
1. A navigációs ablakban válassza a **Bizalmi** > kapcsolatok**függő entitásmegbízhatóságok**lehetőséget. 
1. A **Műveletek csoportban**válassza **a Függő entitás megbízhatóságának hozzáadása lehetőséget.**  
1. A Függő entitás megbízhatóságának hozzáadása varázsló **Adatforrás kiválasztása**csoportjában használja az **Adatok importálása az interneten vagy a helyi hálózaton közzétett függő entitás adatait.** Adja meg ezt az `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`összevonási metaadat-URL-címet: .  Hagyja meg a többi alapértelmezett beállítást. Kattintson a **Bezárás** gombra.
1. Megnyílik **a Jogcímszabályok szerkesztése** varázsló. 
1. A **Jogcímszabályok szerkesztése** varázslóban válassza **a Szabály hozzáadása**lehetőséget. A **Szabálytípus kiválasztása**csoportban válassza **a Jogcímek küldése egyéni szabállyal**lehetőséget. Válassza a *Tovább lehetőséget.* 
1. A **Jogcímszabály konfigurálása**csoportban adja meg a következő értékeket:

   - **Jogcímszabály neve**: Probléma nem módosítható azonosító  
   - **Egyéni szabály**:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Válassza a **Finish** (Befejezés) elemet. 
1. A **Jogcímszabályok szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra.  
1. Ugyanebben a **Jogcímszabályok szerkesztése** varázslóban válassza **a Szabály hozzáadása**lehetőséget. A **Cohose szabálytípusa csoportban**válassza **az LDAP-attribútumok küldése jogcímként**lehetőséget. Válassza a **Tovább lehetőséget.**
1. A **Jogcímszabály konfigurálása**csoportban adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mail jogcímszabály  
   - **Attribútumtároló**: Active Directory  
   - **LDAP attribútum**: E-mail címek  
   - **Kimenő jogcím típusa:** E-mail cím 

1.  Válassza a **Finish** (Befejezés) elemet. 
1.  A **Jogcímszabályok szerkesztése** ablakban megjelenik az új szabály. Kattintson az **Alkalmaz** gombra.  
1.  Kattintson az **OK** gombra. Az AD FS-kiszolgáló most már konfigurálva van a WS-Fed használatával történő közvetlen összevonáshoz.

## <a name="next-steps"></a>További lépések
Ezután [konfigurálja a közvetlen összevonást az Azure AD-ben](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) az Azure AD-portálon vagy a PowerShell használatával. 
