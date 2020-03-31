---
title: Alkalmazásproxy cookie-beállításai – Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory (Azure AD) hozzáférési és munkamenet-cookie-kat biztosít a helyszíni alkalmazások alkalmazásproxyn keresztülvaló eléréséhez. Ebben a cikkben megtudhatja, hogyan használhatja és konfigurálhatja a cookie-beállításokat.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481364"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-beállítások a helyszíni alkalmazások Azure Active Directoryban való eléréséhez

Az Azure Active Directory (Azure AD) hozzáférési és munkamenet-cookie-kat biztosít a helyszíni alkalmazások alkalmazásproxyn keresztülvaló eléréséhez. További információ az Alkalmazásproxy cookie-beállításainak használatáról. 

## <a name="what-are-the-cookie-settings"></a>Mik a cookie-beállítások?

[Az alkalmazásproxy](application-proxy.md) a következő hozzáférési és munkamenet-cookie-beállításokat használja.

| Cookie-beállítás | Alapértelmezett | Leírás | Javaslatok |
| -------------- | ------- | ----------- | --------------- |
| Csak HTTP-s cookie használata | **Nem** | **Igen** lehetővé teszi, hogy az alkalmazásproxy tartalmazza a HTTPOnly jelzőt a HTTP-válaszfejlécekben. Ez a jelző további biztonsági előnyökkel jár, például megakadályozza, hogy az ügyféloldali parancsfájlok (CSS) másolják vagy módosítsák a cookie-kat.<br></br><br></br>Mielőtt támogattuk volna a CSAK HTTP-beállítást, az alkalmazásproxy titkosított és továbbított cookie-kat egy biztonságos TLS-csatornán keresztül, hogy védelmet nyújtson a módosítás ellen. | Az **Igen** használata a további biztonsági előnyök miatt.<br></br><br></br>A **Nem** használata olyan ügyfelek vagy felhasználói ügynökök esetében, amelyek nek hozzáférésre van szükségük a munkamenet-cookie-hoz. Használja például a **Nem** programot egy olyan RDP- vagy MTSC-ügyfélhez, amely alkalmazásproxyn keresztül csatlakozik egy távoli asztali átjárókiszolgálóhoz.|
| Biztonságos cookie használata | **Nem** | **Igen** lehetővé teszi, hogy az alkalmazásproxy tartalmazza a Biztonságos jelzőt a HTTP-válaszfejlécekben. A biztonságos cookie-k növelik a biztonságot azáltal, hogy a cookie-kat egy TLS-védett csatornán, például HTTPS-en keresztül továbbítják. Ez megakadályozza, hogy illetéktelen felek jogosulatlan ultrái tartsák be a cookie-kat a cookie-k egyértelmű szövegben történő továbbítása miatt. | Az **Igen** használata a további biztonsági előnyök miatt.|
| Állandó cookie használata | **Nem** | **Igen** lehetővé teszi, hogy az Alkalmazásproxy úgy állítsa be a hozzáférési cookie-kat, hogy ne járjanak le, amikor a webböngésző be van zárva. Az adatmegőrzés a hozzáférési jogkivonat lejártáig, illetve az állandó cookie-k manuális törléséig tart. | A Felhasználók hitelesítésével kapcsolatos biztonsági kockázat miatt használja a **Nem** lehetőséget.<br></br><br></br>Javasoljuk, hogy csak **olyan** régebbi alkalmazások esetén használja az Igen-t, amelyek nem tudják megosztani a cookie-kat a folyamatok között. Jobb, ha frissíti az alkalmazást, hogy kezelje a cookie-k megosztását a folyamatok között, ahelyett, hogy állandó cookie-kat használna. Előfordulhat például, hogy állandó cookie-kra van szükség ahhoz, hogy a felhasználó egy SharePoint-webhelyről intéző nézetben nyithassa meg az Office-dokumentumokat. Állandó cookie-k nélkül ez a művelet sikertelen lehet, ha a hozzáférési cookie-k nincsenek megosztva a böngésző, a felfedező folyamat és az Office-folyamat között. |

## <a name="samesite-cookies"></a>SameSite cookie-k
A Chrome 80-as verziótól kezdve és végül a Chromiumot használó böngészőkben a [SameSite](https://web.dev/samesite-cookies-explained) attribútumot nem tartalmazó cookie-kat a rendszer úgy kezeli, mintha **a SameSite=Lax**értékre állítanák őket. A SameSite attribútum deklarálja, hogy a cookie-kat hogyan kell korlátozni az azonos webhely környezetére. Ha a beállítás Lax, a cookie csak küldeni az azonos helyszíni kérelmek vagy felső szintű navigáció. Az alkalmazásproxy azonban megköveteli, hogy ezeket a cookie-kat a harmadik fél környezetében őrizzék meg annak érdekében, hogy a felhasználók megfelelően bejelentkezve maradjanak a munkamenet során. Ennek köszönhetően az alkalmazásproxy-hozzáférés és a munkamenet-cookie-k frissítéseit a változás kedvezőtlen hatásának elkerülése érdekében tesszük. A frissítések a következők:

* A **SameSite** attribútum beállítása **Nincs (Nincs)** tulajdonságra. Ez lehetővé teszi az alkalmazásproxy-hozzáférés és a munkamenetek cookie-jainak megfelelő elküldését a harmadik fél környezetében.
* A **Biztonságos cookie használata** beállítás alapértelmezettként az **Igen** beállítást használja. A Chrome-nak a cookie-któl is szüksége van a Biztonságos jelző megadására, különben elutasítjuk. Ez a módosítás az alkalmazásproxyn keresztül közzétett összes meglévő alkalmazásra vonatkozik. Vegye figyelembe, hogy az alkalmazásproxy-hozzáférési cookie-k mindig biztonságosak, és csak HTTPS-en keresztül továbbíthatók. Ez a módosítás csak a munkamenet-cookie-kra vonatkozik.

Az Alkalmazásproxy cookie-k ezen módosításai a Chrome 80 megjelenési dátuma előtt néhány héttel jelennek meg.

Továbbá, ha a háttéralkalmazás olyan cookie-kat használ, amelyeknek harmadik fél környezetében kell rendelkezésre állniuk, akkor kifejezetten jelentkeznie kell az alkalmazás megváltoztatásával, hogy a SameSite=None legyen ezekhez a cookie-khoz. Az alkalmazásproxy a Set-Cookie fejlécet az URL-ekre fordítja, és tiszteletben tartja a háttéralkalmazás által beállított cookie-k beállításait.



## <a name="set-the-cookie-settings---azure-portal"></a>A cookie-beállítások beállítása - Azure portal
A cookie-beállítások beállítása az Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Keresse meg az **Azure Active Directory** > **Enterprise alkalmazások** > **at Minden alkalmazás**.
3. Válassza ki azt az alkalmazást, amelynek cookie-beállítását engedélyezni szeretné.
4. Kattintson **az Alkalmazásproxy gombra.**
5. A **További beállítások csoportban**állítsa a cookie-beállítást **Igen** vagy **Nem**beállításra.
6. A módosítások alkalmazásához kattintson a **Mentés** gombra. 

## <a name="view-current-cookie-settings---powershell"></a>Az aktuális cookie-beállítások megtekintése – PowerShell

Az alkalmazás aktuális cookie-beállításainak megtekintéséhez használja ezt a PowerShell-parancsot:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Cookie-beállítások beállítása - PowerShell

A következő PowerShell-parancsokban ```<ObjectId>``` az alkalmazás ObjectId azonosítója található. 

**Csak http-cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Biztonságos cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Állandó cookie-k**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
