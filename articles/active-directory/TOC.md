# Áttekintés
## [Mi az az Azure Active Directory?](active-directory-whatis.md)
## [Tudnivalók az Azure-identitáskezelésről](identity-fundamentals.md)
## [Az Azure identitáskezelési megoldásainak ismertetése](understand-azure-identity-solutions.md)
## [Hibrid identitáskezelési megoldás](choose-hybrid-identity-solution.md)
## [Azure-előfizetések társítása](active-directory-how-subscriptions-associated-directory.md)
## [Gyakori kérdések](active-directory-faq.md)
## [Újdonságok](whats-new.md)


# Bevezetés
## [Bevezetés az Azure AD használatába](get-started-azure-ad.md)
## [Azure AD Premium-fiók regisztrálása](active-directory-get-started-premium.md)
## [Egyéni tartománynév hozzáadása](add-custom-domain.md)
## [Vállalati arculat konfigurálása](customize-branding.md)
## [Felhasználók hozzáadása az Azure AD-hez](add-users-azure-active-directory.md)
## [Licencek hozzárendelése felhasználókhoz](license-users-groups.md)
## [Új jelszó önkiszolgáló kérésének konfigurálása](active-directory-passwords-getting-started.md)


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
### [Törölt felhasználó visszaállítása](active-directory-users-restore.md)
### [Vendég felhasználók hozzáadása másik címtárból (B2B)](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [B2B-felhasználók rendszergazdák általi hozzáadása](active-directory-b2b-admin-add-users.md)
#### [B2B-felhasználók infomunkások általi hozzáadása](active-directory-b2b-iw-add-users.md)
#### [API és testreszabás](active-directory-b2b-api.md)
#### [Kód- és Azure PowerShell-minták](active-directory-b2b-code-samples.md)
#### [Minta önkiszolgáló bejelentkezési portálhoz](active-directory-b2b-self-service-portal.md)
#### [Meghívó e-mail](active-directory-b2b-invitation-email.md)
#### [Meghívó beváltása](active-directory-b2b-redemption-experience.md)
#### [B2B-felhasználók hozzáadása meghívás nélkül](active-directory-b2b-add-user-without-invite.md)
#### [Feltételes hozzáférés B2B-hez](active-directory-b2b-mfa-instructions.md)
#### [B2B megosztási szabályzatok](active-directory-b2b-delegate-invitations.md)
#### [B2B-felhasználó hozzáadása szerepkörhöz](active-directory-b2b-add-guest-to-role.md)
#### [Dinamikus csoportok és B2B-felhasználók](active-directory-b2b-dynamic-groups.md)
#### [Naplózás és jelentéskészítés](active-directory-b2b-auditing-and-reporting.md)
#### [B2B hibrid cégekhez és vállalkozásokhoz](active-directory-b2b-hybrid-organizations.md)
#### [Külső B2B- és Office 365-megosztás](active-directory-b2b-o365-external-user.md)
#### [B2B-licencelés](active-directory-b2b-licensing.md)
#### [Aktuális korlátozások](active-directory-b2b-current-limitations.md)
#### [GYIK](active-directory-b2b-faq.md)
#### [B2B-hibaelhárítás](active-directory-b2b-troubleshooting.md)
#### [A B2B-felhasználók ismertetése](active-directory-b2b-user-properties.md)
#### [B2B-felhasználói jogkivonat](active-directory-b2b-user-token.md)
#### [Azure AD B2B integrált alkalmazások](active-directory-b2b-configure-saas-apps.md)
#### [B2B-felhasználói jogcímek társítása](active-directory-b2b-claims-mapping.md)
#### [B2B-együttműködés összehasonlítása B2C-vel](active-directory-b2b-compare-b2c.md)
#### [Támogatás igénybevétele B2B-hez](active-directory-b2b-support.md)

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
### [Jelszavak áttekintése](active-directory-passwords-overview.md)
### Felhasználói dokumentumok
#### [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
#### [Ajánlott eljárások a jelszavakhoz](active-directory-secure-passwords.md)
#### [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
### [Az SSPR működése](active-directory-passwords-how-it-works.md)
### [SSPR üzembehelyezési útmutató](active-directory-passwords-best-practices.md)
### [SSPR és Windows 10](active-directory-passwords-login.md)
### [SSPR-házirendek ](active-directory-passwords-policy.md)
### [SSPR-testreszabás](active-directory-passwords-customize.md)
### [SSPR-adatkövetelmények](active-directory-passwords-data.md)
### [SSPR-jelentéskészítés](active-directory-passwords-reporting.md)
### Informatikai rendszergazdák: Új jelszavak kérése
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [SSPR licenc](active-directory-passwords-licensing.md)
### [Jelszóvisszaíró](active-directory-passwords-writeback.md)
### [Hibaelhárítás](active-directory-passwords-troubleshoot.md)
### [GYIK](active-directory-passwords-faq.md)


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
### [Áttekintés](active-directory-enable-sso-scenario.md)
### [Bevezetés](active-directory-integrating-applications-getting-started.md)
### [SaaS-alkalmazások integrációjának oktatóanyagai](active-directory-saas-tutorial-list.md)
### [Felhőalkalmazások felderítése](cloudappdiscovery-get-started.md)
#### [Pillanatkép-jelentések létrehozása](cloudappdiscovery-set-up-snapshots.md)
#### [Folyamatos jelentéskészítés konfigurálása](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Egyéni naplóelemző használata](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### Ügynökalapú felderítés
##### [Mit jelent a felhőalkalmazások felderítése?](active-directory-cloudappdiscovery-whatis.md)
##### [A beállításjegyzék-beállítások frissítése](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [A biztonsággal és adatvédelemmel kapcsolatos tudnivalók](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Felhasználók átadása és az átadás megszüntetése SaaS-alkalmazásokban](active-directory-saas-app-provisioning.md)
#### [Alkalmazások integrációjának oktatóanyagai](active-directory-saas-tutorial-list.md)
#### [Automatikus üzembe helyezés SCIM-kompatibilis alkalmazásokhoz](active-directory-scim-provisioning.md)
#### [Attribútumleképezések testreszabása](active-directory-saas-customizing-attribute-mappings.md)
#### [Kifejezések írása attribútumleképezésekhez](active-directory-saas-writing-expressions-for-attribute-mappings.md)
#### [Hatókörszűrők használata](active-directory-saas-scoping-filters.md)
#### [Jelentés a felhasználók automatikus átadásáról](active-directory-saas-provisioning-reporting.md)
#### [A felhasználók átadásának hibaelhárítása](active-directory-application-provisioning-content-map.md)



