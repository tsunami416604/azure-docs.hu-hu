# [Az Azure AD Domain Services dokumentációja](index.yml)

# Áttekintés
## [Az Azure AD tartományi szolgáltatások ismertetése](active-directory-ds-overview.md)
## Ez a megfelelő választás Önnek?
### [Összehasonlítás a Windows Server AD-vel](active-directory-ds-comparison.md)
### [Összehasonlítás az Azure AD Joinnal](active-directory-ds-compare-with-azure-ad-join.md)
## [Újdonságok](https://azure.microsoft.com/updates/?product=active-directory-ds)
## [Szolgáltatások](active-directory-ds-features.md)
## [Forgatókönyvek](active-directory-ds-scenarios.md)
## [A szinkronizálás működése](active-directory-ds-synchronization.md)
## [Kompatibilis külső szoftverek](active-directory-ds-compatible-software.md)

# Bevezetés
## [1. feladat: az alapvető beállítások konfigurálása](active-directory-ds-getting-started.md)
## [2. feladat: a hálózati beállítások konfigurálása](active-directory-ds-getting-started-network.md)
## [3. feladat: rendszergazdai csoport konfigurálása és az Azure AD Domain Services engedélyezése](active-directory-ds-getting-started-admingroup.md)
## [4. feladat: a virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
## [5. feladat: jelszókivonatok szinkronizálásának engedélyezése](active-directory-ds-getting-started-password-sync.md)

# Útmutató
## [Felügyelt tartomány állapotának az ellenőrzése](active-directory-ds-check-health.md)
## [Az Azure AD Domain Services használata az Azure CSP-előfizetésekben](active-directory-ds-csp.md)
## [Az Azure AD Domain Services engedélyezése a PowerShell-lel](active-directory-ds-enable-using-powershell.md)
## Csatlakozás egy felügyelt tartományhoz
### [Windows Server rendszerű virtuális gép](active-directory-ds-admin-guide-join-windows-vm-portal.md)
### [Windows Server rendszerű virtuális gép sablonból](active-directory-ds-join-windows-vm-template.md)
### [CentOS](active-directory-ds-join-centos-linux-vm.md)
### [CoreOS](active-directory-ds-join-coreos-linux-vm.md)
### [Red Hat Enterprise Linux](active-directory-ds-join-rhel-linux-vm.md)
### [Ubuntu Server](active-directory-ds-join-ubuntu-linux-vm.md)
## Felügyelt tartomány kezelése
### [Felügyelt tartomány kezelése](active-directory-ds-admin-guide-administer-domain.md)
### [DNS-kezelés felügyelt tartományon](active-directory-ds-admin-guide-administer-dns.md)
### Biztonságos LDAP konfigurálása felügyelt tartományhoz
#### [1. feladat: tanúsítvány beszerzése biztonságos LDAP-hoz](active-directory-ds-admin-guide-configure-secure-ldap.md)
#### [2. feladat: a megbízható LDAP-tanúsítványának exportálása](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
#### [3. feladat: biztonságos LDAP engedélyezése a felügyelt tartományhoz az Azure portállal](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
#### [4. feladat: A DNS konfigurálása a felügyelt tartomány internetről való elérésére](active-directory-ds-ldaps-configure-dns.md)
#### [5. feladat: Kötés létrehozása a felügyelt tartománnyal és a biztonságos LDAP-hozzáférés zárolása](active-directory-ds-ldaps-bind-lockdown.md)
#### [A biztonságos LDAP hibaelhárítása](active-directory-ds-ldaps-troubleshoot.md)

### [Szervezeti egység létrehozása egy felügyelt tartományon](active-directory-ds-admin-guide-create-ou.md)
### [Csoportosan felügyelt szolgáltatásfiók létrehozása felügyelt tartományon](active-directory-ds-create-gmsa.md)
### [Csoportházirend-kezelése felügyelt tartományon](active-directory-ds-admin-guide-administer-group-policy.md)
## [Virtuális hálózat kiválasztása](active-directory-ds-networking.md)
## Alkalmazások üzembe helyezése
### [Profilszinkronizálás támogatásának beállítása SharePoint-kiszolgálóhoz](active-directory-ds-enable-sharepoint-profile-sync.md)
### [A Kerberos által korlátozott delegálás konfigurálása](active-directory-ds-enable-kcd.md)
### [Üzembe helyezés az Azure AD-alkalmazásproxyval](active-directory-ds-deploy-azure-app-proxy.md)
## [Felügyelt tartomány törlése](active-directory-ds-disable-aadds.md)
## Hibaelhárítás
### [Gyakori kérdések](active-directory-ds-faqs.md)
### [Hibaelhárítási útmutató](active-directory-ds-troubleshooting.md)
### [Riasztások hibaelhárítása](active-directory-ds-troubleshoot-alerts.md)
#### [Hálózati biztonsági csoportok hibás konfigurációinak javítása](active-directory-ds-troubleshoot-nsg.md)
#### [Hiányzó szolgáltatásnevek visszaállítása](active-directory-ds-troubleshoot-service-principals.md)
#### [A biztonságos LDAP hibái](active-directory-ds-troubleshoot-ldaps.md)
### [Eltérő bérlők hibáinak megoldása](active-directory-ds-mismatched-tenant-error.md)
### [Felfüggesztett tartományok](active-directory-ds-suspension.md)


# Referencia
## [Kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory)

# Kapcsolódó témakörök
## [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
## [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)
## [Többtényezős hitelesítés](../active-directory/authentication/multi-factor-authentication.md)

# További források
## [Azure AD visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Kapcsolat](active-directory-ds-contact-us.md)
## [Díjszabás](https://azure.microsoft.com/pricing/details/active-directory-ds/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=active-directory-ds)
