---
title: Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016 eszközök |} A Microsoft Docs
description: Hibaelhárítás az Azure Active Directory hibrid-hez csatlakoztatott eszközök a Windows 10 és Windows Server 2016-ban.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb7dc356c8101c1b0907818b45618ef6372c691
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517436"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016-eszközök 

Ez a cikk a következő ügyfelekre alkalmazható:

-   Windows 10
-   Windows Server 2016

Más Windows-ügyfelek számára, lásd: [hibaelhárítás hibrid Azure Active Directory-hez csatlakoztatott eszközök alacsonyabb szintű](troubleshoot-hybrid-join-windows-legacy.md).

Ez a cikk feltételezi, hogy [konfigurált hibrid Azure Active Directory-hez csatlakoztatott eszközök](hybrid-azuread-join-plan.md) támogatásához a következő esetekben:

- Eszközalapú feltételes hozzáférés

- [Vállalati barangolás beállításai](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Vállalati Windows Hello](../active-directory-azureadjoin-passport-deployment.md)


Ez a dokumentum nyújt hibaelhárítási útmutatót kapcsolatos lehetséges problémák megoldását. 


A Windows 10 és Windows Server 2016, a hibrid Azure Active Directory join támogatja a Windows 10, 2015 novemberi frissítés vagy újabb verzió. Az Évfordulós frissítés használatát javasoljuk.

## <a name="step-1-retrieve-the-join-status"></a>1. lépés: A csatlakozás állapotának lekérése 

**A csatlakozás állapotának lekéréséhez:**

1. Nyissa meg a parancssort rendszergazdaként

2. Típus **dsregcmd/status**



```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```



## <a name="step-2-evaluate-the-join-status"></a>2. lépés: A csatlakozás állapota kiértékelése 

Tekintse át a következő mezőket, és győződjön meg arról, hogy rendelkeznek-e a várt értékek:

### <a name="azureadjoined--yes"></a>AzureAdJoined : IGEN  

Ez a mező jelzi, hogy az eszköz csatlakozott-e az Azure ad-ben. Ha az érték **nem**, az Azure ad join még nem fejeződött be. 

**Lehetséges okok:**

- A számítógép a csatlakozzon a hitelesítés sikertelen.

- A szervezet, amely nem a számítógép felderítette van egy HTTP-proxy

- A számítógép nem érhető el a hitelesítést az Azure AD vagy az Azure DRS-regisztráció

- A számítógép nem a szervezet belső hálózati vagy VPN-lehet közvetlen üzemel egy a helyszíni AD-tartományvezérlő.

- Ha a számítógép TPM, rossz állapotban lehet.

- Van egy kiszolgálóhiba, a szolgáltatások a dokumentum a korábban feljegyzett, hogy ki kell újra ellenőrzése. Néhány gyakori példa:

    - Az összevonási kiszolgálón nincs engedélyezve a WS-Trust végpontok

    - Az összevonási kiszolgálón nem engedélyezi a számítógépekről érkező bejövő hitelesítés integrált Windows-hitelesítés használata a hálózaton.

    - Nem, amely az ellenőrzött tartomány nevét az Azure ad-ben az AD-erdőhöz, ahol a számítógép tartozik a Szolgáltatáskapcsolati pont objektum

---

### <a name="domainjoined--yes"></a>DomainJoined: IGEN  

Ez a mező jelzi, hogy az eszköz csatlakozott a helyszíni Active Directory-e. Ha az érték **nem**, az eszköz nem hajtható végre, a hibrid Azure AD-csatlakozás.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Ez a mező jelzi, hogy az eszköz regisztrálva van-e személyes eszközként az Azure AD-vel (megjelölve *munkahelyhez csatlakoztatott*). Ennek az értéknek kell lennie **nem** egy tartományhoz csatlakoztatott számítógép, amely egyben hibrid Azure AD-csatlakoztatott. Ha az érték **Igen**, egy munkahelyi vagy iskolai fiókot hozzá lett adva, a hibrid Azure AD-csatlakozás a befejezése előtt. Ebben az esetben a fiók figyelmen kívül hagyja az Évfordulós frissítés Windows 10 (1607-es) verziójának használatakor.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Igen, és AzureADPrt: IGEN
  
Ezek a mezők azt jelzik, hogy a felhasználó sikeresen hitelesítést az Azure AD az eszközre való bejelentkezéskor. Ha az értékek **nem**, előfordulhat, hogy onnan:

- Hibás tárkulcs (Találkozhatunk) (Ellenőrizze az emelt szintű futtatás közben KeySignTest) regisztráláskor az eszközhöz társított TPM-ben.

- Alternatív bejelentkezési Azonosítóval

- A HTTP-Proxy nem található.

## <a name="next-steps"></a>További lépések

Ha kérdése van, tekintse meg a [Eszközfelügyelet – gyakori kérdések](faq.md) 
