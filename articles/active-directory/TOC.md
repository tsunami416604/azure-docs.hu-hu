# [Az Azure Active Directory dokumentációja](index.md)

# Áttekintés
## [Mi az az Azure Active Directory?](fundamentals/active-directory-whatis.md)
## [Tudnivalók az Azure-identitáskezelésről](fundamentals/identity-fundamentals.md)
## [Az Azure identitáskezelési megoldásainak ismertetése](fundamentals/understand-azure-identity-solutions.md)
## [Hibrid identitáskezelési megoldás](choose-hybrid-identity-solution.md)
## [Azure-előfizetések társítása](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Lakhely és adatkezelési szempontok](fundamentals/active-directory-data-storage-eu.md)
## [Gyakori kérdések](fundamentals/active-directory-faq.md)
## [Újdonságok](fundamentals/whats-new.md)


# Bevezetés
## [Bevezetés az Azure AD használatába](fundamentals/get-started-azure-ad.md)
## [Azure AD Premium-fiók regisztrálása](fundamentals/active-directory-get-started-premium.md)
## [Egyéni tartománynév hozzáadása](fundamentals/add-custom-domain.md)
## [Vállalati arculat konfigurálása](fundamentals/customize-branding.md)
## [Felhasználók hozzáadása az Azure AD-hez](fundamentals/add-users-azure-active-directory.md)
## [Licencek hozzárendelése felhasználókhoz](fundamentals/license-users-groups.md)
## [Új jelszó önkiszolgáló kérésének konfigurálása](authentication/quickstart-sspr.md)
## [Vállalat adatvédelmi információinak hozzáadása az Azure AD-ben](active-directory-properties-area.md)


# Útmutató
## Tervezés és kialakítás
### [Az Azure AD architektúrájának ismertetése](fundamentals/active-directory-architecture.md)
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
### [Új felhasználók hozzáadása az Azure AD-hez](fundamentals/add-users-azure-active-directory.md)
### [Felhasználói profilok kezelése](fundamentals/active-directory-users-profile-azure-portal.md)
### [Felhasználói jelszavak alaphelyzetbe állítása](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Fiókok megosztása](active-directory-sharing-accounts.md)
### [Felhasználók hozzárendelése rendszergazdai szerepkörökhöz ](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Törölt felhasználó visszaállítása](fundamentals/active-directory-users-restore.md)
### [Vendég felhasználók hozzáadása másik címtárból (B2B)](b2b/what-is-b2b.md)
#### [B2B-felhasználók rendszergazdák általi hozzáadása](b2b/add-users-administrator.md)
#### [B2B-felhasználók infomunkások általi hozzáadása](b2b/add-users-information-worker.md)
#### [API és testreszabás](b2b/customize-invitation-api.md)
#### [Google-összevonás](b2b/google-federation.md)
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

## [Csoportok és tagok kezelése](fundamentals/active-directory-manage-groups.md)
### [Csoportok kezelése](fundamentals/active-directory-groups-create-azure-portal.md)
### [Törölt Office 365-csoport visszaállítása](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Csoportbeállítások kezelése](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Jelentések kezelése](reports-monitoring/overview-reports.md)
### [Bejelentkezési tevékenység](reports-monitoring/concept-sign-ins.md)
### [Naplózási tevékenység](reports-monitoring/concept-audit-logs.md)
### [Veszélyeztetett felhasználók](reports-monitoring/concept-user-at-risk.md)
### [Kockázatos bejelentkezések](reports-monitoring/concept-risky-sign-ins.md)
### [Kockázati események](reports-monitoring/concept-risk-events.md)
### [Naplók monitorozása az Azure Monitorral](reports-monitoring/overview-activity-logs-in-azure-monitor.md)
### [GYIK](reports-monitoring/reports-faq.md)

### Feladatok
#### [Nevesített helyek konfigurálása](active-directory-named-locations.md)
#### [Tevékenységjelentések keresése](reports-monitoring/howto-find-activity-reports.md)
#### [Az Azure AD Power BI-tartalomcsomag használata](reports-monitoring/howto-power-bi-content-pack.md)
#### [Kockázatosként megjelölt felhasználók szervizelése](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Tevékenységnaplók irányítása egy Azure Event Hub-központba](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Tevékenységnaplók archiválása egy Azure Storage-fiókba](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Tevékenységnaplók integrálása a Splunkkal az Azure Monitor használatával](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)

