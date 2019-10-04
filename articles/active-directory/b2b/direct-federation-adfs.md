---
title: Állítsa be a B2B - Azure Active Directory-hez és a egy AD FS közvetlen összevonási |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az AD FS közvetlen összevonási Identitásszolgáltatóként, így a vendégek jelentkezhetnek be az Azure AD-alkalmazások
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
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544321"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Példa: Az Active Directory összevonási szolgáltatások (AD FS) közvetlen összevonási (előzetes verzió)
|     |
| --- |
| Közvetlen összevonási az Azure Active Directory nyilvános előzetes verziójú funkció. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk azt ismerteti, hogyan állítható be [összevonási közvetlen](direct-federation.md) SAML 2.0 vagy WS-Fed identitás-szolgáltatóként az Active Directory összevonási szolgáltatások (AD FS) használatával. Közvetlen összevonási támogatása érdekében egyes attribútumokról és jogcímekről kell konfigurálni, az identitásszolgáltató. Közvetlen összevonási identitásszolgáltatót konfigurálása mutatja be, az Active Directory összevonási szolgáltatások (AD FS) fogjuk használni példaként. Bemutatjuk, hogyan állítható be az AD FS SAML identitás-szolgáltatóként, mind a WS-Fed identitás-szolgáltatóként.

> [!NOTE]
> Ez a cikk ismerteti, hogyan állítsa be az AD FS SAML mind a WS-Fed illusztrációs célokat szolgálnak. Közvetlen összevonási Integrációk, amelyben az identitásszolgáltató-e az AD FS javasoljuk hogy WS-Fed protokollként. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>SAML 2.0 direct összevonás az AD FS konfigurálása
Az Azure AD B2B identitásszolgáltatókkal, az alább felsorolt különleges követelményeket az SAML protokollt használó összevonásához konfigurálható. A SAML-konfigurációs lépések bemutatják, hogy ez a szakasz bemutatja, hogyan SAML 2.0-hoz készült Active Directory összevonási szolgáltatások beállítása. 

Közvetlen összevonási beállításával kapcsolatban a következő attribútumokkal kell érkeznie az identitásszolgáltatótól az SAML 2.0 válaszként. Ezek az attribútumok konfigurálható az online biztonsági jogkivonatokkal kapcsolatos szolgáltatástól XML-fájl összekapcsolása, vagy írja be őket manuálisan. 12. lépés [teszt az AD FS-példány létrehozása](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) ismerteti, hogyan találhatja meg az AD FS-végpontok vagy a metaadatok URL-CÍMÉT, például létrehozása `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A kibocsátó URI-t a partner identitásszolgáltató, például `http://www.example.com/exk10l6w90DHM0yi...`         |

Az identitásszolgáltató által kibocsátott SAML 2.0 jogkivonat konfigurálni kell a következő jogcímek:


|Attribútum  |Érték  |
|---------|---------|
|NameID Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Ez a szakasz azt ábrázolja, hogyan konfigurálja a szükséges attribútumok és a jogcímek az AD FS használatával a SAML 2.0 identitásszolgáltató példaként.

### <a name="before-you-begin"></a>Előkészületek

