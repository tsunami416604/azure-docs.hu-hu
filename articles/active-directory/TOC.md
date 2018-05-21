# Áttekintés
## [Mi az az Azure Active Directory?](active-directory-whatis.md)
## [Tudnivalók az Azure-identitáskezelésről](identity-fundamentals.md)
## [Az Azure identitáskezelési megoldásainak ismertetése](understand-azure-identity-solutions.md)
## [Hibrid identitáskezelési megoldás](choose-hybrid-identity-solution.md)
## [Azure-előfizetések társítása](active-directory-how-subscriptions-associated-directory.md)
## [Lakhely és adatkezelési szempontok](active-directory-data-storage-eu.md)
## [Gyakori kérdések](active-directory-faq.md)
## [Újdonságok](whats-new.md)


# Bevezetés
## [Bevezetés az Azure AD használatába](get-started-azure-ad.md)
## [Azure AD Premium-fiók regisztrálása](active-directory-get-started-premium.md)
## [Egyéni tartománynév hozzáadása](add-custom-domain.md)
## [Vállalati arculat konfigurálása](customize-branding.md)
## [Felhasználók hozzáadása az Azure AD-hez](add-users-azure-active-directory.md)
## [Licencek hozzárendelése felhasználókhoz](license-users-groups.md)
## [Új jelszó önkiszolgáló kérésének konfigurálása](authentication/quickstart-sspr.md)
## [Vállalat adatvédelmi információinak hozzáadása az Azure AD-ben](active-directory-properties-area.md)


# Útmutató
## Tervezés és kialakítás
### [Az Azure AD architektúrájának ismertetése](active-directory-architecture.md)
### [Jogcímtársítások az Azure Active Directoryban](active-directory-claims-mapping.md)
### [Hibrid identitáskezelési megoldás üzembe helyezése](active-directory-hybrid-identity-design-considerations-overview.md)
#### Követelmények meghatározása
##### [Identitáskezelés](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Címtár-szinkronizálás](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Többtényezős hitelesítés](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Az identitás-életciklus stratégiája](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Az adatbiztonság tervezése](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Adatvédelem](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Tartalomkezelés](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Hozzáférés-vezérlés](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidensmegoldás](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Az identitás-életciklus megtervezése
##### [Feladatok](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Bevezetési stratégia](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Következő lépések](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Eszközök összehasonlítása](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Felhasználók kezelése
### [Új felhasználók hozzáadása az Azure AD-hez](add-users-azure-active-directory.md)
### [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
### [Fiókok megosztása](active-directory-sharing-accounts.md)
### [Felhasználók hozzárendelése rendszergazdai szerepkörökhöz ](active-directory-users-assign-role-azure-portal.md)
### [Vendég felhasználók hozzáadása másik címtárból (B2B)](b2b/what-is-b2b.md)
#### [B2B-felhasználók rendszergazdák általi hozzáadása](b2b/add-users-administrator.md)
#### [B2B-felhasználók infomunkások általi hozzáadása](b2b/add-users-information-worker.md)
#### [API és testreszabás](b2b/customize-invitation-api.md)
#### [Kód- és Azure PowerShell-minták](b2b/code-samples.md)
#### [Minta önkiszolgáló bejelentkezési portálhoz](b2b/self-service-portal.md)
#### [Meghívó e-mail](b2b/invitation-email-elements.md)
#### [Meghívó beváltása](b2b/redemption-experience.md)
#### [B2B-felhasználók hozzáadása meghívás nélkül](b2b/add-user-without-invite.md)
#### [Meghívások engedélyezése vagy letiltása](b2b/allow-deny-list.md)
#### [Feltételes hozzáférés B2B-hez](b2b/conditional-access.md)
#### [B2B megosztási szabályzatok](b2b/delegate-invitations.md)
#### [B2B-felhasználó hozzáadása szerepkörhöz](b2b/add-guest-to-role.md)
#### [Dinamikus csoportok és B2B-felhasználók](b2b/use-dynamic-groups.md)
#### [Vállalat elhagyása](b2b/leave-the-organization.md)
#### [Naplózás és jelentéskészítés](b2b/auditing-and-reporting.md)
#### [B2B hibrid cégekhez és vállalkozásokhoz](b2b/hybrid-organizations.md)
##### [B2B-felhasználók hozzáférésének engedélyezése a helyi alkalmazásokhoz](b2b/hybrid-cloud-to-on-premises.md)
##### [Helyi felhasználók hozzáférésének engedélyezése a felhőalkalmazásokhoz](b2b/hybrid-on-premises-to-cloud.md)
#### [Külső B2B- és Office 365-megosztás](b2b/o365-external-user.md)
#### [B2B-licencelés](b2b/licensing-guidance.md)
#### [Aktuális korlátozások](b2b/current-limitations.md)
#### [GYIK](b2b/faq.md)
#### [B2B-hibaelhárítás](b2b/troubleshoot.md)
#### [A B2B-felhasználók ismertetése](b2b/user-properties.md)
#### [B2B-felhasználói jogkivonat](b2b/user-token.md)
#### [Azure AD B2B integrált alkalmazások](b2b/configure-saas-apps.md)
#### [B2B-felhasználói jogcímek társítása](b2b/claims-mapping.md)
#### [B2B-együttműködés összehasonlítása B2C-vel](b2b/compare-with-b2c.md)
#### [Támogatás igénybevétele B2B-hez](b2b/get-support.md)

