---
title: A tanúsítvány megújítása az Office 365 és az Azure AD felhasználók |} Microsoft Docs
description: Ez a cikk ismerteti az Office 365-felhasználók e-mailt, amely értesíti azokat a tanúsítvány megújításával kapcsolatos problémák megoldásához.
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
ms.author: billmath
ms.openlocfilehash: f0435f1c5aae9381c76441b1233a47799af94768
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Office 365 és az Azure Active Directory összevonási tanúsítványok megújítása
## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) és Active Directory összevonási szolgáltatások (AD FS) közötti sikeres összevonáshoz az Azure ad biztonsági jogkivonatok aláírásához az AD FS által használt tanúsítványok illeszkednie kell az Azure ad-ben konfigurált. Bármely eltérés megszakadt bizalmi kapcsolat vezethet. Az Azure AD gondoskodik arról, hogy ezek az információk tárolódik szinkronban központi telepítésekor az AD FS és a webalkalmazás-Proxy (az extranetes hozzáféréshez).

Ez a cikk nyújt további információt a jogkivonat-aláíró tanúsítványok kezelése és szinkronban maradjanak az Azure ad-vel, a következő esetekben:

* A webalkalmazás-Proxy nem telepít, és ezért az összevonási metaadatok nem érhető el az extranetről.
* Az alapértelmezett konfigurációt, az AD FS jogkivonat-aláíró tanúsítványok nem használ.
* Egy külső identitásszolgáltatótól használ.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Az AD FS jogkivonat-aláíró tanúsítványok az alapértelmezett konfiguráció
A jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványokat általában önaláírt tanúsítványokat, és egy évig helyes. Alapértelmezés szerint az AD FS tartalmaz egy automatikus megújítási nevezett folyamat **autocertificaterollover beállítást**. Ha az AD FS 2.0-s vagy újabb verzióját használja, Office 365 és az Azure AD automatikusan frissíti a tanúsítvány lejárata előtt.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Az Office 365 portálra vagy e-mailt megújítási értesítés
> [!NOTE]
> Ha kapott e-mailben vagy kéri, hogy a tanúsítvány megújítása az Office, tekintse meg a portál értesítései [jogkivonat-aláíró tanúsítványok módosításai kezelése](#managecerts) való ellenőrizze, hogy semmilyen művelet végrehajtására. Microsoft vezethet a tanúsítvány megújításához küldi el, akkor is, ha nincs teendő értesítések lehetséges problémát tisztában.
>
>

Az Azure AD megkísérli a figyelheti az összevonási metaadatok, és a jogkivonat-aláíró tanúsítványok, ahogy azt a metaadatok frissítéséhez. a jogkivonat-aláíró tanúsítványok lejárta előtt 30 nappal az Azure AD ellenőrzi, ha új érhetők el tanúsítványok az összevonási metaadatok lekérdezésével.

* Ha sikeresen kérdezze le az összevonási metaadatok és az új tanúsítványok beolvasása, nem e-mailben értesítést vagy az Office 365 portál figyelmeztetés a felhasználó számára jelenik meg.
* Ha az új jogkivonat-aláíró tanúsítványok nem olvashatók be, vagy az összevonási metaadatok nem érhető el, vagy nincs engedélyezve az automatikus tanúsítványváltást, mert az Azure AD kibocsát egy e-mail értesítések és az Office 365 portál figyelmeztetés.

![Az Office 365 portál értesítései](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Ha az AD FS-ben az üzletmenet folytonosságának biztosítása használ, győződjön meg arról, hogy a kiszolgáló rendelkezik-e a következő frissítéseket, úgy, hogy nem történik meg, az ismert problémák a hitelesítési hibák száma. Ez csökkenti az ismert AD FS proxy server kapcsolatos problémát a megújítási és későbbi megújítási időszakok:
>
> Server 2012 R2 - [Windows Server előfordulhat, hogy 2014 összegzése](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 és a 2012 - [proxyn keresztül történő hitelesítés nem sikerül, a Windows Server 2012 vagy Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Ha a tanúsítványok frissítenie kell ellenőrzése <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>1. lépés: Ellenőrizze az autocertificaterollover beállítást
Az AD FS kiszolgálón nyissa meg a Powershellt. Ellenőrizze, hogy az autocertificaterollover beállítást értéke TRUE.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Ha az AD FS 2.0 használ, előbb futtassa az Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>2. lépés: Győződjön meg róla, hogy az AD FS és az Azure AD Szinkronizáló
Az AD FS kiszolgálón nyissa meg az Azure AD PowerShell-parancssorba, és az Azure AD connect.

> [!NOTE]
> Letöltheti az Azure AD PowerShell [Itt](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Ellenőrizze a tanúsítványok az AD FS és az Azure AD megbízik a megadott tartomány tulajdonságai.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Ha mindkét a kimenetek az ujjlenyomatok egyeznek, a tanúsítványok szinkronban az Azure AD-val.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>3. lépés: Ellenőrizze, hogy ha a tanúsítvány érvényessége hamarosan lejár
A Get-MsolFederationProperty vagy a Get-AdfsCertificate kimenetét ellenőrizze az időpontra a "Nem után." Ha a dátumot 30 napon belül számítógépnél, kell hajtsa végre a műveletet.

| AutoCertificateRollover | Az Azure ad-vel szinkronizált tanúsítványok | Az összevonási metaadatok nyilvánosan elérhető | Érvényesség | Műveletek |
|:---:|:---:|:---:|:---:|:---:|
| Igen |Igen |Igen |- |Nincs szükség művelet végrehajtására. Lásd: [automatikusan megújítási jogkivonat-aláíró tanúsítvány](#autorenew). |
| Igen |Nem |- |Kevesebb, mint 15 nappal |Azonnal újítsa meg. Lásd: [manuális megújítása jogkivonat-aláíró tanúsítvány](#manualrenew). |
| Nem |- |- |30 napon belül |Azonnal újítsa meg. Lásd: [manuális megújítása jogkivonat-aláíró tanúsítvány](#manualrenew). |

\[Nem számít,-]

## Újítsa meg a tokent aláíró tanúsítványa automatikusan (ajánlott) <a name="autorenew"></a>
Nincs szükség semmilyen manuális lépések végrehajtását a következő két teljesülése esetén:

* A webalkalmazás-Proxy, amelyek lehetővé teszik, hogy az összevonási metaadatok az extranetről érkező telepített.
* Az AD FS alapértelmezett konfigurációt (tehát az autocertificaterollover beállítást engedélyezve van) használ.

Ellenőrizze a következőket győződjön meg arról, hogy a tanúsítvány automatikusan frissíthető.

**1. Az AD FS tulajdonság autocertificaterollover beállítást igaz értékre kell állítani.** Ez azt jelzi, hogy az AD FS automatikusan hoz létre, új jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványok, a régi előtt kiépítettektől eltérő lejár.

**2. Az AD FS összevonási metaadatok nyilvánosan elérhető.** Ellenőrizze, hogy az összevonási metaadatok nyilvánosan elérhető nyissa meg a következő URL-cím (ki a vállalati hálózat) a nyilvános interneten számítógépről:

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

Ha `(your_FS_name) `a szervezet használja, például: fs.contoso.com összevonási szolgáltatás állomásnévvel váltja fel.  Ha Ön is ellenőrizheti a beállítások sikeresen, Önnek nincs további lépésekre.  

Példa: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## A jogkivonat-aláíró manuálisan a tanúsítvány megújítása <a name="manualrenew"></a>
Előfordulhat, hogy szeretné megújítani a jogkivonat-aláíró tanúsítványokat manuálisan. Például a következő esetekben előfordulhat, hogy jobban használható manuális megújítási:

* Jogkivonat-aláíró tanúsítványok nem önaláírt tanúsítványokat is. Ennek leggyakoribb oka az, hogy a szervezet kezeli-e az AD FS-tanúsítványok egy szervezeti hitelesítésszolgáltatótól származó regisztrálva.
* Hálózati biztonság nem teszi lehetővé az összevonási metaadatok nyilvánosan elérhető legyen.

Ezekben az esetekben minden alkalommal, amikor frissíti a jogkivonat-aláíró tanúsítványok, frissíteni kell az Office 365 tartomány frissítés-MsolFederatedDomain PowerShell-parancs használatával.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>1. lépés: Győződjön meg arról, hogy az AD FS rendelkezik-e az új jogkivonat-aláíró tanúsítványok
**Nem alapértelmezett konfigurációja**

Ha egy nem alapértelmezett konfigurációt, az AD FS használ (ahol **autocertificaterollover beállítást** értéke **hamis**), valószínűleg használ (nem önaláírt) egyéni tanúsítványokat. Az AD FS jogkivonat-aláíró tanúsítványok megújítása kapcsolatos további információkért lásd: [az ügyfelek nem használják az AD FS önaláírt tanúsítványok](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Az összevonási metaadatok nincs nyilvánosan elérhető**

Másrészről Ha **autocertificaterollover beállítást** értéke **igaz**, de az összevonási metaadatok nem nyilvánosan elérhető, először győződjön meg arról, hogy új jogkivonat-aláíró tanúsítványok az AD FS hozták létre. Győződjön meg arról, hogy az új jogkivonat-aláíró tanúsítványok az alábbi lépések megtételével:

1. Győződjön meg arról, hogy van bejelentkezve az elsődleges AD FS-kiszolgálón.
2. Ellenőrizze a jelenlegi aláírási tanúsítványok az AD FS nyisson meg egy PowerShell-parancsablakot, és futtassa a következő parancsot:

    PS C:\>Get-ADFSCertificate – CertificateType jogkivonat-aláíró

   > [!NOTE]
   > Ha az AD FS 2.0 használ, először Add-Pssnapin Microsoft.Adfs.Powershell kell futtatni.
   >
   >
3. Tekintse meg a felsorolt tanúsítványokat, a parancs kimenete. Az AD FS generált új tanúsítványt, ha megjelenik a kimenetben két tanúsítványt: egyet, amelynek a **IsPrimary** értéke **igaz** és a **NotAfter** dátum belül 5 napos, és egy, amelynek van **IsPrimary** van **hamis** és **NotAfter** készül a jövőben egy év.
4. Ha csak egy tanúsítványt, és a **NotAfter** 5 napon belül dátum, akkor kell új tanúsítvány létrehozásához.
5. Hozzon létre új tanúsítványt, hajtsa végre a következő parancsot egy PowerShell-parancs parancssorba: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. A frissítés ellenőrizze újra a következő parancs futtatásával: PS C:\>Get-ADFSCertificate – CertificateType jogkivonat-aláíró

Most már két tanúsítványt kell szerepelnie, amely az egyik van egy **NotAfter** körülbelül egy évet a jövőben a, és amelynek a **IsPrimary** értéke **hamis**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>2. lépés: Az új jogkivonat-aláíró tanúsítványok az Office 365 bizalmi kapcsolat frissítése
Office 365 frissítése az új jogkivonat-aláíró tanúsítványok használható a megbízhatósági kapcsolatban, az alábbiak szerint.

1. Nyissa meg a Microsoft Azure Active Directory-modul Windows PowerShell.
2. Run $cred=Get-Credential. Ha ez a parancsmag felszólítja a hitelesítő adatokat, írja be a felhőalapú szolgáltatás rendszergazdai fiók hitelesítő adatait.
3. Futtassa a Connect-MsolService – hitelesítőadat-$cred. Ez a parancsmag csatlakoztatja a felhőalapú szolgáltatáshoz. Olyan környezetben, amely csatlakoztatja a felhőalapú szolgáltatás létrehozása előtt meg kell adni a eszköz által telepített további parancsmagok futtató.
4. Ha ezek a parancsok olyan számítógépre, amely nem az elsődleges összevonási kiszolgálót használ, futtassa a Set-MSOLAdfscontext-számítógép &lt;elsődleges AD FS-kiszolgáló&gt;, ahol &lt;elsődleges AD FS-kiszolgáló&gt; a belső teljes tartományneve az elsődleges AD FS-kiszolgáló neve. Ez a parancsmag létrehozza a olyan környezetben, amely az AD FS csatlakoztatja.
5. Futtassa az Update-MSOLFederatedDomain – tartománynév &lt;tartomány&gt;. Ez a parancsmag frissíti a felhő szolgáltatásba az AD FS beállításait, és a megbízhatósági kapcsolat a két konfigurálja.

> [!NOTE]
> Ha több felső szintű tartomány, például a contoso.com és fabrikam.com, támogatásához szükséges kell használnia a **SupportMultipleDomain** bármely parancsmagok kapcsoló. További információkért lásd: [több felső szintű tartomány támogatása](active-directory-aadconnect-multiple-domains.md).
>


## Azure AD-megbízhatóság javítása az Azure AD Connect használatával <a name="connectrenew"></a>
Ha konfigurálta az AD FS-farm és az Azure AD-megbízhatóság az Azure AD Connect használatával, az Azure AD Connect használatával észleli, ha szeretné-e a jogkivonat-aláíró tanúsítványok semmilyen művelet végrehajtására. Ha a tanúsítványok van szüksége, használhatja az Azure AD Connect ehhez.

További információkért lásd: [a megbízhatóság javítása](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Az AD FS és az Azure AD tanúsítvány frissítési lépések
Jogkivonat-aláíró tanúsítványok vannak szabványos X509 összes jogkivonatot, amely az összevonási kiszolgáló biztonságos aláírásához használt tanúsítványok. Jogkivonat-visszafejtési tanúsítványok olyan szabványos X509 minden olyan bejövő jogkivonatot visszafejtéséhez használt tanúsítványok. 

Alapértelmezés szerint az AD FS jogkivonat-aláíró és jogkivonat-visszafejtési tanúsítványok automatikusan létrehozni a kezdeti konfiguráció során és során a tanúsítványok vannak a lejárati dátum van konfigurálva.

Az Azure AD megpróbálja beolvasni új tanúsítványt az összevonási szolgáltatás metaadat 30 nap az aktuális tanúsítvány lejárta előtt. Abban az esetben, ha egy új tanúsítvány jelenleg nem érhető el, az Azure AD változatlanul felügyelik a metaadatok napi rendszeres időközönként. Az új tanúsítvány áll rendelkezésre a metaadatokban, rögtön a összevonási beállítások a tartomány frissítését az új tanúsítvány információkkal. Használhat `Get-MsolDomainFederationSettings` ellenőrizheti, ha látja az új tanúsítványt a NextSigningCertificate / SigningCertificate.

További információ a jogkivonat-aláíró tanúsítványok az AD FS: [beszerzése és konfigurálása jogkivonat-aláíró és jogkivonat visszafejtési tanúsítványok az AD FS Szolgáltatásokhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
