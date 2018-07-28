---
title: Tanúsítvány-megújítási Office 365 és az Azure AD-felhasználók számára |} A Microsoft Docs
description: Ez a cikk ismerteti az Office 365-felhasználók e-mailt, amely értesíti őket egy tanúsítvány megújításával kapcsolatos hibák elhárítása.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4de24608ba9db174f343bf0d78029913e4b7868f
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325683"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>-Office 365 és az Azure Active Directory összevonási tanúsítványainak megújítása
## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) és az Active Directory összevonási szolgáltatások (AD FS) között sikeres összevonáshoz az Azure AD biztonsági jogkivonatok aláírásához az AD FS által használt tanúsítványok egyeznie kell az Azure ad-ben konfigurált. Ha bármilyen eltérés megszakadt bizalmi kapcsolat vezethet. Az Azure AD biztosítja, hogy ezek az információk tárolt szinkronban központi telepítésekor az AD FS és a webalkalmazás-Proxy (az extranetes hozzáféréshez).

Ez a cikk nyújt további információt a jogkivonat-aláíró tanúsítványok kezeléséhez, és szinkronizálja őket az Azure ad-vel, a következő esetekben:

* A webalkalmazás-Proxy nem telepít, és ezért az összevonási metaadatok nem áll rendelkezésre az extranetről.
* Nem használja az alapértelmezett konfiguráció az AD FS jogkivonat-aláíró tanúsítványokat.
* Egy harmadik féltől származó identitásszolgáltatót használja.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Az AD FS jogkivonat-aláíró tanúsítványok alapértelmezett konfigurációja
A jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványokat általában önaláírt tanúsítványokat, és egy évig jó. Alapértelmezés szerint az AD FS tartalmaz egy automatikus megújítás nevű folyamat **AutoCertificateRollover**. Ha az AD FS 2.0-s vagy újabb verzióját használja, az Office 365 és az Azure AD automatikusan frissíti a tanúsítvány lejárata előtt.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Az Office 365 portálon és a egy e-mailt a megújítási értesítés
> [!NOTE]
> Ha kapott e-mailben vagy rákérdez arra, hogy a tanúsítvány megújítása az Office, tekintse meg a portál értesítései [módosításával a jogkivonat-aláíró tanúsítványok](#managecerts) , ellenőrizze, hogy semmit sem kell. A Microsoft a egy lehetséges probléma, amely értesítéseket küld a rendszer, még akkor is, ha semmit nem kell a tanúsítvány megújításához vezethet.
>
>

Az Azure AD megpróbálja az összevonási metaadatok monitorozása, és a jogkivonat-aláíró tanúsítványokat, amint azt a metaadatok frissítése. a jogkivonat-aláíró tanúsítványok lejárta előtt 30 nappal az Azure AD ellenőrzi, ha új tanúsítványok érhetők el az összevonási metaadatok lekérdezésével.

* Ha sikerült lekérdezni az összevonási metaadatok és az új tanúsítványok lekérése, nem e-mail-értesítés vagy az Office 365 portálon figyelmeztetés a felhasználónak jelenik meg.
* Ha nem tudja lekérni az új jogkivonat-aláíró tanúsítványokat, vagy az összevonási metaadatok nem érhető el, vagy nincs engedélyezve az automatikus tanúsítványváltást, mert az Azure AD kibocsát e-mailben értesítést és a egy figyelmeztetés, az Office 365-portálon.

![Az Office 365 portál értesítései](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Ha az AD FS-ben az üzletmenet folytonosságának biztosítása használ, győződjön meg arról, hogy a kiszolgáló rendelkezik-e az alábbi frissítések, így nem fordulhat elő, az ismert problémák hitelesítési hibák. Ez csökkenti az ismert AD FS proxy server kapcsolatos problémát a megújítási és a jövőbeli megújítási időszak:
>
> Server 2012 R2 - [Windows Server 2014. május összegzése](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 és 2012 - [proxyn keresztül történő hitelesítés nem sikerül, a Windows Server 2012 vagy Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Ellenőrizze a tanúsítványokat kell-e frissíteni kell <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>1. lépés: Az autocertificaterollover funkció állapotának ellenőrzése
Az AD FS-kiszolgálóra nyissa meg a Powershellt. Ellenőrizze, hogy az AutoCertificateRollover érték a True értékre van állítva.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Ha az AD FS 2.0 használja, először futtassa az Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>2. lépés: Győződjön meg róla, hogy az AD FS és az Azure AD szinkronizálása
Az AD FS-kiszolgálón nyissa meg az MSOnline PowerShell-parancssort, és az Azure AD connect.

> [!NOTE]
> MSOL-parancsmagok az MSOnline PowerShell-modul részét képezik.
> Az MSOnline PowerShell modul letöltheti közvetlenül a PowerShell-galériából.
> 
>

    Install-Module MSOnline

Csatlakozzon az Azure ad-bA az MSOnline PowerShell-modul.

    Import-Module MSOnline
    Connect-MsolService

Ellenőrizze a tanúsítványok konfigurálása az AD FS és az Azure AD megbízzon a megadott tartomány tulajdonságai.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

A mindkét kimenetekben az ujjlenyomatok egyeznek, ha az Azure ad-val szinkronizálva a tanúsítványai.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>3. lépés: Ellenőrizze, hogy-e a tanúsítvány hamarosan lejár
A Get-MsolFederationProperty vagy a Get-AdfsCertificate kimenetben ellenőrizze az időpontra a "Nem később." Ha a dátum 30 napnál kevesebb azonnal, a művelet kell tennie.

| AutoCertificateRollover | Az Azure ad-vel szinkronizált tanúsítványok | Összevonási metaadatok nyilvánosan elérhető-e | Érvényesség | Műveletek |
|:---:|:---:|:---:|:---:|:---:|
| Igen |Igen |Igen |- |Nincs szükség művelet végrehajtására. Lásd: [automatikus megújítása jogkivonat-aláíró tanúsítvány](#autorenew). |
| Igen |Nem |- |Kevesebb mint 15 napon keresztül |Újítsa meg azonnal. Lásd: [manuális megújítás jogkivonat-aláíró tanúsítvány](#manualrenew). |
| Nem |- |- |30 napnál kevesebb |Újítsa meg azonnal. Lásd: [manuális megújítás jogkivonat-aláíró tanúsítvány](#manualrenew). |

\[Nem számít,-]

## A jogkivonat-aláíró tanúsítvány automatikus megújítása (ajánlott) <a name="autorenew"></a>
Nem kell hajtsa végre az összes manuális lépést, ha a következőkre mindegyike teljesül:

* Telepítette a webalkalmazás-Proxy, amely engedélyezheti az extranetből az összevonási metaadatok hozzáférést.
* Használja az AD FS alapértelmezett konfigurációt (tehát az autocertificaterollover funkció engedélyezve van).

Ellenőrizze a következőket győződjön meg arról, hogy a tanúsítvány automatikusan frissíthetők legyenek.

**1. Az AD FS tulajdonság autocertificaterollover beállítást igaz értékre kell állítani.** Ez azt jelzi, hogy az AD FS automatikusan hoz létre új jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványok, a régi előtt kiépítettektől lejár.

**2. Az AD FS összevonási metaadatainak nyilvánosan elérhető-e.** Ellenőrizze, hogy az összevonási metaadatok nyilvánosan elérhető-e a nyilvános interneten (engedményt a vállalati hálózat) egy számítógépről a következő URL-cím megnyitásával:

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

ahol `(your_FS_name) `a szervezet használja, például fs.contoso.com összevonási szolgáltatás állomásnévvel váltja fel.  Ha Ön is ellenőrizheti, ezek a beállítások sikeresen, nincs teendője.  

Példa: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## A jogkivonat-aláíró tanúsítványának manuális megújítása <a name="manualrenew"></a>
Előfordulhat, hogy a jogkivonat-aláíró tanúsítványok manuális megújítás választja. Ha például a következő esetekben előfordulhat, hogy továbbfejlesztésében manuális megújítás:

* Jogkivonat-aláíró tanúsítványok nem önaláírt tanúsítványokat is. Ennek leggyakoribb oka az, hogy a munkahelye felügyeli-e az AD FS-tanúsítványok regisztrált egy vállalati hitelesítésszolgáltatótól.
* Hálózati biztonság nem engedélyezi az összevonási metaadatok kell nyilvánosan elérhetőnek lenniük.

Ezekben a forgatókönyvekben minden alkalommal, amikor frissíti a jogkivonat-aláíró tanúsítványokat, is frissítenie kell az Office 365-tartomány Update-MsolFederatedDomain PowerShell-parancs használatával.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>1. lépés: Győződjön meg arról, hogy az AD FS rendelkezik-e az új jogkivonat-aláíró tanúsítványok
**Nem alapértelmezett konfigurációja**

Ha egy nem alapértelmezett AD FS konfigurációját használja (ahol **AutoCertificateRollover** értékre van állítva **hamis**), valószínűleg használ (nem önaláírt) egyéni tanúsítványokat. Az AD FS jogkivonat-aláíró tanúsítványok megújítása kapcsolatos további információkért lásd: [az ügyfelek nem használja az AD FS önaláírt tanúsítványok](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Összevonási metaadatok nem érhető el nyilvánosan**

Másrészről Ha **AutoCertificateRollover** értékre van állítva **igaz**, azonban az összevonási metaadatok nem érhető el nyilvánosan, először győződjön meg arról, hogy új jogkivonat-aláíró tanúsítványok az AD FS által létrehozott. Győződjön meg arról, új jogkivonat-aláíró tanúsítványok az alábbi lépések megtételével rendelkezik:

1. Győződjön meg arról, hogy van bejelentkezve az elsődleges AD FS-kiszolgálóra.
2. A jelenlegi aláíró tanúsítványok az AD FS ellenőrzéséhez nyissa meg egy PowerShell-parancsablakot, és futtassa a következő parancsot:

    PS C:\>Get-ADFSCertificate – CertificateType jogkivonat-aláíró

   > [!NOTE]
   > Ha az AD FS 2.0 használ, Add-Pssnapin Microsoft.Adfs.Powershell először érdemes futtatnia.
   >
   >
3. Tekintse meg a parancs kimenete a felsorolt tanúsítványokat. Az AD FS generált új tanúsítványt, ha két tanúsítványt a kimenetben az: egyet, amelynek a **IsPrimary** érték **igaz** és a **NotAfter** dátum 5 napon belül van , és a egy, amelynek **IsPrimary** van **hamis** és **NotAfter** körülbelül egy évre előre.
4. Ha csak egy tanúsítványt, és a **NotAfter** 5 napon belül dátum, létre kell hoznia egy új tanúsítványt.
5. Létrehoz egy új tanúsítványt, hajtsa végre a következő parancsot egy PowerShell parancssorában: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. A frissítés ellenőrizze újra a következő parancs futtatásával: PS C:\>Get-ADFSCertificate – CertificateType jogkivonat-aláíró

Most már két tanúsítványt kell szerepelnie, amelyek közül az egyik rendelkezik egy **NotAfter** körülbelül egy évet a jövőben a, és amelyekre a **IsPrimary** érték **hamis**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>2. lépés: Az új jogkivonat-aláíró tanúsítványok az Office 365 bizalmi kapcsolat frissítése
Frissítse az új jogkivonat-aláíró tanúsítványokat kell használni, a bizalmi kapcsolat a következő Office 365-höz.

1. Nyissa meg a Microsoft Azure Active Directory-modul Windows Powershellhez készült.
2. Run $cred=Get-Credential. Ha ez a parancsmag kéri a hitelesítő adatokat, írja be a felhőalapú szolgáltatás rendszergazdai fiók hitelesítő adatait.
3. Futtassa a Connect-MsolService – hitelesítőadat-$cred. Ezzel a parancsmaggal csatlakozik a felhőszolgáltatáshoz. Környezet, amely csatlakoztatja a felhőalapú szolgáltatás létrehozása előtt meg kell adni az eszköz által telepített további parancsmagokat egyikét futtatja.
4. Ha ezeket a parancsokat futtat egy számítógép, amely nem az AD FS elsődleges összevonási kiszolgálón, futtassa a Set-MSOLAdfscontext-számítógép &lt;elsődleges AD FS-kiszolgáló&gt;, ahol &lt;elsődleges AD FS-kiszolgáló&gt; van a belső teljes Tartományneve az elsődleges AD FS-kiszolgáló neve. Ez a parancsmag létrehoz egy környezetet, amely csatlakoztatja az AD FS-hez.
5. Futtassa az Update-MSOLFederatedDomain-DomainName &lt;tartomány&gt;. Ez a parancsmag frissíti a felhőalapú szolgáltatásba az AD FS beállításokat, és konfigurálja a kettő közötti bizalmi kapcsolat.

> [!NOTE]
> Ha támogatja a több legfelső szintű tartományok, mint például a contoso.com és fabrikam.com, módosítania kell használnia a **SupportMultipleDomain** váltson, azok a parancsmagok. További információkért lásd: [több felső szintű tartomány támogatása](active-directory-aadconnect-multiple-domains.md).
>


## Az Azure AD-megbízhatóság javítása az Azure AD Connect használatával <a name="connectrenew"></a>
Ha az AD FS-farm és az Azure AD-megbízhatóság konfigurált Azure AD Connect használatával, az Azure AD Connect használatával észleli, ha szeretné-e a jogkivonat-aláíró tanúsítványok bármilyen művelet végrehajtása. Ha a tanúsítványok megújítása van szüksége, használhatja az Azure AD Connect ehhez.

További információkért lásd: [a bizalmi kapcsolat javítása](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Az AD FS és az Azure AD tanúsítványszolgáltatások frissítési lépések
Jogkivonat-aláíró tanúsítványok vannak standard X509, amely az összevonási kiszolgáló összes jogkivonat biztonságos aláírásához használt tanúsítványok. Jogkivonat-visszafejtési tanúsítványok olyan szabványos X509 minden olyan bejövő jogkivonatot visszafejtéséhez használt tanúsítványok. 

Alapértelmezés szerint az AD FS jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványok, automatikus létrehozásához a kezdeti konfigurációs időben és amikor a tanúsítványok lejárati hamarosan eléri van konfigurálva.

Az Azure AD megpróbálja beolvasni új tanúsítványt az összevonási szolgáltatás metaadat 30 nappal az aktuális tanúsítvány lejárta előtt. Abban az esetben egy új tanúsítvány jelenleg nem érhető el, Azure ad-ben továbbra is a metaadatok figyelése napi rendszeres időközönként. Amint az új tanúsítványt a metaadatok között érhető el, a tartomány összevonási beállításainak frissülnek az új tanúsítvány információt. Használhat `Get-MsolDomainFederationSettings` ellenőrzése, ha megjelenik az új tanúsítvány található a NextSigningCertificate / SigningCertificate.

További információ a jogkivonat-aláíró tanúsítványok az AD FS: [beszerzése és konfigurálása jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványok az AD FS-hez](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
