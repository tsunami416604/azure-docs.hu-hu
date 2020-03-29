---
title: Hogyan működik az egyszeri szolgáltatás a helyszíni erőforrásokhoz az Azure AD-hez csatlakozó eszközökön | Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672704"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Hogyan működik az egyszeri szolgáltatás a helyszíni erőforrásokhoz az Azure AD-hez csatlakozó eszközökön?

Valószínűleg nem meglepő, hogy egy Azure Active Directoryhoz (Azure AD) csatlakozott eszköz egyetlen bejelentkezési (SSO) élményt nyújt a bérlő felhőalapú alkalmazásaiszámára. Ha a környezet rendelkezik egy helyszíni Active Directory (AD), kiterjesztheti az egyszeri szolgáltatás élményt ezeken az eszközökön is.

Ez a cikk bemutatja, hogyan működik ez.

## <a name="prerequisites"></a>Előfeltételek

 Ha az Azure AD-hez csatlakoztatott gépek nem csatlakoznak a szervezet hálózatához, VPN-re vagy más hálózati infrastruktúrára van szükség. A helyszíni egyszeri szolgáltatások hoz szükség a helyszíni AD DS-tartományvezérlők közötti helyszíni kommunikációra.

## <a name="how-it-works"></a>Működés 

Mivel csak egyetlen felhasználónevet és jelszót kell megjegyeznie, az egyszeri bejelentkezés leegyszerűsíti az erőforrásokhoz való hozzáférést, és javítja a környezet biztonságát. Az Azure AD-hez csatlakozott eszközzel a felhasználók már rendelkeznek egy sso-élménya a felhőalapú alkalmazások a környezetben. Ha a környezet rendelkezik egy Azure AD és egy helyszíni AD, érdemes kiterjeszteni az egyszeri szolgáltatás szolgáltatás ának hatókörét a helyszíni vonal-és üzletági (LOB) alkalmazások, fájlmegosztások és nyomtatók.

Az Azure AD-hez csatlakozó eszközök nem ismerik a helyszíni AD-környezetet, mert nincsenek csatlakoztatva hozzá. Az Azure AD Connect tel azonban további információkat adhat meg ezeknek az eszközöknek a helyszíni AD-ről.

Egy környezet, amely egy Azure AD és egy helyszíni AD is ismert hibrid környezetben is ismert. Ha hibrid környezettel rendelkezik, valószínű, hogy már rendelkezik az Azure AD Connect üzembe helyezésével a helyszíni identitásadatok szinkronizálásához a felhőben. A szinkronizálási folyamat részeként az Azure AD Connect szinkronizálja a helyszíni felhasználói adatokat az Azure AD-vel. Amikor egy felhasználó bejelentkezik egy Azure AD-hez csatlakozott eszköz egy hibrid környezetben:

1. Az Azure AD elküldi a nevét a helyszíni tartomány a felhasználó tagja vissza az eszközre.
1. A helyi biztonsági szervezet (LSA) szolgáltatás engedélyezi a Kerberos-hitelesítést az eszközön.

A Kerberos-t a felhasználó helyszíni környezetében kérelmező erőforrás hoz való hozzáférési kísérlet során az eszköz:

1. Elküldi a helyszíni tartomány adatait és a felhasználói hitelesítő adatokat a található tartományvezérlőnek a felhasználó hitelesítésének leigazolása érdekében.
1. Kerberos [jegymegadó jegy (TGT) fogadása,](/windows/desktop/secauthn/ticket-granting-tickets) amely az AD-hez összekapcsolt erőforrások eléréséhez használatos. Ha az AAD-csatlakozási tartomány TGT-jének lekérésére tett kísérlet sikertelen (a kapcsolódó DCLocator időtúllépést okozhat késleltetés), a Hitelesítő adatok kezelőjének bejegyzéseit kísérli meg, vagy a felhasználó hitelesítési előugró ablakot kap a célerőforrás hitelesítő adatainak megadására.

A Windows integrált **hitelesítéshez** konfigurált összes alkalmazás zökkenőmentesen kapja meg az egyszeri bejelentkezést, amikor a felhasználó megpróbálja elérni őket.

A Windows Hello for Business további konfigurációt igényel a helyszíni egyszeri szolgáltatás engedélyezéséhez egy Azure AD-hez csatlakozott eszközről. További információt az [Azure AD-hez csatlakozó eszközök konfigurálása a helyszíni egyszeri bejelentkezés a Windows Hello for Business használatával című](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)témakörben talál. 

## <a name="what-you-get"></a>Amihez jut

Az SSO használatával egy Azure AD-hez csatlakozott eszközön a következőket teheti: 

- UNC elérési út elérése AD tagkiszolgálón
- Windows-alapú biztonságra konfigurált AD-tag webkiszolgálójának elérése 

Ha windowsos eszközről szeretné kezelni a helyszíni AD-t, telepítse a [Windows 10 távoli kiszolgálófelügyeleti eszközeit.](https://www.microsoft.com/download/details.aspx?id=45520)

Az alábbi eszközöket használhatja:

- Az Active Directory – felhasználók és számítógépek (ADUC) beépülő modul az összes AD-objektum felügyeletéhez. Meg kell azonban adnia azt a tartományt, amelyhez manuálisan szeretne csatlakozni.
- Az AD-hez csatlakozó DHCP-kiszolgáló felügyeletére szolgáló DHCP beépülő modul. Előfordulhat azonban, hogy meg kell adnia a DHCP-kiszolgáló nevét vagy címét.
 
## <a name="what-you-should-know"></a>Alapismeretek

Előfordulhat, hogy módosítania kell a [tartományalapú szűrést](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) az Azure AD Connectben annak érdekében, hogy a szükséges tartományokra vonatkozó adatok szinkronizálva legyenek.

Az Active Directory-számítógép-hitelesítéstől függő alkalmazások és erőforrások nem működnek, mert az Azure AD-hez csatlakozó eszközök nem rendelkeznek számítógép-objektummal az AD-ben. 

Nem oszthat meg fájlokat más felhasználókkal egy Azure AD-hez csatlakoztatott eszközön.

## <a name="next-steps"></a>További lépések

További információ: [Mi az eszközkezelés az Azure Active Directoryban?](overview.md) 
