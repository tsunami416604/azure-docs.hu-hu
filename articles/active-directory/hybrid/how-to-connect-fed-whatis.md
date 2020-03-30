---
title: Azure AD Connect és összevonás | Microsoft dokumentumok
description: Ez az oldal az Azure AD Connect et használó AD FS-műveletekre vonatkozó összes dokumentáció központi helye.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331544"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect és összevonás
Az Azure Active Directory (Azure AD) Connect lehetővé teszi az összevonás konfigurálását a helyszíni Active Directory-összevonási szolgáltatásokkal (AD FS) és az Azure AD-vel. Az összevonási bejelentkezéssel engedélyezheti a felhasználóknak, hogy a helyszíni jelszavaikkal jelentkezzenek be az Azure AD-alapú szolgáltatásokba – és a vállalati hálózaton keresztül anélkül, hogy újra meg kellene adniuk a jelszavukat. Az AD FS összevonási beállításának használatával telepítheti az AD FS új telepítését, vagy megadhat egy meglévő telepítést egy Windows Server 2012 R2 farmban.

Ez a témakör az Azure AD Connect összevonással kapcsolatos funkcióival kapcsolatos információk otthona. Felsorolja az összes kapcsolódó témakörre mutató hivatkozásokat. Az Azure AD Connect re mutató hivatkozásokról a [Helyszíni identitások integrálása az Azure Active Directoryval.](whatis-hybrid-identity.md)

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: összevonási témakörök
| Témakör | Mit fed le, és mikor kell elolvasni |
|:--- |:--- |
| **Az Azure AD Connect felhasználói bejelentkezési beállításai** | |
| [A felhasználói bejelentkezési lehetőségek ismertetése](plan-connect-user-signin.md) |Ismerje meg a különböző felhasználói bejelentkezési lehetőségeket, és hogyan befolyásolják az Azure bejelentkezési felhasználói élményt. |
| **Az AD FS telepítése az Azure AD Connect használatával** | |
| [Előfeltételek](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Tekintse meg a sikeres AD FS-telepítés előfeltételeit az Azure AD Connecten keresztül. |
| [AD FS-farm konfigurálása](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Telepítsen egy új AD FS-farmot az Azure AD Connect használatával. |
| [Federate az Azure AD használatával alternatív bejelentkezési azonosító](how-to-connect-fed-management.md#alternateid) | Összevonás konfigurálása alternatív bejelentkezési azonosítóval  |
| **Az AD FS-konfiguráció módosítása** | |
| [A bizalmi kapcsolat javítása](how-to-connect-fed-management.md#repairthetrust) |Javítsa ki a helyszíni AD FS és az Office 365/Azure közötti jelenlegi bizalmi kapcsolatot. |
| [Új AD FS-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addadfsserver) |Az AD FS-farm kibontása egy további AD FS-kiszolgálóval a kezdeti telepítés után. |
| [Új AD FS WAP-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addwapserver) |Az AD FS-farm kibontása egy további WAP-kiszolgálóval a kezdeti telepítés után. |
| [Új összevont tartomány hozzáadása](how-to-connect-fed-management.md#addfeddomain) |Adjon hozzá egy másik tartományt az Azure AD-vel összeegyítendő tartományhoz. |
| [A TLS/SSL tanúsítvány frissítése](how-to-connect-fed-ssl-update.md)| Az AD FS-farm TLS/SSL-tanúsítványának frissítése. |
| [Az Office 365 és az Azure AD összevonási tanúsítványainak megújítása](how-to-connect-fed-o365-certs.md)|Újítsa meg O365-tanúsítványát az Azure AD-vel.|
| **Egyéb összevonási konfiguráció** | |
| [Több Azure AD-példány összevonása egyetlen AD FS-példánnyal](how-to-connect-fed-single-adfs-multitenant-federation.md) | Több Azure AD-d egyesítése egyetlen AD FS-farmmal| 
| [Egyéni vállalati embléma/illusztráció hozzáadása](how-to-connect-fed-management.md#customlogo) |Módosítsa a bejelentkezési élményt az AD FS bejelentkezési lapján megjelenő egyéni embléma megadásával. |
| [Bejelentkezési leírás hozzáadása](how-to-connect-fed-management.md#addsignindescription) |Módosítsa a bejelentkezési leírást az AD FS bejelentkezési lapon. |
| [AD FS jogcímszabályok módosítása](how-to-connect-fed-management.md#modclaims) |Módosítsa vagy adja hozzá az Azure AD Connect szinkronizálási konfigurációjának megfelelő AD FS-ben lévő jogcímszabályokat. |


## <a name="additional-resources"></a>További források
* [Két Azure AD összevont egyetlen AD FS-sel](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Az AD FS üzembe helyezése az Azure-ban](how-to-connect-fed-azure-adfs.md)
* [Magas rendelkezésre állású, földrajzilag elérhető AD FS-telepítés az Azure-ban az Azure Traffic Managerrel](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
