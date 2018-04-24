# Áttekintés
## [Mi az Azure AD Connect?](active-directory-aadconnect.md)
## [Mi az Azure AD Connect-szinkronizálás?](active-directory-aadconnectsync-whatis.md)
### [Felhasználók és kapcsolatok](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Architektúra](active-directory-aadconnectsync-understanding-architecture.md)
### [Deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Deklaratív üzembehelyezési kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Alapértelmezett konfiguráció](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Mi az Azure AD Connect és az összevonás?](active-directory-aadconnectfed-whatis.md)


# Bevezetés
## [Előfeltételek](active-directory-aadconnect-prerequisites.md)
## [Az Azure AD Connect telepítése](active-directory-aadconnect-select-installation.md)
### [Gyorsbeállítások](active-directory-aadconnect-get-started-express.md)
### [Egyéni beállítások](active-directory-aadconnect-get-started-custom.md)
### [Frissítés a DirSync szolgáltatásról](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Frissítés korábbi verzióról](active-directory-aadconnect-upgrade-previous-version.md)
### [Telepítés meglévő ADSync-adatbázis használatával](active-directory-aadconnect-existing-database.md)
### [Telepítés SQL-lel delegált rendszergazdai engedélyekkel](active-directory-aadconnect-sql-delegation.md)

# Útmutató
## Tervezés és kialakítás
### [Tervezési alapelvek](active-directory-aadconnect-design-concepts.md)
### [Azure AD Connect-topológiák](active-directory-aadconnect-topologies.md)
### [Az Active Directory összevonási szolgáltatások az Azure-ban](active-directory-aadconnect-azure-adfs.md)
### [Példányokra vonatkozó különleges szempontok](active-directory-aadconnect-instances.md)
### [Ha már rendelkezik az Azure AD-vel](active-directory-aadconnect-existing-tenant.md)
## [Az Azure AD Connect kezelése](active-directory-aadconnect-whats-next.md)
### [O365- és Azure AD-tanúsítványok megújítása](active-directory-aadconnect-o365-certs.md)
### [Active Directory összevonási szolgáltatások- (AD FS-) farm SSL-tanúsítványának frissítése](active-directory-aadconnectfed-ssl-update.md)
### [Eszközvisszaírás engedélyezése](active-directory-aadconnect-feature-device-writeback.md)
### [Felhasználói bejelentkezési lehetőségek](active-directory-aadconnect-user-signin.md)
#### [Közvetlen egyszeri bejelentkezés](active-directory-aadconnect-sso.md)
##### [Első lépések](active-directory-aadconnect-sso-quick-start.md)
##### [Hogyan működik?](active-directory-aadconnect-sso-how-it-works.md)
##### [Gyakori kérdések](active-directory-aadconnect-sso-faq.md)
##### [Hibaelhárítás](active-directory-aadconnect-troubleshoot-sso.md)
##### [GDPR](active-directory-aadconnect-sso-gdpr.md)
#### [Átmenő hitelesítés](active-directory-aadconnect-pass-through-authentication.md)
##### [Első lépések](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [Hogyan működik?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [Előzetes verziójú ügynökök frissítése](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [Intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [A biztonság részletes bemutatása](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
##### [GDPR](active-directory-aadconnect-pass-through-authentication-gdpr.md)
### [Többtartományos támogatás összevonási szolgáltatásokhoz](active-directory-aadconnect-multiple-domains.md)
### [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md)
### [SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez](active-directory-aadconnect-federation-saml-idp.md)
### [A bejelentkezéshez e-mail-címet használó vendégfelhasználói fiókok szinkronizálása](active-directory-aadconnect-guest-sync.md)


## Azure AD Connect-szinkronizálás kezelése
### [GDPR-megfelelőség és az Azure AD Connect](active-directory-aadconnect-gdpr.md)
### [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Jelszókivonat szinkronizálása](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
### [Azure AD-szolgáltatásfiók](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Telepítő varázsló](active-directory-aadconnectsync-installation-wizard.md)
### [A UserPrincipalName feltöltésének menete](active-directory-aadconnect-userprincipalname.md)
### [Az alapértelmezett konfiguráció módosítása](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [A szűrés konfigurálása](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [Címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Az Azure AD Sync szolgáltatásfiók jelszavának módosítása](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Az AD DS-fiók jelszavának módosítása](active-directory-aadconnectsync-change-addsacct-pass.md)
### [AD lomtárának engedélyezése](active-directory-aadconnectsync-recycle-bin.md)

### [Szinkronizálási szolgáltatáskezelő](active-directory-aadconnectsync-service-manager-ui.md)
#### [Műveletek](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Összekötők](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Metaverzumtervező](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Keresés a metaverzumban](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Összevonási szolgáltatások kezelése
### [Kezelés és testreszabás](active-directory-aadconnect-federation-management.md)
### [Több Azure AD-példány összevonása egyetlen AD FS-példánnyal](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Hibaelhárítás
### [Kapcsolatok](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Hibák a szinkronizálás során](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [Az objektum nincs szinkronizálva](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Objektum szinkronizálása a hibaelhárítási feladat használatával](active-directory-aadconnect-troubleshoot-objectsync.md)
### [Jelszókivonat szinkronizálása](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)
### [userCertificate által okozott LargeObject hiba](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Helyreállítás 10 GB-ra korlátozott LocalDB adatbázisból](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Referencia
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Identitásszinkronizálás és ismétlődő attribútumok rugalmassága](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Hibrid identitás – szükséges portok és protokollok](active-directory-aadconnect-ports.md)
## [Az előzetes verzió szolgáltatásai](active-directory-aadconnect-feature-preview.md)
## [Verzióelőzmények](active-directory-aadconnect-version-history.md)
## [Fiókok és engedélyek](active-directory-aadconnect-accounts-permissions.md)

## Az Azure AD Connect szinkronizálása
### [Az Azure Active Directoryval szinkronizált attribútumok](active-directory-aadconnectsync-attributes-synchronized.md)
### [Összekötő verziókiadásai](active-directory-aadconnectsync-connector-version-history.md)
### [Függvényreferencia](active-directory-aadconnectsync-functions-reference.md)
### [Üzemeltetési feladatok és szempontok](active-directory-aadconnectsync-operations.md)
### [Az Azure AD összevonás kompatibilitási listája](active-directory-aadconnect-federation-compatibility.md)
### [Műszaki fogalmak](active-directory-aadconnectsync-technical-concepts.md)
### [Szolgáltatásjellemzők](active-directory-aadconnectsyncservice-features.md)




# Kapcsolódó témakörök
## [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../connect-health/active-directory-aadconnect-health.md)
## [Hibrid identitáskezelés – kialakítási útmutató](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect – gyakori kérdések](active-directory-aadconnect-faq.md)
##[DirSync elavulása](active-directory-aadconnect-dirsync-deprecated.md)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

