# [Az Azure Active Directory dokumentációja](index.md)

# Áttekintés
## [Mi az az Azure Active Directory?](fundamentals/active-directory-whatis.md)
## [Tudnivalók az Azure-identitáskezelésről](fundamentals/identity-fundamentals.md)
## [Az Azure identitáskezelési megoldásainak ismertetése](fundamentals/understand-azure-identity-solutions.md)
## [Azure-előfizetések társítása](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Lakhely és adatkezelési szempontok](fundamentals/active-directory-data-storage-eu.md)
## [Gyakori kérdések](fundamentals/active-directory-faq.md)
## [Újdonságok](fundamentals/whats-new.md)


# Bevezetés
## [Azure AD Premium-fiók regisztrálása](fundamentals/active-directory-get-started-premium.md)
## [Egyéni tartománynév hozzáadása](fundamentals/add-custom-domain.md)
## [Vállalati arculat konfigurálása](fundamentals/customize-branding.md)
## [Felhasználók hozzáadása az Azure AD-hez](fundamentals/add-users-azure-active-directory.md)
## [Licencek hozzárendelése felhasználókhoz](fundamentals/license-users-groups.md)
## [Új jelszó önkiszolgáló kérésének konfigurálása](authentication/quickstart-sspr.md)
## [Vállalat adatvédelmi információinak hozzáadása az Azure AD-ben](active-directory-properties-area.md)
## [Az Azure Active Directory elérése új bérlő létrehozásához](fundamentals/active-directory-access-create-new-tenant.md)


# Útmutató
## Tervezés és kialakítás
### [Az Azure AD architektúrájának ismertetése](fundamentals/active-directory-architecture.md)
### [Jogcímtársítások az Azure Active Directoryban](develop/active-directory-claims-mapping.md)
### [Hibrid identitáskezelési megoldás üzembe helyezése](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Követelmények meghatározása
##### [Identitáskezelés](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Címtár-szinkronizálás](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Többtényezős hitelesítés](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Az identitás-életciklus stratégiája](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Az adatbiztonság tervezése](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Adatvédelem](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Tartalomkezelés](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Hozzáférés-vezérlés](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidensmegoldás](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Az identitás-életciklus megtervezése
##### [Feladatok](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Bevezetési stratégia](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Következő lépések](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Eszközök összehasonlítása](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Felhasználók kezelése
### [Új felhasználók hozzáadása az Azure AD-hez](fundamentals/add-users-azure-active-directory.md)
### [Felhasználói profilok kezelése](fundamentals/active-directory-users-profile-azure-portal.md)
### [Felhasználói jelszavak alaphelyzetbe állítása](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Felhasználók hozzárendelése rendszergazdai szerepkörökhöz ](fundamentals/active-directory-users-assign-role-azure-portal.md)
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
#### [Gyakori kérdések](b2b/faq.md)
#### [B2B-hibaelhárítás](b2b/troubleshoot.md)
#### [A B2B-felhasználók ismertetése](b2b/user-properties.md)
#### [B2B-felhasználói jogkivonat](b2b/user-token.md)
#### [Azure AD B2B integrált alkalmazások](b2b/configure-saas-apps.md)
#### [B2B-felhasználói jogcímek társítása](b2b/claims-mapping.md)
#### [B2B-együttműködés összehasonlítása B2C-vel](b2b/compare-with-b2c.md)
#### [Támogatás igénybevétele B2B-hez](b2b/get-support.md)

## [Csoportok és tagok kezelése](fundamentals/active-directory-manage-groups.md)
### [Csoportok kezelése](fundamentals/active-directory-groups-create-azure-portal.md)
### [Csoport törlése a tagjaival együtt](fundamentals/active-directory-groups-delete-group.md)
### [Csoportbeállítások kezelése](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Jelentések kezelése](reports-monitoring/overview-reports.md)
### [Bejelentkezési tevékenység](reports-monitoring/concept-sign-ins.md)
### [Naplózási tevékenység](reports-monitoring/concept-audit-logs.md)
### [Veszélyeztetett felhasználók](reports-monitoring/concept-user-at-risk.md)
### [Kockázatos bejelentkezések](reports-monitoring/concept-risky-sign-ins.md)
### [Kockázati események](reports-monitoring/concept-risk-events.md)
### [Naplók monitorozása az Azure Monitorral](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [Gyakori kérdések](reports-monitoring/reports-faq.md)

### Feladatok
#### [Bejelentkezési jelentés letöltése](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Naplózási jelentés letöltése](reports-monitoring/quickstart-download-audit-report.md)
#### [Nevesített helyek konfigurálása](reports-monitoring/quickstart-configure-named-locations.md)
#### [Tevékenységjelentések keresése](reports-monitoring/howto-find-activity-reports.md)
#### [Az Azure AD Power BI-tartalomcsomag használata](reports-monitoring/howto-power-bi-content-pack.md)
#### [Kockázatosként megjelölt felhasználók szervizelése](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Tevékenységnaplók irányítása egy Azure Event Hub-központba](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Tevékenységnaplók archiválása egy Azure Storage-fiókba](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Tevékenységnaplók integrálása a Splunkkal az Azure Monitor használatával](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Tevékenységnaplók integrálása a SumoLogickal az Azure Monitor használatával](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Tevékenységnaplók integrálása a Log Analytics rendszerbe az Azure Monitor segítségével](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

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

