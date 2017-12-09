---
title: "Alkalmazások közzététele az Azure AD-alkalmazásproxyval | Microsoft Docs"
description: "A helyszíni alkalmazások közzététele a felhőbe az Azure AD alkalmazásproxy az Azure portálon."
services: active-directory
documentationcenter: 
author: kgremban
manager: michael.tillman
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 6f848bf619756197e93278f81bc93dfaaf96f0f4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Alkalmazások közzététele az Azure AD-alkalmazásproxyval

Támogatja a távoli dolgozók által az interneten keresztül érhető el a helyszíni alkalmazások közzététele az Azure Active Directory (AD) alkalmazásproxy segítségével. Közzéteheti ezeket az alkalmazásokat az Azure portálon keresztül történő biztonságos távoli hozzáférést a a hálózaton kívülről.

Ez a cikk végigvezeti a lépéseken, az alkalmazásproxy helyszíni alkalmazás közzététele. Ez a cikk befejezése után a felhasználók tudnak távolról fér hozzá az alkalmazást. – Ekkor készen áll az alkalmazás, például egyszeri bejelentkezés, a személyre szabott adatok és a biztonsági követelmények további funkciók konfigurálása.

Ha most ismerkedik az alkalmazásproxy, további információ a cikk Ez a szolgáltatás [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>A távoli hozzáférés a helyszíni alkalmazások közzététele

Kövesse az alábbi lépéseket az alkalmazásproxy az alkalmazások közzététele. Ha még nem már letöltötte és összekötő konfigurálva a szervezet számára, folytassa a [az alkalmazásproxy első lépései, és az összekötő telepítéséhez](active-directory-application-proxy-enable.md) első, majd tegye közzé az alkalmazást.

> [!TIP]
> Ha a kimenő alkalmazásproxy első alkalommal tesztelést, válassza ki a olyan alkalmazás, amely jelszóalapú hitelesítés van beállítva. Alkalmazásproxy más típusú hitelesítés támogatja, de jelszó-alapú alkalmazások létrehozásához a legegyszerűbb, akinek gyorsan. 

1. Jelentkezzen be rendszergazdaként a a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **új alkalmazás**.

  ![Vállalati alkalmazás hozzáadása](./media/application-proxy-publish-azure-portal/add-app.png)

3. Válassza ki **összes**, majd jelölje be **helyszíni alkalmazás**.  

  ![Saját alkalmazás felvétele](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Adja meg a következő információkat az alkalmazásról:

   - **Név**: az alkalmazás a hozzáférési panelre, és az Azure portálon megjelenő nevét. 

   - **Belső URL-cím**: az URL-cím a magánhálózaton belülről történő alkalmazás eléréséhez használt. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Ezzel a módszerrel tegye közzé a ugyanarra a kiszolgálóra, mint a különböző alkalmazások különböző helyeket, és adjon minden egyes saját nevet és hozzáférési szabályokat.

     > [!TIP]
     > Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Például ha az alkalmazása a https://yourapp/app helyen található, és a https://yourapp/media helyen található képeket használ, elérési útként a következőt javasolt közzétenni: https://yourapp/. A belső URL-cím nem jelennek meg a felhasználók kezdőlapja kell. További információkért lásd: [állítsa be a közzétett alkalmazások egyéni kezdőlapját](application-proxy-office365-app-launcher.md).

   - **Külső URL-cím**: A címet a felhasználók halad át a alkalmazást a hálózaton kívüli eléréséhez. Ha nem szeretné, az alapértelmezett alkalmazásproxy tartományát szeretné használni, olvassa el [egyéni tartományok az Azure AD alkalmazásproxy](active-directory-application-proxy-custom-domains.md).
   - **Előzetes hitelesítésének**: hogyan alkalmazásproxy előtt adjon hozzáférést a az alkalmazás ellenőrzi a felhasználót. 

     - Azure Active Directory: Az alkalmazásproxy átirányítja a felhasználókat az Azure AD bejelentkezési oldalára, ahol megtörténik a címtár és az alkalmazás használatára vonatkozó engedélyeik hitelesítése. Azt javasoljuk, hogy ez a beállítás az alapértelmezett, így például a feltételes hozzáférés és a többtényezős hitelesítés az Azure AD biztonsági funkciók előnyeit élvezheti.
     - Átengedés: A felhasználók hitelesítése az Azure Active Directory hozzáférni az alkalmazáshoz nem tartozik. Hitelesítési követelmények, a háttérkiszolgálón is állíthat be.
   - **Összekötő csoport**: összekötők feldolgozni a távoli elérés az alkalmazáshoz, és összekötő csoportok összekötők és a terület, a hálózati vagy a cél alkalmazások rendszerezése súgó. Ha nincs még létrehozva összekötő csoportok, az alkalmazás hozzá van rendelve **alapértelmezett**.

   ![Az alkalmazás konfigurálása](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Szükség esetén további beállításokat. A legtöbb alkalmazás esetén kell venni ezeket a beállításokat az alapértelmezett állapotra. 
   - **Háttér-alkalmazás időtúllépés**: az érték **hosszú** csak akkor, ha az alkalmazás lassú a hitelesítéshez és csatlakozáshoz. 
   - **Fejlécek URL-címek fordítása**: ezt az értéket tartsa **Igen** kivéve, ha az alkalmazás a hitelesítési kérés az eredeti állomásfejléc szükséges.
   - **A kérelem törzsében URL-címek fordítása**: ezt az értéket tartsa **nem** kivéve, ha más helyszíni alkalmazások szoftveresen kötött HTML hivatkozások, és ne használjon egyéni tartományokat. További információkért lásd: [hivatkozásra az alkalmazásproxy fordítási](application-proxy-link-translation.md).
   
   ![Az alkalmazás konfigurálása](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Válassza a **Hozzáadás** lehetőséget.


## <a name="add-a-test-user"></a>Teszt felhasználó hozzáadása 

Tesztelje, hogy az alkalmazás megfelelően lett-e közzétéve, vegye fel egy olyan felhasználói fiókot. Győződjön meg arról, hogy ez a fiók rendelkezik-e engedélyekkel a alkalmazást a vállalati hálózaton belül eléréséhez.

1. Vissza a – első lépések panelen válassza ki a **rendelje hozzá a felhasználót tesztelési**.

  ![Rendelje hozzá a felhasználót teszteléshez](./media/application-proxy-publish-azure-portal/assign-user.png)

2. A felhasználók és csoportok panelen, jelölje ki **Hozzáadás**.

  ![Egy felhasználó vagy csoport hozzáadása](./media/application-proxy-publish-azure-portal/add-user.png)

3. A hozzárendelés hozzáadása panelen válassza ki a **felhasználók és csoportok** válassza ki a hozzáadni kívánt fiókot. 
4. Válassza ki **hozzárendelése**.

## <a name="test-your-published-app"></a>A közzétett alkalmazás tesztelése

A böngészőben navigáljon a közzététel lépés során konfigurált külső URL-CÍMÉT. Meg kell láthatja a kezdőképernyőn, és a beállított vizsgálati fiókkal bejelentkezni.

![A közzétett alkalmazás tesztelése](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Következő lépések
- [Töltse le az összekötők](active-directory-application-proxy-enable.md) és [összekötő csoportok létrehozása a](active-directory-application-proxy-connectors-azure-portal.md) külön hálózatok és helyek alkalmazások közzétételét.

- [Egyszeri bejelentkezés beállítása](application-proxy-sso-azure-portal.md) az újonnan közzétett alkalmazások
