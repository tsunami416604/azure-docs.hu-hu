---
title: Egyéni vezérlők az Azure AD feltételes hozzáférésben
description: Ismerje meg, hogyan működnek az egyéni vezérlők a Azure Active Directory feltételes hozzáférésben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620714"
---
# <a name="custom-controls-preview"></a>Egyéni vezérlők (előzetes verzió)

Az egyéni vezérlők a prémium szintű Azure Active Directory P1 kiadás egyik funkciója. Egyéni vezérlők használatakor a rendszer átirányítja a felhasználókat egy kompatibilis szolgáltatáshoz, hogy az Azure Active Directoryon kívül további követelményeket is kielégítse. Ennek a vezérlőnek a kielégítéséhez a rendszer átirányítja a felhasználó böngészőjét a külső szolgáltatáshoz, végrehajtja a szükséges hitelesítési vagy ellenőrzési tevékenységeket, majd átirányítja a Azure Active Directoryre. Azure Active Directory ellenőrzi a választ, és ha a felhasználó hitelesítése vagy ellenőrzése sikeres volt, a felhasználó folytatja a feltételes hozzáférés folyamatát.

Ezek a vezérlők lehetővé teszik bizonyos külső vagy egyéni szolgáltatások használatát feltételes hozzáférés-vezérlésként, és általában kibővítik a feltételes hozzáférés képességeit.

A jelenleg kompatibilis szolgáltatást nyújtó szolgáltatók a következők:

- [Duo biztonság](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Pingelési identitás](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

A szolgáltatásokkal kapcsolatos további információkért forduljon közvetlenül a szolgáltatóhoz.

## <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozása

Egyéni vezérlő létrehozásához először kapcsolatba kell lépnie a használni kívánt szolgáltatóval. Az egyes nem Microsoft-szolgáltatók saját folyamattal és követelményekkel rendelkeznek a szolgáltatás regisztrálásához, előfizetéséhez vagy egyéb részévé tételéhez, és jelezniük kell, hogy a feltételes hozzáféréssel szeretne integrálni. Ezen a ponton a szolgáltató JSON formátumú adatblokkot biztosít Önnek. Ez az információ lehetővé teszi, hogy a szolgáltató és a feltételes hozzáférés együtt működjön a bérlő számára, létrehozza az új vezérlőt, és meghatározza, hogy a feltételes hozzáférés Hogyan állapítható meg, hogy a felhasználók sikeresen végrehajtották-e az ellenőrzést a szolgáltatóval.

Egyéni vezérlők nem használhatók az Identity Protection automatizálásához, amely többtényezős hitelesítést igényel, vagy a szerepköröket a Privileged Identity Managerben (PIM) kell megemelni.

Másolja a JSON-adatforrást, majd illessze be a kapcsolódó szövegmezőbe. Ne módosítsa a JSON-t, kivéve, ha explicit módon megértette az Ön által végzett módosítást. A változtatások miatt a szolgáltató és a Microsoft közötti kapcsolat megszakadhat, és előfordulhat, hogy az Ön és a felhasználók is zárolják magukat a fiókból.

Az egyéni vezérlő létrehozásának lehetősége a **feltételes hozzáférés** lap **kezelés** szakaszában található.

![Vezérlés](./media/controls/82.png)

Az **új egyéni vezérlő**elemre kattintva megnyílik egy panel, amely a vezérlőelem JSON-adataihoz tartozó szövegmezővel rendelkezik.  

![Vezérlés](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Egyéni vezérlő törléséhez először gondoskodnia kell arról, hogy ne legyen használatban semmilyen feltételes hozzáférési házirendben. Ha elkészült:

1. Ugrás az egyéni vezérlők listára
1. Kattintson a...  
1. Válassza a **Törlés** elemet.

## <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

Egyéni vezérlő szerkesztéséhez törölnie kell a jelenlegi vezérlőt, és létre kell hoznia egy új vezérlőt a frissített információkkal.

## <a name="session-controls"></a>Munkamenet-vezérlőelemek

A munkamenet-vezérlők lehetővé teszik a felhőalapú alkalmazások korlátozott felhasználói élményét. A munkamenet-vezérlőket a Cloud apps kényszeríti, és az Azure AD által az alkalmazással kapcsolatban elérhető további információkra támaszkodik.

![Vezérlés](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Alkalmazás által kényszerített korlátozások használata

Ezzel a vezérlővel megkövetelheti, hogy az Azure AD átadja az eszköz adatait a kiválasztott felhőalapú alkalmazásoknak. Az eszköz adatai lehetővé teszik a felhőalapú alkalmazások számára, hogy megismerjék, hogy a kapcsolat kompatibilis vagy tartományhoz csatlakoztatott eszközről kezdeményezhető-e. Ez a vezérlő csak a SharePoint Online-t és az Exchange Online-t támogatja a kiválasztott felhőalapú alkalmazásokként. Ha bejelöli, a felhőalapú alkalmazás az eszköz információi alapján biztosítja a felhasználók számára az eszköz állapotától függően korlátozott vagy teljes körű felhasználói élményt.

További tudnivalókért lásd:

- [Korlátozott hozzáférés engedélyezése a SharePoint Online-ban](https://aka.ms/spolimitedaccessdocs)
- [Korlátozott hozzáférés engedélyezése az Exchange Online-ban](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Következő lépések

- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).
