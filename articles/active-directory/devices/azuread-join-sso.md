---
title: Hogyan működik az SSO a helyszíni erőforrásokkal az Azure AD-hez csatlakoztatott eszközökön | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672704"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Hogyan működik az SSO a helyszíni erőforrásokkal az Azure AD-hez csatlakoztatott eszközökön

Valószínűleg nem meglepő, hogy egy Azure Active Directory (Azure AD) csatlakoztatott eszköz egyszeri bejelentkezéses (SSO) élményt biztosít a bérlő felhőalapú alkalmazásai számára. Ha a környezet helyszíni Active Directory (AD) rendelkezik, kiterjesztheti az SSO-élményt ezeken az eszközökön.

Ez a cikk a működésének módját ismerteti.

## <a name="prerequisites"></a>Előfeltételek

 Ha az Azure AD-hez csatlakoztatott gépek nincsenek csatlakoztatva a szervezet hálózatához, VPN-vagy egyéb hálózati infrastruktúrára van szükség. A helyszíni egyszeri bejelentkezéshez a helyszíni AD DS tartományvezérlőkkel való helyszíni kommunikáció szükséges.

## <a name="how-it-works"></a>Működés 

Mivel csak egyetlen felhasználónevet és jelszót kell megjegyeznie, az SSO leegyszerűsíti a hozzáférést az erőforrásokhoz, és javítja a környezet biztonságát. Az Azure AD-hez csatlakoztatott eszközzel a felhasználók már rendelkeznek egyszeri bejelentkezéssel a környezetében lévő felhőalapú alkalmazásokhoz. Ha a környezet rendelkezik Azure AD-val és helyszíni AD-vel, érdemes kiterjesztenie az egyszeri bejelentkezés hatókörét a helyszíni üzletági (LOB) alkalmazások, a fájlmegosztás és a nyomtatók számára.

Az Azure AD-hez csatlakoztatott eszközök nem rendelkeznek a helyszíni AD-környezettel kapcsolatos ismeretekkel, mivel azok nem csatlakoznak hozzá. A helyszíni AD-vel kapcsolatos további információkat azonban Azure AD Connect használatával is megadhat.

Az Azure AD-t és a helyszíni AD-t egyaránt tartalmazó környezetek is ismertek hibrid környezettel. Ha hibrid környezettel rendelkezik, valószínű, hogy már rendelkezik Azure AD Connect központilag, hogy szinkronizálja a helyszíni identitás adatait a felhőbe. A szinkronizálási folyamat részeként Azure AD Connect szinkronizálja a helyszíni felhasználói adatokat az Azure AD-vel. Amikor egy felhasználó egy hibrid környezetben jelentkezik be egy Azure AD-hez csatlakoztatott eszközre:

1. Az Azure AD elküldi annak a helyszíni tartománynak a nevét, amelyhez a felhasználó visszaesik az eszközhöz.
1. A helyi biztonsági szervezet (LSA) szolgáltatás engedélyezi a Kerberos-hitelesítést az eszközön.

A felhasználó helyszíni környezetében a Kerberost kérő erőforráshoz való hozzáférési kísérlet során az eszköz:

1. Elküldi a helyszíni tartományi adatokat és a felhasználói hitelesítő adatokat a helyi tartományvezérlőnek a hitelesített felhasználó beszerzéséhez.
1. Az AD-hez csatlakoztatott erőforrások eléréséhez használt Kerberos [-jegy (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) fogadása. Ha a HRE kapcsolódási tartomány TGT beolvasására tett kísérlet meghiúsul (a kapcsolódó DCLocator időtúllépése késleltetheti), a Hitelesítőadat-kezelő bejegyzéseit, vagy a felhasználó kaphat egy hitelesítési előugrót, amely a cél erőforráshoz tartozó hitelesítő adatokat kér le.

Minden, a **Windows rendszerhez integrált hitelesítéshez** konfigurált alkalmazás ZÖKKENŐMENTESEN egyszeri bejelentkezést kap, amikor egy felhasználó megpróbál hozzáférni azokhoz.

A vállalati Windows Hello használatához további konfiguráció szükséges, amely lehetővé teszi a helyszíni egyszeri bejelentkezést egy Azure AD-hez csatlakoztatott eszközről. További információ: az [Azure ad-hez csatlakoztatott eszközök konfigurálása helyszíni egyszeri bejelentkezéshez a Windows Hello for Business használatával](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Amihez jut

Az SSO-val egy Azure AD-hez csatlakoztatott eszközön a következőket teheti: 

- UNC elérési út elérése egy AD-tagkiszolgálón
- Hozzáférés a Windows rendszerhez integrált biztonsági szolgáltatáshoz konfigurált AD tag webkiszolgálóhoz 

Ha Windowsos eszközről szeretné felügyelni a helyszíni AD-t, telepítse a [Windows 10-es Távoli kiszolgálófelügyelet eszközei](https://www.microsoft.com/download/details.aspx?id=45520).

A következőket használhatja:

- A Active Directory felhasználók és számítógépek (ADUC) beépülő modul az összes AD objektum felügyeletéhez. Azonban meg kell adnia azt a tartományt, amelyhez manuálisan szeretne csatlakozni.
- Az AD-hez csatlakoztatott DHCP-kiszolgáló felügyeletére szolgáló DHCP beépülő modul. Előfordulhat azonban, hogy meg kell adnia a DHCP-kiszolgáló nevét vagy címeit.
 
## <a name="what-you-should-know"></a>Alapismeretek

Előfordulhat, hogy módosítania kell a [tartományalapú szűrést](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) Azure ad Connect a szükséges tartományokkal kapcsolatos információk szinkronizálásának biztosítása érdekében.

A Active Directory számítógép-hitelesítéstől függő alkalmazások és erőforrások nem működnek, mert az Azure AD-hez csatlakoztatott eszközök nem rendelkeznek számítógép-objektummal az AD-ben. 

Egy Azure AD-hez csatlakoztatott eszközön nem oszthat meg fájlokat más felhasználókkal.

## <a name="next-steps"></a>További lépések

További információ: [Mi az Eszközkezelés a Azure Active Directory?](overview.md) 