## [Csoportok és tagok kezelése](active-directory-manage-groups.md)
### Csoportok kezelése
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Csoporttagok kezelése](active-directory-groups-members-azure-portal.md)
### [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md)
### [Csoporttagság kezelése](active-directory-groups-membership-azure-portal.md)
### [Licencek hozzárendelése csoportok használatával](active-directory-licensing-whatis-azure-portal.md)
#### [Licencek hozzárendelése egy csoporthoz](active-directory-licensing-group-assignment-azure-portal.md)
#### [A csoportok licencproblémáinak azonosítása és megoldása](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre](active-directory-licensing-group-migration-azure-portal.md)
#### [További forgatókönyvek csoportalapú licenceléshez](active-directory-licensing-group-advanced.md)
#### [Azure PowerShell-példák csoportalapú licenceléshez](active-directory-licensing-ps-examples.md)
#### [Az Azure AD termékeinek és szolgáltatáscsomagjainak referenciája](active-directory-licensing-product-and-service-plan-reference.md)
### [Office 365-csoportok lejáratának beállítása](active-directory-groups-lifecycle-azure-portal.md)
### [Az összes csoport megtekintése](active-directory-groups-view-azure-portal.md)
### [Csoportszintű hozzáférés biztosítása az SaaS-alkalmazásokhoz](active-directory-accessmanagement-group-saasapps.md)
### [Törölt Office 365-csoport visszaállítása](active-directory-groups-restore-azure-portal.md)
### Csoportbeállítások kezelése
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [Parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Speciális szabályok létrehozása
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Önkiszolgáló csoportok beállítása](active-directory-accessmanagement-self-service-group-management.md)
### [Hibaelhárítás](active-directory-accessmanagement-troubleshooting.md)

## [Jelentések kezelése](active-directory-reporting-azure-portal.md)
### [Bejelentkezési tevékenység](active-directory-reporting-activity-sign-ins.md)
### [Naplózási tevékenység](active-directory-reporting-activity-audit-logs.md)
### [Veszélyeztetett felhasználók](active-directory-reporting-security-user-at-risk.md)
### [Kockázatos bejelentkezések](active-directory-reporting-security-risky-sign-ins.md)
### [Kockázati események](active-directory-reporting-risk-events.md)
### [GYIK](active-directory-reporting-faq.md)
### Feladatok
#### [Nevesített helyek konfigurálása](active-directory-named-locations.md)
#### [Tevékenységjelentések keresése](active-directory-reporting-migration.md)
#### [Az Azure Active Directory Power BI-tartalomcsomag használata](active-directory-reporting-power-bi-content-pack-how-to.md)
### Referencia
#### [Megőrzés](active-directory-reporting-retention.md)
#### [Késések](active-directory-reporting-latencies-azure-portal.md)
#### [Értesítések](active-directory-reporting-notifications.md)
#### [Naplózott tevékenységekre vonatkozó referencia](active-directory-reporting-activity-audit-reference.md)
#### [Bejelentkezési tevékenységek hibakódjai](active-directory-reporting-activity-sign-ins-errors.md)
#### [Többtényezős hitelesítés](active-directory-reporting-activity-sign-ins-mfa.md)
### Hibaelhárítás
#### [Hiányzó naplózási adatok](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Hiányzó adatok a letöltésekben](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Az Azure Active Directory-tevékenységnaplók tartalomcsomag-hibái](active-directory-reporting-troubleshoot-content-pack.md)
### [Szoftveres hozzáférés](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Naplózási referencia](active-directory-reporting-api-audit-reference.md)
#### [Bejelentkezési referencia](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Előfeltételek](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Naplózási minták](active-directory-reporting-api-audit-samples.md)
#### [Bejelentkezési minták](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Tanúsítványok használata](active-directory-reporting-api-with-certificates.md)

