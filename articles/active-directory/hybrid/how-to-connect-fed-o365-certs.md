---
title: Tanúsítványmegújítás az Office 365- és az Azure AD-felhasználók számára | Microsoft dokumentumok
description: Ez a cikk ismerteti az Office 365-felhasználók számára, hogy miként oldhatják meg a tanúsítvány megújításával kapcsolatos e-mailekkel kapcsolatos problémákat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244842"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Az Office 365 és az Azure Active Directory összevonási tanúsítványainak megújítása
## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) és az Active Directory összevonási szolgáltatások (AD FS) közötti sikeres összevonáshoz az AD FS által az Azure AD-nek a biztonsági jogkivonatok aláírásához használt tanúsítványoknak meg kell egyezniük az Azure AD-ben konfigurált tanúsítványokkal. Bármilyen eltérés a bizalmi kapcsolat megszakadásához vezethet. Az Azure AD biztosítja, hogy ezek az információk szinkronban maradjanak az AD FS és a webalkalmazás-proxy telepítésekor (az extranetes hozzáférés érdekében).

Ez a cikk további információkat nyújt a jogkivonat-aláíró tanúsítványok kezeléséhez és az Azure AD-vel való szinkronizáláshoz a következő esetekben:

* Nem telepíti a webalkalmazás-proxyt, ezért az összevonási metaadatok nem érhetők el az extraneten.
* Az AD FS alapértelmezett konfigurációját nem használja a tokenaláíró tanúsítványokhoz.
* Külső identitásszolgáltatót használ.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Az AD FS alapértelmezett konfigurációja a tokenaláíró tanúsítványokhoz
A jogkivonat-aláírás és a jogkivonat-visszafejtési tanúsítványok általában önaláírt tanúsítványok, és jó egy évig. Alapértelmezés szerint az AD FS tartalmaz egy automatikus megújítási folyamatot, az **AutoCertificateRollover -t.** Ha Az AD FS 2.0-s vagy újabb verziót használja, az Office 365 és az Azure AD automatikusan frissíti a tanúsítványt, mielőtt lejár.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Megújítási értesítés a Microsoft 365 Felügyeleti központtól vagy e-mailben
> [!NOTE]
> Ha e-mailt vagy portálértesítést kapott, amelyben az Office-tanúsítvány megújítását kéri, olvassa el [a Tokenaláíró tanúsítványok módosításainak kezelése](#managecerts) című témakört, amely ellenőrzi, hogy szükség van-e valamilyen műveletre. A Microsoft nak tudomása van egy lehetséges problémáról, amely a tanúsítványmegújítási értesítések küldéséhez vezethet, még akkor is, ha nincs szükség műveletre.
>
>

Az Azure AD megpróbálja figyelni az összevonási metaadatokat, és frissíti a token aláíró tanúsítványok által jelzett metaadatok. 30 nappal a jogkivonat-aláíró tanúsítványok lejárta előtt az Azure AD ellenőrzi, hogy az összevonási metaadatok lekérdezésével elérhetők-e új tanúsítványok.

* Ha sikeresen le tudja kérni az összevonási metaadatokat, és le szeretné olvasni az új tanúsítványokat, a Microsoft 365 felügyeleti központban nem kap e-mail értesítést vagy figyelmeztetést a felhasználó számára.
* Ha nem tudja beolvasni az új jogkivonat-aláíró tanúsítványokat, vagy azért, mert az összevonási metaadatok nem érhető el, vagy az automatikus tanúsítványváltás nincs engedélyezve, az Azure AD e-mail értesítést és figyelmeztetést ad ki a Microsoft 365 felügyeleti központban.

![Értesítés az Office 365 portáljáról](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Ha AD FS szolgáltatást használ, az üzletmenet folytonosságának biztosítása érdekében ellenőrizze, hogy a kiszolgálók rendelkeznek-e a következő frissítésekkel, hogy az ismert problémák hitelesítési hibái ne fordulhassanak elő. Ez csökkenti az AD FS proxykiszolgáló ismert problémáit a megújítási és jövőbeli megújítási időszakokban:
>
> Server 2012 R2 – [Windows Server 2014.](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 és 2012 – [A proxyn keresztüli hitelesítés sikertelen Windows Server 2012 vagy Windows 2008 R2 SP1 rendszerben](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Annak ellenőrzése, hogy a tanúsítványokat frissíteni kell-e<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>1. lépés: Ellenőrizze az AutoCertificateRollover állapotát
Az AD FS-kiszolgálón nyissa meg a PowerShellt. Ellenőrizze, hogy az AutoCertificateRollover értéke True értékre van-e állítva.

    Get-Adfsproperties

![Automatikus tanúsítványgörgetés](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Ha Az AD FS 2.0-s verziót használja, először futtassa az Add-Pssnapin Microsoft.Adfs.Powershell programot.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>2. lépés: Annak ellenőrzése, hogy az AD FS és az Azure AD szinkronban van-e
Az AD FS-kiszolgálón nyissa meg az MSOnline PowerShell-kérdést, és csatlakozzon az Azure AD-hez.

> [!NOTE]
> Az MSOL-Cmdlets az MSOnline PowerShell modul része.
> Az MSOnline PowerShell-modult közvetlenül a PowerShell-galériából töltheti le.
> 
>

    Install-Module MSOnline

Csatlakozzon az Azure AD-hez az MSOnline PowerShell-modul használatával.

    Import-Module MSOnline
    Connect-MsolService

Ellenőrizze az AD FS és az Azure AD megbízhatósági tulajdonságai a megadott tartományban konfigurált tanúsítványokat.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Ha az ujjlenyomatok mindkét kimenetben egyeznek, a tanúsítványok szinkronban vannak az Azure AD-vel.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>3. lépés: Ellenőrizze, hogy a tanúsítvány hamarosan lejár-e
A Get-MsolFederationProperty vagy a Get-AdfsCertificate kimenetében ellenőrizze a dátumot a "Nem utána" területen. Ha a dátum kevesebb, mint 30 nap van hátra, intézkednie kell.

| Automatikus tanúsítványgörgetés | Az Azure AD-vel szinkronban lévő tanúsítványok | Az összevonási metaadatok nyilvánosan hozzáférhetők | Érvényességét | Műveletek |
|:---:|:---:|:---:|:---:|:---:|
| Igen |Igen |Igen |- |Nincs szükség művelet végrehajtására. Lásd: [A tokenaláíró tanúsítvány automatikus megújítása](#autorenew). |
| Igen |Nem |- |Kevesebb mint 15 nap |Azonnal újítsa meg. Lásd: [Tokenaláíró tanúsítvány megújítása manuálisan.](#manualrenew) |
| Nem |- |- |Kevesebb mint 30 nap |Azonnal újítsa meg. Lásd: [Tokenaláíró tanúsítvány megújítása manuálisan.](#manualrenew) |

\[-] Nem számít

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>A jogkivonat-aláíró tanúsítvány automatikus megújítása (ajánlott)<a name="autorenew"></a>
Nem kell manuális lépéseket végrehajtania, ha mindkét művelet igaz:

* Telepítette a webalkalmazás-proxyt, amely lehetővé teszi az összevonási metaadatok elérését az extranetről.
* Az AD FS alapértelmezett konfigurációját használja (az AutoCertificateRollover engedélyezve van).

Ellenőrizze az alábbiakat, és ellenőrizze, hogy a tanúsítvány automatikusan frissíthető-e.

**1. Az AD FS tulajdonság AutoCertificateRollover értékre kell állítani a True értéket.** Ez azt jelzi, hogy az AD FS automatikusan létrehoz új jogkivonat-aláíró és tokenvisszafejtési tanúsítványokat, mielőtt a régiek lejárnak.

**2. Az AD FS összevonási metaadatok nyilvánosan hozzáférhetők.** Ellenőrizze, hogy az összevonási metaadatok nyilvánosan hozzáférhetők-e a következő URL-címre való navigálással a nyilvános interneten (a vállalati hálózaton kívül) lévő számítógépről:

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

ahol `(your_FS_name)` a szervezet által használt összevonási szolgáltatás állomásnevére kerül a lecserélés, például fs.contoso.com.  Ha mindkét beállítást sikeresen ellenőrizni tudja, nem kell mást tennie.  

Például: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## <a name="renew-the-token-signing-certificate-manually"></a>A jogkivonat-aláíró tanúsítvány manuális megújítása<a name="manualrenew"></a>
Dönthet úgy, hogy manuálisan újítja meg a jogkivonat-aláíró tanúsítványokat. A következő esetek például jobban működhetnek a manuális megújításnál:

* A jogkivonat-aláíró tanúsítványok nem önaláírt tanúsítványok. Ennek leggyakoribb oka, hogy a szervezet kezeli a szervezeti hitelesítésszolgáltatótól regisztrált AD FS-tanúsítványokat.
* A hálózati biztonság nem teszi lehetővé az összevonási metaadatok nyilvános annektitására.

Ezekben a forgatókönyvekben minden alkalommal, amikor frissíti a token aláíró tanúsítványok, akkor is frissítenie kell az Office 365-tartomány segítségével a PowerShell parancs, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>1. lépés: Győződjön meg arról, hogy az AD FS új jogkivonat-aláíró tanúsítványokkal rendelkezik
**Nem alapértelmezett konfiguráció**

Ha az AD FS nem alapértelmezett konfigurációját használja (ahol az **AutoCertificateRollover** értéke **Hamis),** akkor valószínűleg egyéni tanúsítványokat használ (nem önaláírt). Az AD FS-jogkivonat-aláíró tanúsítványok megújításáról az [Útmutató az AD FS önaláírt tanúsítványokat nem használó ügyfelek számára](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)című témakörben talál további információt.

**Az összevonási metaadatok nem nyilvánosak**

Ha viszont az **AutoCertificateRollover** értéke **Igaz,** de az összevonási metaadatok nem nyilvánosan hozzáférhetők, először győződjön meg arról, hogy az AD FS új jogkivonat-aláíró tanúsítványokat hozott létre. Ellenőrizze, hogy rendelkezik-e új jogkivonat-aláíró tanúsítványokkal az alábbi lépésekkel:

1. Ellenőrizze, hogy be van-e jelentkezve az elsődleges AD FS-kiszolgálóra.
2. Ellenőrizze az aktuális aláíró tanúsítványokat az AD FS-ben egy PowerShell parancsablak megnyitásával és a következő parancs futtatásával:

    PS C:\>Get-ADFSCertificate – CertificateType token-aláírás

   > [!NOTE]
   > Ha Az AD FS 2.0-s verziót használja, először futtassa az Add-Pssnapin Microsoft.Adfs.Powershell programot.
   >
   >
3. Tekintse meg a parancs kimenetét a felsorolt tanúsítványokon. Ha az AD FS új tanúsítványt hozott létre, akkor két tanúsítványnak kell lennie a kimenetben: az egyik, amelynek **IsPrimary** **Az Elsődleges** érték **Igaz,** a **NotAfter** dátum pedig 5 napon belül, a másik pedig **hamis,** a **NotAfter** pedig körülbelül egy év a jövőben.
4. Ha csak egy tanúsítványt lát, és a **NotAfter** dátum 5 napon belül van, új tanúsítványt kell létrehoznia.
5. Új tanúsítvány létrehozásához hajtsa végre a következő `PS C:\>Update-ADFSCertificate –CertificateType token-signing`parancsot a PowerShell parancssorában: .
6. Ellenőrizze a frissítést a következő parancs ismételt\>futtatásával: PS C: Get-ADFSCertificate –CertificateType token-signing

Most két tanúsítványt kell felsorolni, amelyek közül az egyik **notafter** dátuma körülbelül egy év a jövőben, és amelyeknél az **IsPrimary** érték **Hamis**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>2. lépés: Az Office 365 megbízhatósági kapcsolatúj jogkivonat-aláíró tanúsítványainak frissítése
Frissítse az Office 365-öt a megbízhatósági kapcsolathoz használandó új jogkivonat-aláíró tanúsítványokkal, az alábbiak szerint.

1. Nyissa meg a Microsoft Azure Active Directory module for Windows PowerShell alkalmazást.
2. Futtassa $cred=Get-Credential. Amikor ez a parancsmag hitelesítő adatokat kér, írja be a felhőszolgáltatás-rendszergazdai fiók hitelesítő adatait.
3. Futtassa a Connect-MsolService –Credential $cred. Ez a parancsmag csatlakoztatja önt a felhőszolgáltatáshoz. Az eszköz által telepített további parancsmagok futtatása előtt létre kell hozni egy környezetet, amely a felhőszolgáltatáshoz csatlakoztatja.
4. Ha ezeket a parancsokat olyan számítógépen futtatja, amely nem az AD FS elsődleges &lt;összevonási kiszolgálója, futtassa a Set-MSOLAdfscontext -Computer AD FS elsődleges kiszolgálót,&gt;ahol &lt;az AD FS elsődleges kiszolgálóaz&gt; elsődleges AD FS-kiszolgáló belső Teljes tartománynév-neve. Ez a parancsmag olyan környezetet hoz létre, amely összeköti Önt az AD FS-sel.
5. Futtassa az Update-MSOLFederatedDomain –DomainName &lt;tartományt.&gt; Ez a parancsmag frissíti az AD FS beállításait a felhőszolgáltatásba, és konfigurálja a kettő közötti megbízhatósági kapcsolatot.

> [!NOTE]
> Ha több legfelső szintű tartományt kell támogatnia, például contoso.com és fabrikam.com, a **SupportMultipleDomain** kapcsolót minden parancsmaggal használnia kell. További információ: [Több legfelső szintű tartomány támogatása.](how-to-connect-install-multiple-domains.md)
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Az Azure AD megbízhatóságának javítása az Azure AD Connect használatával<a name="connectrenew"></a>
Ha az AD FS-farmot és az Azure AD megbízhatósági kapcsolatot az Azure AD Connect használatával konfigurálta, az Azure AD Connect segítségével észlelheti, ha bármilyen műveletet kell végrehajtania a tokenaláíró tanúsítványokhoz. Ha meg kell újítania a tanúsítványokat, ehhez használhatja az Azure AD Connectet.

További információ: [A bizalmi kapcsolat javítása](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Az AD FS és az Azure AD tanúsítványfrissítési lépései
A jogkivonat-aláíró tanúsítványok szabványos X509-tanúsítványok, amelyek az összevonási kiszolgáló által kiadott összes jogkivonat biztonságos aláírására szolgálnak. A tokenvisszafejtési tanúsítványok szabványos X509-es tanúsítványok, amelyek a bejövő jogkivonatok visszafejtésére szolgálnak. 

Alapértelmezés szerint az AD FS úgy van beállítva, hogy automatikusan létrehozza a tokenaláíró és tokenvisszafejtési tanúsítványokat, mind a kezdeti konfigurációs időpontban, mind a tanúsítványok lejárati dátumuk közeledtével.

Az Azure AD megpróbál lekérni egy új tanúsítványt az összevonási szolgáltatás metaadataiból 30 nappal az aktuális tanúsítvány lejárta előtt. Abban az esetben, ha egy új tanúsítvány nem érhető el abban az időben, az Azure AD továbbra is rendszeres napi időközönként figyeli a metaadatokat. Amint az új tanúsítvány elérhetővé válik a metaadatokközött, a tartomány összevonási beállításai frissülnek az új tanúsítványadatokkal. `Get-MsolDomainFederationSettings` Ellenőrizheti, hogy az új tanúsítvány megjelenik-e a NextSigningCertificate /SigningCertificate könyvtárban.

Az AD FS tokenaláíró tanúsítványairól további információt az [AD FS tokenaláíró és tokenvisszafejtési tanúsítványainak beszerzése és konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs) című témakörben talál.