### Referencia
#### [Megőrzés](reports-monitoring/reference-reports-data-retention.md)
#### [Késések](reports-monitoring/reference-reports-latencies.md)
#### [Naplózott tevékenységekre vonatkozó referencia](reports-monitoring/reference-audit-activities.md)
#### [Bejelentkezési tevékenységek hibakódjai](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Az auditnaplók sémájának értelmezése az Azure Monitorban](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [A bejelentkezési napló sémájának értelmezése az Azure Monitorban](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Hibaelhárítás
#### [Hiányzó adatok az Azure AD-tevékenységnaplókban](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Hiányzó adatok a letöltésekben](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Az Azure AD-tevékenységnaplók tartalomcsomag hibái](reports-monitoring/troubleshoot-content-pack.md)
#### [Az Azure AD Reporting API hibái](reports-monitoring/troubleshoot-graph-api.md)

### [Szoftveres hozzáférés](reports-monitoring/concept-reporting-api.md)
#### [Előfeltételek](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Tanúsítványok használata](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Jelszavak kezelése](authentication/concept-sspr-howitworks.md)
### Felhasználói dokumentumok
#### [Jelszó visszaállítása vagy módosítása](user-help/active-directory-passwords-update-your-own-password.md)
#### [Regisztráció önkiszolgáló jelszó-visszaállításra](user-help/active-directory-passwords-reset-register.md)


## Eszközök kezelése
### [Áttekintés](devices/overview.md)

### Gyors útmutatók
#### [Azure AD-ben regisztrált Windows 10 rendszerű eszközök beállítása](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [Az Azure AD-hez csatlakoztatott eszközök beállítása](user-help/device-management-azuread-joined-devices-setup.md)

### Oktatóanyagok
#### [A hibrid Azure AD-csatlakozás konfigurálása felügyelt tartományokhoz](devices/hybrid-azuread-join-managed-domains.md)
#### [A hibrid Azure AD-csatlakozás konfigurálása összevont tartományokhoz](devices/hybrid-azuread-join-federated-domains.md)
#### [A hibrid Azure AD-csatlakozás manuális konfigurálása](devices/hybrid-azuread-join-manual-steps.md)
#### [Az Azure AD-csatlakozás konfigurálása a Windows 10 első futtatásakor](devices/azuread-joined-devices-frx.md)

### Útmutatók
#### [Az Azure AD-csatlakozás megtervezése](devices/azureadjoin-plan.md)
#### [A hibrid Azure AD-csatlakozás implementálásának megtervezése](devices/hybrid-azuread-join-plan.md)
#### [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](devices/hybrid-azuread-join-control.md)
#### [Helyi rendszergazdák hozzárendelése Azure AD-csatlakoztatott eszközökhöz](devices/assign-local-admin.md)
#### [Jelenlegi hibrid Azure AD-csatlakoztatott Windows-eszközök hibaelhárítása](devices/troubleshoot-hybrid-join-windows-current.md)
#### [Korábbi hibrid Azure AD-csatlakoztatott Windows-eszközök hibaelhárítása](devices/troubleshoot-hybrid-join-windows-legacy.md)

### Alapelvek
#### [Eszközök kezelése az Azure Portalon](devices/device-management-azure-portal.md)
#### [Gyakori kérdések](devices/faq.md)

## Alkalmazások kezelése
### [Áttekintés](manage-apps/what-is-application-management.md)
### [Bevezetés](manage-apps/plan-an-application-integration.md)
### [SaaS-alkalmazások integrációjának oktatóanyagai](saas-apps/tutorial-list.md)

