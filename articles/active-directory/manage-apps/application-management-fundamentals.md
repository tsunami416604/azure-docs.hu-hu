---
title: 'Alkalmazáskezelés: Bevált gyakorlatok és ajánlások | Microsoft dokumentumok'
description: Ismerje meg az Azure Active Directoryban található alkalmazások kezelésével kapcsolatos gyakorlati tanácsokat és javaslatokat. Megtudhatja, hogy miként használható a helyszíni alkalmazások automatikus kiépítése és közzététele az alkalmazásproxyval.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085290"
---
# <a name="application-management-best-practices"></a>Alkalmazáskezelés – gyakorlati tanácsok
Ez a cikk javaslatokat és gyakorlati tanácsokat tartalmaz az Azure Active Directoryban (Azure AD), az automatikus üzembe építés és a helyszíni alkalmazások alkalmazásproxyval való közzétételének kezeléséhez.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Felhőalapú alkalmazás és egyszeri bejelentkezési javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Az Azure AD alkalmazásgyűjteményében az alkalmazások  | Az Azure AD rendelkezik egy katalógus, amely több ezer előre integrált alkalmazások, amelyek engedélyezve vannak a vállalati egyszeri bejelentkezés (SSO). Az alkalmazásspecifikus telepítési [útmutatókat az SaaS-alkalmazások oktatóanyagainak listája című témakörben talál.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)  | 
| Összevont SAML-alapú egyszeri felosztott so használata  | Ha egy alkalmazás támogatja, használja az összevont, SAML-alapú egyszeri bejelentkezést az Azure AD-vel a jelszóalapú egyszeri bejelentkezés és az ADFS helyett.  | 
| Tanúsítvány aláíráshoz az SHA-256 használata  | Az Azure AD alapértelmezés szerint az SHA-256 algoritmust használja az SAML-válasz aláírásához. Az SHA-256 használata, kivéve, ha az alkalmazás nak Szüksége van Az SHA-1-re [(lásd: Tanúsítványaláírási beállítások](certificate-signing-options.md) és [alkalmazásbejelentkezési probléma.)](application-sign-in-problem-application-error.md)  | 
| Felhasználói hozzárendelés megkövetelése  | Alapértelmezés szerint a felhasználók anélkül férhetnek hozzájuk a vállalati alkalmazásokhoz, hogy hozzájuk rendelve hozzájuk. Ha azonban az alkalmazás szerepköröket tesz elérhetővé, vagy ha azt szeretné, hogy az alkalmazás megjelenjen a felhasználó hozzáférési paneljén, felhasználói hozzárendelésszükséges. [(Lásd: Fejlesztői útmutató az alkalmazások integrálásához.)](developer-guidance-for-integrating-applications.md)  | 
| A Saját alkalmazások hozzáférési panel telepítése a felhasználók számára | A [hozzáférési](end-user-experiences.md) `https://myapps.microsoft.com` panel egy webalapú portál, amely a felhasználók számára egyetlen belépési pontot biztosít a hozzárendelt felhőalapú alkalmazásokhoz. További képességek, például csoportkezelés és önkiszolgáló jelszó-visszaállítás hozzáadásával a felhasználók megtalálhatják őket a hozzáférési panelen. Lásd: [A hozzáférési panel telepítésének megtervezése](access-panel-deployment-plan.md).
| Csoporthozzárendelés használata  | Ha szerepel az előfizetésben, rendeljen csoportokat egy alkalmazáshoz, így folyamatos hozzáférés-kezelést delegálhat a csoport tulajdonosának. [(Lásd: Fejlesztői útmutató az alkalmazások integrálásához.)](developer-guidance-for-integrating-applications.md)   | 
| A tanúsítványok kezelésének folyamata | Az aláíró tanúsítvány maximális élettartama három év. A tanúsítvány lejáró leállása miatti kimaradás megelőzése vagy minimalizálása érdekében használjon szerepköröket és e-mail terjesztési listákat annak biztosítására, hogy a tanúsítványokkal kapcsolatos változási értesítéseket szorosan figyelje a rendszer. |

## <a name="provisioning-recommendations"></a>Kiépítési javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Az oktatóanyagok használatával állíthatja be a felhőalapú alkalmazások kiépítési környezetét | Tekintse meg a [SaaS-alkalmazások oktatóanyagainak listáját,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) és részletes útmutatást talál a hozzáadni kívánt katalógusalkalmazás kiépítésének konfigurálásához. |
| Kiépítési naplók használata (előzetes verzió) az állapot figyeléséhez | A [létesítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) részletesen ismertetik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve az egyes felhasználók állapotát is. |
| Terjesztési csoport hozzárendelése a létesítési értesítési e-mailhez | A kiépítési szolgáltatás által küldött kritikus riasztások láthatóságának növelése érdekében rendeljen hozzá egy terjesztési csoportot az Értesítési e-mailek beállításhoz. |