Az AD FS-kiszolgáló már állítson fel és működik, ez az eljárás megkezdése előtt. Segítség az AD FS-kiszolgáló beállítása: [teszt AD FS 3.0 példányt hoz létre egy Azure virtuális gép](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Az igényt leírás hozzáadása

1. Válassza ki az AD FS-kiszolgáló **eszközök** > **AD FS-kezelőben**.
2. A navigációs panelen válassza ki **szolgáltatás** > **Jogcímleírások**.
3. A **műveletek**válassza **Jogcímleírások hozzáadása**.
4. Az a **adja hozzá a Jogcímleírások** ablakban adja meg a következő értékeket:

   - **Megjelenítendő név**: Állandó azonosítója
   - **Jogcím-azonosító**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Jelölje be a jelölőnégyzetet a **a jogcímleírások közzététele az összevonás metaadataiban az összevonási szolgáltatás elfogadó jogcím típusaként**.
   - Jelölje be a jelölőnégyzetet a **a jogcímleírások közzététele az összevonás metaadataiban az összevonási szolgáltatás küldhet egy jogcím típusaként**.

5. Kattintson az **OK** gombra.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatóságának hozzáadása és jogcímszabályok

1. Az AD FS-kiszolgálón, lépjen a **eszközök** > **AD FS-kezelőben**.
2. A navigációs panelen válassza ki **megbízhatósági kapcsolatok** > **függő entitás Megbízhatóságai**.
3. A **műveletek**válassza **függő entitás megbízhatóságának hozzáadása**. 
4. A Hozzáadás függő entitás megbízhatósági varázslóban **adatforrás kiválasztása**, használja a kapcsolót **online vagy a helyi hálózaton közzétett adatok importálása a függő entitással kapcsolatos**. Adja meg az összevonási metaadatok URL-cím - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Hagyja meg az egyéb alapértelmezett beállításokat. Válassza ki **Bezárás**.
5. A **Jogcímszabályok szerkesztése** megnyílik a varázsló.
6. Az a **Jogcímszabályok szerkesztése** varázslóban válassza **szabály hozzáadása**. A **szabály típusának kiválasztása**válassza **LDAP attribútumok küldése jogcímekként**. Kattintson a **Tovább** gombra.
7. A **Jogcímszabály konfigurálása**, adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mail jogcímszabályt 
   - **Attribútumtár**: Active Directory 
   - **LDAP attribútum**: E-Mail-Addresses 
   - **Kimenő jogcím típusa**: E-Mail cím

8. Válassza a **Finish** (Befejezés) elemet.
9. A **Jogcímszabályok szerkesztése** ablakban jelennek meg az új szabályt. Kattintson az **Alkalmaz** gombra. 
10. Kattintson az **OK** gombra.  

### <a name="create-an-email-transform-rule"></a>E-mailek átalakítási szabály létrehozása
1. Lépjen a **Jogcímszabályok szerkesztése** kattintson **szabály hozzáadása**. A **szabály típusának kiválasztása**válassza **bejövő jogcím átalakítása** kattintson **tovább**. 
2. A **Jogcímszabály konfigurálása**, adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mailek átalakítási szabály 
   - **Bejövő jogcím típusa**: E-mail cím 
   - **Kimenő jogcím típusa**: Névazonosító 
   - **Kimenő Névazonosító formátuma**: Állandó azonosítója 
   - Válassza **Az összes jogcímérték továbbítása** lehetőséget.

3. Kattintson a **Befejezés**gombra. 
4. A **Jogcímszabályok szerkesztése** ablakban jelennek meg az új szabályokat. Kattintson az **Alkalmaz** gombra. 
5. Kattintson az **OK** gombra. Az AD FS-kiszolgáló lett konfigurálva a közvetlen összevonási az SAML 2.0 protokoll használatával.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>WS-Fed közvetlen összevonás az AD FS konfigurálása 
Az Azure AD B2B identitásszolgáltatókat, amelyek a WS-Fed protokollt használja az alább felsorolt különleges követelményeket az összevonni kívánt konfigurálható. A két WS-Fed szolgáltató jelenleg teszteltük, az Azure AD-kompatibilitás az AD FS és a Shibboleth tartalmazzák. Az Active Directory összevonási szolgáltatások (AD FS) itt, például a WS-Fed identitásszolgáltató fogjuk használni. Között WS-Fed megfelelő szolgáltató és az Azure AD függő entitások megbízhatóságainak létrehozásával kapcsolatos további információkért töltse le az Azure AD Identity Provider kompatibilitási Docs.

Közvetlen összevonási beállításával kapcsolatban a következő attribútumokkal kell érkeznie, a WS-Fed üzenetben az identitásszolgáltatótól. Ezek az attribútumok konfigurálható az online biztonsági jogkivonatokkal kapcsolatos szolgáltatástól XML-fájl összekapcsolása, vagy írja be őket manuálisan. 12. lépés [teszt az AD FS-példány létrehozása](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) ismerteti, hogyan találhatja meg az AD FS-végpontok vagy a metaadatok URL-CÍMÉT, például létrehozása `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A kibocsátó URI-t a partner identitásszolgáltató, például `http://www.example.com/exk10l6w90DHM0yi...`         |

A WS-Fed token az identitásszolgáltató által kiadott jogcímeket szükséges:

|Attribútum  |Érték  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

Ez a szakasz azt ábrázolja, hogyan konfigurálhatja a szükséges attribútumokkal, és a WS-Fed identitásszolgáltató példaként használva az AD FS-jogcímek.

### <a name="before-you-begin"></a>Előkészületek
Az AD FS-kiszolgáló már állítson fel és működik, ez az eljárás megkezdése előtt. Segítség az AD FS-kiszolgáló beállítása: [teszt AD FS 3.0 példányt hoz létre egy Azure virtuális gép](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>A függő entitás megbízhatóságának hozzáadása és jogcímszabályok 
1. Az AD FS-kiszolgálón, lépjen a **eszközök** > **AD FS-kezelőben**. 
1. A navigációs panelen válassza ki **megbízhatósági kapcsolatok** > **függő entitás Megbízhatóságai**. 
1. A **műveletek**válassza **függő entitás megbízhatóságának hozzáadása**.  
1. Függő entitás megbízhatósági kapcsolat varázslót, a hozzáadása **adatforrás kiválasztása**, használja a kapcsolót **online vagy a helyi hálózaton közzétett adatok importálása a függő entitással kapcsolatos**. Adja meg az összevonási metaadatok URL-cím: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Hagyja meg az egyéb alapértelmezett beállításokat. Válassza ki **Bezárás**.
1. A **Jogcímszabályok szerkesztése** megnyílik a varázsló. 
1. Az a **Jogcímszabályok szerkesztése** varázslóban válassza **szabály hozzáadása**. A **szabály típusának kiválasztása**válassza **jogcímek küldése egyéni szabállyal egy**. Kattintson a *Tovább* gombra. 
1. A **Jogcímszabály konfigurálása**, adja meg a következő értékeket:

   - **Jogcímszabály neve**: A probléma nem módosítható azonosító  
   - **Egyéni szabály**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Válassza a **Finish** (Befejezés) elemet. 
1. A **Jogcímszabályok szerkesztése** ablakban jelennek meg az új szabályt. Kattintson az **Alkalmaz** gombra.  
1. Az egyazon **Jogcímszabályok szerkesztése** varázslóban válassza **szabály hozzáadása**. A **Cohose szabálytípus**válassza **LDAP attribútumok küldése jogcímekként**. Kattintson a **Tovább** gombra.
1. A **Jogcímszabály konfigurálása**, adja meg a következő értékeket: 

   - **Jogcímszabály neve**: E-mail jogcímszabályt  
   - **Attribútumtár**: Active Directory  
   - **LDAP attribútum**: E-Mail-Addresses  
   - **Kimenő jogcím típusa**: E-Mail cím 

1.  Válassza a **Finish** (Befejezés) elemet. 
1.  A **Jogcímszabályok szerkesztése** ablakban jelennek meg az új szabályt. Kattintson az **Alkalmaz** gombra.  
1.  Kattintson az **OK** gombra. Az AD FS-kiszolgáló közvetlen összevonási WS-Fed használatával lett konfigurálva.

## <a name="next-steps"></a>További lépések
Ezután fogja [közvetlen összevonás konfigurálása az Azure ad-ben](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) vagy az Azure AD portálon vagy a PowerShell használatával. 
