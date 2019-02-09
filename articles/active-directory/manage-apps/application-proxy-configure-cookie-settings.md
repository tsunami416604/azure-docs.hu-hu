---
title: Application Proxy cookie beállításai – Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directory (Azure AD) rendelkezik hozzáférési és munkamenet-cookie-k elérése a helyszíni proxyn keresztül történő alkalmazás. Ebből a cikkből látni fogja, hogyan használhatja, és adja meg a cookie-beállításokat.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: concept
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 6bd28ba3c7f94a3eb398d0819f1d8df42a765923
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960005"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-k beállításairól a helyszíni alkalmazások az Azure Active Directory eléréséhez

Az Azure Active Directory (Azure AD) rendelkezik hozzáférési és munkamenet-cookie-k elérése a helyszíni proxyn keresztül történő alkalmazás. Ismerje meg, hogyan használható az Application Proxy cookie-k beállításairól. 

## <a name="what-are-the-cookie-settings"></a>Mik azok a cookie-k beállításairól?

[Az alkalmazásproxy](application-proxy.md) az alábbi hozzáférési és munkamenet cookie-k beállításait használja.

| Cookie-k beállítása | Alapértelmezett | Leírás | Javaslatok |
| -------------- | ------- | ----------- | --------------- |
| Csak HTTP-cookie használata | **Nem** | **Igen** lehetővé teszi, hogy tartalmazza a HTTPOnly jelző HTTP-válaszfejlécek az alkalmazásproxyt. Ez a jelző további biztonsági előnyökkel, például megakadályozza az ügyféloldali parancsfájl-kezelési (CSS) általi másolása, vagy a cookie-k módosítását.<br></br><br></br>Hogy támogatva a csak HTTP beállítás, az alkalmazásproxy titkosítva, és módosítás ellen védett SSL csatornán keresztül továbbít a cookie-kat. | Használat **Igen** a további biztonsági előnyök miatt.<br></br><br></br>Használat **nem** ügyfelek vagy felhasználói ügynökök, amelyek hozzáférést igényelnek a munkamenetcookie-t. Például **nem** egy RDP-n vagy MTSC ügyfél, amely csatlakozik egy távoli asztali átjárókiszolgálót alkalmazásproxyn keresztül.|
| Biztonságos cookie használata | **Nem** | **Igen** lehetővé teszi, hogy tartalmazzák a biztonságos Application Proxy HTTP-válaszfejlécek jelölőjét. Biztonságos cookie-k fokozza a biztonságot, például a HTTPS TLS biztonságos csatornán keresztül történő továbbításával a cookie-kat. Ez megakadályozza, hogy a cookie-k szövegként megfigyelésének miatt a cookie-k továbbításának illetéktelen fél által létrehozottat. | Használat **Igen** a további biztonsági előnyök miatt.|
| Állandó cookie használata | **Nem** | **Igen** lehetővé teszi, hogy a Application Proxy állítsa be a hozzáférési cookie-k nem járnak le, ha a böngésző be van zárva. Az adatmegőrzés tart, amíg a hozzáférési jogkivonat lejár, vagy a felhasználó manuálisan törölheti a maradandó cookie-k. | Használat **nem** miatt a biztonsági veszély a hitelesített felhasználók tartásával.<br></br><br></br>Javasoljuk, hogy csak a **Igen** a régebbi alkalmazásokat, amelyek nem lehet megosztani a folyamatok közötti cookie-kat. Érdemes frissíteni az alkalmazás megosztási cookie-k helyett maradandó cookie-k használatával folyamatok közötti kezeléséhez. Ha például szüksége lehet, hogy a felhasználó az Office-dokumentumok megnyitását az Intézőben egy SharePoint-webhelyről maradandó cookie-k. Maradandó cookie-k nélkül ez a művelet sikertelen lehet, ha a hozzáférési cookie-kat a böngészőben, a kezelő folyamat és az Office-folyamat között nem megosztott. |

## <a name="set-the-cookie-settings---azure-portal"></a>Állítsa be a cookie-beállítások – Azure portal
A cookie-k beállításairól az Azure portal használatával beállítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás**.
3. Válassza ki az alkalmazást, amelynek szeretné engedélyezni, egy cookie-beállítást.
4. Kattintson a **alkalmazásproxy**.
5. A **további beállítás**, állítsa be a cookie-k **Igen** vagy **nem**.
6. Kattintson a **mentése** alkalmazza a módosításokat. 

<!---

## View current cookie settings - PowerShell

To see the current cookie settings for the application, use this PowerShell command:  

```PowerShell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## Set cookie settings - PowerShell

In the following PowerShell commands, ```<ObjectId>``` is the ObjectId of the application. 

**Http-Only Cookie** 

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Secure Cookie**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Persistent Cookies**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```

-->
