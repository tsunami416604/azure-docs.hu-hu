---
title: Hibaelhárítás a dsregcmd parancs használatával – Azure Active Directory
description: A dsregcmd kimenetének használata az eszközök állapotának megértéséhez az Azure AD-ben
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128763"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>A dsregcmd paranccsal használható eszközök hibaelhárítása

A dsregcmd /status segédprogramot tartományi felhasználói fiókként kell futtatni.

## <a name="device-state"></a>Eszköz állapota

Ez a szakasz az eszközillesztési állapot paramétereit sorolja fel. Az alábbi táblázat felsorolja a különböző illesztési állapotokban lévő eszköz rekretiójának kritériumait.

| AzureAdCsatlakozott | Vállalathoz csatlakozva | Tartományhoz csatlakozva | Eszköz állapota |
| ---   | ---   | ---   | ---   |
| IGEN | NO | NO | Az Azure AD csatlakozott |
| NO | NO | IGEN | Tartomány csatlakozott |
| IGEN | NO | IGEN | Hibrid AD csatlakozott |
| NO | IGEN | IGEN | Helyszíni DRS-csatlakozás |

> [!NOTE]
> A munkahelyi csatlakozás (Az Azure AD regisztrált) állapota a "Felhasználóállapot" szakaszban jelenik meg

- **AzureAdJoined:** - Állítsa "IGEN" beállításra, ha az eszköz csatlakozik az Azure AD-hez. "NEM" egyébként.
- **EnterpriseJoined:** - Állítsa "IGEN" beállításra, ha az eszköz egy helyszíni DRS-hez csatlakozik. Egy eszköz nem lehet egyszerre EnterpriseJoined és AzureAdJoined.
- **DomainJoined:** - Állítsa "IGEN" értékre, ha az eszköz tartományhoz (AD) csatlakozik.
- **Tartománynév:** - Állítsa a tartomány nevére, ha az eszköz tartományhoz csatlakozik.

### <a name="sample-device-state-output"></a>Mintaeszköz-állapot kimenete

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Eszközadatok

Csak akkor jelenik meg, ha az eszköz az Azure AD-hez vagy hibrid Azure AD-hez csatlakozott (nem az Azure AD regisztrált). Ez a szakasz a felhőben tárolt eszközazonosító adatokat sorolja fel.

- **DeviceId:** - az eszköz egyedi azonosítója az Azure AD-bérlőben
- **Ujjlenyomat:** - Az eszköztanúsítvány ujjlenyomata 
- **DeviceCertificateValidity:** - Az eszköztanúsítvány érvényessége
- **KeyContainerId:** - Az eszköz tanúsítványához társított eszköz titkos kulcsának tárolóazonosítója
- **KeyProvider:** - KeyProvider (Hardver/Szoftver) az eszköz személyes kulcsának tárolására szolgál.
- **TpmProtected:** - "IGEN", ha az eszköz személyes kulcsa hardveres TPM-ben van tárolva.

### <a name="sample-device-details-output"></a>Minta eszköz részletei kimenet

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Bérlő adatai

Csak akkor jelenik meg, ha az eszköz az Azure AD-hez vagy hibrid Azure AD-hez csatlakozott (nem az Azure AD regisztrált). Ez a szakasz felsorolja a közös bérlői részleteket, amikor egy eszköz csatlakozik az Azure AD-hez.

> [!NOTE]
> Ha az Ebben a szakaszban szereplő MDM URL-címek üresek, az azt jelzi, hogy az MDM nem volt konfigurálva, vagy az aktuális felhasználó nem tartozik az MDM-regisztráció hatókörébe. Az MDM-konfiguráció áttekintéséhez tekintse meg az Azure AD Mobilitás-beállításait.

> [!NOTE]
> Még akkor is, ha mdm URL-eket lát, ez nem jelenti azt, hogy az eszközt egy MDM kezeli. Az információ akkor jelenik meg, ha a bérlő rendelkezik MDM-konfiguráció automatikus igénylési akkor is, ha maga az eszköz nem felügyelt. 

### <a name="sample-tenant-details-output"></a>Minta bérlő részletei kimenet

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Felhasználói állapot

