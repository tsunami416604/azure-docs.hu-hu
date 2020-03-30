---
title: Hibrid Azure Active Directory-csatlakozású eszközök hibáinak elhárítása
description: A hibrid Azure Active Directory csatlakozása a Windows 10 és a Windows Server 2016 rendszerhez.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331771"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Hibrid Azure Active Directory-csatlakozású eszközök hibáinak elhárítása 

A cikk tartalma windows 10 vagy Windows Server 2016 rendszert futtató eszközökre vonatkozik.

Más Windows-ügyfelek esetében olvassa el a [hibrid Azure Active Directory csatlakoztatott lefelé csatlakoztatott eszközök hibaelhárítása című cikket.](troubleshoot-hybrid-join-windows-legacy.md)

Ez a cikk feltételezi, hogy [a hibrid Azure Active Directory-csatlakozással csatlakozott eszközöket a](hybrid-azuread-join-plan.md) következő forgatókönyvek támogatására konfigurálta:

- Eszközalapú feltételes hozzáférés
- [A beállítások vállalati barangolása](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Vállalati Windows Hello](../active-directory-azureadjoin-passport-deployment.md)

Ez a dokumentum hibaelhárítási útmutatást nyújt a lehetséges problémák megoldásához. 

Windows 10 és Windows Server 2016 esetén a hibrid Azure Active Directory-csatlakozás támogatja a Windows 10 2015.

## <a name="troubleshoot-join-failures"></a>Csatlakozási hibák elhárítása

### <a name="step-1-retrieve-the-join-status"></a>1. lépés: Az illesztési állapot lekérése 

**Az illesztési állapot beolvasása:**

1. Parancssor megnyitása rendszergazdaként
2. Írja be a következőt: `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>2. lépés: Az illesztési állapot kiértékelése 

Tekintse át a következő mezőket, és győződjön meg arról, hogy rendelkeznek a várt értékekkel:

#### <a name="domainjoined--yes"></a>DomainÖsszekapcsolt: IGEN  

Ez a mező azt jelzi, hogy az eszköz csatlakoztatva van-e egy helyszíni Active Directoryhoz, vagy sem. Ha az érték **NEM,** az eszköz nem tud hibrid Azure AD-csatlakozást végrehajtani.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NEM  

Ez a mező azt jelzi, hogy az eszköz regisztrálva van-e az Azure AD-vel személyes eszközként *(munkahelyi csatlakozásként jelölve).* Ez az érték **nem** kell egy tartományhoz csatlakozó számítógép, amely szintén hibrid Azure AD-hez csatlakozott. Ha az érték **IGEN,** egy munkahelyi vagy iskolai fiók került hozzáadásra a hibrid Azure AD-csatlakozás befejezése előtt. Ebben az esetben a rendszer figyelmen kívül hagyja a fiókot a Windows 10 évfordulós frissítési verziójának (1607) használatakor.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : IGEN  

Ez a mező azt jelzi, hogy az eszköz csatlakoztatva van-e. Az érték **IGEN lesz,** ha az eszköz egy Azure AD-hez csatlakozott eszköz vagy egy hibrid Azure AD-hez csatlakozott eszköz.
Ha az érték **NEM,** az Azure AD-hez való csatlakozás még nem fejeződött be. 

Folytassa a következő lépésekkel a további hibaelhárításhoz.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3. lépés: Az illesztés sikertelen fázisának és a hibakódnak a megkeresése

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb

Keresse meg az "Előző regisztráció" alszakaszt az illesztési állapot kimenetének "Diagnosztikai adatok" szakaszában. Ez a szakasz csak akkor jelenik meg, ha az eszköz tartományhoz csatlakozik, és nem tud hibrid Azure AD-csatlakozás.
A "Hibafázis" mező az illesztési hiba fázisát jelöli, míg az "Ügyfél hibakód" az illesztési művelet hibakódját jelöli.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Régebbi Windows 10-es verziók

Az Eseménynapló naplói segítségével keresse meg az illesztési hibák fázis- és hibakódját.

1. Nyissa meg a **Felhasználói eszköz regisztrálása** eseménynaplókat az eseménynaplóban. Az **Alkalmazások és szolgáltatások naplózása** > **Microsoft** > **Windows** > felhasználói**eszközregisztráció alatt** található
2. Keresse meg az eseményeket a következő eseményazonosítók 304, 305, 307.

![Hibanapló-esemény](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Hibanapló-esemény](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4. lépés: Ellenőrizze a lehetséges okokat és megoldásokat az alábbi listákból

#### <a name="pre-check-phase"></a>Előzetes ellenőrzési fázis

A hiba lehetséges okai:

- Az eszköz nem látja a tartományvezérlőt.
   - Az eszköznek a szervezet belső hálózatán vagy VPN-en kell lennie, és hálózati rálátással kell rendelkeznie egy helyszíni Active Directory (AD) tartományvezérlőhöz.

#### <a name="discover-phase"></a>Fedezze fel a fázist

A hiba lehetséges okai:

- A Szolgáltatás csatlakozási pont (SCP) objektum helytelenül konfigurált/nem olvasható az SCP-objektum dc-ből.
   - Egy érvényes SCP-objektum szükséges az AD erdőben, amelyhez az eszköz tartozik, amely az Azure AD ellenőrzött tartománynévre mutat.
   - A részletek a [Szolgáltatáscsatlakozási pont konfigurálása](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)című szakaszban találhatók.
- Nem sikerült csatlakozni és lekérni a felderítési metaadatokat a felderítési végpontról.
   - Az eszköznek képesnek `https://enterpriseregistration.windows.net`kell lennie a regisztrációs és engedélyezési végpontok felderítéséhez a SYSTEM környezetben. 
   - Ha a helyszíni környezetben kimenő proxyszükséges, a rendszergazdának gondoskodnia kell arról, hogy az eszköz számítógépfiókja képes legyen felderíteni és csendben hitelesíteni a kimenő proxyt.