### [Appok távoli elérése az App Proxyval](active-directory-application-proxy-get-started.md)
#### Bevezetés
##### [Alkalmazásproxy engedélyezése](active-directory-application-proxy-enable.md)
##### [Alkalmazások közzététele](application-proxy-publish-azure-portal.md)
##### [Egyéni tartományok](active-directory-application-proxy-custom-domains.md)
#### [Egyszeri bejelentkezés](application-proxy-sso-overview.md)
##### [Egyszeri bejelentkezés KCD-vel](active-directory-application-proxy-sso-using-kcd.md)
##### [Egyszeri bejelentkezés fejlécekkel](application-proxy-ping-access.md)
##### [Egyszeri bejelentkezés jelszótárolással](application-proxy-sso-azure-portal.md)
#### Alapelvek
##### [Összekötők](application-proxy-understand-connectors.md)
##### [Biztonság](application-proxy-security-considerations.md)
##### [Hálózatok](application-proxy-network-topology-considerations.md)


##### [Frissítés a TMG vagy a UAG rendszerről](application-proxy-transition-from-uag-tmg.md)

#### Speciális konfigurációk
##### [Közzététel külön hálózatokon](active-directory-application-proxy-connectors-azure-portal.md)
##### [Proxykiszolgálók](application-proxy-working-with-proxy-servers.md)
##### [Jogcímbarát alkalmazások](active-directory-application-proxy-claims-aware-apps.md)
##### [Natív ügyfélalkalmazások](active-directory-application-proxy-native-client.md)
##### [Csendes telepítés](active-directory-application-proxy-silent-installation.md)
##### [Egyéni kezdőlap](application-proxy-office365-app-launcher.md)
##### [Beágyazott hivatkozások fordítása](application-proxy-link-translation.md)
#### Közzétételi útmutatók
##### [Távoli asztal](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
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
#### [Alkalmazás elrejtése a felhasználói felületen](active-directory-coreapps-hide-third-party-app.md)
### [Bejelentkezés automatikus gyorsításának konfigurálása HRD-szabályzattal](active-directory-auto-acceleration-using-hrd.md)
### [AD FS-alkalmazások migrálása az Azure AD-be](migrate-adfs-apps-to-azure.md)
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

