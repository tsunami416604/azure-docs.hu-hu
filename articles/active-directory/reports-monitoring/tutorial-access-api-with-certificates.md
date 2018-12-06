---
title: Oktatóanyag az adatok beszerzése az Azure AD Reporting API és tanúsítványok használatával |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan használatával az Azure AD Reporting API és tanúsítványalapú le adatok címtárakból felhasználói beavatkozás nélkül.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7535aad95f7410d25ada232b4946fe52ebc4ba67
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961960"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Oktatóanyag: Adatok lekérése használatával az Azure Active Directory reporting API és tanúsítványok

Az [Azure Active Directory (Azure AD) Reporting API-k](concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. Ha szeretne hozzáférni az Azure AD Reporting API felhasználói beavatkozás nélkül, konfigurálnia kell a tanúsítványokat használ a hozzáférést.

Ebben az oktatóanyagban elsajátíthatja egy tesztcélú tanúsítvánnyal az MS Graph API jelentéskészítési elérésére használhat. Teszt tanúsítványok használata termelési környezetben nem ajánlott. 

## <a name="prerequisites"></a>Előfeltételek

1. Először végezze el a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md). 

2. Töltse le és telepítse [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

3. Telepítés [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - Az ADAL könyvtárakat való hitelesítéshez szükséges
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

4. Ha most először futtassa az modullal **Install-MSCloudIdUtilsModule**, máskülönben importálás segítségével a **Import-Module** Powershell-parancsot. A munkamenet a képernyőhöz hasonlóan kell kinéznie: ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
5. Használja a **New-SelfSignedCertificate** Teszttanúsítvány létrehozása a Powershell-parancsmag segítségével.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

6. Használja a **Export-tanúsítvány** parancsmag exportálhatja, és a egy tanúsítványfájlt.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

1. Keresse meg a [az Azure portal](https://portal.azure.com), jelölje be **Azure Active Directory**, majd **alkalmazásregisztrációk** , és válassza ki az alkalmazást a listából. 

2. Válassza ki **beállítások** > **kulcsok** válassza **nyilvános kulcs feltöltése**.

3. A tanúsítványfájl az előző lépésben válassza ki és **mentése**. 

4. Megjegyzés: az alkalmazás azonosítója, az alkalmazással regisztrált tanúsítvány ujjlenyomatát. Az ujjlenyomatot, a portálon, az alkalmazás oldalán találja **beállítások** kattintson **kulcsok**. Az ujjlenyomat területen lesz a **nyilvános kulcsok** listája.

5. Nyissa meg az alkalmazásjegyzék a beágyazott alkalmazásjegyzék-szerkesztőben, és cserélje le a *keyCredentials* tulajdonság, amely az új tanúsítvány adatait a következő séma használatával. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. A jegyzékfájl mentése. 
  
7. Most hozzáférési jogkivonatot az MS Graph API használatával lekérheti ezt a tanúsítványt. Használja a **Get-MSCloudIdMSGraphAccessTokenFromCert** parancsmagot a MSCloudIdUtils PowerShell-modult, ad át az Alkalmazásazonosítót és az előző lépésben beszerzett ujjlenyomatát. 

 ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. A Powershell-parancsfájlt a hozzáférési jogkivonat segítségével lekérdezése a Graph API-t. Használja a **Invoke-MSCloudIdMSGraphQuery** számbavétele a bejelentkezések és directoryAudits végpont a MSCloudIDUtils parancsmagjával. Ez a parancsmag kezeli a többlapos eredményeket, és elküldi őket a PowerShell-adatcsatorna.

9. A lekérdezés a directoryAudits végpontot, hogy a naplók beolvasása. 
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. A lekérdezés a bejelentkezések végpontot, hogy a bejelentkezési naplók begyűjtéséről.
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Ezután eldöntheti, exportálhatja ezeket az adatokat egy CSV-fájlba, és mentheti egy SIEM-rendszerbe. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Reporting API-k működésével](concept-reporting-api.md)
* [Naplózási referencia API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenységek jelentésének API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