## Alkalmazások kezelése
### [Áttekintés](manage-apps/what-is-application-management.md)
### [Bevezetés](manage-apps/plan-an-application-integration.md)
### [SaaS-alkalmazások integrációjának oktatóanyagai](saas-apps/tutorial-list.md)

### [Felhasználók átadása és az átadás megszüntetése SaaS-alkalmazásokban](manage-apps/user-provisioning.md) 
#### [Alkalmazások integrációjának oktatóanyagai](saas-apps/tutorial-list.md) 
#### [Automatikus üzembe helyezés SCIM-kompatibilis alkalmazásokhoz](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Attribútumleképezések testreszabása](manage-apps/customize-application-attributes.md) 
#### [Kifejezések írása attribútumleképezésekhez](manage-apps/functions-for-customizing-application-data.md) 
#### [Hatókörszűrők használata](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Jelentés a felhasználók automatikus átadásáról](manage-apps/check-status-user-account-provisioning.md) 
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
##### [Helyettesítő karakterek](manage-apps/application-proxy-wildcard.md)
##### [Személyes adatok eltávolítása](manage-apps/application-proxy-remove-personal-data.md)


#### Közzétételi útmutatók
##### [Távoli asztal](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
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

#### [Speciális tanúsítvány-aláírás SAML-alkalmazásokhoz](manage-apps/certificate-signing-options.md)
#### [Alkalmazás elrejtése a felhasználói felületen](manage-apps/hide-application-from-user-portal.md)
### [Bejelentkezés automatikus gyorsításának konfigurálása HRD-szabályzattal](manage-apps/configure-authentication-for-federated-users-portal.md)
### [AD FS-alkalmazások migrálása az Azure AD-be](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Alkalmazások hozzáférésének kezelése](manage-apps/what-is-access-management.md)
#### [Egyszeri bejelentkezéses hozzáférés](manage-apps/what-is-single-sign-on.md)
#### [Egyszeri bejelentkezés tanúsítványai](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Bérlőkorlátozások](manage-apps/tenant-restrictions.md)
#### [SCIM használata a felhasználók átadására](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése](develop/application-consent-experience.md)

### Hibaelhárítás



#### Hozzáférési panel
##### [Az alkalmazás nem jelenik meg](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Váratlan alkalmazás jelenik meg](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Nem lehet bejelentkezni](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Hiba a böngészőbővítmény telepítése közben](manage-apps/access-panel-extension-problem-installing.md)
##### [Az önkiszolgáló alkalmazás-hozzáférés használata](manage-apps/access-panel-manage-self-service-access.md)
##### [Hiba történt az önkiszolgáló alkalmazás-hozzáférés használata közben](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Alkalmazás hozzáadása
##### [Alkalmazás típusának kiválasztása](manage-apps/choose-application-type.md)
##### [Gyakori problémák – katalógusbeli alkalmazások](manage-apps/adding-gallery-app-common-problems.md)
##### [Gyakori problémák – nem katalógusbeli alkalmazások](manage-apps/adding-non-gallery-app-common-problems.md)

#### Alkalmazásproxy
##### [Probléma az alkalmazáslap megjelenítésekor](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Az alkalmazás betöltési ideje túl hosszú](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Az alkalmazáslap hivatkozásai nem működnek](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Milyen portokat nyissak meg az alkalmazáshoz](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Az alkalmazás összekötőcsoportjában nem volt működő összekötő](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Konfigurálás a felügyeleti portálon](manage-apps/application-proxy-config-how-to.md)
##### [Egyszeri bejelentkezés beállítása az alkalmazáshoz](manage-apps/application-proxy-config-sso-how-to.md)
##### [Probléma egy alkalmazás felügyeleti portálon való létrehozásakor](manage-apps/application-proxy-config-problem.md)
##### [A Kerberos által korlátozott delegálás konfigurálása](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurálás a PingAccess segítségével](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [„Can't Access this Corporate Application” (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](manage-apps/application-proxy-connector-installation-problem.md)


#### Alkalmazásregisztráció
##### [Az alkalmazásobjektum mezőinek megadása](develop/registration-config-specific-application-property-how-to.md)
##### [A jogkivonatok alapértelmezett élettartamának módosítása](develop/registration-config-change-token-lifetime-how-to.md)

#### Hitelesítés
##### [Végpontok konfigurálása](develop/registration-config-how-to.md)