### [Felhasználók átadása és az átadás megszüntetése SaaS-alkalmazásokban](active-directory-saas-app-provisioning.md) 
#### [Alkalmazások integrációjának oktatóanyagai](saas-apps/tutorial-list.md) 
#### [Automatikus üzembe helyezés SCIM-kompatibilis alkalmazásokhoz](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Attribútumleképezések testreszabása](active-directory-saas-customizing-attribute-mappings.md) 
#### [Kifejezések írása attribútumleképezésekhez](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Hatókörszűrők használata](active-directory-saas-scoping-filters.md) 
#### [Jelentés a felhasználók automatikus átadásáról](active-directory-saas-provisioning-reporting.md) 
#### [A felhasználók átadásának hibaelhárítása](active-directory-application-provisioning-content-map.md) 

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
##### [Helyettesítő karakterek](active-directory-application-proxy-wildcard.md)
##### [Személyes adatok eltávolítása](manage-apps/application-proxy-remove-personal-data.md)


#### Közzétételi útmutatók
##### [Távoli asztal](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Hibaelhárítás](manage-apps/application-proxy-troubleshoot.md)

### Vállalati alkalmazások kezelése
#### [Alkalmazás hozzáadása](manage-apps/add-application-portal.md)
#### [Bérlőalkalmazások megtekintése](manage-apps/view-applications-portal.md)
#### [Egyszeri bejelentkezés konfigurálása](manage-apps/configure-single-sign-on-portal.md)
#### [Felhasználók hozzárendelése](manage-apps/assign-user-or-group-access-portal.md)
#### [Márkajelzés testreszabása](manage-apps/change-name-or-logo-portal.md)
#### [Felhasználói bejelentkezések letiltása](manage-apps/disable-user-sign-in-portal.md)
#### [Felhasználók eltávolítása](manage-apps/remove-user-or-group-access-portal.md)

#### [Felhasználói fiók üzembe helyezésének kezelése](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Speciális tanúsítvány-aláírás a SAML-alkalmazások számára](manage-apps/certificate-signing-options.md)
#### [Alkalmazás elrejtése a felhasználói felületen](manage-apps/hide-application-from-user-portal.md)
### [Bejelentkezés automatikus gyorsításának konfigurálása HRD-szabályzattal](manage-apps/configure-authentication-for-federated-users-portal.md)
### [AD FS-alkalmazások migrálása az Azure AD-be](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Alkalmazások hozzáférésének kezelése](manage-apps/what-is-access-management.md)
#### [Egyszeri bejelentkezéses hozzáférés](manage-apps/what-is-single-sign-on.md)
#### [Egyszeri bejelentkezés tanúsítványai](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Bérlőkorlátozások](manage-apps/tenant-restrictions.md)
#### [SCIM használata a felhasználók átadására](manage-apps/use-scim-to-provision-users-and-groups.md)


### Hibaelhárítás



#### Hozzáférési panel
##### [Az alkalmazás nem jelenik meg](application-access-panel-unexpected-application-not-appearing.md)
##### [Váratlan alkalmazás jelenik meg](application-access-panel-unexpected-application-appears.md)
##### [Nem lehet bejelentkezni](application-access-panel-web-sign-in-problem.md)
##### [Hiba a böngészőbővítmény telepítése közben](application-access-panel-extension-problem-installing.md)
##### [Az önkiszolgáló alkalmazás-hozzáférés használata](application-access-panel-self-service-applications-how-to.md)
##### [Hiba történt az önkiszolgáló alkalmazás-hozzáférés használata közben](application-access-panel-self-service-applications-problem.md)

#### Alkalmazás hozzáadása
##### [Alkalmazás típusának kiválasztása](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Gyakori problémák – katalógusbeli alkalmazások](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Gyakori problémák – nem katalógusbeli alkalmazások](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Alkalmazásproxy
##### [Probléma az alkalmazáslap megjelenítésekor](application-proxy-page-appearance-broken-problem.md)
##### [Az alkalmazás betöltési ideje túl hosszú](application-proxy-page-load-speed-problem.md)
##### [Az alkalmazáslap hivatkozásai nem működnek](application-proxy-page-links-broken-problem.md)
##### [Milyen portokat nyissak meg az alkalmazáshoz](application-proxy-connectivity-ports-how-to.md)
##### [Az alkalmazás összekötőcsoportjában nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)
##### [Konfigurálás a felügyeleti portálon](application-proxy-config-how-to.md)
##### [Egyszeri bejelentkezés beállítása az alkalmazáshoz](application-proxy-config-sso-how-to.md)
##### [Probléma egy alkalmazás felügyeleti portálon való létrehozásakor](application-proxy-config-problem.md)
##### [A Kerberos által korlátozott delegálás konfigurálása](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurálás a PingAccess segítségével](application-proxy-back-end-ping-access-how-to.md)
##### [„Can't Access this Corporate Application” (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](application-proxy-connector-installation-problem.md)


