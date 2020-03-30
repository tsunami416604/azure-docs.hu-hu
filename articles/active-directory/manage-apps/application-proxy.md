---
title: Távoli hozzáférés helyszíni alkalmazásokhoz – Azure AD alkalmazásproxy
description: Az Azure Active Directory alkalmazásproxyja biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók egy külső URL-címen vagy egy belső alkalmazásportálon keresztül is elérhetik a felhőalapú és a helyszíni alkalmazásokat. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a távoli asztalhoz, a SharePointhoz, a Teamshez, a Tableau-hoz, a Qlik-alkalmazásokhoz és az üzletági (LOB) alkalmazásokba.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274835"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Távoli hozzáférés a helyszíni alkalmazásokhoz az Azure Active Directory alkalmazásproxyján keresztül 

Az Azure Active Directory alkalmazásproxyja biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók egy külső URL-címen vagy egy belső alkalmazásportálon keresztül is elérhetik a felhőalapú és a helyszíni alkalmazásokat. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a távoli asztalhoz, a SharePointhoz, a Teamshez, a Tableau-hoz, a Qlik-alkalmazásokhoz és az üzletági (LOB) alkalmazásokba.

Az Azure AD-alkalmazásproxy használata:

- **Egyszerűen használható**. A felhasználók ugyanúgy érhetik el a helyszíni alkalmazásokat, mint az O365-öt és az Azure AD-vel integrált más SaaS-alkalmazásokat. Nem szükséges módosítani vagy frissíteni az alkalmazásokat ahhoz, hogy használhatók legyenek az alkalmazásproxyval. 

- **Biztonságos**. A helyszíni alkalmazások használhatják az Azure engedélyezési vezérlőit és biztonsági elemzéseket. A helyszíni alkalmazások például feltételes hozzáférést és kétlépéses ellenőrzést használhatnak. Az alkalmazásproxy nem követeli meg a bejövő kapcsolatok megnyitását a tűzfalon keresztül.
 
- **Költséghatékony**. A helyszíni megoldások általában megkövetelik a demilitarizált zónák (DMZs), peremhálózati kiszolgálók vagy más összetett infrastruktúrák beállítását és karbantartását. Az alkalmazásproxy a felhőben fut, ami megkönnyíti a használatát. Az alkalmazásproxy használatához nem kell módosítania a hálózati infrastruktúrát, és nem kell további berendezéseket telepítenie a helyszíni környezetben.

## <a name="what-is-application-proxy"></a>Mi az alkalmazásproxy?
Az alkalmazásproxy az Azure AD egyik szolgáltatása, amely lehetővé teszi a felhasználók számára, hogy egy távoli ügyfélről hozzáférjenek a helyszíni webalkalmazásokhoz. Az alkalmazásproxy tartalmazza a felhőben futó alkalmazásproxy-szolgáltatást és a helyszíni kiszolgálón futó alkalmazásproxy-összekötőt is. Az Azure AD, az alkalmazásproxy-szolgáltatás és az alkalmazásproxy-összekötő együttműködve biztonságosan adja át a felhasználói bejelentkezési jogkivonatot az Azure AD-ből a webalkalmazásnak.

Az alkalmazásproxy a következőkkel működik:

* [Integrált Windows-hitelesítést](application-proxy-configure-single-sign-on-with-kcd.md) használó webalkalmazások a hitelesítéshez  
* Űrlapalapú vagy [fejlécalapú](application-proxy-configure-single-sign-on-with-ping-access.md) hozzáférést használó webalkalmazások  
* Webes API-k, amelyeket különböző eszközökön szeretne elérhetővé tenni a gazdag alkalmazások számára  
* Távoli asztali [átjáró](application-proxy-integrate-with-remote-desktop-services.md) mögött üzemeltetett alkalmazások  
* Az Active Directory hitelesítési könyvtárával (ADAL) integrált bővített ügyfélalkalmazások