#### Feltételes hozzáférés
##### [Az ügyfél nem felelt meg az eszközregisztrációs előfeltételeknek](active-directory-conditional-access.md)
##### [Hogyan és mikor lépnek életbe a vállalati hálózaton kívüli szabályok?](https://aka.ms/calocation)
##### [Hogyan lehet növelni azon eszközök számát, amelyeket a felhasználó az Azure AD-ben regisztrálhat?](active-directory-azureadjoin-setup.md)
##### [Hogyan kell beállítani a feltételes hozzáférést az Exchange Online-ban?](https://aka.ms/csforexchange)
##### [Hogyan kell beállítani a feltételes hozzáférést Windows 7 eszközökön?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Mely alkalmazásokban támogatott a feltételes hozzáférés?](active-directory-conditional-access-supported-apps.md)

#### API keresése
##### [API keresése](develop/api-find-an-api-how-to.md)

#### Hozzáférés-kezelés
##### [Felhasználók és csoportok hozzárendelése az alkalmazáshoz](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Felhasználó alkalmazás-hozzáférésének eltávolítása](manage-apps/methods-for-removing-user-access.md)
##### [Új önkiszolgáló alkalmazás-hozzárendelés konfigurálása](manage-apps/manage-self-service-access.md)
##### [Váratlan felhasználó hozzárendelve](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Váratlan alkalmazás az alkalmazáslistában](manage-apps/application-types.md)

#### Több-bérlős alkalmazások
##### [Új alkalmazás konfigurálása](develop/setup-multi-tenant-app.md)
##### [Hozzáadás az alkalmazáskatalógushoz](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Engedélyek
##### [API engedélyeinek kiválasztása](develop/perms-for-given-api.md)
##### [Delegált és alkalmazásengedélyek](develop/delegated-and-app-perms.md)
##### [Alkalmazás jóváhagyása](develop/consent-framework.md)

#### Kiépítés
##### [Mennyi időt vesz igénybe](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Órákat vesz igénybe – katalógusbeli alkalmazás](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Felhasználók átadásának konfigurálása – katalógusbeli alkalmazás](manage-apps/application-provisioning-config-how-to.md)
##### [Probléma a felhasználók átadásának konfigurálásakor – katalógusbeli alkalmazás](manage-apps/application-provisioning-config-problem.md)
##### [Probléma a rendszergazdai hitelesítő adatok mentésekor a felhasználók átadását végző katalógusbeli alkalmazás konfigurálása közben](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [A felhasználók átadása nem történik meg – katalógusbeli alkalmazás](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Nem a megfelelő felhasználók lettek átadva – katalógusbeli alkalmazás](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Egyszeri bejelentkezés
##### [Válasszon módszert](manage-apps/single-sign-on-modes.md)
##### [Konfigurálás](develop/registration-config-sso-how-to.md)
##### [Összevontak konfigurálása – katalógusbeli alkalmazások](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Gyakori problémák az összevontak konfigurálásakor – katalógusbeli alkalmazások](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Összevontak konfigurálása – nem katalógusbeli alkalmazások](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Gyakori problémák az összevontak konfigurálásakor – nem katalógusbeli alkalmazások](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Jelszó konfigurálása – katalógusbeli alkalmazások](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Gyakori problémák jelszó konfigurálásakor – katalógusbeli alkalmazások](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Jelszó konfigurálása – nem katalógusbeli alkalmazások](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Gyakori problémák jelszó konfigurálásakor – nem katalógusbeli alkalmazások](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problémák a felhasználói bejelentkezéssel
##### [Váratlan jóváhagyási kérés](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Hiba a felhasználói jóváhagyással](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Probléma egyéni portálról való bejelentkezésnél](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Probléma a hozzáférési panelről való bejelentkezésnél](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Hiba az alkalmazás bejelentkezési oldalán](manage-apps/application-sign-in-problem-application-error.md)
##### [Hiba az egyszeri bejelentkezési jelszóval – nem katalógusbeli alkalmazás](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Hiba az egyszeri bejelentkezési jelszóval – katalógusbeli alkalmazás](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Probléma egy Microsoft-alkalmazásba való bejelentkezéskor](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Probléma az összevont egyszeri bejelentkezéskor – nem katalógusbeli alkalmazás](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Probléma az összevont egyszeri bejelentkezéskor – katalógusbeli alkalmazás](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Probléma egy egyénileg fejlesztett alkalmazásban](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Probléma egy helyszíni alkalmazásban – Alkalmazásproxy](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Alkalmazásfejlesztés](active-directory-applications-guiding-developers-for-lob-applications.md)


## Címtár kezelése
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Egyéni tartománynevek
#### [Gyors útmutató](fundamentals/add-custom-domain.md)
### [A címtár felügyelete](fundamentals/active-directory-administer.md)
### [Helyszíni identitások integrálása az Azure AD Connecttel](hybrid/whatis-hybrid-identity.md)

### [A jogkivonatok élettartamának beállítása](develop/active-directory-configurable-token-lifetimes.md)

## Identitások védelme

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)



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
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
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