#### Alkalmazásregisztráció
##### [Az alkalmazásobjektum mezőinek megadása](application-dev-registration-config-specific-application-property-how-to.md)
##### [A jogkivonatok alapértelmezett élettartamának módosítása](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Hitelesítés
##### [Végpontok konfigurálása](application-dev-registration-config-how-to.md)

#### Feltételes hozzáférés
##### [Az ügyfél nem felelt meg az eszközregisztrációs előfeltételeknek](active-directory-conditional-access.md)
##### [A bérlőt letiltja a rendszer, mert a feltételes hozzáférés szabályzatát nem megfelelően állították be](active-directory-conditional-access-device-remediation.md)
##### [Hogyan és mikor lépnek életbe a vállalati hálózaton kívüli szabályok?](https://aka.ms/calocation)
##### [Hogyan lehet növelni azon eszközök számát, amelyeket a felhasználó az Azure AD-ben regisztrálhat?](active-directory-azureadjoin-setup.md)
##### [Hogyan kell beállítani a feltételes hozzáférést az Exchange Online-ban?](https://aka.ms/csforexchange)
##### [Hogyan kell beállítani a feltételes hozzáférést Windows 7 eszközökön?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Mely alkalmazásokban támogatott a feltételes hozzáférés?](active-directory-conditional-access-supported-apps.md)

#### API keresése
##### [API keresése](application-dev-api-find-an-api-how-to.md)

#### Hozzáférés-kezelés
##### [Felhasználók és csoportok hozzárendelése az alkalmazáshoz](application-access-assignment-how-to-add-assignment.md)
##### [Felhasználó alkalmazás-hozzáférésének eltávolítása](application-access-assignment-how-to-remove-assignment.md)
##### [Új önkiszolgáló alkalmazás-hozzárendelés konfigurálása](application-access-self-service-how-to.md)
##### [Váratlan felhasználó hozzárendelve](application-access-unexpected-user-assignment.md)
##### [Váratlan alkalmazás az alkalmazáslistában](application-access-unexpected-application.md)

#### Több-bérlős alkalmazások
##### [Új alkalmazás konfigurálása](application-dev-setup-multi-tenant-app.md)
##### [Hozzáadás az alkalmazáskatalógushoz](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Engedélyek
##### [API engedélyeinek kiválasztása](application-dev-perms-for-given-api.md)
##### [Engedélyek megadása az alkalmazásnak](application-dev-registration-config-grant-permissions-how-to.md)
##### [Delegált és alkalmazásengedélyek](application-dev-delegated-and-app-perms.md)
##### [Alkalmazás jóváhagyása](application-dev-consent-framework.md)

