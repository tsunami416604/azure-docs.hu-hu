---
title: Hibrid Azure Active Directory csatlakoztatott eszközök hibaelhárítása – Azure Active Directory
description: A hibrid Azure Active Directory csatlakoztatott Windows 10 és Windows Server 2016 rendszerű eszközök hibaelhárítása.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4a0f00c8bcf511f220d3e0df81adac1e9ff0d4
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995207"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Hibrid Azure Active Directory csatlakoztatott eszközök hibaelhárítása 

A cikk tartalma a Windows 10 vagy Windows Server 2016 rendszerű eszközökre vonatkozik.

Más Windows-ügyfelek esetén tekintse meg a következő cikket: a [hibrid Azure Active Directory csatlakoztatása a régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md).

Ez a cikk azt feltételezi, hogy a [hibrid Azure Active Directory csatlakoztatott eszközöket](hybrid-azuread-join-plan.md) úgy konfigurálta, hogy támogassa a következő forgatókönyveket:

- Eszköz alapú feltételes hozzáférés
- [A beállítások vállalati barangolása](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Vállalati Windows Hello](../active-directory-azureadjoin-passport-deployment.md)

Ez a dokumentum a lehetséges problémák megoldásához nyújt hibaelhárítási útmutatót. 

Windows 10 és Windows Server 2016 esetén a hibrid Azure Active Directory csatlakozás a 2015-es és újabb verziójú Windows 10-et támogatja.

## <a name="troubleshoot-join-failures"></a>Csatlakozási hibák elhárítása

### <a name="step-1-retrieve-the-join-status"></a>1\. lépés: Az illesztés állapotának beolvasása 

**Az illesztési állapot lekérése:**

1. Nyisson meg egy parancssort rendszergazdaként
2. Típusa `dsregcmd /status`

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

### <a name="step-2-evaluate-the-join-status"></a>2\. lépés: Az illesztés állapotának kiértékelése 

Tekintse át a következő mezőket, és ellenőrizze, hogy a várt értékek szerepelnek-e:

#### <a name="domainjoined--yes"></a>DomainJoined : IGEN  

