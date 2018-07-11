---
title: Az Azure AD Connect és összevonás |} A Microsoft Docs
description: Ezen a lapon a központi hely, az Azure AD Connect használata az AD FS-műveletek összes dokumentumaiból.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ce6b889d23959e9d04b95ec2bc5847340b596448
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915836"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect és összevonás
Az Azure Active Directory (Azure AD) Connect konfigurálását teszi lehetővé, összevonás a helyszíni Active Directory összevonási szolgáltatások (AD FS) és az Azure ad-ben. Az összevonási jelentkezzen be engedélyezheti a felhasználók jelentkezhetnek be, az Azure AD-alapú szolgáltatásokat a helyszíni jelszavak –, és a vállalati hálózaton, írja be újra a jelszavukat anélkül. Az összevonási beállítás és az AD FS használatával telepíthet egy új AD FS telepítése, vagy megadhat egy meglévő telepítését egy Windows Server 2012 R2-farmban.

Ez a témakör az otthoni funkciók összevonási kapcsolatos információk az Azure AD Connect. A lista felsorolja az összes kapcsolódó témakörökre mutató hivatkozásokat. Az Azure AD Connect hivatkozások: [a helyszíni identitások integrálása az Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Az Azure AD Connect: összevonási kapcsolatos témakörök
| Témakör | Azt ismerteti, és mikor érdemes elolvasni |
|:--- |:--- |
| **Az Azure AD Connect felhasználói bejelentkezési lehetőségek** | |
| [Felhasználói bejelentkezési lehetőségek ismertetése](active-directory-aadconnect-user-signin.md) |Ismerje meg a különböző felhasználói bejelentkezés lehetőségei és azok az Azure bejelentkezési felhasználói élményt. |
| **Az AD FS telepítése az Azure AD Connect használatával** | |
| [Előfeltételek](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Tekintse meg az AD FS az Azure AD Connect használatával a sikeres telepítés előfeltételeit. |
| [Az AD FS-farm konfigurálása](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Egy új AD FS-farm telepítése az Azure AD Connect használatával. |
| [Az Azure AD-bA a másodlagos bejelentkezési Azonosítót összevonása ](active-directory-aadconnect-federation-management.md#alternateid) | Alternatív bejelentkezési azonosítóval összevonás konfigurálása  |
| **Az AD FS-konfiguráció módosítása** | |
| [A bizalmi kapcsolat javítása](active-directory-aadconnect-federation-management.md#repairthetrust) |A jelenlegi bizalmi kapcsolat javítása között a helyszíni AD FS és az Office 365 vagy Azure. |
| [Új AD FS-kiszolgáló hozzáadása](active-directory-aadconnect-federation-management.md#addadfsserver) |Bontsa ki a további AD FS-kiszolgáló AD FS farmot kezdeti telepítés után. |
| [Új AD FS-WAP-kiszolgáló hozzáadása](active-directory-aadconnect-federation-management.md#addwapserver) |Bontsa ki az AD FS-farm további webalkalmazás-proxykiszolgálóként (WAP) kiszolgáló a kezdeti telepítés után. |
| [Új összevont tartomány hozzáadása](active-directory-aadconnect-federation-management.md#addfeddomain) |Adjon hozzá egy másik tartomány az Azure ad-ben. |
| [Az SSL-tanúsítvány frissítése](active-directory-aadconnectfed-ssl-update.md)| Frissítse az AD FS-farm SSL-tanúsítványt. |
| [-Office 365 és az Azure AD összevonási tanúsítványainak megújítása](active-directory-aadconnect-o365-certs.md)|Az Azure ad-vel az O365-tanúsítvány megújítása.|
| **Más összevonási konfiguráció** | |
| [Több Azure AD-példány összevonása egyetlen AD FS-példánnyal](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Egyetlen AD FS-farm több Azure AD összevonása| 
| [Adjon hozzá egy egyéni vállalati emblémát/ábra](active-directory-aadconnect-federation-management.md#customlogo) |A bejelentkezési élmény módosítsa úgy az egyéni embléma, amely az AD FS bejelentkezési oldal jelenik meg. |
| [A bejelentkezési leírásának hozzáadása](active-directory-aadconnect-federation-management.md#addsignindescription) |Módosítsa a bejelentkezés leírását az AD FS bejelentkezési oldalára. |
| [Az AD FS-jogcímszabályok módosítása](active-directory-aadconnect-federation-management.md#modclaims) |Módosíthatja, vagy adjon hozzá szabályokat, amelyek megfelelnek az AD FS az Azure AD Connect szinkronizálási konfigurációjában. |


## <a name="additional-resources"></a>További források
* [Többtartományos két Azure ad-ben egyetlen AD FS-sel](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Az AD FS üzembe helyezése az Azure-ban](active-directory-aadconnect-azure-adfs.md)
* [Magas rendelkezésre állás földrajzi határokon átívelő AD FS üzembe helyezése az Azure-ban Azure Traffic Managerrel](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