- Nem sikerült csatlakozni a felhasználói birodalom végpontjához, és nem sikerült végrehajtani a birodalom felderítését. (Csak Windows 10-es és újabb verzió)
   - Az eszköznek képesnek `https://login.microsoftonline.com`kell lennie a RENDSZER környezetben való hozzáférésre az ellenőrzött tartomány tartományfelderítésének elvégzéséhez és a tartomány típusának meghatározásához (felügyelt/összevont).
   - Ha a helyszíni környezetben kimenő proxyszükséges, a rendszergazdának biztosítania kell, hogy az eszköz SYSTEM környezete képes legyen felderíteni és csendben hitelesíteni a kimenő proxyt.

**Gyakori hibakódok:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Ok: Nem lehet olvasni az SCP-objektumot, és leolvasni az Azure AD-bérlői adatokat.
   - Megoldás: Olvassa el a [Szolgáltatáscsatlakozási pont konfigurálása](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)című szakaszt.
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Indok: Általános felderítési hiba. Nem sikerült leválasztani a felderítés metaadatait a DRS-ből.
   - Megoldás: Keresse meg az alábbi alhibát a további vizsgálathoz.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Ok: A művelet időtúlodása a felderítés végrehajtása közben.
   - Megoldás: Győződjön meg arról, hogy `https://enterpriseregistration.windows.net` a RENDSZER környezetben elérhető. További információt a [Hálózati kapcsolat követelményei](hybrid-azuread-join-managed-domains.md#prerequisites)című szakaszban talál.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Indok: Általános Realm Discovery hiba. Nem sikerült meghatározni a tartomány típusát (felügyelt/összevont) az STS-ből. 
   - Megoldás: Keresse meg az alábbi alhibát a további vizsgálathoz.

**Gyakori alhibakódok:**

A felderítési hibakód alhibakódjának megkereséséhez használja az alábbi módszerek egyikét.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb

Keresse meg a "DRS discovery test" az illesztési állapot kimenetének "Diagnosztikai adatok" szakaszában. Ez a szakasz csak akkor jelenik meg, ha az eszköz tartományhoz csatlakozik, és nem tud hibrid Azure AD-csatlakozás.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Régebbi Windows 10-es verziók

Az Eseménynapló naplói segítségével keresse meg az illesztési hibák fázisát és hibakódját.

1. Nyissa meg a **Felhasználói eszköz regisztrálása** eseménynaplókat az eseménynaplóban. Az **Alkalmazások és szolgáltatások naplózása** > **Microsoft** > **Windows** > felhasználói**eszközregisztráció alatt** található
2. A következő eseményazonosítókkal rendelkező események megkeresése 201-es

![Hibanapló-esemény](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Hálózati hibák

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Indok: Nem jött létre kapcsolat a kiszolgálóval
   - Megoldás: Biztosítsa a hálózati kapcsolatot a szükséges Microsoft-erőforrásokkal. További információ: [Hálózati kapcsolati követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Indok: Általános hálózati időhosszabbítás.
   - Megoldás: Biztosítsa a hálózati kapcsolatot a szükséges Microsoft-erőforrásokkal. További információ: [Hálózati kapcsolati követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Ok: A hálózati verem nem tudta dekódolni a kiszolgáló válaszát.
   - Megoldás: Győződjön meg arról, hogy a hálózati proxy nem zavarja és nem módosítja a kiszolgáló válaszát.

###### <a name="http-errors"></a>HTTP-hibák

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Ok: A nem megfelelő bérlőazonosítóval konfigurált SCP-objektum. Vagy nem található aktív előfizetés a bérlőben.
   - Megoldás: Győződjön meg arról, hogy az SCP-objektum a megfelelő Azure AD-bérlőazonosítóval és aktív előfizetésekkel van konfigurálva, vagy a bérlőben van jelen.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Indok: HTTP 503 a DRS-kiszolgálóról.
   - Megoldás: A kiszolgáló jelenleg nem érhető el. jövőbeli csatlakozási kísérletek valószínűleg sikeres, ha a szerver újra online.

###### <a name="other-errors"></a>Egyéb hibák

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Ok: A kiszolgálóválasz JSON-t nem lehet elemezni. Valószínűleg a HTTP 200-at html hitelesítési lappal visszaadott proxy miatt.
   - Megoldás: Ha a helyszíni környezetben kimenő proxyszükséges, a rendszergazdának biztosítania kell, hogy az eszköz SYSTEM környezete képes legyen felderíteni és csendben hitelesíteni a kimenő proxyt.

#### <a name="authentication-phase"></a>Hitelesítési fázis

Csak összevont tartományi fiókokesetén alkalmazható.

A hiba okai:

- Nem lehet csendben beszerezni egy Access-jogkivonatot a DRS-erőforráshoz.
   - A Windows 10-es eszközök az összevonási szolgáltatástól az integrált Windows-hitelesítést egy aktív WS-Trust végponthoz szerzik be az hitelesítési tokent. Részletek: [Összevonási szolgáltatás konfigurációja](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Gyakori hibakódok:**

Az Eseménynapló naplói segítségével keresse meg a hibakódot, az alhibakódot, a kiszolgálói hibakódot és a kiszolgálói hibaüzenetet.

1. Nyissa meg a **Felhasználói eszköz regisztrálása** eseménynaplókat az eseménynaplóban. Az **Alkalmazások és szolgáltatások naplózása** > **Microsoft** > **Windows** > felhasználói**eszközregisztráció alatt** található
2. Események megkeresése a következő 305-ös eseményazonosítóval

![Hibanapló-esemény](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Konfigurálási hibák

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Ok: A hitelesítési protokoll nem WS-Trust.
   - Megoldás: A helyszíni identitásszolgáltatónak támogatnia kell a WS-Trust-ot 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Ok: A helyszíni összevonási szolgáltatás nem adott vissza XML-választ.
   - Megoldás: Győződjön meg arról, hogy a MEX-végpont érvényes XML-t ad vissza. Győződjön meg arról, hogy a proxy nem zavarja a nem xml válaszokat, és nem ad vissza nem xml válaszokat.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Ok: Nem sikerült felderíteni a felhasználónév/jelszó hitelesítésének végpontját.
   - Megoldás: Ellenőrizze a helyszíni identitásszolgáltató beállításait. Győződjön meg arról, hogy a WS-Trust végpontok engedélyezve vannak, és győződjön meg arról, hogy a MEX válasz tartalmazza ezeket a helyes végpontokat.

##### <a name="network-errors"></a>Hálózati hibák

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Indok: Általános hálózati időhosszabbítás.
   - Megoldás: Győződjön meg arról, hogy `https://login.microsoftonline.com` a RENDSZER környezetben elérhető. Győződjön meg arról, hogy a helyszíni identitásszolgáltató elérhető a RENDSZER környezetben. További információ: [Hálózati kapcsolati követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Ok: Az auth-végpontdal való kapcsolat megszakadt.
   - Megoldás: Próbálkozzon újra valamikor, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Ok: A Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL), a kiszolgáló által küldött tanúsítvány nem érvényesíthető.
   - Megoldás: Ellenőrizze az ügyfél időeltérését. Próbálkozzon újra valamikor, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Ok: A sikertelen `https://login.microsoftonline.com` kapcsolódási kísérlet.
   - Megoldás: Ellenőrizze a `https://login.microsoftonline.com`hálózati kapcsolatot a rendszerhez.

##### <a name="other-errors"></a>Egyéb hibák

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Ok: SAML-jogkivonat ot a helyszíni identitásszolgáltató nem fogadta el az Azure AD.
   - Megoldás: Ellenőrizze az összevonási kiszolgáló beállításait. Keresse meg a kiszolgáló hibakódját a hitelesítési naplókban.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Ok: A kiszolgáló WS-Trust válasza hibakivételt jelentett, és nem tudta leadni a helyesállítást
   - Megoldás: Ellenőrizze az összevonási kiszolgáló beállításait. Keresse meg a kiszolgáló hibakódját a hitelesítési naplókban.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Ok: Hibaüzenetet kapott, amikor hozzáférési jogkivonatot próbált beszerezni a jogkivonat végpontjáról.
   - Megoldás: Keresse meg az alapul szolgáló hibát az ADAL-naplóban. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Indok: Általános ADAL hiba
   - Megoldás: Keresse meg az alhibakódot vagy a kiszolgáló hibakódját a hitelesítési naplókból.
    
#### <a name="join-phase"></a>Illesztési fázis

A hiba okai:

Keresse meg a regisztrációs típust, és keresse meg a hibakódot az alábbi listából.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb

Keresse meg az "Előző regisztráció" alszakaszt az illesztési állapot kimenetének "Diagnosztikai adatok" szakaszában. Ez a szakasz csak akkor jelenik meg, ha az eszköz tartományhoz csatlakozik, és nem tud hibrid Azure AD-csatlakozás.
A "Regisztrációs típus" mező az elvégzett illesztés típusát jelöli.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Régebbi Windows 10-es verziók

Az Eseménynapló naplói segítségével keresse meg az illesztési hibák fázisát és hibakódját.

1. Nyissa meg a **Felhasználói eszköz regisztrálása** eseménynaplókat az eseménynaplóban. Az **Alkalmazások és szolgáltatások naplózása** > **Microsoft** > **Windows** > felhasználói**eszközregisztráció alatt** található
2. A következő eseményazonosítókkal rendelkező események megkeresése

![Hibanapló-esemény](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>A DRS-kiszolgálóról visszaadott HTTP-hibák

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Ok: Hibaválaszt kapott a DRS-től a Következő hibakóddal: "DirectoryError"
   - Megoldás: A lehetséges okok és megoldások érdekében olvassa el a kiszolgáló hibakódját.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Ok: Hibakódot kapott a DRS-től a következő hibakóddal: "AuthenticationError" és ErrorSubCode is NOT "DeviceNotFound". 
   - Megoldás: A lehetséges okok és megoldások érdekében olvassa el a kiszolgáló hibakódját.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Ok: Hibaválaszt kapott a DRS-től a Következő hibakóddal: "DirectoryError"
   - Megoldás: A lehetséges okok és megoldások érdekében olvassa el a kiszolgáló hibakódját.

##### <a name="tpm-errors"></a>TPM-hibák

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Ok: A TPM-művelet nem sikerült vagy érvénytelen volt
   - Felbontás: Valószínűleg rossz sysprep lemezkép miatt. Győződjön meg arról, hogy a gép, amelyről a sysprep-rendszerkészültel készült, nem az Azure AD-hez csatlakozott, hibrid Azure AD-hez csatlakozott, vagy az Azure AD regisztrált.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Indok: Általános TPM-hiba. 
   - Megoldás: Tiltsa le a TPM-et az ezzel a hibával rendelkező eszközökön. A Windows 10 1809-es és újabb verziói automatikusan észlelik a TPM-hibákat, és a TPM használata nélkül befejezik a hibrid Azure AD-csatlakozást.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Ok: A TPM FIPS módban jelenleg nem támogatott.
   - Megoldás: Tiltsa le a TPM-et az ezzel a hibával rendelkező eszközökön. A Windows 1809 automatikusan észleli a TPM-hibákat, és a TPM használata nélkül befejezi a hibrid Azure AD-csatlakozást.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Indok: A TPM zárolva van.
   - Megoldás: Átmeneti hiba. Várja meg a újratöltődési időszakot. A csatlakozási kísérletnek egy idő után sikeresnek kell lennie. További információ a [TPM alapjai](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) című cikkben található.

##### <a name="network-errors"></a>Hálózati hibák

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Indok: Általános hálózati időout próbál regisztrálni az eszközt DRS
   - Megoldás: Ellenőrizze a `https://enterpriseregistration.windows.net`hálózati kapcsolatot a hoz.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Ok: A kiszolgáló nevét vagy címét nem lehetett feloldani.
   - Megoldás: Ellenőrizze a `https://enterpriseregistration.windows.net`hálózati kapcsolatot a hoz. Győződjön meg arról, hogy az állomásnév DNS-feloldása pontos az n/w-ban és az eszközön.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Ok: A kiszolgálóval való kapcsolat rendellenesen megszakadt.
   - Megoldás: Próbálkozzon újra valamikor, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről.

##### <a name="federated-join-server-errors"></a>Összevont illesztési kiszolgáló hibái

| Kiszolgálóhiba-kód | Kiszolgálói hibaüzenet | Lehetséges okok | Megoldás: |
| --- | --- | --- | --- |
| DirectoryError (Címtárhiba) | A kérés ideiglenesen van szabályozva. Próbálkozzon 300 másodperc után. | A várt hiba. Valószínűleg azért, mert több regisztrációs kérelmek gyors egymásutánban. | Újraillesztés a újratöltődési időszak után |

##### <a name="sync-join-server-errors"></a>Csatlakozási kiszolgáló szinkronizálási hibái

| Kiszolgálóhiba-kód | Kiszolgálói hibaüzenet | Lehetséges okok | Megoldás: |
| --- | --- | --- | --- |
| DirectoryError (Címtárhiba) | AADSTS90002: <UUID> A bérlő nem található. Ez a hiba akkor fordulhat elő, ha nincsenek aktív előfizetések a bérlőhöz. Kérdezze meg az előfizetés rendszergazdáját. | Az SCP-objektum bérlőazonosítója helytelen | Győződjön meg arról, hogy az SCP-objektum a megfelelő Azure AD-bérlőazonosítóval és aktív előfizetésekkel van konfigurálva, és a bérlőben van jelen. |
| DirectoryError (Címtárhiba) | A megadott azonosítóval megadott eszközobjektum nem található. | A szinkronizálási illesztés várt hibája. Az eszközobjektum nem lett szinkronizálva az AD-ről az Azure AD-re | Várja meg, amíg az Azure AD Connect szinkronizálása befejeződik, és a szinkronizálás befejezése utáni következő csatlakozási kísérlet megoldja a problémát |
| Hitelesítési hiba | A célszámítógép biztonsági azonosítójának ellenőrzése | Az Azure AD-eszközön lévő tanúsítvány nem egyezik meg a blob aláírásához használt tanúsítványsal a szinkronizálási illesztés során. Ez a hiba általában azt jelenti, hogy a szinkronizálás még nem fejeződött be. |  Várja meg, amíg az Azure AD Connect szinkronizálása befejeződik, és a szinkronizálás befejezése utáni következő csatlakozási kísérlet megoldja a problémát |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5. lépés: Naplók gyűjtése és kapcsolatfelvétel a Microsoft támogatási szolgálatával

Get nyilvános szkriptek itt: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Nyisson meg egy `start_ngc_tracing_public.cmd`rendszergazdai parancssort, és futtassa a parancsot.
2. Hajtsa végre a probléma reprodukálásához szükséges lépéseket.
3. A napló futtatásának `stop_ngc_tracing_public.cmd`leállítása a végrehajtással.
4. Zip és küldje el `%SYSTEMDRIVE%\TraceDJPP\*` a naplók alatt elemzésre.

## <a name="troubleshoot-post-join-issues"></a>Csatlakozás utáni problémák elhárítása

### <a name="retrieve-the-join-status"></a>Az illesztési állapot lekérése 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: IGEN és AzureADPrt: IGEN
  
Ezek a mezők azt jelzik, hogy a felhasználó sikeresen hitelesítette-e az Azure AD-t, amikor bejelentkezik az eszközre. Ha az értékek **NEM**, akkor ennek az lehet az oka:

- Hibás tárolási kulcs az eszközhöz társított TPM regisztrációkor (ellenőrizze a KeySignTest futtatása közben emelt szintű).
- Alternatív bejelentkezési azonosító
- A HTTP-proxy nem található

## <a name="known-issues"></a>Ismert problémák
- A Beállítások –>-fiókok –> Access Work vagy School csoportban a hibrid Azure AD-hez csatlakoztatott eszközök két különböző fiókot jeleníthetnek meg, egyet az Azure AD-hez, egyet pedig a helyszíni AD-hez, ha mobil hotspotokhoz vagy külső Wi-Fi-hálózatokhoz csatlakoznak. Ez csak a felhasználói felület problémája, és nincs hatással a funkcionalitásra. 
 
## <a name="next-steps"></a>További lépések

Az [eszközök hibaelhárításának folytatása a Dsregcmd paranccsal](troubleshoot-device-dsregcmd.md)

További kérdések az [eszközkezelésről szóló gyakori kérdések ben](faq.md)