Ez a szakasz az eszközre jelenleg bejelentkezett felhasználó különböző attribútumainak állapotát sorolja fel.

> [!NOTE]
> Az érvényes állapot lekéréséhez a parancsnak felhasználói környezetben kell futnia.

- **NgcSet:** - Állítsa "IGEN" beállításra, ha windows Hello kulcs van beállítva az aktuálisbejelentkezett felhasználóhoz.
- **NgcKeyId:** - A Windows Hello-kulcs azonosítója, ha az aktuálisbejelentkezett felhasználóhoz van beállítva.
- **CanReset:** - Azt jelzi, hogy a Windows Hello kulcsot a felhasználó alaphelyzetbe állíthatja-e. 
- **Lehetséges értékek:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, vagy Ismeretlen, ha hiba. 
- **WorkplaceJoined:** - Állítsa "IGEN" értékre, ha az Azure AD regisztrált fiókjai hozzá lettek adva az eszközhöz az aktuális NTUSER környezetben.
- **WamDefaultSet:** - Állítsa "IGEN" értékre, ha wam alapértelmezett WebAccount-ot hoz létre a bejelentkezett felhasználó számára. Ez a mező hibát jelezhet, ha a dsreg /status rendszergazdajog ú parancssorból fut. 
- **WamDefaultAuthority:** - Az Azure AD "szervezetek" értékre van állítva.
- **WamDefaultId:** -https://login.microsoft.comMindig " " az Azure AD.WamDefaultId: - Always " " for Azure AD.
- **WamDefaultGUID:** - A WAM-szolgáltató (Azure AD/Microsoft-fiók) GUID azonosítója az alapértelmezett WAM WebAccount hoz. 

### <a name="sample-user-state-output"></a>Minta felhasználói állapot kimenete

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-állapot

Ez a szakasz figyelmen kívül hagyható az Azure AD regisztrált eszközök.

> [!NOTE]
> A parancsnak felhasználói környezetben kell futnia az adott felhasználó érvényes állapotának lekéréséhez.

- **AzureAdPrt:** - Állítsa "IGEN" beállítás, ha a bejelentkezett felhasználó prt-je van az eszközön.
- **AzureAdPrtUpdateTime:** - Állítsa be az UTC-ben, amikor a PRT utolsó frissítése.
- **AzureAdPrtExpiryTime:** - Állítsa be az UTC-ben, amikor a PRT lejár, ha nem újítja meg.
- **AzureAdPrtAuthority:** - Az Azure AD-szolgáltató URL-címe
- **EnterprisePrt:** - Állítsa "IGEN" beállításra, ha az eszköz PRT-vel rendelkezik a helyszíni ADFS-ből. A hibrid Azure AD-hez csatlakozó eszközök esetében az eszköz prt-vel rendelkezhet az Azure AD-ből és a helyszíni AD-ből egyidejűleg. A helyszíni összekapcsolt eszközök csak enterprise PRT-vel rendelkeznek.
- **EnterprisePrtUpdateTime:** - Állítsa be az UTC-ben az Enterprise PRT utolsó frissítésének idejét.
- **EnterprisePrtExpiryTime:** - Állítsa be azt az időt UTC-ben, amikor a PRT lejár, ha nem újítja meg.
- **EnterprisePrtAuthority:** - ADFS-szolgáltató URL-címe

### <a name="sample-sso-state-output"></a>Minta SSO-állapot kimenete

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnosztikai adatok

### <a name="pre-join-diagnostics"></a>Előzetes csatlakoztatási diagnosztika

Ez a szakasz csak akkor jelenik meg, ha az eszköz tartományhoz csatlakozik, és nem tud hibrid Azure AD-csatlakozás.

Ez a szakasz különböző teszteket hajt végre az illesztési hibák diagnosztizálása érdekében. Ez a rész az előző (?) részleteit is tartalmazza. Ez az információ tartalmazza a hibafázist, a hibakódot, a kiszolgálókérésazonosítót, a kiszolgálóválasz http állapotát, a kiszolgálóválasz-hibaüzenetet.

