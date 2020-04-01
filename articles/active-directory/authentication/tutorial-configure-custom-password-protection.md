---
title: Egyéni Azure Active Directory jelszavas védelmi listák konfigurálása
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyéni tiltott jelszavas védelmi listákat az Azure Active Directoryhoz a környezetben előforduló gyakori szavak korlátozásához.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252844"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Oktatóanyag: Egyéni tiltott jelszavak konfigurálása az Azure Active Directory jelszavas védelméhez

A felhasználók gyakran hoznak létre olyan jelszavakat, amelyek gyakori helyi szavakat használnak, például iskolát, sportcsapatot vagy híres személyt. Ezek a jelszavak könnyen kitalálható, és gyenge a szótáralapú támadások ellen. Erős jelszavak kényszerítése a szervezetben, az Azure Active Directory (Azure AD) egyéni tiltott jelszó lista segítségével adott karakterláncok kiértékelése és letiltása. A jelszómódosítási kérelem sikertelen, ha az egyéni tiltott jelszavak listájában egyezés van.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyéni tiltott jelszavak engedélyezése
> * Bejegyzések hozzáadása az egyéni tiltott jelszavak listájához
> * A jelszómódosítások tesztelése tiltott jelszóval

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Szükség esetén [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Nem rendszergazdai felhasználó, olyan jelszóval, mint például *a tesztfelhasználó.* Ebben az oktatóanyagban ezzel a fiókkal tesztelheti a jelszómódosítási eseményt.
    * Ha felhasználót kell létrehoznia, olvassa [el a Rövid útmutató: Új felhasználók hozzáadása az Azure Active Directoryhoz című témakört.](../add-users-azure-active-directory.md)
    * A jelszómódosítási művelet tiltott jelszóval történő teszteléséhez az Azure [AD-bérlőt konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz.](tutorial-enable-sspr.md)

## <a name="what-are-banned-password-lists"></a>Mik azok a tiltott jelszólisták?

Az Azure AD tartalmaz egy globális tiltott jelszó listát. A globális tiltott jelszavak listájának tartalma nem külső adatforráson alapul. Ehelyett a globális tiltott jelszó lista az Azure AD biztonsági telemetriai adatok és elemzés folyamatos eredményein alapul. Amikor egy felhasználó vagy rendszergazda megpróbálja módosítani vagy alaphelyzetbe állítani hitelesítő adatait, a rendszer összeveti a kívánt jelszót a tiltott jelszavak listájával. A jelszómódosítási kérelem sikertelen, ha egyezés van a globális tiltott jelszavak listájában.

Annak érdekében, hogy rugalmasan megtudja határozni az engedélyezett jelszavakat, egyéni tiltott jelszólistát is megadhat. Az egyéni tiltott jelszavak listája a globális tiltott jelszavak listájával együtt erős jelszavak at kényszerít a szervezetben. Szervezeti-specifikus kifejezések adhatók hozzá az egyéni tiltott jelszavak listájához, például a következő példák:

* Márkanevek
* Terméknevek
* Helyek, például a vállalat központja
* Vállalatspecifikus belső kifejezések
* Konkrét vállalati jelentéssel bíró rövidítések

Amikor egy felhasználó megpróbál visszaállítani egy jelszót valamire, ami a globális vagy egyéni tiltott jelszavak listáján szerepel, az alábbi hibaüzenetek egyikét látja:

* *Sajnos a jelszó olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálja a jelszót. Próbálkozzon újra egy másik jelszóval.*
* *Sajnos nem használhatja ezt a jelszót, mert olyan szavakat vagy karaktereket tartalmaz, amelyeket a rendszergazda blokkolt. Próbálkozzon újra egy másik jelszóval.*

Az egyéni tiltott jelszavak listája legfeljebb 1000 kifejezésre korlátozódik. Nem nagy jelszavak blokkolására tervezték. Az egyéni tiltott jelszavak listájának előnyeinek maximalizálásához tekintse át az [egyéni tiltott jelszólista fogalmait](concept-password-ban-bad.md#custom-banned-password-list) és a [jelszóértékelési algoritmus áttekintését.](concept-password-ban-bad.md#how-are-passwords-evaluated)

## <a name="configure-custom-banned-passwords"></a>Egyéni tiltott jelszavak konfigurálása

Engedélyezze az egyéni tiltott jelszavak listáját, és adjon hozzá néhány bejegyzést. Az egyéni tiltott jelszavak listájához bármikor hozzáadhat további bejegyzéseket.

Az egyéni tiltott jelszavak listájának engedélyezéséhez és bejegyzések hozzáadásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) *egy globális rendszergazdai* engedélyekkel rendelkező fiókhasználatával.
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **biztonság** a menüben a bal oldalon.
1. A **Kezelés** menüfejléc csoportban válassza a **Hitelesítési módszerek**lehetőséget, majd a **Jelszóvédelem**lehetőséget.
1. Állítsa az **Egyéni lista kényszerítése** beállítást *Igen (Igen)* beállításra.
1. Karakterláncok hozzáadása az **Egyéni tiltott jelszó listához**, soronként egy karakterlánc. A következő szempontok és korlátozások vonatkoznak az egyéni tiltott jelszavak listájára:

    * Az egyéni tiltott jelszavak listája legfeljebb 1000 kifejezést tartalmazhat.
    * Az egyéni tiltott jelszólista nem megkülönbözteti a kis- és nagybetűket.
    * Az egyéni tiltott jelszavak listája gyakori karakterhelyettesítést vesz figyelembe, például "o" és "0", illetve "a" és "@".
    * A karakterlánc minimális hossza négy karakter, a maximális pedig 16 karakter.

    Adja meg a kitiltandó egyéni jelszavakat, ahogy az a következő példában látható

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Hagyja meg a **Jelszavas védelem engedélyezése** a Windows Server Active Directory címén *nem lehetőséget.*
1. Az egyéni tiltott jelszavak és a bejegyzések engedélyezéséhez válassza a **Mentés lehetőséget.**

Az egyéni tiltott jelszólista frissítése több órát is igénybe vehet.

Hibrid környezetesetén [az Azure AD jelszavas védelmét is üzembe helyezheti egy helyszíni környezetben.](howto-password-ban-bad-on-premises-deploy.md) Ugyanazok at használják a globális és az egyéni tiltott jelszólisták a felhőbeli és az on-prem jelszómódosítási kérelmekhez is.

## <a name="test-custom-banned-password-list"></a>Egyéni tiltott jelszavak listájának tesztelése

Ha működés közben szeretné látni az egyéni tiltott jelszavak listáját, próbálja meg módosítani a jelszót az előző szakaszban megadott változatra. Amikor az Azure AD megpróbálja feldolgozni a jelszómódosítása, a jelszó egyeztetve van egy bejegyzés sel az egyéni tiltott jelszavak listájában. Ezután hibaüzenet jelenik meg a felhasználó számára.

> [!NOTE]
> Ahhoz, hogy a felhasználó alaphelyzetbe állíthassa a jelszavát a webalapú portálon, az Azure [AD-bérlőt konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz.](tutorial-enable-sspr.md)

1. Nyissa meg a Saját [https://myapps.microsoft.com](https://myapps.microsoft.com) **alkalmazások** lapot a ban.
1. A jobb felső sarokban jelölje ki a nevét, majd válassza a **Profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. A **Profil** lapon válassza a **Jelszó módosítása**lehetőséget.
1. A **Jelszó módosítása** lapon adja meg a meglévő (régi) jelszót. Adjon meg és erősítsen meg egy új jelszót, amely szerepel az előző szakaszban megadott egyéni tiltott jelszólistában, majd válassza a **Küldés lehetőséget.**
1. A rendszer hibaüzenetet ad vissza, amely arról tájékoztat, hogy a rendszergazda letiltotta a jelszót, ahogy az a következő példában is látható:

    ![Az egyéni tiltott jelszavak listájának részét tartalmazó jelszó használatakor megjelenő hibaüzenet jelenik meg](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a továbbiakban nem szeretné használni az oktatóanyag részeként konfigurált egyéni tiltott jelszólistát, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **biztonság** a menüben a bal oldalon.
1. A **Kezelés** menüfejléc csoportban válassza a **Hitelesítési módszerek**lehetőséget, majd a **Jelszóvédelem**lehetőséget.
1. Állítsa az **Egyéni lista kényszerítése** beállítást *Nem*beállításra.
1. Az egyéni tiltott jelszó konfigurációjának frissítéséhez válassza a **Mentés gombot.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte és konfigurálta az Azure AD egyéni jelszavas védelmi listáit. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyéni tiltott jelszavak engedélyezése
> * Bejegyzések hozzáadása az egyéni tiltott jelszavak listájához
> * A jelszómódosítások tesztelése tiltott jelszóval

> [!div class="nextstepaction"]
> [A kockázatalapú Azure Multi-Factor Authentication engedélyezése](tutorial-mfa-applications.md)