Az alkalmazásproxy támogatja az egyszeri bejelentkezést. A támogatott módszerekről az [Egyszeri bejelentkezési módszer kiválasztása](what-is-single-sign-on.md#choosing-a-single-sign-on-method)című témakörben talál további információt.

Alkalmazásproxy ajánlott, hogy a távoli felhasználók hozzáférjenek a belső erőforrásokhoz. Az alkalmazásproxy felváltja a VPN vagy fordított proxy szükségességét. Nem a vállalati hálózat belső felhasználói számára készült.  Ezek a felhasználók, akik szükségtelenül használják az alkalmazásproxyt, váratlan és nemkívánatos teljesítményproblémákat okozhatnak.

## <a name="how-application-proxy-works"></a>Az alkalmazásproxy működése

Az alábbi ábrán bemutatja, hogyan működik együtt az Azure AD és az Alkalmazásproxy a helyszíni alkalmazások egyszeri bejelentkezésének biztosítása érdekében.

![AzureAD alkalmazásproxy-diagram](./media/application-proxy/azureappproxxy.png)

1. Miután a felhasználó egy végponton keresztül hozzáfért az alkalmazáshoz, a felhasználó az Azure AD bejelentkezési lapra irányítja. 
2. Sikeres bejelentkezés után az Azure AD egy jogkivonatot küld a felhasználó ügyféleszközére.
3. Az ügyfél elküldi a jogkivonatot az alkalmazásproxy szolgáltatásnak, amely lekéri az egyszerű felhasználónevet (UPN) és a rendszerbiztonsági rendszer egyszerű felhasználónevét (SPN) a jogkivonatból. Az alkalmazásproxy ezután elküldi a kérelmet az alkalmazásproxy-összekötőnek.
4. Ha konfigurált a egyszeri bejelentkezés, az összekötő elvégzi a felhasználó nevében szükséges további hitelesítést.
5. Az összekötő elküldi a kérelmet a helyszíni alkalmazás.  
6. A válasz az összekötőn és az alkalmazásproxy-szolgáltatáson keresztül kerül elküldésre a felhasználónak.

| Összetevő | Leírás |
| --------- | ----------- |
| Végpont  | A végpont egy URL vagy egy [végfelhasználói portál.](end-user-experiences.md) A felhasználók a hálózaton kívül is elérhetik az alkalmazásokat egy külső URL-cím elérésével. A hálózat felhasználói URL-címen vagy végfelhasználói portálon keresztül érhetik el az alkalmazást. Amikor a felhasználók ezek a végpontok egyikét, az Azure AD-ben hitelesítik magukat, és majd az összekötőn keresztül a helyszíni alkalmazás.|
| Azure AD | Az Azure AD a hitelesítést a felhőben tárolt bérlői címtár használatával hajtja végre. |
| Alkalmazásproxy szolgáltatás | Ez az alkalmazásproxy-szolgáltatás az Azure AD részeként fut a felhőben. Továbbítja a bejelentkezési jogkivonatot a felhasználótól az alkalmazásproxy-összekötőnek. Az alkalmazásproxy továbbítja a kéréselérhető fejléceit, és a protokoll nak függően állítja be a fejléceket az ügyfél IP-címére. Ha a proxyhoz érkező kérelem már rendelkezik ezzel a fejléccel, az ügyfél IP-címe hozzáadódik a fejléc értékét tartalmazó vesszővel elválasztott lista végéhez.|
| Alkalmazásproxy-összekötő | Az összekötő egy könnyű ügynök, amely a hálózaton belüli Windows Server rendszeren fut. Az összekötő kezeli a felhőbeli alkalmazásproxy-szolgáltatás és a helyszíni alkalmazás közötti kommunikációt. Az összekötő csak kimenő kapcsolatokat használ, így nem kell megnyitnia a bejövő portokat, vagy semmit sem kell helyeznie a DMZ-ben. Az összekötők állapot nélküliek, és szükség esetén adatokat vonnak le a felhőből. Az összekötőkről, például a terheléselosztásról és a hitelesítésről az [Azure AD alkalmazásproxy-összekötők ismertetése című](application-proxy-connectors.md)témakörben talál további információt.|
| Active Directory (AD) | Az Active Directory a helyszíni futtatása a tartományi fiókok hitelesítésének végrehajtásához fut. Egyszeri bejelentkezés konfigurálásakor az összekötő kommunikál az AD-vel a szükséges további hitelesítés végrehajtásához.
| Helyszíni alkalmazás | Végül a felhasználó hozzáférhet egy helyszíni alkalmazáshoz. 

## <a name="next-steps"></a>További lépések
Az alkalmazásproxy használatának megkezdéséhez olvassa el [az Oktatóanyag: Helyszíni alkalmazás hozzáadása távoli elérést az alkalmazásproxyn keresztül című témakörben.](application-proxy-add-on-premises-application.md) 

A legfrissebb híreket és frissítéseket az [Alkalmazásproxy blogban](https://blogs.technet.com/b/applicationproxyblog/) talál.