- **Felhasználói környezet:** - Az a környezet, amelyben a diagnosztika fut. Lehetséges értékek: SYSTEM, UN-ELEVATED User, ELEVATED User. 

   > [!NOTE]
   > Mivel a tényleges illesztés a RENDSZER környezetben történik, a rendszerkörnyezetben a diagnosztika futtatása a legközelebb áll a tényleges illesztési forgatókönyvhöz. A DIAgnosztika SYSTEM környezetben történő futtatásához a dsregcmd /status parancsot rendszergazdai jogú parancssorból kell futtatni.

- **Ügyfélidő:** - A rendszeridő UTC-ben.
- **AD-kapcsolati teszt:** - A teszt kapcsolódási tesztet hajt végre a tartományvezérlőn. A teszt hibája valószínűleg csatlakozási hibákat eredményez az ellenőrzés előtti fázisban.
- **AD konfigurációs teszt:** - Ellenőrizze az olvasásokat, és ellenőrizze, hogy az SCP-objektum megfelelően van-e konfigurálva a helyszíni AD erdőben. A teszt hibái valószínűleg a 0x801c001d hibakódú felderítési fázis csatlakozási hibáit eredményeznék.
- **DRS-felderítési teszt:** - A teszt lekéri a DRS-végpontokat a felderítési metaadat-végpontból, és felhasználói birodalom-kérelmet hajt végre. A teszt hibái valószínűleg csatlakozási hibákat eredményeznek a felderítési fázisban.
- **DRS-kapcsolatteszt:** - A teszt alapvető kapcsolódási tesztet hajt végre a DRS-végponton.
- **Jogkivonat-beszerzési teszt:** – Tesztelje az Azure AD hitelesítési jogkivonatot, ha a felhasználó bérlője össze van téve. A teszt hibái valószínűleg csatlakozási hibákat eredményeznének az auth fázisban. Ha az auth sikertelen szinkronizálási illesztést a rendszer tartalékként kísérel meg, kivéve, ha a tartalék kifejezetten le van tiltva az alábbi beállításkulcs-beállításokkal.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Tartalék a Szinkronizálás-csatlakozáshoz:** - Állítsa "Engedélyezve" értékre, ha a fenti beállításkulcs, hogy megakadályozza az illesztés hitelesítési hibákkal való szinkronizálását, NINCS jelen. Ez a beállítás a Windows 10 1803-as és újabb verzióiból érhető el.
- **Előző regisztráció:** - Az előző csatlakozási kísérlet bekövetkezésének időpontja. A rendszer csak a sikertelen csatlakozási kísérleteket naplózza.
- **Hibafázis:** - Az illesztés azon szakasza, amelyben megszakadt. A lehetséges értékek az előzetes ellenőrzés, a felfedezés, az auth, a csatlakozás.
- **Ügyfél hibakódja:** - A visszaadott ügyfélhibakód (HRESULT).
- **Kiszolgálóhibakódja:** - Kiszolgálói hibakód, ha a rendszer kérést küldött a kiszolgálónak, és a kiszolgáló hibakóddal válaszolt. 
- **Kiszolgálói üzenet:** - A kiszolgáló üzeneta a hibakóddal együtt visszaadva.
- **Https status:** - A kiszolgáló által visszaadott http-állapot.
- **Kérelemazonosító:** - A kiszolgálónak küldött ügyfél-kérelemazonosító. Hasznos a kiszolgálóoldali naplókkal való korrelációhoz.

### <a name="sample-pre-join-diagnostics-output"></a>Minta előzetes csatlakoztatási diagnosztikai kimenete

A következő példa bemutatja a diagnosztikai teszt sikertelen felderítési hibával.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

A következő példa azt mutatja, diagnosztikai tesztek haladnak, de a regisztrációs kísérlet sikertelen volt egy könyvtárhiba, amely várhatóan a szinkronizálási illesztés. Miután az Azure AD Connect szinkronizálási feladat befejeződött, az eszköz képes lesz csatlakozni.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Csatlakozás utáni diagnosztika

Ez a szakasz a felhőhöz csatlakozott eszközön végrehajtott józansági ellenőrzések kimenetét jeleníti meg.