Ez a mező jelzi, hogy az eszköz egy helyszíni Active Directoryhoz csatlakozik-e, vagy sem. Ha az érték **nem**, az eszköz nem tud hibrid Azure ad-csatlakozást végezni.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Ez a mező azt jelzi, hogy az eszköz regisztrálva van-e az Azure AD-ben személyes eszközként (munkahelyhez csatlakoztatottként megjelölve). Ez az érték **nem** lehet olyan tartományhoz csatlakoztatott számítógép esetében, amely szintén hibrid Azure ad-hez csatlakozik. Ha az érték **Igen**, a hibrid Azure ad-csatlakozás befejezése előtt munkahelyi vagy iskolai fiók lett hozzáadva. Ebben az esetben a rendszer figyelmen kívül hagyja a fiókot, ha a Windows 10 évfordulós frissítési verzióját használja (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : IGEN  

Ez a mező jelzi, hogy az eszköz csatlakozik-e az Azure AD-hez. Ha az érték **nem**, az Azure ad-hez való csatlakozás még nem fejeződött be. 

További hibaelhárításhoz folytassa a következő lépésekkel.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3\. lépés: Itt megtalálhatja azt a fázist, amelyben a csatlakozás meghiúsult és a errorcode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb verziók

Keresse meg a "korábbi regisztráció" alszakaszt az illesztési állapot kimenetének "diagnosztikai adatokat" tartalmazó szakaszában.
A "hiba fázis" mező azt jelzi, hogy az illesztési hiba fázisa az "ügyfél ErrorCode" alatt az illesztési művelet hibakódját jelöli.

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

A csatlakozási hibák fázisának és hibakódjának megkereséséhez használja a Eseménynapló naplókat.

1. Nyissa meg a **felhasználó-eszköz regisztrációjának** eseménynaplóit az eseménynaplóban. Az **alkalmazások és szolgáltatások naplóban** > található**Microsoft** > **Windows** > **felhasználói eszköz regisztrálása**
2. Keressen eseményeket a következő eventIDs 304, 305, 307.

![Sikertelen naplózási esemény](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Sikertelen naplózási esemény](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4\. lépés: Az alábbi listán szereplő lehetséges okok és megoldások keresése

#### <a name="pre-check-phase"></a>Előzetes ellenőrzési fázis

A hiba lehetséges okai:

- Az eszköz nem rendelkezik a tartományvezérlő felé irányuló pillantással.
   - Az eszköznek a szervezet belső hálózatán vagy VPN-en kell lennie a helyszíni Active Directory (AD) tartományvezérlőhöz való hálózati vonallal.

#### <a name="discover-phase"></a>Felderítési fázis

A hiba lehetséges okai:

- A szolgáltatáskapcsolódási pont (SCP) objektum hibásan van konfigurálva/nem olvasható a SCP-objektum a TARTOMÁNYVEZÉRLŐről.
   - Érvényes SCP-objektumra van szükség az AD-erdőben, amelyhez az eszköz tartozik, amely egy ellenőrzött tartománynévre mutat az Azure AD-ben.
   - A részletek a [szolgáltatás kapcsolódási pontjának konfigurálása](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)című szakaszban találhatók.
- Sikertelen volt a kapcsolódás és a felderítési metaadatok beolvasása a felderítési végpontról.
   - A regisztrációs és engedélyezési végpontok felderítéséhez az eszköznek képesnek kell lennie a rendszerkörnyezethez való hozzáférésre `https://enterpriseregistration.windows.net`. 
   - Ha a helyszíni környezet kimenő proxyt igényel, a rendszergazdának biztosítania kell, hogy az eszköz számítógépfiókja képes legyen felderíteni és csendes módon hitelesíteni a kimenő proxyt.
- Nem sikerült csatlakozni a felhasználói tartomány végponthoz, és el kell végezni a tartomány felderítését. (Windows 10 1809-es és újabb verziók esetén)
   - Az eszköznek hozzá kell férnie `https://login.microsoftonline.com`a rendszerkörnyezetben az ellenőrzött tartományhoz tartozó tartományi felderítés végrehajtásához és a tartomány típusának (felügyelt/összevont) meghatározásához.
   - Ha a helyszíni környezet kimenő proxyt igényel, a RENDSZERGAZDÁnak biztosítania kell, hogy az eszközön lévő rendszerkörnyezet képes legyen felderíteni és csendes módon hitelesíteni a kimenő proxyt.

**Gyakori hibakódok:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Ok Nem lehet beolvasni az SCP-objektumot, és le kell kérni az Azure AD-bérlő adatait.
   - Megoldás: Tekintse meg a [szolgáltatási kapcsolódási pont konfigurálása](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)című szakaszt.
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Ok Általános felderítési hiba. Nem sikerült lekérni a felderítési metaadatokat a DRS-ből.
   - Megoldás: Keresse meg az alábbi alhibaot a további vizsgálathoz.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Ok A művelet időtúllépést észlelt a felderítés végrehajtása során.
   - Megoldás: Győződjön meg `https://enterpriseregistration.windows.net` arról, hogy a rendszer elérhető a rendszerkörnyezetben. További információt a [hálózati kapcsolatra vonatkozó követelmények](hybrid-azuread-join-managed-domains.md#prerequisites)című szakaszban talál.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Ok Általános tartomány-felderítési hiba. Nem sikerült meghatározni a tartomány típusát (felügyelt/összevont) az STS-ből. 
   - Megoldás: Keresse meg az alábbi alhibaot a további vizsgálathoz.

**Gyakori alhibakódok:**

A felderítési hibakód alhibakódjának megkereséséhez használja az alábbi módszerek egyikét.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb verziók

Keresse meg a "DRS Discovery test" részt az illesztési állapot kimenetének "diagnosztikai adatokat" tartalmazó szakaszában.

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

A csatlakozási hibák fázisának és ErrorCode megkereséséhez használja Eseménynapló naplókat.

1. Nyissa meg a **felhasználó-eszköz regisztrációjának** eseménynaplóit az eseménynaplóban. Az **alkalmazások és szolgáltatások naplóban** > található**Microsoft** > **Windows** > **felhasználói eszköz regisztrálása**
2. Keressen eseményeket a következő eventIDs 201

![Sikertelen naplózási esemény](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Hálózati hibák

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Ok Nem lehet csatlakozni a kiszolgálóhoz.
   - Megoldás: Gondoskodjon a szükséges Microsoft-erőforrásokhoz való hálózati kapcsolatról. További információ: [hálózati kapcsolatra vonatkozó követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Ok Általános hálózati időtúllépés.
   - Megoldás: Gondoskodjon a szükséges Microsoft-erőforrásokhoz való hálózati kapcsolatról. További információ: [hálózati kapcsolatra vonatkozó követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Ok A hálózati verem nem tudta dekódolni a kiszolgáló válaszát.
   - Megoldás: Győződjön meg arról, hogy a hálózati proxy nem zavarja és nem módosítja a kiszolgáló válaszát.

###### <a name="http-errors"></a>HTTP-hibák

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Ok Nem megfelelő bérlői AZONOSÍTÓval konfigurált SCP-objektum. Vagy nem találhatók aktív előfizetések a bérlőben.
   - Megoldás: Győződjön meg arról, hogy az SCP-objektum a megfelelő Azure AD-bérlői AZONOSÍTÓval és aktív előfizetésekkel van konfigurálva, vagy a bérlőben van.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Ok HTTP 503 a DRS-kiszolgálóról.
   - Megoldás: A kiszolgáló jelenleg nem érhető el. a jövőbeli csatlakozási kísérletek valószínűleg sikeresek lesznek, ha a kiszolgáló ismét online állapotba kerül.

###### <a name="other-errors"></a>Egyéb hibák

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Ok Nem sikerült elemezni a kiszolgálói válasz JSON-fájlját. Valószínű, hogy a proxy egy HTML-hitelesítéssel rendelkező HTTP 200-t ad vissza.
   - Megoldás: Ha a helyszíni környezet kimenő proxyt igényel, a RENDSZERGAZDÁnak biztosítania kell, hogy az eszközön lévő rendszerkörnyezet képes legyen felderíteni és csendes módon hitelesíteni a kimenő proxyt.

#### <a name="authentication-phase"></a>Hitelesítési fázis

Csak összevont tartományi fiókok esetében alkalmazható.

Hiba okai:

- Nem lehet csendes hozzáférési tokent beolvasni a DRS-erőforráshoz.
   - A Windows 10-es eszközök hitelesítési jogkivonatot szerzik be az összevonási szolgáltatástól az integrált Windows-hitelesítéssel egy aktív WS-Trust végponton. Részletek: [összevonási szolgáltatás konfiguráció](hybrid-azuread-join-manual.md##set-up-issuance-of-claims)

**Gyakori hibakódok:**

Eseménynapló naplók használatával keresse meg a hibakódot, az alhibakódot, a kiszolgálói hibakódot és a kiszolgálói hibaüzenetet.

1. Nyissa meg a **felhasználó-eszköz regisztrációjának** eseménynaplóit az eseménynaplóban. Az **alkalmazások és szolgáltatások naplóban** > található**Microsoft** > **Windows** > **felhasználói eszköz regisztrálása**
2. Keressen eseményeket a következő Napszállta 305

![Sikertelen naplózási esemény](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Konfigurálási hibák

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Ok A hitelesítési protokoll nem a WS-Trust.
   - Megoldás: A helyszíni identitás-szolgáltatónak támogatnia kell a WS-Trust szolgáltatást 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Ok A helyszíni összevonási szolgáltatás nem adott vissza XML-választ.
   - Megoldás: Győződjön meg arról, hogy a MEX-végpont érvényes XML-t ad vissza. Győződjön meg arról, hogy a proxy nem zavarja és nem XML-válaszokat ad vissza.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Ok Nem lehetett felderíteni a végpontot a Felhasználónév/jelszó hitelesítéshez.
   - Megoldás: Keresse meg a helyszíni identitás-szolgáltató beállításait. Győződjön meg arról, hogy a WS-Trust végpontok engedélyezve vannak, és ellenőrizze, hogy a MEX-válasz tartalmazza-e a megfelelő végpontokat.

##### <a name="network-errors"></a>Hálózati hibák

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Ok Általános hálózati időtúllépés.
   - Megoldás: Győződjön meg `https://login.microsoftonline.com` arról, hogy a rendszer elérhető a rendszerkörnyezetben. Győződjön meg arról, hogy a helyszíni identitás-szolgáltató elérhető a rendszerkörnyezetben. További információ: [hálózati kapcsolatra vonatkozó követelmények](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Ok Megszakadt az Auth-végponttal létesített kapcsolatok.
   - Megoldás: Próbálkozzon újra egy kis ideig, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Ok A kiszolgáló által eljuttatott SSL-(SSL-) tanúsítványt nem lehetett érvényesíteni.
   - Megoldás: Keresse meg az ügyfél időkorlátját. Próbálkozzon újra egy kis ideig, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Ok `https://login.microsoftonline.com` Sikertelen kapcsolódási kísérlet.
   - Megoldás: Keresse meg a `https://login.microsoftonline.com`hálózati kapcsolódást.

##### <a name="other-errors"></a>Egyéb hibák

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Ok Az Azure AD nem fogadta el az SAML-jogkivonatot a helyszíni identitás-szolgáltatótól.
   - Megoldás: Keresse meg az összevonási kiszolgáló beállításait. Keresse meg a kiszolgálói hibakódot a hitelesítési naplókban.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Ok A kiszolgáló WS-Trust válasza hibát jelzett, és nem tudta lekérni az állítást
   - Megoldás: Keresse meg az összevonási kiszolgáló beállításait. Keresse meg a kiszolgálói hibakódot a hitelesítési naplókban.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Ok Hiba történt a hozzáférési jogkivonat lekérése közben a jogkivonat-végpontról.
   - Megoldás: Keresse meg a mögöttes hibát a ADAL naplóban. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Ok Általános ADAL-hiba
   - Megoldás: Keresse meg az alhiba kódját vagy a kiszolgálói hibakódot a hitelesítési naplókból.
    
#### <a name="join-phase"></a>Csatlakozás fázisa

Hiba okai:

Keresse meg a regisztrációs típust, és keresse meg a hibakódot az alábbi listából.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 és újabb verziók

Keresse meg a "korábbi regisztráció" alszakaszt az illesztési állapot kimenetének "diagnosztikai adatokat" tartalmazó szakaszában.
A "regisztráció típusa" mező azt jelzi, hogy milyen típusú csatlakozást hajtottak végre.

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

A csatlakozási hibák fázisának és ErrorCode megkereséséhez használja Eseménynapló naplókat.

1. Nyissa meg a **felhasználó-eszköz regisztrációjának** eseménynaplóit az eseménynaplóban. Az **alkalmazások és szolgáltatások naplóban** > található**Microsoft** > **Windows** > **felhasználói eszköz regisztrálása**
2. Keressen eseményeket a következő eventIDs 204

![Sikertelen naplózási esemény](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>A DRS-kiszolgáló által visszaadott HTTP-hibák

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Ok Hibaüzenet érkezett a DRS-ből a ErrorCode: Címtárhiba
   - Megoldás: A lehetséges okok és megoldások érdekében tekintse meg a kiszolgáló hibakódját.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Ok Hibaüzenet érkezett a DRS-ből a ErrorCode: A "AuthenticationError" és a ErrorSubCode nem "DeviceNotFound". 
   - Megoldás: A lehetséges okok és megoldások érdekében tekintse meg a kiszolgáló hibakódját.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Ok Hibaüzenet érkezett a DRS-ből a ErrorCode: Címtárhiba
   - Megoldás: A lehetséges okok és megoldások érdekében tekintse meg a kiszolgáló hibakódját.

##### <a name="tpm-errors"></a>TPM-hibák

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Ok A TPM-művelet sikertelen vagy érvénytelen volt.
   - Megoldás: Valószínűleg egy hibás Sysprep-rendszerkép miatt. Győződjön meg arról, hogy a számítógép, amelyről a Sysprep-rendszerkép létrejött, nem csatlakozik az Azure AD-hez, a hibrid Azure AD-hez csatlakozott vagy az Azure AD regisztrálva van
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Ok Általános TPM-hiba. 
   - Megoldás: Tiltsa le a TPM modult a hibával rendelkező eszközökön. A Windows 10 1809-es és újabb verziója automatikusan észleli a TPM-hibákat, és a TPM használata nélkül befejezi a hibrid Azure AD-csatlakozást.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Ok A TPM FIPS módban jelenleg nem támogatott.
   - Megoldás: Tiltsa le a TPM modult a hibával rendelkező eszközökön. A Windows 1809 automatikusan észleli a TPM-hibákat, és a TPM használata nélkül befejezi a hibrid Azure AD-csatlakozást.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Ok A TPM zárolva van.
   - Megoldás: Átmeneti hiba. Várjon a hűtési időszakra. Egy kis idő elteltével a csatlakozás sikertelen lesz. További információt a [TPM alapjai](https://docs.microsoft.com/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) című cikkben talál.

##### <a name="network-errors"></a>Hálózati hibák

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Ok Általános hálózati időtúllépés az eszköz a DRS-ben való regisztrálására tett kísérlet során
   - Megoldás: A `https://enterpriseregistration.windows.net`hálózati kapcsolat ellenőrzése.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Ok A kiszolgáló neve vagy címe nem oldható fel.
   - Megoldás: A `https://enterpriseregistration.windows.net`hálózati kapcsolat ellenőrzése. Győződjön meg arról, hogy az állomásnév DNS-feloldása pontos az n/w-ben és az eszközön.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Ok A kiszolgálóval létesített kapcsolatok rendellenesen megszakadtak.
   - Megoldás: Próbálkozzon újra egy kis ideig, vagy próbáljon meg csatlakozni egy másik stabil hálózati helyről.

##### <a name="federated-join-server-errors"></a>Összevont kapcsolódási kiszolgáló hibái

| Kiszolgáló hibakódja | Kiszolgálóhiba üzenet | Lehetséges okok | Megoldás: |
| --- | --- | --- | --- |
| Címtárhiba | A kérést átmenetileg szabályozzák. Próbálkozzon 300 másodperc elteltével. | Várt hiba. Valószínűleg azért, mert a gyors öröklés több regisztrációs kérelmet tesz elérhetővé. | Csatlakozzon újra a hűtési időszak után |

##### <a name="sync-join-server-errors"></a>Csatlakozási kiszolgáló hibáinak szinkronizálása

| Kiszolgáló hibakódja | Kiszolgálóhiba üzenet | Lehetséges okok | Megoldás: |
| --- | --- | --- | --- |
| Címtárhiba | AADSTS90002: A <UUID> bérlő nem található. Ez a hiba akkor fordulhat elő, ha a bérlőhöz nem tartoznak aktív előfizetések. Egyeztessen az előfizetés rendszergazdájával. | A SZOLGÁLTATÁSKAPCSOLÓDÁSI pont objektumának bérlői azonosítója helytelen | Győződjön meg arról, hogy az SCP-objektum a megfelelő Azure AD-bérlői AZONOSÍTÓval és aktív előfizetésekkel van konfigurálva, és szerepel a bérlőn. |
| Címtárhiba | Nem található az eszköz objektuma a megadott azonosító alapján. | A szinkronizáláshoz való csatlakozás várható hiba. Az eszköz objektuma nem lett szinkronizálva az AD-ből az Azure AD-be | Várjon, amíg a Azure AD Connect szinkronizálás befejeződik, és a szinkronizálás befejezését követően a következő csatlakozási kísérlet megoldja a problémát |
| AuthenticationError | A célszámítógép SID-azonosítójának ellenőrzése | Az Azure AD-eszközön lévő tanúsítvány nem felel meg a blob aláírásához használt tanúsítványnak a szinkronizálási csatlakozás során. Ez a hiba általában azt jelenti, hogy a szinkronizálás még nem fejeződött be. |  Várjon, amíg a Azure AD Connect szinkronizálás befejeződik, és a szinkronizálás befejezését követően a következő csatlakozási kísérlet megoldja a problémát |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5\. lépés: Naplók és kapcsolattartók összegyűjtése Microsoft ügyfélszolgálata

Nyilvános parancsfájlok beszerzése itt: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Nyisson meg egy rendszergazdai parancssort, `start_ngc_tracing_public.cmd`és futtassa a parancsot.
2. A probléma ismételt előállításához hajtsa végre a következő lépéseket:.
3. Állítsa le a naplózási parancsfájl futtatását `stop_ngc_tracing_public.cmd`a végrehajtásával.
4. Zip-ben, és küldje `%SYSTEMDRIVE%\TraceDJPP\*` el a naplókat az elemzéshez.

## <a name="troubleshoot-post-join-issues"></a>Csatlakozás utáni problémák elhárítása

### <a name="retrieve-the-join-status"></a>Az illesztés állapotának beolvasása 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: IGEN és AzureADPrt: IGEN
  
Ezek a mezők jelzik, hogy a felhasználó sikeresen hitelesített-e az Azure AD-be az eszközre való bejelentkezéskor. Ha az értékek **nem**, akkor a következők miatt lehet:

- A regisztráció során az eszközhöz társított TPM rossz tárolási kulcsa (a KeySignTest-t a emelt szintű futtatás közben kell ellenőriznie).
- Másodlagos bejelentkezési azonosító
- A HTTP-proxy nem található

## <a name="known-issues"></a>Ismert problémák
- A beállítások – > fiókok – > hozzáférés munkahelyi vagy iskolai rendszerhez, a hibrid Azure AD-hez csatlakoztatott eszközök két különböző fiókot mutatnak be, amelyek közül az egyik az Azure AD, egy pedig a helyszíni AD-hez, a mobil hozzáférési pontokhoz vagy külső WiFi hálózatokhoz csatlakoztatva. Ez csak egy felhasználói felületi probléma, és nincs hatással a funkcióra. 
 
## <a name="next-steps"></a>További lépések

[Az eszközök hibaelhárításának folytatása a dsregcmd parancs használatával](troubleshoot-device-dsregcmd.md)

További kérdések: eszközkezelés – [Gyakori kérdések](faq.md)