## Jelszavak kezelése
### [Jelszavak áttekintése](authentication/active-directory-passwords-overview.md)
### Felhasználói dokumentumok
#### [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
#### [Ajánlott eljárások a jelszavakhoz](active-directory-secure-passwords.md)
#### [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
### [Az SSPR működése](authentication/concept-sspr-howitworks.md)
### [SSPR üzembehelyezési útmutató](authentication/howto-sspr-deployment.md)
### [SSPR és Windows 10](authentication/tutorial-sspr-windows.md)
### [SSPR-házirendek ](authentication/concept-sspr-policy.md)
### [SSPR-testreszabás](authentication/concept-sspr-customization.md)
### [SSPR-adatkövetelmények](authentication/howto-sspr-authenticationdata.md)
### [SSPR-jelentéskészítés](authentication/howto-sspr-reporting.md)
### Informatikai rendszergazdák: Új jelszavak kérése
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [SSPR licenc](authentication/concept-sspr-licensing.md)
### [Jelszóvisszaíró](authentication/howto-sspr-writeback.md)
### [Hibaelhárítás](authentication/active-directory-passwords-troubleshoot.md)
### [GYIK](authentication/active-directory-passwords-faq.md)


## Eszközök kezelése
### [Bevezetés](device-management-introduction.md)
### [Az Azure Portal használata](device-management-azure-portal.md)
### [Az Azure AD Join megtervezése](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Gyakori kérdések](device-management-faq.md)
### Feladatok
#### [Azure AD-ben regisztrált Windows 10 rendszerű eszközök beállítása](device-management-azuread-registered-devices-windows10-setup.md)
#### [Az Azure AD-hez csatlakoztatott eszközök beállítása](device-management-azuread-joined-devices-setup.md)
#### [Hibrid Azure AD-hez csatlakoztatott eszközök beállítása](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Helyszíni üzembe helyezés](active-directory-device-registration-on-premises-setup.md)
#### [Azure AD-csatlakozás a Windows 10 első futtatási élménye alkalmával](device-management-azuread-joined-devices-frx.md)
### Hibaelhárítás
#### [Hibrid Azure AD-csatlakoztatott Windows 10 és Windows Server 2016 rendszerű eszközök](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Hibrid Azure AD-csatlakoztatott, régebbi Windows-eszközök](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Alkalmazások kezelése
### [Áttekintés](manage-apps/what-is-application-management.md)
### [Bevezetés](manage-apps/plan-an-application-integration.md)
### [SaaS-alkalmazások integrációjának oktatóanyagai](active-directory-saas-tutorial-list.md)
### [Felhőalkalmazások felderítése](manage-apps/cloud-app-discovery.md)
#### [Pillanatkép-jelentések létrehozása](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [Folyamatos jelentéskészítés konfigurálása](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Egyéni naplóelemző használata](https://docs.microsoft.com/cloud-app-security/custom-log-parser)


### [Appok távoli elérése az App Proxyval](manage-apps/application-proxy.md)
#### Bevezetés
##### [Alkalmazásproxy engedélyezése](manage-apps/application-proxy-enable.md)
##### [Alkalmazások közzététele](manage-apps/application-proxy-publish-azure-portal.md)
##### [Egyéni tartományok](manage-apps/application-proxy-configure-custom-domain.md)
#### [Egyszeri bejelentkezés](manage-apps/application-proxy-single-sign-on.md)
##### [Egyszeri bejelentkezés KCD-vel](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Egyszeri bejelentkezés fejlécekkel](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Egyszeri bejelentkezés jelszótárolással](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Alapelvek
##### [Összekötők](manage-apps/application-proxy-connectors.md)
##### [Biztonság](manage-apps/application-proxy-security.md)
##### [Hálózatok](manage-apps/application-proxy-network-topology.md)


##### [Frissítés a TMG vagy a UAG rendszerről](manage-apps/application-proxy-migration.md)

#### Speciális konfigurációk
##### [Közzététel külön hálózatokon](manage-apps/application-proxy-connector-groups.md)
##### [Proxykiszolgálók](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Jogcímbarát alkalmazások](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Natív ügyfélalkalmazások](manage-apps/application-proxy-configure-native-client-application.md)
##### [Csendes telepítés](manage-apps/application-proxy-register-connector-powershell.md)
##### [Egyéni kezdőlap](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Beágyazott hivatkozások fordítása](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
#### Közzétételi útmutatók
##### [Távoli asztal](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](application-proxy-teams.md)
##### [Tableau](active-directory-application-proxy-tableau.md)

