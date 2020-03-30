---
title: Az Azure pont-hely közötti kapcsolódási problémáinak elhárítása
titleSuffix: Azure VPN Gateway
description: További információ a helyek közötti kapcsolódási problémák elhárításáról.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297629"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Hibaelhárítás: Az Azure pont-hely közötti kapcsolatokkal kapcsolatos problémái

Ez a cikk a helyek közötti gyakori kapcsolódási problémákat sorolja fel. A bizottság ismerteti e problémák lehetséges okait és megoldásait is.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-ügyfélhiba: Nem található tanúsítvány

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyfél használatával próbál csatlakozni egy Azure virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Nem található olyan tanúsítvány, amely használható lenne ezzel az Extensible Authentication Protocol protokollal. (798-as hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az ügyféltanúsítvány hiányzik a **Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok**mappából.

### <a name="solution"></a>Megoldás

A probléma megoldásához hajtsa végre az alábbi lépéseket:

1. Tanúsítványkezelő megnyitása: Kattintson a **Start**gombra, írja be **a Számítógép-tanúsítványok kezelése**, majd a **számítógép-tanúsítványok kezelése** a keresési eredményben.

2. Győződjön meg arról, hogy a következő tanúsítványok a megfelelő helyen találhatók:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Jelenlegi felhasználó\Személyes\Tanúsítványok |
    | AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

3. Nyissa meg a\<C:\Users UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID> webhelyet, és manuálisan telepítse a tanúsítványt (*.cer fájl) a felhasználó és a számítógép tárolójára.

Az ügyféltanúsítvány telepítéséről a helyek közötti [kapcsolatok tanúsítványainak létrehozása és exportálása](vpn-gateway-certificates-point-to-site.md)című témakörben talál további információt.

> [!NOTE]
> Az ügyféltanúsítvány importálásakor ne jelölje be az **Erős személyes kulcs védelmének engedélyezése** jelölőnégyzetet.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>A számítógép és a VPN-kiszolgáló közötti hálózati kapcsolat nem jött létre, mert a távoli kiszolgáló nem válaszol

### <a name="symptom"></a>Hibajelenség

Amikor a Windows IKEv2 használatával próbál csatlakozni egy Azure virtuális hálózati átjáróhoz, a következő hibaüzenet jelenik meg:

**A számítógép és a VPN-kiszolgáló közötti hálózati kapcsolat nem jött létre, mert a távoli kiszolgáló nem válaszol**

### <a name="cause"></a>Ok
 
 A probléma akkor jelentkezik, ha a Windows verziója nem támogatja az internetes kulcscsere töredezettségét
 
### <a name="solution"></a>Megoldás

Az IKEv2 Windows 10 és Server 2016 rendszeren támogatott. Ahhoz azonban, hogy használni tudja az IKEv2-t, helyileg telepítenie kell a frissítéseket, és meg kell adnia a beállításkulcs értékét. A Windows 10 előtti operációsrendszer-verziók nem támogatottak, és csak SSTP-t tudnak használni.

A Windows 10 vagy a Server 2016 előkészítése az IKEv2 használatára:

1. Telepítse a frissítést.

   | Operációs rendszer verziója | Dátum | Szám/hivatkozás |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, 1607-es verzió | 2018. január 17. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, 1703-as verzió | 2018. január 17. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 1709-es verzió | 2018. március 22.March 22, 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Adja meg a beállításkulcs értékét. A rendszerleíró `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` adatbázisban REG_DWORD kulcs létrehozása vagy beállítása 1-re.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-ügyfélhiba: A fogadott üzenet váratlan vagy rosszul formázott volt

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyfél használatával próbál csatlakozni egy Azure virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**A fogadott üzenet váratlan vagy rosszul formázott volt. (Hiba 0x80090326)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az alábbi feltételek valamelyike teljesül:

- A felhasználó által definiált útvonalak (UDR) alapértelmezett útvonallal rendelkező használata az átjáró alhálózaton helytelenül van beállítva.
- A legfelső szintű tanúsítvány nyilvános kulcsa nem töltődik fel az Azure VPN-átjáróba. 
- A kulcs sérült vagy lejárt.

### <a name="solution"></a>Megoldás

A probléma megoldásához hajtsa végre az alábbi lépéseket:

1. Távolítsa el az UDR-t az átjáró-alhálózaton. Győződjön meg arról, hogy az UDR megfelelően továbbítja az összes forgalmat.
2. Ellenőrizze a főtanúsítvány állapotát az Azure Portalon, hogy megállapítsa, visszavonták-e. Ha nem vonják vissza, próbálja meg törölni a főtanúsítványt, és töltse fel újra. További információt a [Tanúsítványok létrehozása című témakörben talál.](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-ügyfélhiba: Feldolgozott, de megszüntetett tanúsítványlánc 

### <a name="symptom"></a>Hibajelenség 

Amikor a VPN-ügyfél használatával próbál csatlakozni egy Azure virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Olyan tanúsítványlánc, amelyet egy olyan főtanúsítványban dolgoztak fel, amelyet a megbízhatósági szolgáltató nem bízott meg.**

### <a name="solution"></a>Megoldás

1. Győződjön meg arról, hogy a következő tanúsítványok a megfelelő helyen találhatók:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Jelenlegi felhasználó\Személyes\Tanúsítványok |
    | *Azuregateway– GUID*.cloudapp.net  | Jelenlegi felhasználó\Megbízható legfelső szintű hitelesítésszolgáltatók|
    | *AzureGateway– GUID*.cloudapp.net, AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

2. Ha a tanúsítványok már a helyen vannak, próbálja meg törölni és újratelepíteni a tanúsítványokat. Az **azuregateway-*GUID*.cloudapp.net** tanúsítvány az Azure Portalról letöltött VPN-ügyfélkonfigurációs csomagban található. A fájlarchiválók segítségével kibonthatja a fájlokat a csomagból.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fájlletöltési hiba: A cél URI nincs megadva

### <a name="symptom"></a>Hibajelenség

A következő hibaüzenet jelenik meg:

**Fájlletöltési hiba. A cél URI nincs megadva.**

### <a name="cause"></a>Ok 

A problémát helytelen átjárótípus oka. 

### <a name="solution"></a>Megoldás

A VPN-átjárótípusnak **VPN-nek**kell lennie, a VPN-típusnak **pedig RouteBased**típusúnak kell lennie.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-ügyfélhiba: Az Azure VPN egyéni parancsfájlja nem sikerült 

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyfél használatával próbál csatlakozni egy Azure virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Az egyéni parancsfájl (az útválasztási tábla frissítéséhez) nem sikerült. (8007026f hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a hely-pont VPN-kapcsolatot parancsikon használatával próbálja megnyitni.

### <a name="solution"></a>Megoldás 

Nyissa meg közvetlenül a VPN-csomagot, ahelyett, hogy a parancsikonból nyitná meg.

## <a name="cannot-install-the-vpn-client"></a>A VPN-ügyfél nem telepíthető

### <a name="cause"></a>Ok 

A virtuális hálózat VPN-átjárójának megbízhatóságához további tanúsítvány szükséges. A tanúsítvány szerepel az Azure Portalról létrehozott VPN-ügyfélkonfigurációs csomagban.

### <a name="solution"></a>Megoldás

Bontsa ki a VPN-ügyfél konfigurációs csomagját, és keresse meg a .cer fájlt. A tanúsítvány telepítéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg az mmc.exe.open mmc.exe.Open mmc.exe.
2. Adja hozzá a **Tanúsítványok** beépülő modult.
3. Válassza **Computer** ki a helyi számítógép számítógépfiókját.
4. Kattintson a jobb gombbal a **Megbízható legfelső szintű hitelesítésszolgáltatók** csomópontra. Kattintson az Összes feladat > **importálása gombra,** és keresse meg a VPN-ügyfél konfigurációs csomagból kinyert .cer fájlt. **All-Task**
5. Indítsa újra a számítógépet. 
6. Próbálja meg telepíteni a VPN-ügyfelet.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Az Azure Portal hibája: Nem sikerült menteni a VPN-átjárót, és az adatok érvénytelenek

### <a name="symptom"></a>Hibajelenség

Amikor megpróbálja menteni a VPN-átjáró módosításait az Azure Portalon, a következő hibaüzenet jelenik meg:

**Nem sikerült menteni a &lt;virtuális hálózati átjáró *átjárónevét*&gt;. A &lt; *tanúsítványtanúsítvány-azonosító* &gt; adatai érvénytelenek.**

### <a name="cause"></a>Ok 

Ez a probléma akkor fordulhat elő, ha a feltöltött főtanúsítvány nyilvános kulcsa érvénytelen karaktert, például szóközt tartalmaz.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a tanúsítványban szereplő adatok nem tartalmaznak érvénytelen karaktereket, például sortöréseket (kocsivissza). A teljes értéknek egy hosszú sornak kell lennie. A következő szöveg a tanúsítvány mintája:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Az Azure Portal hibája: Nem sikerült menteni a VPN-átjárót, és az erőforrás neve érvénytelen

### <a name="symptom"></a>Hibajelenség

Amikor megpróbálja menteni a VPN-átjáró módosításait az Azure Portalon, a következő hibaüzenet jelenik meg: 

**Nem sikerült menteni a &lt;virtuális hálózati átjáró *átjárónevét*&gt;. A &lt; *feltölteni* &gt; kívánt erőforrásnévtanúsítvány neve érvénytelen.**

### <a name="cause"></a>Ok

A probléma oka az, hogy a tanúsítvány neve érvénytelen karaktert, például szóközt tartalmaz. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal hiba: VPN-csomagfájl letöltési hiba 503

### <a name="symptom"></a>Hibajelenség

A VPN-ügyfél konfigurációs csomagjának letöltésekénél a következő hibaüzenet jelenik meg:

**Nem sikerült letölteni a fájlt. Hiba részletei: hiba 503. A kiszolgáló foglalt.**
 
### <a name="solution"></a>Megoldás

Ezt a hibát ideiglenes hálózati probléma okozhatja. Próbálja meg újra letölteni a VPN-csomagot néhány perc múlva.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Az Azure VPN-átjáró frissítése: Minden ponttól helyig tartó ügyfél nem tud csatlakozni

### <a name="cause"></a>Ok

Ha a tanúsítvány élettartama során több mint 50 százalék, a tanúsítvány átgördül.

### <a name="solution"></a>Megoldás

A probléma megoldásához töltse le újra és telepítse újra a Point to Site csomagot az összes ügyfélen.

## <a name="too-many-vpn-clients-connected-at-once"></a>Túl sok VPN-ügyfél csatlakozik egyszerre

A rendszer eléri a megengedett kapcsolatok maximális számát. Az Azure Portalon megtekintheti a csatlakoztatott ügyfelek teljes számát.

## <a name="vpn-client-cannot-access-network-file-shares"></a>A VPN-ügyfél nem tud hozzáférni a hálózati fájlmegosztásokhoz

### <a name="symptom"></a>Hibajelenség

A VPN-ügyfél csatlakozott az Azure virtuális hálózathoz. Az ügyfél azonban nem tud hozzáférni a hálózati megosztásokhoz.

### <a name="cause"></a>Ok

Az SMB protokoll fájlmegosztási hozzáférésre szolgál. A kapcsolat indításakor a VPN-ügyfél hozzáadja a munkamenet hitelesítő adatait, és a hiba bekövetkezik. A kapcsolat létrejötte után az ügyfél nek a Kerberos-hitelesítéshez a gyorsítótár hitelesítő adatait kell használnia. Ez a folyamat lekérdezéseket kezdeményez a kulcselosztó központba (tartományvezérlőhöz) egy token lekéréséhez. Mivel az ügyfél az internetről csatlakozik, előfordulhat, hogy nem tudja elérni a tartományvezérlőt. Ezért az ügyfél nem tudja átadni a Kerberos és az NTLM között. 

Az ügyfél csak akkor kér hitelesítő adatokat, ha érvényes (SAN=UPN-nel rendelkező) tanúsítvánnyal rendelkezik, amelyet az a tartomány állít ki, amelyhez csatlakozik. Az ügyfélnek fizikailag is csatlakoznia kell a tartományi hálózathoz. Ebben az esetben az ügyfél megpróbálja használni a tanúsítványt, és eléri a tartományvezérlőt. Ezután a kulcselosztó központ "KDC_ERR_C_PRINCIPAL_UNKNOWN" hibát ad vissza. Az ügyfél kénytelen átadni az NTLM-nek. 

### <a name="solution"></a>Megoldás

A probléma kerülő megoldása érdekében tiltsa le a tartományi hitelesítő adatok gyorsítótárazását a következő rendszerleíró alkulcsról: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nem található a pont-hely VPN kapcsolat a Windows rendszerben a VPN-ügyfél újratelepítése után

### <a name="symptom"></a>Hibajelenség

Eltávolítja a pont-hely VPN-kapcsolatot, majd újratelepíti a VPN-ügyfelet. Ebben az esetben a VPN-kapcsolat nincs sikeresen konfigurálva. A Windows **Hálózati kapcsolatok** beállításaiban nem jelenik meg a VPN-kapcsolat.

### <a name="solution"></a>Megoldás

A probléma megoldásához törölje a régi VPN-ügyfél konfigurációs fájljait a **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>,majd **futtassa újra a VPN-ügyfél telepítőjét.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>A pont-hely VPN-ügyfél nem tudja feloldani a helyi tartomány erőforrásainak teljes tartománynát

### <a name="symptom"></a>Hibajelenség

Ha az ügyfél pont-hely VPN-kapcsolathasználatával csatlakozik az Azure-hoz, nem tudja feloldani a helyi tartomány erőforrásainak teljes tartománynát.

### <a name="cause"></a>Ok

A pont-hely VPN-ügyfél az Azure virtuális hálózatában konfigurált Azure DNS-kiszolgálókat használja. Az Azure DNS-kiszolgálók elsőbbséget élveznek az ügyfélben konfigurált helyi DNS-kiszolgálókkal szemben, így az összes DNS-lekérdezés az Azure DNS-kiszolgálókra kerül. Ha az Azure DNS-kiszolgálók nem rendelkeznek a helyi erőforrások rekordjaival, a lekérdezés sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megoldásához győződjön meg arról, hogy az Azure virtuális hálózaton használt Azure DNS-kiszolgálók képesek megoldani a DNS-rekordok a helyi erőforrások. Ehhez használhatja a DNS-továbbítókat vagy a feltételes továbbítókat. További információ: [Névfeloldás a saját DNS-kiszolgálóhasználatával](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A pont-hely VPN-kapcsolat létrejött, de továbbra sem tud csatlakozni az Azure-erőforrásokhoz 

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a VPN-ügyfél nem kapja meg az útvonalakat az Azure VPN-átjáró.

### <a name="solution"></a>Megoldás

A probléma megoldásához állítsa alaphelyzetbe az [Azure VPN-átjárót.](vpn-gateway-resetgw-classic.md) Az új útvonalak használatának biztosításához a pont-hely VPN-ügyfeleket újra le kell tölteni a virtuális hálózati társviszony-létesítés sikeres konfigurálása után.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Hiba: "A visszavonási függvény nem tudta ellenőrizni a visszavonást, mert a visszavonási kiszolgáló offline állapotban volt. (Hiba: 0x80092013)"

### <a name="causes"></a>Okok
Ez a hibaüzenet akkor jelenik http://crl3.digicert.com/ssca-sha2-g1.crl http://crl4.digicert.com/ssca-sha2-g1.crlmeg, ha az ügyfél nem tud hozzáférni a és a hoz.  A visszavonási ellenőrzéshez hozzá kell férni e két helyhez.  Ez a probléma általában a proxykiszolgálóval konfigurált ügyfélen fordul elő. Bizonyos környezetekben, ha a kérelmek nem megy keresztül a proxy kiszolgálón, akkor meg lesz tagadni az Edge tűzfal.

### <a name="solution"></a>Megoldás

Ellenőrizze a proxykiszolgáló beállításait, győződjön http://crl3.digicert.com/ssca-sha2-g1.crl http://crl4.digicert.com/ssca-sha2-g1.crlmeg arról, hogy az ügyfél hozzáférhet a és a hoz.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-ügyfélhiba: A kapcsolat a RAS/VPN-kiszolgálón konfigurált házirend miatt lett letiltva. (812-es hiba)

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a VPN-ügyfél hitelesítéséhez használt RADIUS-kiszolgáló helytelen beállításokkal rendelkezik, vagy az Azure Gateway nem tudja elérni a Radius-kiszolgálót.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy a RADIUS-kiszolgáló megfelelően van-e konfigurálva. További információ: [RADIUS-hitelesítés integrálása az Azure többtényezős hitelesítési kiszolgálóval című témakörben.](../active-directory/authentication/howto-mfaserver-dir-radius.md)

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"405-ös hiba", amikor letölti a főtanúsítványt a VPN-átjáróból

### <a name="cause"></a>Ok

A főtanúsítvány nincs telepítve. A főtanúsítvány az ügyfél **Megbízható tanúsítványok** tárolójában van telepítve.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-ügyfélhiba: A távoli kapcsolat nem jött létre, mert a vpn-alagutak megkísérlése nem sikerült. (800-as hiba) 

### <a name="cause"></a>Ok

A hálózati adapter illesztőprogramja elavult.

### <a name="solution"></a>Megoldás

A hálózati adapter illesztőprogramjának frissítése:

1. Kattintson **a Start**gombra, írja be **az Eszközkezelő**parancsot, és válassza ki az eredménylistából. Ha a rendszer rendszergazdai jelszót vagy megerősítést kér, írja be a jelszót, vagy adja meg a megerősítést.
2. A **Hálózati adapterek** kategóriákban keresse meg a frissíteni kívánt hálózati adaptert.  
3. Kattintson duplán az eszköz nevére, válassza **az Illesztőprogram frissítése**lehetőséget, és válassza a Keresés **automatikusan a frissített illesztőprogramot**lehetőséget.
4. Ha a Windows nem talál új illesztőprogramot, megpróbálhat megkeresni egyet az eszköz gyártójának webhelyén, és kövesse az utasításokat.
5. Indítsa újra a számítógépet, és próbálkozzon újra a kapcsolattal.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-ügyfélhiba: VPN-kapcsolat <VPN Connection Name>tárcsázása , Állapot = VPN-platform nem indított kapcsolatot

A RasClient eseménynaplójában a következő hiba is <User> megjelenhet: "A felhasználó egy sikertelen nevű <VPN Connection Name> kapcsolatot tárcsázott. A hiba miatt visszaadott hibakód 1460."

### <a name="cause"></a>Ok

The Azure VPN Client does not have the "Background apps" App Permission enabled in App Settings for Windows.

### <a name="solution"></a>Megoldás

1. A Windows rendszerben nyissa meg a Beállítások -> Adatvédelem -> Háttéralkalmazások
2. Az "Alkalmazások futtatása a háttérben" között váltása bekapcsolva

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Hiba: "A fájl letöltési hiba cél URI nincs megadva"

### <a name="cause"></a>Ok

Ezt az okozza, hogy helytelen átjárótípus van konfigurálva.

### <a name="solution"></a>Megoldás

Az Azure VPN-átjárótípusnak VPN-nek kell lennie, a VPN-típusnak **pedig RouteBased**típusúnak kell lennie.

## <a name="vpn-package-installer-doesnt-complete"></a>A VPN-csomag telepítője nem fejeződik be

### <a name="cause"></a>Ok

Ezt a problémát az előző VPN-ügyféltelepítések okozhatják. 

### <a name="solution"></a>Megoldás

Törölje a régi VPN-ügyfél konfigurációs fájljait a **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** és futtassa újra a VPN-ügyfél telepítőjét. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>A VPN-ügyfél hibernálja vagy alvó állapotban van egy idő után

### <a name="solution"></a>Megoldás

Ellenőrizze az alvó és hibernált beállításokat abban a számítógépen, amelyen a VPN-ügyfél fut.