#### Kiépítés
##### [Mennyi időt vesz igénybe](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Órákat vesz igénybe – katalógusbeli alkalmazás](application-provisioning-when-will-provisioning-finish.md)
##### [Felhasználók átadásának konfigurálása – katalógusbeli alkalmazás](application-provisioning-config-how-to.md)
##### [Probléma a felhasználók átadásának konfigurálásakor – katalógusbeli alkalmazás](application-provisioning-config-problem.md)
##### [Probléma a rendszergazdai hitelesítő adatok mentésekor a felhasználók átadását végző katalógusbeli alkalmazás konfigurálása közben](application-provisioning-config-problem-storage-limit.md)
##### [A felhasználók átadása nem történik meg – katalógusbeli alkalmazás](application-provisioning-config-problem-no-users-provisioned.md)
##### [Nem a megfelelő felhasználók lettek átadva – katalógusbeli alkalmazás](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Egyszeri bejelentkezés
##### [Válasszon módszert](application-config-sso-how-to-choose-sign-on-method.md)
##### [Konfigurálás](application-dev-registration-config-sso-how-to.md)
##### [Összevontak konfigurálása – katalógusbeli alkalmazások](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Gyakori problémák az összevontak konfigurálásakor – katalógusbeli alkalmazások](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Összevontak konfigurálása – nem katalógusbeli alkalmazások](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Gyakori problémák az összevontak konfigurálásakor – nem katalógusbeli alkalmazások](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Jelszó konfigurálása – katalógusbeli alkalmazások](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Gyakori problémák jelszó konfigurálásakor – katalógusbeli alkalmazások](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Jelszó konfigurálása – nem katalógusbeli alkalmazások](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Gyakori problémák jelszó konfigurálásakor – nem katalógusbeli alkalmazások](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Problémák a felhasználói bejelentkezéssel
##### [Váratlan jóváhagyási kérés](application-sign-in-unexpected-user-consent-prompt.md)
##### [Hiba a felhasználói jóváhagyással](application-sign-in-unexpected-user-consent-error.md)
##### [Probléma egyéni portálról való bejelentkezésnél](application-sign-in-other-problem-deeplink.md)
##### [Probléma a hozzáférési panelről való bejelentkezésnél](application-sign-in-other-problem-access-panel.md)
##### [Hiba az alkalmazás bejelentkezési oldalán](application-sign-in-problem-application-error.md)
##### [Hiba az egyszeri bejelentkezési jelszóval – nem katalógusbeli alkalmazás](application-sign-in-problem-password-sso-non-gallery.md)
##### [Hiba az egyszeri bejelentkezési jelszóval – katalógusbeli alkalmazás](application-sign-in-problem-password-sso-gallery.md)
##### [Probléma egy Microsoft-alkalmazásba való bejelentkezéskor](application-sign-in-problem-first-party-microsoft.md)
##### [Probléma az összevont egyszeri bejelentkezéskor – nem katalógusbeli alkalmazás](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Probléma az összevont egyszeri bejelentkezéskor – katalógusbeli alkalmazás](application-sign-in-problem-federated-sso-gallery.md)
##### [Probléma egy egyénileg fejlesztett alkalmazásban](application-sign-in-problem-custom-dev.md)
##### [Probléma egy helyszíni alkalmazásban – Alkalmazásproxy](application-sign-in-problem-on-premises-application-proxy.md)

### [Alkalmazásfejlesztés](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentumtár](active-directory-apps-index.md)

## Címtár kezelése
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egyéni tartománynevek
#### [Gyors útmutató](fundamentals/add-custom-domain.md)
### [A címtár felügyelete](fundamentals/active-directory-administer.md)
### [Vállalati állapothordozás](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Bekapcsolás](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Csoportházirend-beállítások](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-beállítások](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Hibaelhárítás](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Helyszíni identitások integrálása az Azure AD Connecttel](./connect/active-directory-aadconnect.md)

### [A jogkivonatok élettartamának beállítása](active-directory-configurable-token-lifetimes.md)

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
### Tanúsítványalapú hitelesítés
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Első lépések](active-directory-certificate-based-authentication-get-started.md)

### Azure AD Identity Protection
#### [Áttekintés](identity-protection/overview.md)
#### [Bekapcsolás](identity-protection/enable.md)
#### [Biztonsági rések keresése](identity-protection/vulnerabilities.md)
#### [Kockázati események](active-directory-identity-protection-risk-events.md)
#### [Értesítések](identity-protection/notifications.md)
#### [Bejelentkezési felület](identity-protection/flows.md)
#### [Kockázati események szimulálása](identity-protection/playbook.md)
#### [Felhasználók tiltásának feloldása](identity-protection/howto-unblock-user.md)
#### [Gyakori kérdések](identity-protection/faqs.md)
#### [Szószedet](identity-protection/glossary.md)
#### [Microsoft Graph](identity-protection/graph-get-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Az AD FS üzembe helyezése az Azure-ban](active-directory-aadconnect-azure-adfs.md)
### [Magas rendelkezésre állás](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Az aláírás-kivonatoló algoritmus módosítása](active-directory-federation-sha256-guidance.md)

## [Hibaelhárítás](fundamentals/active-directory-troubleshooting-support-howto.md)

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

# Kapcsolódó témakörök
## [Többtényezős hitelesítés](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD fejlesztőknek](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# További források
## [Azure AD-üzembehelyezési tervek](./fundamentals/active-directory-deployment-plans.md)
## [Azure visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Díjszabás](https://azure.microsoft.com/pricing/details/active-directory/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
