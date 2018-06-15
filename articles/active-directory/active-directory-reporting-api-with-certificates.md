---
title: Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával | Microsoft Docs
description: Ismerteti, hogy hogyan kérhetők le adatok címtárakból felhasználói beavatkozás nélkül az Azure AD Reporting API és tanúsítványalapú hitelesítés használatával.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 5ceb3d1ef99189e5b0cfcc48b38906ed28c07730
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588957"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

Az [Azure Active Directory (Azure AD) Reporting API-k](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Az Azure AD Reporting API felhasználói beavatkozás nélküli lekéréséhez konfigurálja a tanúsítványhasználatot.

A cikk tartalma:

- Ismerteti, hogyan férhet hozzá az Azure AD Reporting API-hoz tanúsítványok használatával.
- A cikk feltételezi, hogy [rendelkezik az Azure Active Directory Reporting API-hoz való hozzáférés előfeltételeivel](active-directory-reporting-api-prerequisites-azure-portal.md). 


A Reporting API tanúsítványokkal történő eléréséhez a következőkre lesz szüksége:

1. Az előfeltételek telepítése
2. A tanúsítvány beállítása az appban 
3. Engedélyek megadása
4. Hozzáférési jogkivonat lekérése




További információ a forráskódról: [A Report API modul használata](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

## <a name="install-prerequisites"></a>Az előfeltételek telepítése

Előzőleg telepítenie kell az Azure AD PowerShell V2-t és az AzureADUtils modult.

1. Az Azure AD PowerShell V2-t az [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) webhelyen található utasításokat követve töltheti le és telepítheti.

2. Az Azure AD Utils modult az [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) webhelyről töltheti le. 
  Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - a Nugetet használó ADAL legújabb verzióját,
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

**Az Azure AD Utils modul telepítése:**

1. Hozzon létre egy könyvtárat a segédprogramok modul mentéséhez (például c:\azureAD), és töltse le a modult a GitHubról.
2. Nyisson meg egy PowerShell munkamenetet, és lépjen be a most létrehozott könyvtárba. 
3. Importálja a modult, és telepítse a PowerShell modulútvonalára az Install-AzureADUtilsModule parancsmaggal. 

A munkamenetnek ehhez hasonlóan kell kinéznie:

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>A tanúsítvány beállítása az appban

**A tanúsítvány beállítása az alkalmazásban:**

1. [Szerezze be az alkalmazás objektumazonosítóját](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) az Azure Portalról. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Nyisson meg egy PowerShell-munkamenetet, és csatlakozzon az Azure-hoz a Connect-AzureAD parancsmaggal.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Az AzureADUtils New-AzureADApplicationCertificateCredential parancsmagjával adjon hozzá egy tanúsítványalapú hitelesítő adatot. 

>[!Note]
>A korábban rögzített, alkalmazáshoz tartozó objektumazonosítón kívül a tanúsítványobjektumot is meg kell adnia (ezt a Cert: meghajtóval szerezheti be).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Hozzáférési jogkivonat lekéréséhez használja az AzureADUtils **Get-AzureADGraphAPIAccessTokenFromCert** parancsmagját. 

>[!NOTE]
>Az előző szakaszban használt objektumazonosító helyett az alkalmazásazonosítót kell használnia.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>A Graph API meghívása a hozzáférési jogkivonattal

Most létrehozhatja a szkriptet. Az alábbi példában az AzureADUtils **Invoke-AzureADGraphAPIQuery** parancsmagját használjuk. Ez a parancsmag kezeli a többlapos eredményeket, majd elküldi őket a PowerShell-folyamatba. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Most már exportálhatja az eredményeket egy CSV-fájlba, és mentheti egy SIEM-rendszerbe. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 

## <a name="next-steps"></a>További lépések

- [Ismerkedés a Reporting API-k működésével](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Saját megoldás létrehozása](active-directory-reporting-api-getting-started-azure-portal.md#customize)




