---
title: "Az Azure pont – hely kapcsolat kapcsolatos problémák elhárítása |} Microsoft Docs"
description: "Útmutató a pont-pont csatlakozási hibák elhárítása."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: genli
ms.openlocfilehash: 69d363b5ff0b94884cf6d13ae0260f3747e4e69a
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Hibáinak elhárítása: Az Azure pont – hely kapcsolat problémák

A cikk ismerteti, amelyekkel Ön is szembesülhet pont-pont csatlakozási probléma. Lehetséges okait és megoldásait ezeket a problémákat is ismerteti.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-ügyfél hiba: A tanúsítvány nem található.

### <a name="symptom"></a>Jelenség

A VPN-ügyfél használatával csatlakoznak az Azure virtuális hálózat megkísérlésekor a következő hibaüzenet jelenhet meg:

**A tanúsítvány nem található, amely használható a bővíthető hitelesítési protokoll. (798 hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az ügyfél-tanúsítványa nem található a **tanúsítványok - aktuális User\Personal\Certificates**.

### <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az alábbi tanúsítványok vannak-e a megfelelő helyen:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuális User\Personal\Certificates |
    | Azuregateway -*GUID*. cloudapp.net  | Aktuális User\Trusted legfelső szintű hitelesítésszolgáltatók|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

2. Nyissa meg a felhasználók számára\<felhasználónév > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, manuálisan telepítse a tanúsítványt (*.cer fájlt) a felhasználói és számítógép tárolójában.

Az ügyféltanúsítvány telepítésével kapcsolatos további információkért lásd: [tanúsítvány létrehozása és exportálása a pont – hely kapcsolatok](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Amikor importálja az ügyféltanúsítványt, ne jelölje be a **titkos kulcs erős védelmének engedélyezése** lehetőséget.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-ügyfél hiba: A fogadott üzenet nem várt vagy rosszul formázott

### <a name="symptom"></a>Jelenség

A VPN-ügyfél használatával csatlakoznak az Azure virtuális hálózat megkísérlésekor a következő hibaüzenet jelenhet meg:

**A fogadott üzenet volt-e váratlan vagy rosszul formázott. (0x80090326 hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha a legfelső szintű tanúsítvány nyilvános kulcsa nem van töltve az Azure VPN gateway. Ez akkor is előfordulhat, ha a kulcs sérült vagy lejárt.

### <a name="solution"></a>Megoldás

A probléma megoldásához, a legfelső szintű tanúsítvány megtekintéséhez, hogy azt vissza lett vonva az Azure portálon állapotának ellenőrzéséhez. Ha nincs visszavonva, próbálja meg törölni a főtanúsítványt és reupload. További információkért lásd: [olyan tanúsítványokat hoznak létre](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-ügyfél hiba: A tanúsítványlánc feldolgozása, de a megszakadt 

### <a name="symptom"></a>Jelenség 

A VPN-ügyfél használatával csatlakoznak az Azure virtuális hálózat megkísérlésekor a következő hibaüzenet jelenhet meg:

**A tanúsítványlánc feldolgozása, de a legfelső szintű tanúsítvány nem bízik meg a megbízható szolgáltatót megszakadt.**

### <a name="solution"></a>Megoldás

1. Győződjön meg arról, hogy az alábbi tanúsítványok vannak-e a megfelelő helyen:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuális User\Personal\Certificates |
    | Azuregateway -*GUID*. cloudapp.net  | Aktuális User\Trusted legfelső szintű hitelesítésszolgáltatók|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

2. Ha a tanúsítvány már a helyen, próbálja meg törölni a tanúsítványokat, és telepítse újra. A  **azuregateway -*GUID*. az ügyfél VPN-konfiguráció Azure-portálról letöltött csomag cloudapp.net** tanúsítvány van. Fájl archivers segítségével csomagolja ki a fájlokat a csomagból.

## <a name="file-download-error-target-uri-is-not-specified"></a>Letöltési hiba: nincs megadva a cél URI Azonosítóját

### <a name="symptom"></a>Jelenség

A következő hibaüzenetet kapja:

**Hiba történt a fájl letöltése. Nincs megadva a cél URI Azonosítóját.**

### <a name="cause"></a>Ok 

A probléma miatt egy hibás átjáró típusa. 

### <a name="solution"></a>Megoldás

A VPN-átjáró típusúnak kell lennie **VPN**, és a VPN-típus lehet **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-ügyfél hiba: az Azure VPN egyéni parancsprogram végrehajtása sikertelen volt 

### <a name="symptom"></a>Jelenség

A VPN-ügyfél használatával csatlakoznak az Azure virtuális hálózat megkísérlésekor a következő hibaüzenet jelenhet meg:

**Egyéni parancsfájl (frissítés az útválasztási táblában) sikertelen volt. (8007026f hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a webhely pont közötti VPN-kapcsolat megnyitása egy helyi használatával próbálja.

### <a name="solution"></a>Megoldás 

Nyissa meg a VPN-csomag közvetlenül helyett a helyi megnyitja azt.

## <a name="cannot-install-the-vpn-client"></a>A VPN-ügyfél nem tudja telepíteni.

### <a name="cause"></a>Ok 

Kiegészítő igazolás hogy bízzon meg a VPN-átjáró a virtuális hálózat szükséges. A tanúsítványt a VPN-konfigurációs ügyfélcsomag, az Azure-portálon létrehozott tartalmazza.

### <a name="solution"></a>Megoldás

Bontsa ki a VPN-ügyfélcsomag konfigurációs, és keresse meg a .cer fájlt. A tanúsítvány telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a mmc.exe.
2. Adja hozzá a **tanúsítványok** beépülő modult.
3. Válassza ki a **számítógép** fiókot a helyi számítógépen.
4. Kattintson a jobb gombbal a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópont. Kattintson a **minden-tevékenység** > **importálási**, és keresse meg a .cer fájlt, a VPN-ügyfélcsomag konfigurációs kibontott.
5. Indítsa újra a számítógépet. 
6. Próbálja meg telepíteni a VPN-ügyfél.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Az Azure portál hiba: nem sikerült menteni a VPN-átjáró, és az adat érvénytelen

### <a name="symptom"></a>Jelenség

A VPN-átjáró módosításainak mentése az Azure-portálon megkísérlésekor a következő hibaüzenet jelenhet meg:

**Nem sikerült menteni a virtuális hálózati átjáró &lt;* átjárónevet*&gt;. Tanúsítvány adatainak &lt; *tanúsítvány azonosító* &gt; van invalid.* *

### <a name="cause"></a>Ok 

Ez a probléma akkor fordulhat elő, ha a legfelső szintű tanúsítvány nyilvános kulcsa feltöltött tartalmaz egy érvénytelen karakter, például egy szóközzel.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az adatok a tanúsítvány nem tartalmaz érvénytelen karaktereket, például sortörést (kocsivissza). Az egész értéknek kell lennie egy hosszú sor. A következő szöveg látható egy minta a tanúsítványt:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Az Azure portál hiba: nem sikerült menteni a VPN-átjáró, és az erőforrás neve érvénytelen.

### <a name="symptom"></a>Jelenség

A VPN-átjáró módosításainak mentése az Azure-portálon megkísérlésekor a következő hibaüzenet jelenhet meg: 

**Nem sikerült menteni a virtuális hálózati átjáró &lt;* átjárónevet*&gt;. Az erőforrásnév &lt; *megpróbálja feltölteni a tanúsítvány neve* &gt; van érvénytelen **.

### <a name="cause"></a>Ok

A probléma oka, hogy a tanúsítvány neve érvénytelen karaktert, például a szóközt tartalmaz. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Az Azure portál hiba: VPN csomag fájl letöltési hiba 503-as

### <a name="symptom"></a>Jelenség

Töltse le a VPN-ügyfélcsomag konfigurációs megkísérlésekor a következő hibaüzenet jelenhet meg:

**Nem sikerült letölteni a fájlt. Hiba legutolsó részletes adatai: 503-as hiba. A kiszolgáló túlterhelt.**
 
### <a name="solution"></a>Megoldás

Ez a hiba átmeneti hálózati probléma okozhatja. Próbálkozzon újra néhány perc múlva a VPN-csomagjának letöltése.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Az Azure VPN Gateway frissítése: minden P2S-ügyfelek nem tudnak csatlakozni

### <a name="cause"></a>Ok

Ha a tanúsítvány több mint 50 %-a keresztül élettartamuk, a tanúsítvány frissítése.

### <a name="solution"></a>Megoldás

Ez a probléma megoldása érdekében hozzon létre, és újra elosztják a VPN-ügyfelek az új tanúsítványok. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Túl sok a VPN-ügyfelek egyszerre csatlakoztatva

Minden egyes VPN-átjáró esetén engedélyezett kapcsolatok maximális száma 128. Láthatja, hogy az Azure portálon csatlakoztatott ügyfelek teljes száma.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Pont – hely típusú VPN helytelenül 10.0.0.0/8 útvonal hozzáadása az útvonaltábla

### <a name="symptom"></a>Jelenség

Amikor a VPN-kapcsolatot a pont-pont ügyfélen, a VPN-ügyfél az Azure virtuális hálózat felé útvonal kell hozzáadása. Az IP-segítő szolgáltatás hozzá kell adnia egy útvonalat a VPN-ügyfelek az alhálózathoz. 

A VPN-ügyfél tartomány 10.0.0.0/8, például a 10.0.12.0/24 kisebb alhálózatának tartozik. Helyett 10.0.12.0/24 útvonal, a 10.0.0.0/8 adjon hozzá egy útvonalat magasabb prioritással bír, amely. 

Ez helytelen az útvonal más hálózatokkal helyszíni, amely lehet, hogy a 10.0.0.0/8 tartományba 10.50.0.0/24, például egy másik alhálózat tartozik, amelyek nem rendelkeznek egy adott útvonal definiálva kapcsolat megszakad. 

### <a name="cause"></a>Ok

Ez szándékosan van a Windows-ügyfelek. Amikor az ügyfél a PPP IPCP protokollt használja, az IP-címet a alagútkapcsolat megszerzi a kiszolgálóról (a jelen esetben a VPN-átjáró). Azonban a protokoll korlátozása, mert az ügyfél nem rendelkezik az alhálózati maszkot. Mivel más módon nem lehet legyen, az ügyfél megpróbálja kitalálni az alhálózati maszkot az osztály az alagutat illesztő IP-cím alapján. 

Ezért adjon hozzá egy útvonalat a következő statikus hozzárendelés alapján: 

Ha a cím az Öné egy--> alkalmazása /8

Ha a cím az Öné B--> osztály /16 alkalmazása

Ha a cím az Öné C--> osztály /24 alkalmazása

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-ügyfél nem tud hozzáférni a hálózati fájlmegosztások

### <a name="symptom"></a>Jelenség

A VPN-ügyfél csatlakozott az Azure virtuális hálózat. Az ügyfél nem érhető el, hálózati megosztások.

### <a name="cause"></a>Ok

Az SMB protokollt használják a fájlmegosztási hozzáférést. Amikor a rendszer kezdeményezi a kapcsolatot, a VPN-ügyfél hozzáadása a munkamenet hitelesítő adatokat, és a hiba akkor fordul elő. A kapcsolat létrejötte után a rendszer kényszeríti az ügyfelet, a gyorsítótár hitelesítő adatok használata a Kerberos-hitelesítést. A folyamat elindul a lekérdezéseket, amelyekkel a kulcsszolgáltató (tartományvezérlő) a szolgáltatáshitelesítést egy token. Mivel az ügyfél az internetről csatlakozik, akkor nem feltétlenül tudni érnie a tartományvezérlő. Ezért az ügyfél nem feladatátvételt a Kerberos NTLM. 

Az egyetlen alkalom, hogy az ügyfélnek meg kell adnia a hitelesítő adatok megadása, ha van egy érvényes tanúsítványt (tárolóhálózathoz = UPN) kiállítja a tartomány, amelyhez csatlakozik. Az ügyfél is kell fizikailag csatlakozniuk kell a tartományi hálózaton. Ebben az esetben az ügyfél megpróbálja a tanúsítványt használni, és egészítse ki a tartományvezérlőre. A kulcsszolgáltató majd egy "KDC_ERR_C_PRINCIPAL_UNKNOWN" hibaüzenetet adja vissza. Az ügyfél NTLM átvehetné kényszeríti. 

### <a name="solution"></a>Megoldás

A probléma megoldása érdekében tiltsa le a gyorsítótárba helyezése tartományi hitelesítő adatait a következő beállításkulcsot: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nem található a Windows a pont-pont VPN-kapcsolatot a VPN-ügyfél újratelepítése után

### <a name="symptom"></a>Jelenség

Távolítsa el a pont-pont VPN-kapcsolatot, és telepítse újra a VPN-ügyfél. Ebben a helyzetben a VPN-kapcsolat beállítása nem sikerült. A VPN-kapcsolatot a nem látja a **hálózati kapcsolat** a Windows beállításai.

### <a name="solution"></a>Megoldás

A probléma megoldása érdekében törölje a régi VPN ügyfél konfigurációs fájlokat a **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections**, majd futtassa újra a VPN-ügyfél telepítőjét.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Pont-pont VPN-ügyfél nem tudja feloldani az erőforrásokat a helyi tartomány teljes Tartományneve

### <a name="symptom"></a>Jelenség

Ha az ügyfél pont-pont VPN-kapcsolat használatával kapcsolódik az Azure-ba, a helyi tartományban lévő erőforrások FQND nem tud feloldani.

### <a name="cause"></a>Ok

Pont-pont VPN-ügyfél használ az Azure DNS-kiszolgálók, az Azure virtuális hálózat konfigurált. Az Azure DNS-kiszolgálók felülbírálják a helyi DNS-kiszolgálók DNS-lekérdezéseket kerülnek az Azure DNS-kiszolgálók, az ügyfél konfigurált. Az Azure DNS-kiszolgálók nem rendelkezik a helyi erőforrások vonatkozó bejegyzéseket, ha a lekérdezés nem sikerült.

### <a name="solution"></a>Megoldás

A probléma elhárításához győződjön meg arról, hogy az Azure DNS-kiszolgálók, amely használja az Azure virtuális hálózaton történő oldhatja helyi erőforrások DNS-rekordjait. Ehhez használhatja a DNS-továbbítók vagy feltételes továbbítók. További információkért lásd: [névfeloldáshoz a saját DNS-kiszolgáló](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A pont-pont VPN-kapcsolat létrejött, de még nem csatlakozik Azure-erőforrások 

### <a name="cause"></a>Ok

A probléma akkor fordulhat elő, ha a VPN-ügyfél nem kap az útvonalakat az Azure VPN gateway.

### <a name="solution"></a>Megoldás

Ez a probléma megoldásához [alaphelyzetbe állítani az Azure VPN gateway](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Hiba: "a visszavont tanúsítványok függvény nem tudta ellenőrizni a visszavont tanúsítványok, mert a visszavont tanúsítványok kiszolgálója offline állapotban volt. (Hiba 0x80092013)"

### <a name="causes"></a>Okok
Ez a hibaüzenet akkor fordul elő, ha az ügyfél nem tud hozzáférni a http://crl3.digicert.com/ssca-sha2-g1.crl és http://crl4.digicert.com/ssca-sha2-g1.cr.  A visszavonási állapotának ellenőrzése a két hely hozzáférésre van szüksége.  Ez a probléma általában akkor fordul elő, az ügyfélen, amely a proxykiszolgáló konfigurálva van. Bizonyos környezetekben Ha a kérelem nem fog a proxykiszolgálón keresztül, a rendszer megtagadja a peremhálózati tűzfalon.

### <a name="solution"></a>Megoldás

Ellenőrizze a proxykiszolgáló beállításait, győződjön meg arról, hogy az ügyfél http://crl3.digicert.com/ssca-sha2-g1.crl és http://crl4.digicert.com/ssca-sha2-g1.cr hozzáférhet-e.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-ügyfél hiba: A kapcsolatot a távelérési kiszolgáló és a VPN-kiszolgálón konfigurált házirend miatt nem tudta. (812-es hiba)

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a RADIUS-kiszolgáló, amelyet a VPN-ügyfél hitelesítéséhez használt helytelen beállításokkal rendelkezik. 

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a RADIUS-kiszolgáló megfelelően van-e konfigurálva. További információkért lásd: [integrálni RADIUS-hitelesítés az Azure multi-factor Authentication kiszolgáló](../multi-factor-authentication/multi-factor-authentication-get-started-server-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Hiba 405" Ha a VPN-átjáró legfelső szintű tanúsítvány letöltése

### <a name="cause"></a>Ok

Legfelső szintű tanúsítvány nem volt telepítve. A legfelső szintű tanúsítvány telepítve van az ügyfél a **megbízható tanúsítványokat** tárolja.