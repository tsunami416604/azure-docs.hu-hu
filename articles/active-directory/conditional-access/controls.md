---
title: Mik azok a hozzáférés-vezérlés az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, hogyan hozzáférés-vezérlés az Azure Active Directory feltételes hozzáférés során.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5fc672898a56d8b3e1486b1d8d84cf532fa2b6d
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509411"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Mik azok a hozzáférés-vezérlés az Azure Active Directory feltételes hozzáférés?

A [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférésének a felhőalapú alkalmazások. Feltételes hozzáférési szabályzatot adja meg a válasz ("Ehhez") való indítására, a házirend ("Amikor ez megtörténik") az az oka.

![Szabályozás](./media/controls/10.png)

A feltételes hozzáférés, a környezetben

- "**Ebben az esetben**" nevezzük **feltételek**
- "**Majd ehhez**" nevezzük **hozzáférés-vezérlés**

Egy feltétel utasítást a vezérlők együttes használata a feltételes hozzáférési szabályzatot jelöli.

![Szabályozás](./media/controls/61.png)

Egyes vezérlőelemek követelmény, hogy teljesülnek a személy által vagy rendszer jelentkezik be, vagy a felhasználó korlátozás teheti a bejelentkezést követően.

Vezérlők két típusa van:

- **Engedélyezési vezérlők** - kapu hozzáférés
- **Munkamenet-vezérlők** – hozzáférés korlátozása a munkameneten belül

Ez a témakör ismerteti a különféle vezérlők az Azure AD feltételes hozzáférése által biztosított. 

## <a name="grant-controls"></a>Engedélyezési vezérlők

Az engedély teljesen letiltja a hozzáférést, vagy a kívánt vezérlők kiválasztásával további követelmények a hozzáférést. Több vezérlő esetén is van szüksége:

- Az összes kiválasztott teljesítendő vezérlők (*és*)
- Az egyik kijelölt vezérlő teljesítendő (*vagy*)

![Szabályozás](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Ez a vezérlő segítségével a többtényezős hitelesítést a megadott felhő-alkalmazás elérésére. Ez a vezérlő támogatja a következő multi-factor Authentication szolgáltatók:

- Azure Multi-Factor Authentication
- Egy a helyszíni többtényezős hitelesítési szolgáltató az Active Directory összevonási szolgáltatások (AD FS) együtt.

Multi-factor authentication szolgáltatás használatával segít az erőforrások védelme az elsődleges hitelesítő adatokat egy érvényes felhasználó hozzáférést szerzett előfordulhat, hogy jogosulatlan felhasználók ne férhessenek hozzá.

### <a name="compliant-device"></a>Megfelelő eszköz

Konfigurálhatja, amelyek a eszközalapú feltételes hozzáférési szabályzatokat. Az eszközalapú feltételes hozzáférési szabályzat célja, hogy csak a kiválasztott felhőalapú alkalmazások a hozzáférési jogot [felügyelt eszközök](require-managed-devices.md). Egy megfelelőként megjelölt eszköz megkövetelése az egyik lehetőség van a felügyelt eszközökről való hozzáférés korlátozásához. Eszköz megfelelőként jelölhetők Intune (a bármely eszköz operációs rendszere), vagy a külső MDM-rendszer Windows 10 rendszerű eszközökhöz. Eszköz operációs rendszer típusa Windows 10-es nem harmadik fél mobileszköz-kezelési rendszerek nem támogatottak. 

Az eszköz regisztrálva kell lennie az Azure AD, mielőtt azt is megfelelőként megjelölt. Három lehetősége van az eszköz regisztrálása: 

- Azure AD-ben regisztrált eszközök
- Azure AD-hez csatlakoztatott eszközök  
- Hibrid Azure AD-csatlakoztatott eszközök

Ezek a beállítások a cikkben említett [eszközidentitás mi?](../devices/overview.md)

További információkért lásd: [a feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés igénylése a felügyelt eszközök](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-csatlakoztatott eszköz

Az Azure AD-eszköztől az eszközalapú feltételes hozzáférési szabályzatok konfigurálására egy másik lehetőség egy hibrid megkövetelése. Ez a követelmény a Windows asztali számítógépek, laptopok és a helyszíni Active Directory-tartományhoz csatlakoztatott vállalati táblagépek hivatkozik. Ha ezt a beállítást, a feltételes hozzáférési szabályzat biztosít a helyszíni Active Directory és az Azure Active Directoryhoz csatlakoztatott eszközök a hozzáférés a hozzáférési kísérletek.  

További információkért lásd: [beállítása az Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok](require-managed-devices.md).

### <a name="approved-client-app"></a>Jóváhagyott ügyfélalkalmazás

Mivel az alkalmazottak mobileszközök mind a személyes és munkahelyi feladatokhoz, érdemes elért eszközök még abban az esetben, ha azok által nem kezelt, vállalati adatok védelme érdekében lehetősége.
Használhat [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy) független a mobileszköz-kezelési (MDM) megoldásoktól a vállalati adatok védelme érdekében.

A jóváhagyott ügyfélalkalmazások is egy ügyfélalkalmazás, amely megpróbálja elérni a felhőalapú alkalmazások támogatásához szükséges [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy). Például korlátozhatja hozzáférést az Exchange online-hoz az Outlook alkalmazást. Más néven van egy feltételes hozzáférési szabályzat megköveteli a jóváhagyott ügyfélalkalmazások [alkalmazásalapú feltételes hozzáférési szabályzat](app-based-conditional-access.md). Támogatott a jóváhagyott ügyfélalkalmazások listájának megtekintéséhez lásd: [jóváhagyott alkalmazás megkövetelése ügyfél](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Alkalmazásvédelmi szabályzat (előzetes verzió)

Mivel az alkalmazottak mobileszközök mind a személyes és munkahelyi feladatokhoz, érdemes elért eszközök még abban az esetben, ha azok által nem kezelt, vállalati adatok védelme érdekében lehetősége.
Használhat [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy) független a mobileszköz-kezelési (MDM) megoldásoktól a vállalati adatok védelme érdekében.

Az alkalmazásvédelmi szabályzatot, a hozzáférés korlátozható az ügyfélalkalmazások számára, amely az Azure-bA jelentett AD rendelkezik, miután megkapta [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy). Például korlátozhatja hozzáférést az Exchange online-hoz az Outlook alkalmazást, amely rendelkezik az Intune alkalmazásvédelmi szabályzat. Más néven az alkalmazásvédelmi szabályzatot igénylő feltételes hozzáférési szabályzat [app protection-alapú feltételes hozzáférési szabályzat](app-protection-based-conditional-access.md). 

Az Azure AD-alkalmazás házirend által védett megjelölhető előtt regisztrálni kell az eszközt.

Védett ügyfélalkalmazások, a támogatott házirend listájáért lásd [alkalmazás alkalmazásvédelmi szabályzat követelménye úgy](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Használati feltételek

Ön megkövetelheti, hogy a felhasználó a bérlőben, hogy engedélyt adjanak az a használati feltételeket, mielőtt erőforrásokhoz való hozzáférést megkapják. Rendszergazdaként konfigurálhatja, és a PDF-dokumentumok feltöltésével testre szabhatja a használati feltételeket. Ha egy felhasználó hányadik hetére esik a hozzáférés egy alkalmazás hatókörének csak kapnak Ha született a használati feltételeket.

## <a name="custom-controls-preview"></a>Egyéni vezérlők (előzetes verzió)

Egyéni vezérlők, amelyek az Azure Active Directory Premium P1 kiadás képességet. Egyéni vezérlők használatakor a rendszer átirányítja a felhasználók egy kompatibilis a szolgáltatás további Azure Active Directoryn kívül követelmények teljesítéséhez. Ez a vezérlő eleget kell tenniük a felhasználók a külső szolgáltatást a rendszer átirányítja, hajtja végre a szükséges hitelesítés vagy érvényesítési tevékenységek és vissza az Azure Active Directory átirányítja. Az Azure Active Directory ellenőrzi a választ, és ha a felhasználó sikeresen hitelesített vagy érvényesítve, a felhasználó továbbra is a feltételes hozzáférési folyamat.

Ezek a vezérlők bizonyos külső vagy egyéni szolgáltatásokat, a feltételes hozzáférés-vezérlés használatának engedélyezése, és általában bővítése a feltételes hozzáférés.

Jelenleg az olyan kompatibilis szolgáltatást nyújtó szolgáltatók a következők:

- [A Duo biztonsági](https://duo.com/docs/azure-ca)
- [Entrust-Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [A ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec virtuális IP-cím](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [A Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Ezeket a szolgáltatásokat a további információkért forduljon közvetlenül a szolgáltatók.

### <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozását

Egyéni vezérlő létrehozására, hogy kapcsolatba kell lépni a használni kívánt szolgáltató. Minden nem Microsoft-szolgáltató saját folyamat és a követelmények, regisztráció, előfizethet, vagy ellenkező esetben a szolgáltatás részévé váltak, valamint azt, hogy szeretné-e a feltételes hozzáférés integrálása rendelkezik. Ezen a ponton a szolgáltató biztosít Önnek egy adatblokk JSON formátumban. Ezeket az adatokat lehetővé teszi, hogy a szolgáltató és a feltételes hozzáférés működjön együtt a bérlő számára, hoz létre az új vezérlőt, és határozza meg, honnan feltételes hozzáférést, hogy ha a felhasználók sikeres végrehajtása után a hitelesítési szolgáltatóval.

Egyéni vezérlők csak akkor használható, az Identity Protection Automation szolgáltatással többtényezős hitelesítés megkövetelésével vagy a Privileged Identity Manager (PIM) szerepkörök megszerzését.

A JSON-adatokat másolja és illessze be a kapcsolódó szövegmezőbe. Nem ne módosítsa a JSON-ná, kivéve, ha explicit módon megismeri a módosítást hajt végre. Bármilyen módosítást sikerült megszünteti a kapcsolatot a szolgáltató és a Microsoft és potenciálisan zárolása és a felhasználók a fiókok nem.

Egyéni vezérlő létrehozásának lehetősége van a **kezelés** szakaszában a **feltételes hozzáférési** lapot.

![Szabályozás](./media/controls/82.png)

Kattintson a **új egyéni vezérlő**, a JSON-adatok a vezérlőelem egy szövegmező megnyílik egy panel.  

![Szabályozás](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Egyéni vezérlő törlése, akkor előbb ellenőrizze, hogy azt nem használja a bármely feltételes hozzáférési szabályzat. A befejezést követően:

1. Nyissa meg az egyéni vezérlők listája
1. Kattintson...  
1. Válassza a **Törlés** elemet.

### <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

Egyéni vezérlő szerkesztése, törlése az aktuális vezérlőelem, és hozzon létre egy új vezérlőt a frissített adatokat.

## <a name="session-controls"></a>Munkamenet-vezérlők

Munkamenet-vezérlők engedélyezze a felhőalapú alkalmazások korlátozott felhasználói élményt. A munkamenet-vezérlők felhőalkalmazások tartat be, és az alkalmazáshoz a munkamenet-információk az Azure AD által biztosított további információk alapján.

![Szabályozás](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Alkalmazás által kényszerített korlátozások használata

Ez a vezérlő segítségével az eszköz információkat adnak át a kiválasztott felhőalapú alkalmazások Azure AD szükséges. Az eszköz adatait lehetővé teszi, hogy a felhőalapú alkalmazások tudni, hogy a kompatibilis vagy tartományhoz csatlakoztatott eszköz kezdeményezni a kapcsolatot. Ez a vezérlő támogatja a SharePoint Online és Exchange online-hoz csak kijelölt felhőalapú alkalmazásokat. Kiválasztásakor a cloud app függően az eszköz állapotát, korlátozott vagy teljes felhasználói élmény a felhasználók számára az eszköz adatait használja.

További tudnivalókért lásd:

- [Korlátozott hozzáférés a SharePoint online-nal engedélyezése](https://aka.ms/spolimitedaccessdocs)
- [Az Exchange Online korlátozott hozzáférés engedélyezése](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [megkövetelése MFA konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés](app-based-mfa.md).
- Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [gyakorlati tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md).
