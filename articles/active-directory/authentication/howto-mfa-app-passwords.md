---
title: Az Azure Multi-Factor Authentication-Azure Active Directory alkalmazás jelszavának konfigurálása
description: Ismerje meg, hogyan konfigurálhatja és használhatja az alkalmazás jelszavait az Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b247b64d563bc2b12c5bffff6a460d77cb96207
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485601"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Az Azure Multi-Factor Authentication engedélyezése és használata az alkalmazás jelszavait használó örökölt alkalmazásokkal

Egyes alkalmazások, például az Office 2010 vagy a korábbi és az Apple mail előtt iOS 11, nem támogatják a többtényezős hitelesítést. Az alkalmazások nem úgy vannak konfigurálva, hogy elfogadják a hitelesítés másodlagos formáját vagy a kérést. Ha ezeket az alkalmazásokat biztonságos módon szeretné használni az Azure Multi-Factor Authentication a felhasználói fiókokhoz, az alkalmazás jelszavait is használhatja. Ezek az alkalmazások jelszavai lecserélték a hagyományos jelszót, hogy az alkalmazások megkerüljék a többtényezős hitelesítést, és megfelelően működjenek.

A modern hitelesítés a Microsoft Office 2013-ügyfelek és újabb rendszerek esetében támogatott. Az Office 2013-ügyfelek, beleértve az Outlookot, támogatják a modern hitelesítési protokollokat, és lehetővé tehetik a kétlépéses ellenőrzéssel való munkát. Az ügyfél engedélyezése után az alkalmazás jelszavai nem szükségesek az ügyfélhez.

Ez a cikk bemutatja, hogyan engedélyezheti és használhatja az alkalmazás jelszavait olyan örökölt alkalmazásokhoz, amelyek nem támogatják a többtényezős hitelesítési kéréseket.

>[!NOTE]
> Az alkalmazás jelszavai nem működnek a feltételes hozzáférésen alapuló multi-Factor Authentication házirendek és a modern hitelesítés használatával.

## <a name="overview-and-considerations"></a>Áttekintés és szempontok

Ha egy felhasználói fiók engedélyezve van az Azure Multi-Factor Authenticationhoz, a rendszer a rendszeres bejelentkezési kérést egy további ellenőrzésre vonatkozó kérelem megszakítja. Egyes régebbi alkalmazások nem értik ezt a megszakítást a bejelentkezési folyamat során, így a hitelesítés meghiúsul. A felhasználói fiókok biztonságának fenntartásához és az Azure Multi-Factor Authentication engedélyezéséhez az alkalmazás jelszavait a felhasználó normál felhasználóneve és jelszava helyett használhatja. Ha a bejelentkezés során használt alkalmazás jelszava nincs további ellenőrzési kérés, a hitelesítés sikeres lesz.

Az alkalmazás jelszavai automatikusan létrejönnek, és a felhasználó nem adja meg. Ez az automatikusan generált jelszó megnehezíti, hogy egy támadó kitalálja, hogy ez biztonságosabb. A felhasználóknak nem kell nyomon követniük a jelszavakat, vagy minden alkalommal be kell őket írni, amikor az alkalmazás jelszavait csak egyszer kell beírni.

Az alkalmazás jelszavainak használatakor a következő szempontokat kell figyelembe venni:

* Felhasználónként legfeljebb 40 alkalmazás jelszava lehet.
* A jelszavakat gyorsítótárazó és a helyszíni helyzetekben használatos alkalmazások sikertelenek lehetnek, mert az alkalmazás jelszava nem ismert a munkahelyi vagy iskolai fiókon kívül. Példa erre a forgatókönyvre a helyszíni Exchange-e-mailek, de az archivált levelek a felhőben vannak. Ebben az esetben ugyanez a jelszó nem működik.
* Ha az Azure Multi-Factor Authentication engedélyezve van egy felhasználói fiókban, az alkalmazás jelszavai a legtöbb nem böngészővel rendelkező ügyféllel használhatók, mint az Outlook és a Microsoft Skype vállalati verzió. A rendszergazdai műveletek azonban nem hajthatók végre az alkalmazások jelszavainak használatával a nem böngészőalapú alkalmazásokon, például a Windows PowerShellen keresztül. A műveletek akkor sem hajthatók végre, ha a felhasználó rendelkezik rendszergazdai fiókkal.
    * A PowerShell-parancsfájlok futtatásához hozzon létre egy erős jelszót tartalmazó szolgáltatásfiókot, és ne engedélyezze a fiókot kétlépéses ellenőrzéshez.

>[!WARNING]
> Az alkalmazások jelszavai nem működnek olyan hibrid környezetekben, ahol az ügyfelek a helyszíni és a Felhőbeli automatikus észlelési végpontokkal kommunikálnak. A helyi hitelesítéshez tartományi jelszavak szükségesek. A felhővel való hitelesítéshez az alkalmazás jelszavai szükségesek.

### <a name="app-password-names"></a>Alkalmazás jelszavának nevei