## <a name="application-proxy-recommendations"></a>Alkalmazásproxy-javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Belső erőforrások táveléréséhez használja az alkalmazásproxyt | Az alkalmazásproxy használata ajánlott, hogy a távoli felhasználók hozzáférjenek a belső erőforrásokhoz, és lecseréljék a VPN vagy fordított proxy szükségességét. Nem a vállalati hálózaton belüli erőforrások elérésére szolgál, mert késést adhat hozzá.
| Egyéni tartományok használata | Egyéni tartományok beállítása az alkalmazásokhoz (lásd: [Egyéni tartományok konfigurálása](application-proxy-configure-custom-domain.md)) úgy, hogy a felhasználók és az alkalmazások URL-címei a hálózaton belülről vagy kívülről is működjenek. Az ön márkajelzését is szabályozhatja, és testreszabhatja az URL-eket.  Egyéni tartománynevek használata esetén tervezze meg, hogy nyilvános tanúsítványt szerez be egy nem a Microsoft tól megbízható hitelesítésszolgáltatótól. Az Azure Application Proxy támogatja a szabványos, ([helyettesítő karakter)](application-proxy-wildcard.md)vagy SAN-alapú tanúsítványokat. (Lásd: [Alkalmazásproxy-tervezés](application-proxy-deployment-plan.md).) |
| Felhasználók szinkronizálása az alkalmazásproxy telepítése előtt | Az alkalmazásproxy üzembe helyezése előtt szinkronizálja a felhasználói identitásokat egy helyszíni címtárból, vagy hozza létre őket közvetlenül az Azure AD-ben. Az identitás-szinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést biztosítana számukra az App Proxy által közzétett alkalmazásokhoz. Azt is biztosítja a szükséges felhasználói azonosító információkat, hogy végre egyszeri bejelentkezés (SSO). (Lásd: [Alkalmazásproxy-tervezés](application-proxy-deployment-plan.md).) |
| Kövesse tippjeinket a magas rendelkezésre állás és a terheléselosztás érdekében | Ha meg szeretné tudni, hogyan áramlik a forgalom a felhasználók, az Alkalmazásproxy-összekötők és a háttéralkalmazás-kiszolgálók között, és tippeket szeretne kapni a teljesítmény és a terheléselosztás optimalizálásához, olvassa el [az Alkalmazásproxy-összekötők és -alkalmazások magas rendelkezésre állású és terheléselosztási témakört.](application-proxy-high-availability-load-balancing.md) |
| Több összekötő használata | A nagyobb rugalmasság, rendelkezésre állás és méretezés érdekében használjon két vagy több alkalmazásproxy-összekötőt [(lásd: Alkalmazásproxy-összekötők).](application-proxy-connectors.md) Hozzon létre összekötőcsoportokat, és győződjön meg arról, hogy minden összekötőcsoport rendelkezik legalább két összekötővel (három összekötő optimális). |
| Keresse meg az alkalmazáskiszolgálók közelében lévő összekötő kiszolgálókat, és győződjön meg arról, hogy ugyanabban a tartományban vannak | A teljesítmény optimalizálása érdekében fizikailag keresse meg az összekötő kiszolgálót az alkalmazáskiszolgálók közelében (lásd: [Hálózati topoológia szempontok](application-proxy-network-topology.md)). Emellett az összekötő kiszolgálónak és a webalkalmazás-kiszolgálóknak ugyanahhoz az Active Directory tartományhoz kell tartozniuk, vagy a megbízható tartományokra kell kiterjedniük. Ez a konfiguráció szükséges az integrált Windows-hitelesítéssel (IWA) és a Kerberos korlátozott delegálással (KCD) rendelkező sso-hoz. Ha a kiszolgálók különböző tartományokban találhatók, erőforrás-alapú delegálást kell használnia az egyszeri bejelentkezéshez (lásd: [KCD az application proxyval történő egyszeri bejelentkezéshez).](application-proxy-configure-single-sign-on-with-kcd.md) |
| Összekötők automatikus frissítésének engedélyezése | Engedélyezze az összekötők automatikus frissítéseit a legújabb funkciókhoz és hibajavításokhoz. A Microsoft közvetlen támogatást nyújt a legújabb csatlakozóverzióhoz és egy verzióhoz. [(Lásd: Alkalmazásproxy kiadási verzióelőzményei.)](application-proxy-release-version-history.md) |
| A helyszíni proxy megkerülése | A könnyebb karbantartás érdekében konfigurálja az összekötőt úgy, hogy megkerülje a helyszíni proxyt, így az közvetlenül csatlakozik az Azure-szolgáltatásokhoz. [(Lásd: Alkalmazásproxy-összekötők és proxykiszolgálók.)](application-proxy-configure-connectors-with-proxy-servers.md) |
| Azure AD alkalmazásproxy használata webalkalmazás-proxyn keresztül | Az Azure AD alkalmazásproxy használata a legtöbb helyszíni forgatókönyvhöz. Webalkalmazás-proxy csak olyan forgatókönyvekben, amelyek megkövetelik a proxykiszolgáló a AD FS, és ahol nem használhatja az egyéni tartományok az Azure Active Directoryban. [(Lásd: Alkalmazásproxy áttelepítése.)](application-proxy-migration.md) |