#### [Hibaelhárítás](active-directory-application-proxy-troubleshoot.md)

### Vállalati alkalmazások kezelése
#### [Felhasználók hozzárendelése](active-directory-coreapps-assign-user-azure-portal.md)
#### [Márkajelzés testreszabása](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Felhasználói bejelentkezések letiltása](active-directory-coreapps-disable-app-azure-portal.md)
#### [Felhasználók eltávolítása](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Az összes alkalmazás megtekintése](active-directory-coreapps-view-azure-portal.md)
#### [Felhasználói fiók üzembe helyezésének kezelése](active-directory-enterprise-apps-manage-provisioning.md)
#### [Egyszeri bejelentkezés kezelése nagyvállalati alkalmazások esetében](active-directory-enterprise-apps-manage-sso.md)
#### [Speciális tanúsítvány-aláírás a SAML-alkalmazások számára](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Külső gyártótól származó alkalmazás elrejtése a felhasználói felületen](active-directory-coreapps-hide-third-party-app.md)
### [Bejelentkezés automatikus gyorsításának konfigurálása HRD-szabályzattal](active-directory-auto-acceleration-using-hrd.md)

### [Alkalmazások hozzáférésének kezelése](active-directory-managing-access-to-apps.md)
#### [Egyszeri bejelentkezéses hozzáférés](active-directory-appssoaccess-whatis.md)
#### [Egyszeri bejelentkezés tanúsítványai](active-directory-sso-certs.md)
#### [Bérlőkorlátozások](active-directory-tenant-restrictions.md)
#### [SCIM használata a felhasználók átadására](active-directory-scim-provisioning.md)

### [Hibaelhárítás](active-directory-application-troubleshoot-content-map.md)
#### [Alkalmazásfejlesztés](active-directory-application-dev-troubleshoot-content-map.md)
##### [Konfigurálás és regisztráció](active-directory-application-dev-config-content-map.md)
##### [Fejlesztés](active-directory-application-dev-development-content-map.md)
#### [Alkalmazáskezelés](active-directory-application-management-troubleshoot-content-map.md)
##### [Konfigurálás](active-directory-application-config-content-map.md)
##### [Bejelentkezés](active-directory-application-sign-in-content-map.md)
##### [Kiépítés](active-directory-application-provisioning-content-map.md)
##### [Hozzáférés-kezelés](active-directory-application-access-content-map.md)
##### [Hozzáférési panel](active-directory-application-access-panel-content-map.md)
##### [Alkalmazásproxy](active-directory-application-proxy-content-map.md)
##### [Feltételes hozzáférés](active-directory-application-conditional-access-content-map.md)
### [Alkalmazásfejlesztés](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentumtár](active-directory-apps-index.md)

## Címtár kezelése
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egyéni tartománynevek
#### [Gyors útmutató](add-custom-domain.md)
#### [Egyéni tartománynevek hozzáadása](active-directory-domains-manage-azure-portal.md)
### [A címtár felügyelete](active-directory-administer.md)
### [Több címtár](active-directory-licensing-directory-independence.md)
### [Önkiszolgáló regisztráció](active-directory-self-service-signup.md)
### [Nem felügyelt könyvtár átvétele](domains-admin-takeover.md)
### [Vállalati állapothordozás](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Bekapcsolás](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Csoportházirend-beállítások](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-beállítások](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Hibaelhárítás](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Helyszíni identitások integrálása az Azure AD Connecttel](./connect/active-directory-aadconnect.md)

## [Az Azure-hoz való hozzáférés kezelése](../role-based-access-control/toc.yml)

## Erőforrások hozzáférésének delegálása
### [Rendszergazdai szerepkörök](active-directory-assign-admin-roles-azure-portal.md)
#### [Rendszergazdai szerepkörök hozzárendelése](active-directory-users-assign-role-azure-portal.md)
#### [Alapértelmezett felhasználói engedélyek](users-default-permissions.md)
### [Felügyeleti egységek](active-directory-administrative-units-management.md)
### [A jogkivonatok élettartamának beállítása](active-directory-configurable-token-lifetimes.md)
### [A rendszergazdai fiókok vészhelyzeti hozzáférésének kezelése](active-directory-admin-manage-emergency-access-accounts.md)
### [Kiemelt szerepkörök védelme](admin-roles-best-practices.md)