Az alkalmazás jelszavának nevének tükröznie kell azt az eszközt, amelyen a használatban van. Ha olyan laptopja van, amely nem böngésző alkalmazásokkal, például az Outlook, a Word és az Excel alkalmazással rendelkezik, hozzon létre egy **laptop** nevű alkalmazást az alkalmazások számára. Hozzon létre egy **Desktop** nevű másik alkalmazás-jelszót ugyanahhoz az asztali számítógépen futó alkalmazásokhoz.

Azt javasoljuk, hogy eszközönként hozzon létre egy alkalmazási jelszót, és ne egy alkalmazás jelszavaként.

## <a name="federated-or-single-sign-on-app-passwords"></a>Összevont vagy egyszeri bejelentkezési alkalmazás jelszavai

Az Azure AD támogatja az összevonást vagy az egyszeri bejelentkezést (SSO) helyszíni Active Directory tartományi szolgáltatások (AD DS). Ha a szervezete az Azure AD-vel összevont és Azure Multi-Factor Authenticationt használ, a következő alkalmazás-jelszóra vonatkozó szempontokat kell figyelembe venni:

>[!NOTE]
> A következő pontok csak az összevont (SSO) ügyfelekre érvényesek.

* Az alkalmazások jelszavait az Azure AD ellenőrzi, ezért az összevonás mellőzése. Az összevonás csak az alkalmazás jelszavának beállításakor használatos.
* Az identitás-szolgáltató (identitásszolgáltató) nem kapcsolódik az összevont (SSO) felhasználókhoz, a passzív folyamattól eltérően. Az alkalmazás jelszavait a munkahelyi vagy iskolai fiók tárolja. Ha a felhasználó elhagyja a vállalatot, a felhasználó adatai valós **időben, a** felhasználók adatait a munkahelyi vagy iskolai fiókba áramlanak. A fiók letiltása/törlése akár három órát is igénybe vehet, ami késleltetheti az alkalmazás jelszavának letiltását/törlését az Azure AD-ben.
* A helyszíni ügyfél Access Control beállításait az alkalmazás jelszavai szolgáltatása nem veszi figyelembe.
* Nem érhető el helyszíni hitelesítési naplózási vagy naplózási képesség az alkalmazás jelszavai szolgáltatásával.

Egyes speciális architektúrák a többtényezős hitelesítéshez szükséges hitelesítő adatok kombinációját igénylik az ügyfelekkel. Ezek a hitelesítő adatok a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót, valamint az alkalmazás jelszavait is tartalmazhatják. A követelmények attól függnek, hogyan történik a hitelesítés. A helyszíni infrastruktúrát, a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót hitelesítő ügyfelek esetében kötelező megadni. Az Azure AD-t hitelesítő ügyfelek esetében szükség van egy alkalmazás jelszavára.

Tegyük fel például, hogy a következő architektúrával rendelkezik:

* Active Directory helyszíni példányát az Azure AD-vel összevontuk.
* Az Exchange Online-t használja.
* A Skype vállalati verzió a helyszínen használható.
* Az Azure Multi-Factor Authentication-t használja.

Ebben az esetben a következő hitelesítő adatokat használja:

* A Skype vállalati verzióba való bejelentkezéshez használja a munkahelyi vagy iskolai fiókja felhasználónevét és jelszavát.
* Ha a címjegyzéket az Exchange Online-hoz kapcsolódó Outlook-ügyfélről szeretné elérni, használja az alkalmazás jelszavát.

## <a name="allow-users-to-create-app-passwords"></a>Alkalmazások jelszavainak létrehozásának engedélyezése a felhasználók számára

Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Az alkalmazás jelszava funkciót engedélyezni kell, mielőtt a felhasználók használni tudják őket. Ha lehetővé szeretné tenni a felhasználóknak az alkalmazás jelszavának létrehozását, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg és válassza ki a **Azure Active Directory**, majd a **felhasználók**lehetőséget.
3. A *felhasználók* ablak tetején lévő navigációs sávon válassza a **multi-Factor Authentication** lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai**elemet.
5. A **szolgáltatás beállításai** lapon jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget a nem böngésző alkalmazásokba való bejelentkezéshez** .

    ![Képernyőkép a Azure Portalról, amely a többtényezős hitelesítés szolgáltatás beállításait mutatja be az alkalmazás jelszavainak felhasználójának engedélyezéséhez](media/concept-authentication-methods/app-password-authentication-method.png)

## <a name="create-an-app-password"></a>Alkalmazás jelszavának létrehozása

Amikor a felhasználók befejezik az Azure Multi-Factor Authentication kezdeti regisztrációját, a regisztrációs folyamat végén lehetősége van az alkalmazás jelszavának létrehozására.

A felhasználók a regisztráció után is létrehozhatnak alkalmazás-jelszavakat. További információk és részletes lépések a felhasználók számára: Mik az [alkalmazások jelszavai az Azure-ban multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan engedélyezhető a felhasználók számára az Azure-Multi-Factor Authentication gyors regisztrálása: a [kombinált biztonsági információk regisztrációjának áttekintése](concept-registration-mfa-sspr-combined.md).
