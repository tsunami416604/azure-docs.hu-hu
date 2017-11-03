A szervezetek használ több [(SaaS) szolgáltatott szoftver](https://azure.microsoft.com/overview/what-is-saas/) termelékenység alkalmazások mert felhőtechnológia és eszközök egyre jobban elérhető. SaaS-alkalmazások számának növekedésével válik kihívást, a rendszergazdák fiókok kezelése és hozzáférési jogokat, illetve a felhasználók menthetik a különböző jelszavukat. Ezeket az alkalmazásokat kezeléséhez külön-külön extra munkahelyi hoz létre, és kevésbé biztonságos.

* Sok jelszót nyomon követéséhez rendelkező alkalmazottak általában módszerekkel kevésbé biztonságos, ne feledje, hogy le jelszavak írása, vagy használja ugyanazt a jelszót több fiókon keresztül.
* Amikor új alkalmazott megérkeznek, vagy hagyja el egy, a fiókba kell külön-külön kiépített vagy rendszer leépíti.
* Emellett az alkalmazottak kezdheti SaaS-alkalmazások használatát a munkahelyi áthaladás nélkül informatikai, ami azt jelenti, hogy a rendszergazdák nem hagyott jóvá, és nem figyelési rendszereken a saját felhasználói hoznak létre.  

A megoldás összes ezekkel a kihívásokkal az egyszeri bejelentkezés (SSO). A legegyszerűbb több alkalmazás kezelése és egységes bejelentkezés élményt biztosít a felhasználók is. Azure Active Directory (Azure AD) robusztus SSO megoldást nyújt, és számos elérhető előre integrált alkalmazás, a rendszergazdák számára, hogy gyorsan állítson be egy új alkalmazást, és indítsa el a felhasználók kiépítés oktatóanyag rendelkezik.

## <a name="how-does-azure-active-directory-integrate-apps"></a>Hogyan Azure Active Directory alkalmazások integrálása?
Azure AD lehetővé teszi az alkalmazások és a kiépített fiókok integrálhatja. Ezt rábízhatja a két módszer egyikét.

* Ha az alkalmazás előre integrált az alkalmazásban gyűjteménye, megnyithatja, hogy az alkalmazások telepítése és konfigurálása a beállítások lehetővé teszik a SSO portálon keresztül. Minden gyűjtemény alkalmazás kezdheti által hajtsa végre az egyszerű részletes útmutatás a jelenik meg a gyűjtemény és az Azure-portálon való egyszeri bejelentkezés engedélyezése.
* Ha az alkalmazás nincs a katalógusban, továbbra is állíthatja be a legtöbb alkalmazások az Azure AD egyéni alkalmazásként. Ehhez egy kicsit nagyobb technikai segítséget konfigurálása. Bármely alkalmazás, amely támogatja az SAML 2.0, mint egy összevont alkalmazás vagy a bármely alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal jelszó SSO alkalmazásként is hozzáadhat.

Abban az esetben, ha a felhasználók által létrehozott SaaS-alkalmazásokhoz, amelyek nem felügyeli a saját felhasználói informatikai, a [a Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) eszköz megoldást kínál. Ez az eszköz azonosítására, mely alkalmazások vannak használatban a szervezet és a hozzájuk használó felhasználók száma a webes forgalom figyelése Informatikai ezek az információk segítségével megtudhatja, milyen alkalmazásokat a felhasználók által előnyben részesített, és döntse el, amely az egyszeri bejelentkezés az Azure AD-be integrálható.  

Alkalmazások integrálása az Azure AD-be, amikor leképezheti a felhasználók meglévő alkalmazás identitását a saját Azure AD-identitásokat.  

