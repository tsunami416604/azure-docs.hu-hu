<properties
    pageTitle="Azure Active Directory – gyakori kérdések | Microsoft Azure"
    description="Az Azure Active Directoryval kapcsolatos gyakori kérdések az Azure és az Azure Active Directory elérésével, a jelszókezeléssel és az alkalmazások elérésével kapcsolatos kérdésekre ad választ."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>


# Azure Active Directory – gyakori kérdések

Az Azure Active Directory egy átfogó szolgáltatott identitási (IDaaS) megoldás, amely az identitások, a hozzáférés-kezelés és a biztonság minden szempontját lefedi.


További részletekért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md).



## Az Azure és az Azure Active Directory elérése


**K: Miért kapok „Nem találhatók előfizetések” üzenetet, amikor megpróbálom elérni az Azure AD-t a klasszikus Azure portálon (https://manage.windowsazure.com)?**

**V:** A klasszikus Azure portál eléréséhez mindegyik felhasználónak engedélyekre van szüksége egy Azure-előfizetés keretében. Ha fizetős Office 365- vagy Azure AD-fiókkal rendelkezik, látogasson el a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) címre egy egyszeri aktiváláshoz. Egyébként aktiválnia kell egy teljes[Azure-próbaverziót](https://azure.microsoft.com/pricing/free-trial/) vagy egy fizetős előfizetést. 

További információ:

- [How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](active-directory-how-subscriptions-associated-directory.md)

- [Manage the directory for your Office 365 subscription in Azure (Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban)](active-directory-manage-o365-subscription.md)

---

**K: Milyen kapcsolat áll fenn az Azure AD, az Office 365 és az Azure között?**

**V:** Az Azure Active Directory általános identitás- és hozzáférési képességeket nyújt az összes Microsoft Online-szolgáltatáshoz. Akár az Office 365, a Microsoft Azure, az Intune vagy más rendszert használ, már használja az Azure AD-t a bejelentkezéshez és a hozzáférés-kezeléshez ezen szolgáltatások mindegyikéhez. 

Valójában a Microsoft Online-szolgáltatásokhoz Ön által engedélyezett összes felhasználó felhasználói fiókként van meghatározva egy vagy több Azure AD-példányban. Engedélyezheti ezeket a fiókokat az ingyenes Azure AD-képességekhez, például a felhőalkalmazások eléréséhez.
 
Ezenkívül a fizetős Azure AD-szolgáltatások (pl. az alapszintű, prémium, EMS Azure AD stb.) átfogó vállalati méretezéskezelési és biztonsági megoldásokkal egészítenek ki más online szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.


---



## Első lépések a Hybrid Azure AD-ben


**K: Hogyan csatlakoztathatom a helyszíni címtáramat az Azure AD-hez?**

**V:** A helyszíni címtárát az **Azure AD Connect** használatával csatlakoztathatja az Azure AD-hez. 

További részletekért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).


---

**K: Hogyan állíthatok be egyszeri bejelentkezést a helyszíni címtár és a felhőalkalmazásaim között?**

**V:** Csak a helyszíni címtár és az Azure AD között kell beállítania egyszeri bejelentkezést. Amíg az Azure AD-n keresztül éri el a felhőalkalmazásokat, a szolgáltatás automatikusan átirányítja a felhasználókat, hogy megfelelően hitelesíteni tudják magukat a helyszíni hitelesítő adataikkal.

Az egyszeri bejelentkezés helyszínről végzett implementálása könnyen elérhető olyan összevonási megoldásokkal, mint az ADFS, illetve a jelszókivonat-szinkronizálás konfigurálásával. Könnyedén telepítheti mindkét lehetőséget az Azure AD Connect konfigurációs varázslójával.
  

További részletekért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
  

---

**K: Az Azure Active Directoryban elérhető önkiszolgáló portál a szervezetemben lévő felhasználók számára?**

**V:** Igen, az Azure Active Directory az [Azure AD Hozzáférési panelén](http://myapps.microsoft.com) keresztül biztosítja a felhasználóknak az önkiszolgáló felületet és az alkalmazások elérését. Ha Ön Office 365-felhasználó, észreveheti, hogy számos képesség megegyezik az Office 365 portál képességeivel. 

További információkért lásd: [Bevezetés a Hozzáférési panel használatába](active-directory-saas-access-panel-introduction.md). 



---

**K: Az Azure AD segít a helyszíni infrastruktúrám kezelésében?**

**V:** Igen, segít. Az Azure AD Prémium kiadás tartalmazza a **Connect Health** eszközt. Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  

További részletekért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](active-directory-aadconnect-health.md).  

---

## Jelszókezelés

**K: Használhatom az Azure AD jelszóvisszaírást jelszó-szinkronizálás nélkül? (Avagy: az Azure AD SSPR-t jelszóvisszaírás nélkül szeretném használni, de nem szeretném a jelszavaimat a felhőben tárolni.)**

