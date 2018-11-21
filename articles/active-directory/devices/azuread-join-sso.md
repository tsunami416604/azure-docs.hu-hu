---
title: A helyszíni erőforrásokhoz való egyszeri bejelentkezés működése az Azure AD-hez csatlakoztatott eszközök |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 49fab25d772dba431bb2eb1ccac8a5e6daf41667
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277297"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>A helyszíni erőforrásokhoz való egyszeri bejelentkezés működése az Azure AD-hez csatlakoztatott eszközök

Már valószínűleg nem meglepő, hogy az Azure Active Directory (Azure AD) eszköztől, egyszeri bejelentkezés (SSO) környezetet biztosít a bérlő felhőalkalmazásokhoz. Ha a környezetben a helyszíni Active Directory (AD), ezeken az eszközökön, hogy az egyszeri Bejelentkezéses felhasználói élmény bővítheti.

Ez a cikk bemutatja, hogyan is működik mindez.

## <a name="how-it-works"></a>Működés 

Ne feledje, csak egy egyetlen felhasználónév és jelszó van szüksége, mivel az egyszeri Bejelentkezéssel egyszerűbbé teszi az erőforrásokhoz való hozzáférés, és biztonságosabbá teszi a környezetében. Egy az Azure AD-csatlakoztatott eszköz, a felhasználóknak már van egy egyszeri Bejelentkezéses felhasználói élmény, a felhőalapú alkalmazások a környezetben. Ha a környezetben az Azure ad-ben, és a egy helyszíni ad-ben, érdemes lehet bontsa ki az egyszeri Bejelentkezéses felhasználói élmény hatókörét a saját helyszíni üzletági (LOB) alkalmazások, a fájlmegosztások, és a nyomtatókat.  


Az Azure AD-csatlakoztatott eszközök rendelkezik nincs ismereteket a helyszíni AD-környezetet, mivel azok nem csatlakoztatott. Azonban, az további információkkal kapcsolatban, hogy a helyszíni ad-ben, ezeket az eszközöket az Azure AD Connecttel.
Egy környezetet, amelyben mindkettő szerepel, egy Azure ad-ben és a egy helyszíni ad-ben, akkor is ismert hibrid környezettel rendelkezik. Ha hibrid környezettel rendelkezik, akkor valószínű, hogy már rendelkezik Azure AD Connect telepített szinkronizálhatja a helyszíni identitás adatait a felhőbe. A szinkronizálási folyamat részeként az Azure AD Connect szinkronizálja a helyszíni tartomány információk az Azure ad-hez. Amikor egy felhasználó bejelentkezik az Azure ad-hez csatlakoztatott eszközök hibrid környezetben:

1. Az Azure AD elküldi a helyszíni tartomány a felhasználó nevét tartomány tagja, vissza az eszközön. 

2. A helyi biztonsági szervezet (LSA) szolgáltatás lehetővé teszi a Kerberos-hitelesítést az eszközön.

A felhasználó a helyi tartomány, az eszköz az erőforráshoz hozzáférést próbál:

1. A tartomány információk segítségével keresse meg a tartományvezérlő (DC). 

2. A helyszíni tartomány adatait és a felhasználói hitelesítő adatokat küld a található tartományvezérlő a hitelesített felhasználó beolvasásához.

3. A Kerberos kap [jegykiadó jegyet (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) AD-hez csatlakoztatott erőforrások eléréséhez használt.

Minden alkalmazás konfigurált **Windows-hitelesítést** zökkenőmentesen kaphat az egyszeri bejelentkezés, ha egy felhasználó megpróbál hozzáférni őket.  

Windows Hello for Business további konfigurálására van szükség ahhoz, hogy a helyszíni egyszeri bejelentkezés az Azure AD-csatlakoztatott eszközről. További információkért lásd: [konfigurálása az Azure AD-hez csatlakoztatott eszközök esetében a helyszíni egyszeri bejelentkezést a használatával Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Amihez jut

Az egyszeri bejelentkezés egy Azure AD a csatlakoztatott a eszköz használható: 

- Egy AD tagkiszolgálón UNC elérési út elérésére

- Egy AD tag webkiszolgálón beállított Windows integrált biztonsági eléréséhez 



Ha szeretné kezelni a helyszíni AD egy Windows-eszközről, telepítse a [távoli kiszolgáló felügyeleti eszközei a Windows 10-es](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

Használhatja:

- Az Active Directory – felhasználók és számítógépek (aduc-ban) beépülő modul az összes AD-objektumok felügyeletéhez. Azonban akkor adja meg a tartományt, amelyeket manuálisan csatlakozni szeretne.

- A DHCP beépülő modul egy AD-hez csatlakoztatott DHCP-kiszolgáló felügyeletéhez. Azonban szükség lehet a DHCP-kiszolgáló nevét vagy címét adja meg.

 
## <a name="what-you-should-know"></a>Alapismeretek

Előfordulhat, hogy módosítani kell a [tartományalapú szűrés](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) az Azure AD Connectben, győződjön meg arról, hogy a rendszer szinkronizálja az adatokat a szükséges tartományok.

Alkalmazásokhoz és erőforrásokhoz ettől az Active Directory számítógép-hitelesítés nem működik, mert az Azure AD-hez csatlakoztatott eszközök nincsenek számítógép-objektumot az ad-ben. 

Az Azure AD-csatlakoztatott eszközön lévő többi felhasználóval fájlmegosztást nem lehet.

## <a name="next-steps"></a>További lépések

További információkért lásd: [Eszközfelügyelet az Azure Active Directoryban?](overview.md) 