## [Az Azure-hoz való hozzáférés kezelése](toc.yml)

## Erőforrások hozzáférésének delegálása
### [Rendszergazdai szerepkörök](active-directory-assign-admin-roles-azure-portal.md)
#### [Rendszergazdai szerepkör hozzárendelése felhasználóhoz](active-directory-users-assign-role-azure-portal.md)
#### [A tagok és vendégfelhasználók engedélyeinek összehasonlítása](users-default-permissions.md)
### [Felügyeleti egységek](active-directory-administrative-units-management.md)
### [A jogkivonatok élettartamának beállítása](active-directory-configurable-token-lifetimes.md)
### [A rendszergazdai fiókok vészhelyzeti hozzáférésének kezelése](active-directory-admin-manage-emergency-access-accounts.md)

## Hozzáférési felülvizsgálatok
### [Hozzáférési felülvizsgálatok áttekintése](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md)
### [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md)
### [Hozzáférési felülvizsgálat végrehajtása](active-directory-azure-ad-controls-perform-access-review.md)
### [A hozzáférés felülvizsgálata](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Vendéghozzáférés hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Felhasználói hozzáférések kezelése felülvizsgálatokkal](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Programok és vezérlők kezelése](active-directory-azure-ad-controls-manage-programs-controls.md)


## Identitások védelme
### [Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md)
#### [Vezérlők](active-directory-conditional-access-controls.md)
#### [Helyfeltétel](active-directory-conditional-access-locations.md)
#### [Első lépések](active-directory-conditional-access-azure-portal-get-started.md)
#### [Ajánlott eljárások](active-directory-conditional-access-best-practices.md)
#### [Az Office 365-szolgáltatások eszközszabályzatainak ismertetése](active-directory-conditional-access-device-policies.md)
#### [Klasszikus szabályzatok migrálása](active-directory-conditional-access-migration.md)
#### [Lehetőségelemzési eszköz](active-directory-conditional-access-whatif.md)
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
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## Más szolgáltatások integrálása az Azure AD-vel
### [LinkedIn integrálása az Azure AD-vel](linkedin-integration.md)

## [AD DS üzembe helyezése Azure-beli virtuális gépeken](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory Azure-beli virtuális gépeken](active-directory-deploying-ws-ad-guidelines.md)
### [Replika tartományvezérlő egy Azure-beli virtuális gépen](active-directory-install-replica-active-directory-domain-controller.md)
### [Új erdő egy Azure-beli virtuális hálózaton](active-directory-new-forest-virtual-machine.md)



## [Az AD FS üzembe helyezése az Azure-ban](active-directory-aadconnect-azure-adfs.md)
### [Magas rendelkezésre állás](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Az aláírás-kivonatoló algoritmus módosítása](active-directory-federation-sha256-guidance.md)

## [Hibaelhárítás](active-directory-troubleshooting-support-howto.md)
### [Egy Active Directory-elem hiányzik vagy nem érhető el – Hibaelhárítás](active-directory-troubleshooting.md)

## Az Azure AD központi telepítése – A koncepció igazolása (Proof of Concept, PoC)
### [PoC-útmutató: Bevezetés](active-directory-playbook-intro.md)
### [PoC-útmutató: Kellékek](active-directory-playbook-ingredients.md)
### [PoC-útmutató: Megvalósítás](active-directory-playbook-implementation.md)
### [PoC-útmutató: Építőelemek](active-directory-playbook-building-blocks.md)


# Referencia
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
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
