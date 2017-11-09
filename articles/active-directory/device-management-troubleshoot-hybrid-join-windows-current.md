---
title: "Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016-os eszközök |} Microsoft Docs"
description: "Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016-os eszközök."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ffce288555e718b37882f30f877162d0c0467f1f
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016-os eszközök 

Ez a témakör a következő alkalmazható a következő ügyfelekre:

-   Windows 10
-   Windows Server 2016

Egyéb Windows-ügyfelein, lásd: [hibaelhárítás hibrid Azure Active Directoryhoz csatlakoztatott régebbi eszközök](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Ez a témakör azt feltételezi, hogy [konfigurált hibrid Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md) a következő forgatókönyvek támogatása céljából:

- Eszközalapú feltételes hozzáférés

- [Vállalati központi beállítások](active-directory-windows-enterprise-state-roaming-overview.md)

- [Vállalati Windows Hello](active-directory-azureadjoin-passport-deployment.md)


Ez a dokumentum a lehetséges problémák megoldásához nyújt hibaelhárítási útmutatót. 


Windows 10 és Windows Server 2016, az Azure Active Directory join hibrid támogatja, a Windows 2015. November 10. frissítés vagy újabb verzió. A évforduló frissítés használatát javasoljuk.

## <a name="step-1-retrieve-the-join-status"></a>1. lépés: Az illesztés állapotának lekérése 

**Az illesztési állapotának lekérése:**

1. Nyissa meg a parancssort rendszergazdaként

2. Típus **dsregcmd/status**



    +----------------------------------------------------------------------+
   | Az eszköz állapotát |}+----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Nincs DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 ujjlenyomat: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform kriptográfiai szolgáltató TpmProtected: Igen KeySignTest:: futtatásához emelt szintű teszteléséhez.
                  IDP: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0-ás JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0-ás KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: Igen tartománynév: CONTOSO
    
    +----------------------------------------------------------------------+
   | A felhasználói állapot |}+----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: szervezetek WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd) AzureAdPrt: Igen



## <a name="step-2-evaluate-the-join-status"></a>2. lépés: Csatlakozás állapotának kiértékelésére. 

Tekintse át a következő mezőket, és győződjön meg arról, hogy rendelkezik-e a várt értékek:

### <a name="azureadjoined--yes"></a>AzureAdJoined: Igen  

Ez a mező jelzi, hogy az eszköz csatlakozott-e az Azure ad-val. Ha az érték **nem**, az Azure AD join még nem fejeződött be. 

**Lehetséges okok:**

- A számítógép a csatlakozzon a hitelesítés sikertelen.

- Nincs olyan szervezet, amely a számítógép nem észlelhetők egy HTTP-proxy

- A számítógép nem érhető el az Azure AD-hitelesítés vagy Azure DRS a regisztrációhoz

- A számítógép nem lehet a szervezet belső hálózaton vagy a VPN a közvetlen sor a láthatáron egy a helyszíni AD-tartományvezérlő.

- Ha a számítógép TPM-mel, az állapota lehet.

- Előfordulhat, a szolgáltatások helytelen beállítása a dokumentum korábban feljegyzett, hogy újra ellenőrizni kell. Gyakori példák:

    - Az összevonási kiszolgálón nincs engedélyezve a WS-Trust végpontok

    - Az összevonási kiszolgálón nem engedélyezi a bejövő hitelesítést számítógépekről integrált Windows-hitelesítés segítségével a hálózaton.

    - Nincs szolgáltatáskapcsolódási pont objektum mutat, a ellenőrzött tartomány nevét az Azure ad-ben az Active Directory-erdőben, ahol a számítógép tartozik

---

### <a name="domainjoined--yes"></a>DomainJoined: Igen  

Ez a mező jelzi, hogy az eszköz csatlakozott a helyi Active Directory-e. Ha az érték **nem**, az eszköz nem tudja végrehajtani a Azure AD hibrid csatlakozzon.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: nincs  

Ez a mező jelzi, hogy az eszköz regisztrálva van-e az Azure AD egy személyes eszközként (jelölésű *munkahelyhez csatlakoztatott*). Ez az érték legyen **nem** egy tartományhoz csatlakozó számítógép, amely egyúttal az Azure AD hibrid csatlakoztatva. Ha az érték **Igen**, a munkahelyi vagy iskolai fiók hozzá lett adva, az Azure AD hibrid csatlakozási befejezése előtt. Ebben az esetben a fiók rendszer figyelmen kívül hagyja a Windows 10 (1607) évforduló frissítés verzióját használja.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Igen és AzureADPrt: Igen
  
Ezek a mezők azt jelzi, hogy az Azure AD rendelkezik sikeresen hitelesíteni a felhasználót az eszköz történő bejelentkezéskor. Ha az értékek **nem**, annak oka az lehet esedékes:

- Hibás tárolási kulcs (STK) az eszköz regisztrálásakor (Ellenőrizze az emelt szintű futtatása közben KeySignTest) társított TPM.

- Másodlagos bejelentkezési Azonosítóval

- HTTP-Proxy nem található.

## <a name="next-steps"></a>Következő lépések

Kérdéseit, tekintse meg a [eszköz felügyeleti kapcsolatos gyakori kérdések](device-management-faq.md) 