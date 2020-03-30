---
title: Oktatóanyag az AD jelentéskészítő API-hoz tanúsítványokkal | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure AD reporting API-t tanúsítványhitelesítő adatokkal a könyvtárakból felhasználói beavatkozás nélkül.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014264"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Oktatóanyag: Adatok beszereznie az Azure Active Directory jelentéskészítési API-val tanúsítványokkal

Az [Azure Active Directory (Azure AD) Reporting API-k](concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. Ha felhasználói beavatkozás nélkül szeretné elérni az Azure AD Reporting API-t, konfigurálnia kell a tanúsítványok használatához való hozzáférést.

Ebben az oktatóanyagban megtudhatja, hogyan használhat teszttanúsítványt az MS Graph API jelentéskészítéshez való eléréséhez. Nem javasoljuk a teszttanúsítványok használatát éles környezetben. 

## <a name="prerequisites"></a>Előfeltételek

1. A bejelentkezési adatok eléréséhez győződjön meg arról, hogy rendelkezik egy prémium (P1/P2) licenccel rendelkező Azure Active Directory-bérlővel. Az [Azure Active Directory-kiadás](../fundamentals/active-directory-get-started-premium.md) frissítéséhez olvassa el az Azure Active Directory Premium szolgáltatás első lépéseit. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezett tevékenységi adatokkal, az adatok nak néhány napig kell tartaniuk, amíg az adatok megjelennek a jelentésekben a prémium licencre való frissítés után. 

2. Hozzon létre vagy váltson át egy felhasználói fiókra a **globális rendszergazda,** a **biztonsági rendszergazda,** a **biztonsági olvasó** vagy a **jelentésolvasó** szerepkörben a bérlő számára. 

3. Töltse ki [az Azure Active Directory jelentéskészítési API elérésének előfeltételeit.](howto-configure-prerequisites-for-reporting-api.md) 

4. Töltse le és telepítse [az Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)alkalmazást.

5. Telepítse [az MSCloudIdUtils alkalmazást.](https://www.powershellgallery.com/packages/MSCloudIdUtils/) Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - A hitelesítéshez szükséges ADAL-kódtárak
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

6. Ha ez az első alkalom, hogy a modul segítségével fut **Install-MSCloudIdUtilsModule**, egyébként importálja azt az **Import-Module** Powershell paranccsal. A munkamenetnek a következő ![képernyőhöz hasonlóan kell kinéznie: Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. A **New-SelfSignedCertificate** Powershell parancsmunk használatával hozzon létre egy teszttanúsítványt.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. A **Tanúsítvány exportálása** parancssegítségével exportálhatja azt egy tanúsítványfájlba.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

1. Keresse meg az [Azure Portalon,](https://portal.azure.com)válassza az **Azure Active Directory**, majd válassza ki az alkalmazás **regisztrációk,** és válassza ki az alkalmazást a listából. 

2. Válassza a **Beállítások** > **kulcsok lehetőséget,** majd a **Nyilvános kulcs feltöltése**lehetőséget.

3. Jelölje ki a tanúsítványfájlt az előző lépésből, és válassza a **Mentés gombot.** 

4. Jegyezze fel az alkalmazásazonosítót és az alkalmazáshoz regisztrált tanúsítvány ujjlenyomatát. Az ujjlenyomat megkereséséhez az alkalmazás lapján a portálon válassza a **Beállítások** lapot, és kattintson a **Kulcsok gombra.** Az ujjlenyomat a **Nyilvános kulcsok** lista alatt lesz.

5. Nyissa meg az alkalmazásjegyzéket a szövegközi jegyzékszerkesztőben, és cserélje le a *keyCredentials tulajdonságot* az új tanúsítványadatokra a következő séma használatával. 

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

6. Mentse az utaslistát. 
  
7. Most egy hozzáférési jogkivonatot kaphat az MS Graph API-hoz ezzel a tanúsítvánnyal. Használja a **Get-MSCloudIdMSGraphAccessTokenFromCert** parancsmag az MSCloudIdUtils PowerShell modul, halad az alkalmazásazonosító és az ujjlenyomat kapott az előző lépésben. 

   ![Azure portál](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Használja a powershell-parancsfájl hozzáférési jogkivonatát a Graph API lekérdezéséhez. Az **MSCloudIDUtils meghívó-MSCloudIdMSGraphQuery** parancsmagjával számba vetheti a bejelentkezéseket és a directoryAudits végpontot. Ez a parancsmag kezeli a többoldalas eredményeket, és elküldi ezeket az eredményeket a PowerShell-folyamatnak.

9. A citatár lekérdezéseNaplózás végpont a naplónaplók beolvasásához. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. A bejelentkezési végpont lekérdezése a bejelentkezési naplók beolvasásához.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Most már választhatja, hogy ezeket az adatokat CSV-be exportálja, és siem-rendszerbe menti. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Reporting API-k működésével](concept-reporting-api.md)
* [Naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentés API-jának hivatkozása](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
