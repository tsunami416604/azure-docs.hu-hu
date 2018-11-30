---
author: zhangmanling
ms.service: cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 7f80c8f1773cfeb8ddfb222d068a5c6571c2e5c7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331058"
---
## <a name="prerequisites"></a>Előfeltételek
A CDN felügyeleti kódírás, mielőtt néhány előkészítő ahhoz, hogy a kódot, amellyel kezelheti az Azure Resource Manager kell elvégeznie. Ehhez az előkészítés, kell tennie:

* Hozzon létre egy erőforráscsoportot, ebben az oktatóanyagban létrehozott CDN-profil tartalmazza
* A hitelesítés, az alkalmazás Azure Active Directory konfigurálása
* Engedélyek alkalmazása az erőforráscsoportot, hogy az Azure AD-bérlőből csak a jogosult felhasználók kezelhessék a CDN-profilhoz

### <a name="creating-the-resource-group"></a>Az erőforráscsoport létrehozása
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Kattintson az **Erőforrás létrehozása** gombra.
3. Keresse meg **erőforráscsoport** és az erőforrás-csoport panelen kattintson **létrehozás**.

    ![Egy új erőforráscsoport létrehozása](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Adja az erőforráscsoportnak *CdnConsoleTutorial*.  Válassza ki az előfizetését, és válasszon egy Önhöz közeli helyet.  Ha szeretné, kattintson a **rögzítés az irányítópulton** jelölőnégyzet rögzítése az irányítópulton az erőforráscsoportot a portálon.  Kiemelt elemek újabb láthatóbbá.  Beállítás megadása után kattintson a **létrehozás**.

    ![Az erőforráscsoport elnevezése](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Miután az erőforráscsoport jön létre, ha nem rögzítette az irányítópultra, található kattintva **Tallózás**, majd **erőforráscsoportok**.  A megnyitáshoz kattintson az erőforráscsoport.  Jegyezze fel a **előfizetés-azonosító**. Szükségünk van rá később.

    ![Az erőforráscsoport elnevezése](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Az Azure AD-alkalmazás létrehozásához és engedélyek alkalmazásához
Alkalmazás-hitelesítés az Azure Active Directoryval kétféle módszerrel: egyéni felhasználók számára, vagy egy egyszerű szolgáltatást. Egyszerű szolgáltatás hasonlít a Windows-szolgáltatásfiókot.  Egy adott felhasználó a CDN-profilok interakcióba engedélyek megadását, helyett inkább engedélyekkel az egyszerű szolgáltatás.  Az egyszerű szolgáltatások jellemzően használják az automatikus, nem interaktív folyamat.  Bár ebben az oktatóanyagban egy interaktív konzolt alkalmazás ír, a szolgáltatás egyszerű módszer az fogunk összpontosítani.

Egyszerű szolgáltatás létrehozása több lépést, mint például az Azure Active Directory-alkalmazás létrehozása áll.  Szeretne létrehozni, azt, hogy fogunk [ezt az oktatóanyagot követve](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Ügyeljen arra, hogy az összes lépését kövesse a [társított oktatóanyag](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Ez *fontos* elvégzése, hogy pontosan leírtak szerint.  Mindenképpen jegyezze fel a **bérlőazonosító**, **bérlői tartomány neve** (általában egy *. onmicrosoft.com* tartomány, ha egy egyéni tartomány megadása), **ügyfél-azonosító** , és **ügyfél-hitelesítési kulcs**, mivel később ezt az információra van szükségünk.  Ügyeljen arra, hogy ezáltal az **ügyfél-azonosító** és **ügyfél-hitelesítési kulcs**, mivel ezeket a hitelesítő adatokat hajthat végre műveleteket, mint az egyszerű szolgáltatás bárki által használható.
>
> Amikor a több-bérlős alkalmazás konfigurálása nevű lépést, válassza ki a **nem**.
>
> Mikor jelenik meg a lépés [alkalmazások szerepkörhöz rendeléséhez](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), használja a korábban létrehozott erőforráscsoportot *CdnConsoleTutorial*, de helyett a **olvasó** szerepkör hozzárendelése a **CDN-profil Közreműködője** szerepkör.  Az alkalmazás hozzárendelése után a **CDN-profil Közreműködője** az erőforráscsoport ebben az oktatóanyagban lépjen vissza a szerepkört. 
>
>

Miután létrehozta az egyszerű szolgáltatásnév és hozzárendelt a **CDN-profil Közreműködője** szerepkör, a **felhasználók** az erőforráscsoport panelen a következő képhez hasonlóan kell kinéznie.

![Felhasználók panel](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktív felhasználói hitelesítéssel
Ha helyett egy egyszerű szolgáltatást, nem kell egyéni interaktív felhasználói hitelesítéssel, a folyamat hasonlít, amely egy egyszerű szolgáltatás számára.  Valójában szüksége, kövesse ugyanazt az eljárást, de néhány kisebb módosításokat.

> [!IMPORTANT]
> Csak kövesse a következő lépések, ha az egyes felhasználói hitelesítés használata helyett egy egyszerű szolgáltatás kiválasztása.
>
>

1. Az alkalmazás létrehozásakor helyett **webalkalmazás**, válassza a **natív alkalmazás**.

    ![Natív alkalmazás](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. A következő oldalon meg kell adni egy **átirányítási URI**.  Az URI-azonosítója nem érvényesíthető, de ne feledje, hogy a megadott. Szükség esetén később.
3. Nem kell létrehozni egy **ügyfél-hitelesítési kulcs**.
4. Egyszerű szolgáltatás hozzárendelése helyett a **CDN-profil Közreműködője** szerepkör fogunk hozzárendelése egyes felhasználókhoz vagy csoportokhoz.  Ebben a példában azt láthatja, hogy hozzárendelt *CDN Mintafelhasználó* , a **CDN-profil Közreműködője** szerepkör.  

    ![Az egyes felhasználói hozzáférés](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
