---
title: Hozzáférés-vezérlés Azure Active Directory feltételes hozzáféréshez
description: Ismerje meg, hogyan működik a hozzáférés-vezérlés a Azure Active Directory feltételes hozzáférésben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8894078a15bf37a5383cdff3721f4bf7be910
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186213"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Mik azok a hozzáférés-vezérlések Azure Active Directory feltételes hozzáféréshez?

A [Azure Active Directory (Azure ad) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)segítségével szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá a felhőalapú alkalmazásokhoz. A feltételes hozzáférési szabályzatban megadhatja a választ ("ezt") a szabályzat aktiválásának okáról ("Ha ez megtörténik").

![Vezérlés](./media/controls/10.png)

A feltételes hozzáférés kontextusában

- "**Ha ez történik**", az ún. **feltételek**
- "**Ezt követően ezt**" hozzáférés- **vezérlésnek** nevezzük

Egy feltétel-utasítás és a vezérlők kombinációja feltételes hozzáférési szabályzatot jelöl.

![Vezérlés](./media/controls/61.png)

Mindegyik vezérlő olyan követelmény, amelyet a személynek vagy a rendszernek be kell tartania, vagy meg kell határoznia, hogy mit tehet a felhasználó a bejelentkezés után.

Két típusú vezérlő létezik:

- **Vezérlőelemek engedélyezése** a Gate-hozzáféréshez
- **Munkamenet-vezérlők** – a hozzáférés korlátozása egy munkameneten belül

Ez a témakör az Azure AD feltételes hozzáférésben elérhető különböző vezérlőket ismerteti. 

## <a name="grant-controls"></a>Vezérlők megadása

A Grant Controls segítségével a kívánt vezérlők kiválasztásával letilthatja a hozzáférést, vagy további követelményekkel is engedélyezheti a hozzáférést. Több vezérlő esetén a következőket kérheti:

- Az összes kijelölt vezérlő (*és*)
- Egy kiválasztott vezérlőelem teljesítése (*vagy*)