## Hozzáférési felülvizsgálatok
### [Hozzáférési felülvizsgálatok áttekintése](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md)
### [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md)
### [Hozzáférési felülvizsgálat végrehajtása](active-directory-azure-ad-controls-perform-access-review.md)
### [A hozzáférés felülvizsgálata](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Vendéghozzáférés hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Felhasználói hozzáférések kezelése felülvizsgálatokkal](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Programok és vezérlők kezelése](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Hozzáférési felülvizsgálatok eredményeinek lekérése](active-directory-azure-ad-controls-retrieve-access-review.md)

## Identitások védelme
### [Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md)
#### [Feltételek](active-directory-conditional-access-conditions.md)
#### [Helyfeltételek](active-directory-conditional-access-locations.md)
#### [Vezérlők](active-directory-conditional-access-controls.md)
#### [Első lépések](active-directory-conditional-access-azure-portal-get-started.md)
#### [Ajánlott eljárások](active-directory-conditional-access-best-practices.md)
#### [Az Office 365-szolgáltatások eszközszabályzatainak ismertetése](active-directory-conditional-access-device-policies.md)
#### [Klasszikus szabályzatok migrálása](active-directory-conditional-access-migration.md)
#### [Lehetőségelemzési eszköz](active-directory-conditional-access-whatif.md)
#### Első lépések
##### [Felhőalkalmazásonkénti MFA konfigurálása](active-directory-conditional-access-app-based-mfa.md)
#### Feladatok
##### [Klasszikus MFA-szabályzat migrálása](active-directory-conditional-access-migration-mfa.md)
##### [Eszközalapú feltételes hozzáférés beállítása](active-directory-conditional-access-policy-connected-applications.md)
##### [Alkalmazásalapú feltételes hozzáférés beállítása](active-directory-conditional-access-mam.md)
##### [A felhasználókra és az alkalmazásra vonatkozó használati feltételek biztosítása](active-directory-tou.md)
##### [VPN-kapcsolat beállítása](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [A SharePoint és az Exchange Online beállítása](active-directory-conditional-access-no-modern-authentication.md)
##### [Kijavítás](active-directory-conditional-access-device-remediation.md)
#### [Technikai útmutató](active-directory-conditional-access-technical-reference.md)
#### [Gyakori kérdések](active-directory-conditional-faqs.md)

### Windows Hello
#### [Jelszóhasználat nélküli hitelesítés](active-directory-azureadjoin-passport.md)
#### [A Vállalati Windows Hello engedélyezése](active-directory-azureadjoin-passport-deployment.md)
### Tanúsítványalapú hitelesítés
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Első lépések](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Bekapcsolás](active-directory-identityprotection-enable.md)
#### [Biztonsági rések keresése](active-directory-identityprotection-vulnerabilities.md)
#### [Kockázati események](active-directory-identity-protection-risk-events.md)
#### [Értesítések](active-directory-identityprotection-notifications.md)
#### [Bejelentkezési felület](active-directory-identityprotection-flows.md)
#### [Kockázati események szimulálása](active-directory-identityprotection-playbook.md)
#### [Felhasználók tiltásának feloldása](active-directory-identityprotection-unblock-howto.md)
#### [Gyakori kérdések](active-directory-identity-protection-faqs.md)
#### [Szószedet](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## [Más szolgáltatások integrálása az Azure AD-vel]()
### [LinkedIn-integráció engedélyezése](linkedin-integration.md)

## [Az AD FS üzembe helyezése az Azure-ban](active-directory-aadconnect-azure-adfs.md)
### [Magas rendelkezésre állás](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Az aláírás-kivonatoló algoritmus módosítása](active-directory-federation-sha256-guidance.md)

## [Hibaelhárítás](active-directory-troubleshooting-support-howto.md)

## Az Azure AD központi telepítése – A koncepció igazolása (Proof of Concept, PoC)
### [PoC-útmutató: Bevezetés](active-directory-playbook-intro.md)
### [PoC-útmutató: Kellékek](active-directory-playbook-ingredients.md)
### [PoC-útmutató: Megvalósítás](active-directory-playbook-implementation.md)
### [PoC-útmutató: Építőelemek](active-directory-playbook-building-blocks.md)


# Referencia
## [Kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell-parancsmagok](/powershell/azure/overview)
## [Java API-referencia](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Szolgáltatási korlátozások](active-directory-service-limits-restrictions.md)

# Kapcsolódó témakörök
## [Többtényezős hitelesítés](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD fejlesztőknek](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# További források
## [Azure visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Díjszabás](https://azure.microsoft.com/pricing/details/active-directory/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