- **AadRecoveryEnabled:** - Ha "IGEN", az eszközben tárolt kulcsok nem használhatók, és az eszköz meg van jelölve a helyreállításhoz. A következő bejelentkezés elindítja a helyreállítási folyamatot, és újra regisztrálja az eszközt.
- **KeySignTest:** - Ha "ÁT" az eszköz kulcsok jó állapotban vannak. Ha a KeySignTest sikertelen, az eszköz általában meg lesz jelölve a helyreállításhoz. A következő bejelentkezés elindítja a helyreállítási folyamatot, és újra regisztrálja az eszközt. A hibrid Azure AD-hez csatlakozó eszközök a helyreállítás néma. Míg az Azure AD csatlakozott, vagy az Azure AD regisztrált, az eszközök kérni fogja a felhasználói hitelesítés t, és szükség esetén regisztrálja újra az eszközt. **A KeySignTest emelt szintű jogosultságokat igényel.**

#### <a name="sample-post-join-diagnostics-output"></a>Minta csatlakozás utáni diagnosztikai kimenet

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC előfeltétel-ellenőrzés

Ez a szakasz a Windows Hello for Business (WHFB) kiépítésének perquisite ellenőrzéseit végzi. 

> [!NOTE]
> Előfordulhat, hogy nem látja az NGC előzetes ellenőrzési adatait a dsregcmd /status kapcsolóban, ha a felhasználó már sikeresen konfigurálta a WHFB-t.

- **IsDeviceJoined:** - Állítsa "IGEN" beállításra, ha az eszköz csatlakozik az Azure AD-hez.
- **IsUserAzureAD:** - Állítsa "IGEN" beállításra, ha a bejelentkezett felhasználó jelen van az Azure AD-ben.
- **PolicyEnabled:** - Állítsa "IGEN" értékre, ha a WHFB-házirend engedélyezve van az eszközön.
- **PostLogonEnabled:** - Állítsa "IGEN" értékre, ha a WHFB-regisztrációt a platform natív módon indítja el. Ha "NEM" értékre van állítva, az azt jelzi, hogy a Windows Hello vállalati verzióra való regisztrációt egy egyéni mechanizmus váltja ki.
- **DeviceEligible:** - Állítsa "IGEN" értékre, ha az eszköz megfelel a WHFB-re való regisztrálás hardverkövetelményének.
- **SessionIsNotRemote:** - Állítsa "IGEN" beállításra, ha az aktuális felhasználó közvetlenül az eszközre van bejelentkezve, és nem távolról.
- **CertEnrollment:** - A WHFB tanúsítványmegbízhatósági telepítésére jellemző, jelezve a WHFB tanúsítványigénylési hatóságát. Állítsa "regisztrációs hatóság", ha a whfb-házirend forrása csoportházirend, "mobileszköz-kezelés", ha a forrás MDM. "nincs" egyébként
- **AdfsRefreshToken:** - a WHFB tanúsítványmegbízhatóság ikörnyezetére jellemző. Csak akkor jelenik meg, ha a CertEnrollment "beléptetési hatóság". Azt jelzi, hogy az eszköz rendelkezik-e vállalati PRT-vel a felhasználó számára.
- **AdfsRaIsReady:** - a WHFB tanúsítványmegbízhatóság ikörnyezetére jellemző.  Csak akkor jelenik meg, ha a CertEnrollment "beléptetési hatóság". Állítsa "IGEN" értékre, ha az ADFS a felderítési metaadatokban a WHFB-t támogatja, *és* ha a bejelentkezési tanúsítványsablon elérhető.
- **LogonCertTemplateReady:** - a WHFB tanúsítványmegbízhatóság ikörnyezetére jellemző. Csak akkor jelenik meg, ha a CertEnrollment "beléptetési hatóság". Állítsa "IGEN" beállításra, ha a bejelentkezési tanúsítványsablon állapota érvényes, és segít az ADFS RA hibaelhárításában.
- **PreReqResult:** - Az összes WHFB előfeltétel-értékelés eredményét adja meg. Állítsa a "Kiépítés" értékre, ha a WHFB-regisztráció bejelentkezés utáni feladatként indul el, amikor a felhasználó legközelebb bejelentkezik.

### <a name="sample-ngc-prerequisite-check-output"></a>Minta NGC előfeltétel-ellenőrzés kimenete

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>További lépések

További kérdések az [eszközkezelésről szóló gyakori kérdések ben](faq.md)