**V:** Nem kell szinkronizálnia az AD-jelszavakat az Azure AD-vel a visszaírás engedélyezéséhez. Összevont környezetben az Azure AD SSO a helyszíni címtárra támaszkodik a felhasználó hitelesítéséhez. Ehhez a forgatókönyvhöz nem kell nyomon követni a helyszíni jelszót az Azure AD-ben.

---

**K: Mennyi ideig tart a jelszavak visszaírása az AD-ben a helyszínen?**

**V:** A jelszavak visszaírása valós időben történik. 

További részletekért lásd: [A jelszókezelés első lépései](active-directory-passwords-getting-started.md) 


---

**K: Használhatok jelszóvisszaírást rendszergazda által kezelt jelszavakhoz?**

**V:** Igen, ha engedélyezett a jelszóvisszaírás, a rendszergazda által végzett jelszóműveleteket a rendszer visszaírja a helyszíni környezetbe.  

A jelszavakkal kapcsolatos kérdésekre adott további válaszokért lásd: [Jelszókezelés – gyakori kérdések](active-directory-passwords-faq.md).

---

## Alkalmazás-hozzáférés


**K: Hol találom az Azure Ad-vel előre integrált alkalmazások és azok képességeinek listáját?**

**V:** Az Azure AD a Microsoft vállalat, az alkalmazásszolgáltatók és a partnerek több mint 2600 előre integrált alkalmazásával rendelkezik. Mindegyik előre integrált alkalmazás támogatja az egyszeri bejelentkezést. Az egyszeri bejelentkezéssel a szervezeti hitelesítő adatokkal érheti el az alkalmazásokat. Néhány alkalmazás az automatikus üzembe helyezést és megszüntetést is támogatja.

Az előre integrált alkalmazások teljes listájáért lásd: [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**K: Mit tegyek, ha a kívánt alkalmazás nem szerepel az Azure AD Marketplace-en?**

**V:** Az Azure AD Prémium kiadásban bármely alkalmazást felveheti és konfigurálhatja. Az alkalmazás képességeitől és a beállításaitól függően konfigurálhat egyszeri bejelentkezést és automatikus üzembe helyezést.  

További információ:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](active-directory-saas-custom-apps.md)
- [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](active-directory-scim-provisioning.md) 


---

**K: Hogyan jelentkeznek be a felhasználók az alkalmazásokba az Azure Active Directoryval?**
 
**V:** Az Azure Active Directory több módot nyújt arra, hogy a felhasználók megtekinthessék és elérhessék az alkalmazásaikat, például a következőket:

- Azure AD Hozzáférési panel

- Office 365 alkalmazásindító

- Közvetlen bejelentkezés az összevont alkalmazásokba

- Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

További információk: [Azure AD integrált alkalmazások üzembe helyezése a felhasználók számára](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**K: Az Azure Active Directory milyen különböző módokon teszi lehetővé a hitelesítést és az egyszeri bejelentkezést az alkalmazásokba?**
 
**V:** Az Azure Active Directory számos szabványos protokollt támogat a hitelesítéshez és az engedélyezéshez, például ilyen a SAML 2.0, az OpenID Connect, az OAuth 2.0 és a WS-Federation. Az Azure AD a jelszótárolást és az automatikus bejelentkezési képességeket is támogatja olyan alkalmazásoknál, amelyek csak az űrlapalapú hitelesítést támogatják.  

További információkért lásd:

- [Hitelesítési forgatókönyvek az Azure AD-hez](active-directory-authentication-scenarios.md)

- [Az Active Directory hitelesítési protokolljai](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Hogyan működik az egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**K: Felvehetek helyszínen futtatott alkalmazásokat?**

**V:** Az Azure AD-alkalmazásproxy egyszerű és biztonságos hozzáférést nyújt az Ön által választott helyszíni webalkalmazásokhoz. Ezeket az alkalmazásokat ugyanúgy érheti el, ahogyan a SaaS-alkalmazásokat az Azure Active Directoryban. Nincs szükség VPN-re vagy a hálózati infrastruktúra módosítására.  

További információ: [How to provide secure remote access to on-premises applications](active-directory-application-proxy-get-started.md) (Helyszíni alkalmazások biztonságos távoli elérése).


--- 

**K: Hogyan követelhetem meg az MFA-t adott alkalmazásokhoz hozzáféréssel rendelkező felhasználóknál?**

**V:** Az Azure AD feltételes hozzáférésével minden alkalmazáshoz egyedi hozzáférési házirendet rendelhet. A házirendjében mindig megkövetelheti az MFA-t, vagy csak akkor, amikor a felhasználók nem csatlakoznak a helyi hálózathoz.  

További információ: [Az Office 365 és az Azure Active Directoryhoz csatlakoztatott egyéb alkalmazások hozzáférésének biztonságossá tétele](active-directory-conditional-access.md).


---

**K: Mi a SaaS-alkalmazások automatizált felhasználókiépítése?**

**V:** Az Azure Active Directory lehetővé teszi, hogy automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását számos népszerű felhőalapú (SaaS) alkalmazásban. 

További információ: [A felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](active-directory-saas-app-provisioning.md)

---






<!--HONumber=Sep16_HO4-->


