---
title: Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával | Microsoft Docs
description: Ismerteti, hogy hogyan kérhetők le adatok címtárakból felhasználói beavatkozás nélkül az Azure AD Reporting API és tanúsítványalapú hitelesítés használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0da0e5d4b7dd8ff000d6c56716bea1b36935af01
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928906"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával

Az [Azure Active Directory (Azure AD) Reporting API-k](active-directory-reporting-api-getting-started-azure-portal.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. Ha szeretne hozzáférni az Azure AD Reporting API felhasználói beavatkozás nélkül, konfigurálja a tanúsítványok a hozzáférést.

Ez az alábbi lépésekből áll:

1. [Az előfeltételek telepítése](#install-prerequisites)
2. [A tanúsítvány regisztrálása az alkalmazásban](#register-the-certificate-in-your-app)
3. [Hozzáférési jogkivonat beszerzése az MS Graph API-hoz](#get-an-access-token-for-ms-graph-api)
4. [Az MS Graph API-végpontokon lekérdezése](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Az előfeltételek telepítése

1. Győződjön meg róla, hogy végrehajtotta a [az Azure Active Directory reporting API elérésének előfeltételeit](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Töltse le és telepítse az Azure AD Powershell V2-webhelyen található utasításokat követve [Azure Active Directory PowerShell-lel](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)

3. Telepítse a MSCloudIDUtils származó a [PowerShell-Galériabeli - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ez a modul számos segédprogramként használható parancsmagot biztosít, többek között:
    - Az ADAL könyvtárakat való hitelesítéshez szükséges
    - a felhasználó, alkalmazáskulcsok és tanúsítványok jogkivonatainak elérését az ADAL használatával,
    - a lapokra bontott eredményeket kezelő Graph API-t.

4. Ha most először futtassa az modullal **Install-MSCloudIdUtilsModule** befejezni a telepítést, ellenkező esetben egyszerűen importálhatja használatával a **Import-Module** Powershell-parancsot.

A munkamenet a képernyőhöz hasonlóan kell kinéznie:

  ![Windows PowerShell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>A tanúsítvány regisztrálása az alkalmazásban

1. Első lépésként nyissa meg az alkalmazás regisztrációs oldalát. Ezt megteheti az is a [az Azure portal](https://portal.azure.com), majd **Azure Active Directory**, majd kattintson a **alkalmazásregisztrációk** és az alkalmazás kiválasztása a listából. 

2. Ezután kövesse a lépéseket [a tanúsítvány regisztrálása az Azure ad-vel](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) az alkalmazáshoz. 

3. Megjegyzés: az alkalmazás azonosítója, az alkalmazással regisztrált tanúsítvány ujjlenyomatát. Az ujjlenyomatot, a portálon, az alkalmazás oldalán találja **beállítások** kattintson **kulcsok**. Az ujjlenyomat területen lesz a **nyilvános kulcsok** listája.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Hozzáférési jogkivonat beszerzése az MS Graph API-hoz

Hozzáférési jogkivonat beszerzése az MS Graph API-hoz, használja a **Get-MSCloudIdMSGraphAccessTokenFromCert** parancsmagot a MSCloudIdUtils PowerShell-modult. 

>[!NOTE]
>Szeretné használni az Alkalmazásazonosítót (más néven ClientID) és a tanúsítvány ujjlenyomata a tanúsítvány a számítógép tanúsítványtárolójában (CurrentUser tárolóba vagy a helyi gépen lévő tanúsítványtároló) telepítve van a titkos kulccsal.
>

 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>A Graph API meghívása a hozzáférési jogkivonattal

A Powershell-szkript a hozzáférési jogkivonat használhatja, a Graph API lekérdezése. Az alábbiakban egy példa használ a **Invoke-MSCloudIdMSGraphQuery** számbavétele a bejelentkezések vagy diectoryAudits végpont a MSCloudIDUtils parancsmagjával. Ez a parancsmag kezeli a többlapos eredményeket, majd elküldi őket a PowerShell-folyamatba.

### <a name="query-the-directoryaudits-endpoint"></a>Lekérdezés a DirectoryAudits végpont
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Lekérdezés a bejelentkezések végpont
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Ezután eldöntheti, exportálhatja ezeket az adatokat egy CSV-fájlba, és mentheti egy SIEM-rendszerbe. A szkriptet be is csomagolhatja egy ütemezett feladatba az Azure AD-adatok bérlőtől való időszakos lekérésére úgy is, hogy nem kell a forráskódban tárolnia az alkalmazáskulcsokat. 


## <a name="next-steps"></a>További lépések

- [Ismerkedés a Reporting API-k működésével](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Saját megoldás létrehozása](active-directory-reporting-api-getting-started-azure-portal.md#customize)