![Vezérlés](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Ezzel a vezérlőelemmel megkövetelheti a többtényezős hitelesítés használatát a megadott felhőalapú alkalmazás eléréséhez. Ez a vezérlő a következő multi-Factor providerket támogatja:

- Azure Multi-Factor Authentication
- Helyszíni multi-Factor Authentication-szolgáltató, Active Directory összevonási szolgáltatások (AD FS) (AD FS) kombinálva.

A többtényezős hitelesítés használatával megvédheti az erőforrásokat egy olyan jogosulatlan felhasználó számára, aki egy érvényes felhasználó elsődleges hitelesítő adataihoz tudott hozzáférni.

### <a name="compliant-device"></a>Megfelelő eszköz

Konfigurálhatja az eszközön alapuló feltételes hozzáférési szabályzatokat. Az eszköz alapú feltételes hozzáférési szabályzat célja, hogy csak a [felügyelt eszközökről](require-managed-devices.md)engedélyezze a kiválasztott felhőalapú alkalmazásokhoz való hozzáférést. Az eszköz megfelelőként való megjelölésének megkövetelése az egyik lehetőség, hogy korlátozza a hozzáférést a felügyelt eszközökhöz. Az eszközök az Intune (bármilyen eszköz operációs rendszer esetén) vagy a külső gyártótól származó, Windows 10-es eszközökhöz tartozó MDM-rendszerek által megfelelőként jelölhetők meg. A harmadik féltől származó MDM rendszerek nem Windows 10 rendszerű eszközökön használhatók. 

Az eszköznek regisztrálva kell lennie az Azure AD-ben, mielőtt a megfelelőnek lenne megjelölve. Az eszközök regisztrálásához három lehetőség közül választhat: 

- Azure AD-ben regisztrált eszközök
- Azure AD-hez csatlakoztatott eszközök  
- Hibrid Azure AD-csatlakoztatott eszközök

Ezt a három lehetőséget a cikk a [Mi az eszköz identitása](../devices/overview.md) című cikkben tárgyalja?

További információkért lásd: [felügyelt eszközök megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakoztatott eszköz

Egy hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése egy másik lehetőség, amely az eszközön alapuló feltételes hozzáférési szabályzatokat konfigurálhatja. Ez a követelmény olyan Windows rendszerű asztali számítógépekre, laptopokra és vállalati tablettákra vonatkozik, amelyek egy helyszíni Active Directoryhoz vannak csatlakoztatva. Ha ez a beállítás be van jelölve, a feltételes hozzáférési szabályzat hozzáférést biztosít a helyszíni Active Directoryhoz és a Azure Active Directoryhoz csatlakoztatott eszközökhöz való hozzáférési kísérletekhez. A Mac-eszközök nem támogatják a hibrid Azure AD-csatlakozást.

További információ: [Azure Active Directory eszközön alapuló feltételes hozzáférési házirendek beállítása](require-managed-devices.md).

### <a name="approved-client-app"></a>Jóváhagyott ügyfélalkalmazás

Mivel az alkalmazottak a személyes és a munkahelyi feladatokhoz egyaránt használják a mobileszközök használatát, érdemes lehet az eszköz használatával hozzáférni a vállalati adatokhoz, még abban az esetben is, ha azokat nem Ön felügyeli.
Az [Intune app Protection-szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) segítségével megvédheti vállalata adatait a mobileszköz-kezelési (Mdm) megoldástól függetlenül.

A jóváhagyott ügyfélalkalmazások esetében olyan ügyfélalkalmazás szükséges, amely megpróbál hozzáférni a felhőalapú alkalmazásokhoz az [Intune app Protection-szabályzatok](https://docs.microsoft.com/intune/app-protection-policy)támogatásához. Például korlátozhatja az Exchange Online-hoz való hozzáférést az Outlook alkalmazáshoz. A jóváhagyott ügyfélalkalmazások használatát igénylő feltételes hozzáférési szabályzatot az [alkalmazás-alapú feltételes hozzáférési szabályzatnak](app-based-conditional-access.md)is nevezzük. A támogatott jóváhagyott ügyfélalkalmazások listáját lásd: az ügyfél- [alkalmazásra vonatkozó jóváhagyott követelmény](concept-conditional-access-grant.md#require-approved-client-app).

### <a name="app-protection-policy-preview"></a>App Protection-házirend (előzetes verzió)

Mivel az alkalmazottak a személyes és a munkahelyi feladatokhoz egyaránt használják a mobileszközök használatát, érdemes lehet az eszköz használatával hozzáférni a vállalati adatokhoz, még abban az esetben is, ha azokat nem Ön felügyeli.
Az [Intune app Protection-szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) segítségével megvédheti vállalata adatait a mobileszköz-kezelési (Mdm) megoldástól függetlenül.

Az alkalmazás-védelmi szabályzattal korlátozhatja az Azure AD-be jelentett ügyfélalkalmazások hozzáférését az [Intune app Protection-házirendekkel](https://docs.microsoft.com/intune/app-protection-policy). Például korlátozhatja az Exchange Online-hoz való hozzáférést az Intune app Protection-szabályzattal rendelkező Outlook alkalmazáshoz. Az alkalmazás-védelmi szabályzatot igénylő feltételes hozzáférési szabályzatok [alkalmazás-védelmi alapú feltételes hozzáférési szabályzatként](concept-conditional-access-session.md#application-enforced-restrictions)is ismertek. 

Az eszköznek regisztrálva kell lennie az Azure AD-ben ahhoz, hogy egy alkalmazás védett házirendként legyen megjelölve.

A támogatott szabályzatok által védett ügyfélalkalmazások listáját lásd: az [alkalmazás védelmére vonatkozó követelmény](concept-conditional-access-session.md#application-enforced-restrictions).

### <a name="terms-of-use"></a>Használati feltételek

Megkövetelheti, hogy a bérlő felhasználója beleférjen a használati feltételekbe, mielőtt hozzáférést kap egy erőforráshoz. Rendszergazdaként a használati feltételek konfigurálását és testreszabását PDF-dokumentum feltöltésével végezheti el. Ha a felhasználó beletartozik az alkalmazáshoz való hozzáféréshez, csak akkor kap hozzáférést, ha a használati feltételek megegyeznek.

## <a name="custom-controls-preview"></a>Egyéni vezérlők (előzetes verzió)

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

### <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozása

Egyéni vezérlő létrehozásához először kapcsolatba kell lépnie a használni kívánt szolgáltatóval. Az egyes nem Microsoft-szolgáltatók saját folyamattal és követelményekkel rendelkeznek a szolgáltatás regisztrálásához, előfizetéséhez vagy egyéb részévé tételéhez, és jelezniük kell, hogy a feltételes hozzáféréssel szeretne integrálni. Ezen a ponton a szolgáltató JSON formátumú adatblokkot biztosít Önnek. Ez az információ lehetővé teszi, hogy a szolgáltató és a feltételes hozzáférés együtt működjön a bérlő számára, létrehozza az új vezérlőt, és meghatározza, hogy a feltételes hozzáférés Hogyan állapítható meg, hogy a felhasználók sikeresen végrehajtották-e az ellenőrzést a szolgáltatóval.

Egyéni vezérlők nem használhatók az Identity Protection automatizálásához, amely többtényezős hitelesítést igényel, vagy a szerepköröket a Privileged Identity Managerben (PIM) kell megemelni.

Másolja a JSON-adatforrást, majd illessze be a kapcsolódó szövegmezőbe. Ne módosítsa a JSON-t, kivéve, ha explicit módon megértette az Ön által végzett módosítást. A változtatások miatt a szolgáltató és a Microsoft közötti kapcsolat megszakadhat, és előfordulhat, hogy az Ön és a felhasználók is zárolják magukat a fiókból.

Az egyéni vezérlő létrehozásának lehetősége a **feltételes hozzáférés** lap **kezelés** szakaszában található.

![Vezérlés](./media/controls/82.png)

Az **új egyéni vezérlő**elemre kattintva megnyílik egy panel, amely a vezérlőelem JSON-adataihoz tartozó szövegmezővel rendelkezik.  

![Vezérlés](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Egyéni vezérlő törléséhez először gondoskodnia kell arról, hogy ne legyen használatban semmilyen feltételes hozzáférési házirendben. Ha elkészült:

1. Ugrás az egyéni vezérlők listára
1. Kattintson a...  
1. Válassza a **Törlés** elemet.

### <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

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
