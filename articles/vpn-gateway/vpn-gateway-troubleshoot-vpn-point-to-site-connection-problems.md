---
title: Az Azure pont – hely kapcsolati problémák elhárítása |} A Microsoft Docs
description: Útmutató a pont – hely kapcsolati problémák elhárításához.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 72cf094dc6206fcb156a3e4dae6e89662e2085d8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434857"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Hibaelhárítása: Az Azure pont – hely kapcsolati problémák

Ez a cikk felsorolja az általános előfordulhat, hogy pont – hely kapcsolati problémák. Emellett ismerteti a lehetséges okait és megoldásait ezeket a problémákat.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-ügyfél hiba: A tanúsítvány nem található.

### <a name="symptom"></a>Jelenség

Ha a VPN-ügyfél használatával Azure-beli virtuális hálózathoz csatlakozni próbál, a következő hibaüzenet jelenhet meg:

**Nem található tanúsítvány, amely a bővíthető hitelesítési protokoll használható. (798 hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az ügyféltanúsítvány hiányzik a **tanúsítványok – aktuális felhasználó\személyes\tanúsítványok**.

### <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Nyissa meg a tanúsítványkezelőben: kattintson a **Start**, típus **számítógép-tanúsítványok kezelése**, és kattintson a **számítógép-tanúsítványok kezelése** a keresési eredményekben.

2. Győződjön meg arról, hogy a megfelelő helyen vannak a következő tanúsítványok:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuális felhasználó\személyes\tanúsítványok útvonalon |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuális User\Trusted legfelső szintű hitelesítésszolgáltatók|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

3. Lépjen a C:\Users\<UserName > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, manuálisan telepítse a tanúsítványt (*.cer fájlt) a felhasználó és számítógép tárolójában.

Az ügyféltanúsítvány telepítésével kapcsolatos további információkért lásd: [létrehozása és exportálása pont – hely típusú kapcsolatok tanúsítványainak](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Amikor importálja az ügyféltanúsítványt, ne jelölje be a **titkos kulcs erős védelmének engedélyezése** lehetőséget.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-ügyfél hiba: A kapott üzenetet nem várt vagy rosszul formázott

### <a name="symptom"></a>Jelenség

Ha a VPN-ügyfél használatával Azure-beli virtuális hálózathoz csatlakozni próbál, a következő hibaüzenet jelenhet meg:

**A kapott üzenetet volt, váratlan vagy rosszul formázott. (0x80090326 hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha a következő feltételek valamelyike teljesül:

- A használható felhasználó által megadott útvonalak (UDR) alapértelmezett útvonalat az átjáró-alhálózat a helytelenül van beállítva.
- A legfelső szintű tanúsítvány nyilvános kulcsa nem feltöltve be az Azure VPN gatewayen. 
- A kulcs sérült vagy lejárt.

### <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Távolítsa el az átjáró alhálózatán található udr-t. Ellenőrizze, hogy az udr-t minden forgalmat megfelelően továbbítja.
2. Az Azure Portalon tekintheti meg, hogy azt visszavonták a legfelső szintű tanúsítvány állapotának ellenőrzéséhez. Ha nincs visszavonva, próbálja meg törölni a a főtanúsítványt és reupload. További információkért lásd: [tanúsítványok létrehozása](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-ügyfél hiba: tanúsítványlánc dolgozva, de leállt 

### <a name="symptom"></a>Jelenség 

Ha a VPN-ügyfél használatával Azure-beli virtuális hálózathoz csatlakozni próbál, a következő hibaüzenet jelenhet meg:

**Tanúsítványlánc dolgozva, de egy főtanúsítványt, amely nem tekinti megbízhatónak a megbízható szolgáltatót a leállt.**

### <a name="solution"></a>Megoldás

1. Győződjön meg arról, hogy a megfelelő helyen vannak a következő tanúsítványok:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuális felhasználó\személyes\tanúsítványok útvonalon |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuális User\Trusted legfelső szintű hitelesítésszolgáltatók|
    | AzureGateway -*GUID*. cloudapp.net, AzureRoot.cer    | Helyi számítógép\Megbízható legfelső szintű hitelesítésszolgáltatók|

2. Ha a tanúsítványok már a helyen, próbálja meg törölni a tanúsítványokat, és telepítse újra. A **azuregateway -*GUID*. cloudapp.net** tanúsítványa megtalálható-e a VPN-ügyfélkonfigurációs csomagot, amely az Azure Portalról letöltött. Fájl archivers használhatja a fájlok kibontásához a csomagból.

## <a name="file-download-error-target-uri-is-not-specified"></a>Letöltési hiba: nincs megadva a cél URI azonosítója

### <a name="symptom"></a>Jelenség

A következő hibaüzenet jelenhet meg:

**Fájlletöltési hiba. Nincs megadva a cél URI azonosítója.**

### <a name="cause"></a>Ok 

A probléma miatt egy hibás átjáró típusa. 

### <a name="solution"></a>Megoldás

A VPN gateway típusúnak kell lennie **VPN**, és a VPN-típust kell **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-ügyfél hiba: az Azure VPN egyéni parancsprogram végrehajtása sikertelen volt 

### <a name="symptom"></a>Jelenség

Ha a VPN-ügyfél használatával Azure-beli virtuális hálózathoz csatlakozni próbál, a következő hibaüzenet jelenhet meg:

**Egyéni parancsfájl (frissítés az útválasztási táblázat) nem sikerült. (Error 8007026f)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nyissa meg a webhely pont közötti VPN-kapcsolat egy parancsikont a kívánt.

### <a name="solution"></a>Megoldás 

Nyissa meg a VPN-csomag közvetlenül nem kell megnyitnia azt a parancsikont.

## <a name="cannot-install-the-vpn-client"></a>A VPN-ügyfél nem tudja telepíteni.

### <a name="cause"></a>Ok 

Egy további tanúsítvány megbízhatónak a VPN-átjáró a virtuális hálózat szükséges. A tanúsítvány tartalmazza a VPN-ügyfélkonfigurációs csomagot, amely akkor jön létre, az Azure Portalról.

### <a name="solution"></a>Megoldás

Bontsa ki a VPN-ügyfélkonfigurációs csomagot, és keresse meg a .cer fájlt. A tanúsítvány telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az mmc.exe.
2. Adja hozzá a **tanúsítványok** beépülő modult.
3. Válassza ki a **számítógép** fiók a helyi számítógépen.
4. Kattintson a jobb gombbal a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópont. Kattintson a **minden-tevékenység** > **importálás**, és keresse meg a .cer fájlt, a VPN-ügyfélkonfigurációs csomagot kinyert.
5. Indítsa újra a számítógépet. 
6. Próbálja meg telepíteni a VPN-ügyfél.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Az Azure portal hiba: nem sikerült menteni a VPN-átjáró és az adat érvénytelen

### <a name="symptom"></a>Jelenség

Amikor megpróbálja menteni a VPN Gateway az Azure Portalon, a következő hibaüzenet jelenhet meg:

**Nem sikerült menteni a virtuális hálózati átjáró &lt; *átjárónév*&gt;. Tanúsítvány Data beállítása esetében &lt; *azonosító tanúsítvány* &gt; je neplatná.**

### <a name="cause"></a>Ok 

Ez a probléma akkor fordulhat elő, ha a legfelső szintű tanúsítvány nyilvános kulcsát feltöltött érvénytelen karaktert, például egy szóközt tartalmaz.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az adatok a tanúsítvány nem tartalmaz érvénytelen karaktereket, például a sortörések (kocsivisszák). Az egész értéknek kell lennie egy hosszú sor. A következő szöveg egy példa a tanúsítvány:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Az Azure portal hiba: nem sikerült menteni a VPN-átjáró és az erőforrás neve érvénytelen.

### <a name="symptom"></a>Jelenség

Amikor megpróbálja menteni a VPN Gateway az Azure Portalon, a következő hibaüzenet jelenhet meg: 

**Nem sikerült menteni a virtuális hálózati átjáró &lt; *átjárónév*&gt;. Erőforrás neve &lt; *megpróbálja feltölteni tanúsítványnév* &gt; érvénytelen**.

### <a name="cause"></a>Ok

A probléma oka, hogy a tanúsítvány neve érvénytelen karaktert, például egy szóközt tartalmaz. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Hiba az Azure portal: VPN csomag fájlletöltési hiba 503-as

### <a name="symptom"></a>Jelenség

A VPN-ügyfél konfigurációs csomagjának letöltése megkísérlésekor a következő hibaüzenet jelenhet meg:

**Nem sikerült letölteni a fájlt. A hiba részletei: 503-as hiba. A kiszolgáló elfoglalt.**
 
### <a name="solution"></a>Megoldás

Ez a hiba átmeneti hálózati probléma okozhatja. Próbálkozzon újra néhány perc múlva a VPN-csomag letöltése.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Az Azure VPN-átjáró frissítésének: minden pont helyrendszer-ügyfelek nem tudnak csatlakozni

### <a name="cause"></a>Ok

Ha a tanúsítvány több mint 50 %-os keresztül élettartamuk, a tanúsítvány frissítése.

### <a name="solution"></a>Megoldás

A probléma megoldásához, telepítse újra az összes ügyfél a hely csomag pontot.

## <a name="too-many-vpn-clients-connected-at-once"></a>Túl sok a VPN-ügyfelek egyszerre csatlakoztatott

Minden egyes VPN Gateway az engedélyezett kapcsolatok maximális száma 128. Láthatja, hogy az Azure Portalon csatlakoztatott ügyfelek teljes száma.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Pont – hely VPN 10.0.0.0/8 útvonal helytelen az útvonaltábla hozzáadása

### <a name="symptom"></a>Jelenség

Amikor a VPN-kapcsolatot az a pont – hely ügyfelet, a VPN-ügyfelet hozzá kell adnia egy útvonalat az Azure virtuális hálózat felé. Az IP-segítő szolgáltatás hozzá kell adnia egy útvonalat a VPN-ügyfelek az alhálózat. 

A VPN-ügyfél tartomány 10.0.0.0/8, például 10.0.12.0/24 kisebb alhálózatához tartozik. Helyett 10.0.12.0/24 egy útvonalat, a 10.0.0.0/8 adjon hozzá egy útvonalat, amely magasabb prioritással bír. 

Ez az útvonal helytelen a kapcsolat más helyszíni hálózatokkal, előfordulhat, hogy a 10.0.0.0/8 tartományba 10.50.0.0/24, például egy másik alhálózathoz tartoznak, amelyek nem rendelkeznek meghatározott meghatározott útvonal működésképtelenné válik. 

### <a name="cause"></a>Ok

Ez szándékosan van Windows-ügyfelek számára. Amikor az ügyfél a PPP IPCP protokollt használja, a kiszolgálóról (az ebben az esetben a VPN gateway) lekéri az alagútkapcsolaton IP-címét. Azonban a protokoll korlátozását, mert az ügyfél nem rendelkezik az alhálózati maszkot. Mivel más módon nem lehet, hogy álljon, az ügyfél megpróbálja kitalálni az alhálózati maszkot az osztály az alagút adapter IP-cím alapján. 

Ezért adjon hozzá egy útvonalat a következő statikus hozzárendelés alapján: 

Ha a cím tartozik, a osztály--> alkalmazása /8

Ha a cím B--> osztályhoz tartozik a alkalmazni /16

Ha a cím C--> osztályhoz tartozik a alkalmazni /24

### <a name="solution"></a>Megoldás

A leghosszabb előtag-megfeleltetés vagy alacsonyabb metrika (ezért magasabb prioritású), mint a pont helyrendszer-útválasztási táblázatához beszúrásra az egyéb hálózatok útvonalakkal rendelkezik. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-ügyfél nem tud hozzáférni a hálózati fájlmegosztások

### <a name="symptom"></a>Jelenség

A VPN-ügyfél csatlakozik-e az Azure virtuális hálózat. Azonban az ügyfél nem fér hozzá hálózati megosztások.

### <a name="cause"></a>Ok

Az SMB protokoll a fájl megosztás eléréséhez használatos. Amikor a rendszer kezdeményezi a kapcsolatot, a VPN-ügyfél hozzáad a munkamenet hitelesítő adatokat, és a hiba akkor fordul elő. A kapcsolat létrejötte után az ügyfél kényszeríti a gyorsítótár hitelesítő adatok használata a Kerberos-hitelesítéshez. Ez a folyamat elindítja a kulcsszolgáltató (tartományvezérlő) jogkivonat beszerzéséhez a lekérdezések. Mivel az ügyfél az internetről csatlakozik, nem lehet képes elérni a tartományvezérlőn. Ezért az ügyfél nem átadja a feladatokat a Kerberos, NTLM. 

Az egyetlen alkalom, hogy az ügyfél kéri a hitelesítő adatokat a következő esetekben érvényes tanúsítvánnyal rendelkezik (SAN-nal UPN =) kiállító a tartományban, amelyhez csatlakozik. Az ügyfél is fizikailag kapcsolódnia kell a tartományi hálózaton. Ebben az esetben az ügyfél megpróbálja használni a tanúsítványt, és a tartományvezérlő véglegesítéskor. A kulcsszolgáltató majd egy "KDC_ERR_C_PRINCIPAL_UNKNOWN" hibát ad vissza. Az ügyfél átadja a feladatokat az NTLM kényszeríti. 

### <a name="solution"></a>Megoldás

A probléma megoldásához tiltsa le a gyorsítótárazását a tartományi hitelesítő adatait a következő beállításkulcsot: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nem található a Windows a pont – hely VPN-kapcsolatot a VPN-ügyfél újratelepítése után

### <a name="symptom"></a>Jelenség

Távolítsa el a pont – hely VPN-kapcsolatot, és telepítse újra a VPN-ügyfél. Ebben a helyzetben a VPN-kapcsolat nem történik meg sikeresen. A VPN-kapcsolatot a meg nem látja a **hálózati kapcsolatok** beállítások a Windows.

### <a name="solution"></a>Megoldás

A probléma megoldásához, törölje a régi VPN-ügyfél konfigurációs fájljainak a **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >**, majd futtassa újra a VPN-ügyfél telepítő .

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Pont – hely VPN-ügyfél nem tudja feloldani az erőforrásokat a helyi tartomány teljes Tartományneve

### <a name="symptom"></a>Jelenség

Amikor az ügyfél Azure pont – hely VPN-kapcsolat használatával kapcsolódik, nem oldható fel a FQND az erőforrások a helyi tartományban.

### <a name="cause"></a>Ok

Pont – hely VPN-ügyfél használja az Azure DNS-kiszolgálók, amelyek az Azure virtuális hálózatban vannak konfigurálva. Az Azure DNS-kiszolgálók felülbírálják a helyi DNS-kiszolgálók, amelyek az ügyfél vannak konfigurálva, így minden DNS-lekérdezést küld az Azure DNS-kiszolgálók. Az Azure DNS-kiszolgálók nem rendelkeznek a rekordokat a helyi erőforrásokhoz, ha a lekérdezés nem sikerült.

### <a name="solution"></a>Megoldás

A probléma megoldásához, győződjön meg arról, hogy az Azure DNS-kiszolgálók, amelyek használják az Azure-beli virtuális hálózaton, hogy fel tudja oldani a helyi erőforrások DNS-rekordjait. Ehhez használhatja a DNS-továbbítók vagy feltételes továbbítók. További információkért lásd: [névfeloldás saját DNS-kiszolgáló használatával](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A pont – hely VPN-kapcsolat létrejött, de még mindig nem lehet csatlakoztatni az Azure-erőforrásokhoz 

### <a name="cause"></a>Ok

A probléma akkor fordulhat elő, ha a VPN-ügyfél nem kap az útvonalakat az Azure VPN Gateway átjárón.

### <a name="solution"></a>Megoldás

Ez a probléma megoldásához [Azure VPN gateway alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Hiba: "a visszavont tanúsítványok függvény nem tudta ellenőrizni a visszavont tanúsítványok, mert a visszavont tanúsítványok kiszolgálója offline állapotban volt. (Hiba 0x80092013)"

### <a name="causes"></a>Okok
Ez a hibaüzenet akkor fordul elő, ha az ügyfél nem tud hozzáférni http://crl3.digicert.com/ssca-sha2-g1.crl és http://crl4.digicert.com/ssca-sha2-g1.crl.  A visszavonás ellenőrzését az alábbi két hely hozzáférésre van szüksége.  Ez a probléma általában akkor fordul elő, az ügyfél, amely a proxykiszolgáló konfigurálva van. Bizonyos környezetekben Ha a kérelmek nem fogja a proxykiszolgálón keresztül, a rendszer megtagadja a peremhálózati tűzfalon.

### <a name="solution"></a>Megoldás

Ellenőrizze a proxykiszolgáló beállításait, győződjön meg arról, hogy az ügyfél hozzáférhet http://crl3.digicert.com/ssca-sha2-g1.crl és http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-ügyfél hiba: A kapcsolat nem tudta konfigurálni a RAS/VPN-kiszolgáló házirend miatt. (812-hiba)

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a RADIUS-kiszolgáló, amely a VPN-ügyfél hitelesítéséhez használt helytelen beállításokkal rendelkezik, vagy az Azure-átjáró nem érhető el a Radius-kiszolgáló.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a RADIUS-kiszolgáló megfelelően van-e konfigurálva. További információkért lásd: [integrálása RADIUS-hitelesítés az Azure multi-factor Authentication-kiszolgáló](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Hiba 405" Ha a VPN-átjáró legfelső szintű tanúsítvány letöltése

### <a name="cause"></a>Ok

Legfelső szintű tanúsítvány nincs telepítve volt. A legfelső szintű tanúsítvány telepítése az ügyfél **megbízható tanúsítványokat** tárolásához.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-ügyfél hiba: A távoli kapcsolat nem történt, mert a megkísérelt VPN-alagút nem sikerült. (Error 800) 

### <a name="cause"></a>Ok

A hálózati adapter illesztőprogramja elavult.

### <a name="solution"></a>Megoldás

A hálózati illesztőprogram frissítése:

1. Kattintson a **Start**, típus **Eszközkezelő**, és az eredmények listájában válassza ki. Ha a rendszergazdai jelszót vagy megerősítést kér, adja meg a jelszót, vagy erősítse meg a műveletet.
2. Az a **hálózati adapterek** kategóriák, keresse meg a frissíteni kívánt hálózati Adaptert.  
3. Kattintson duplán az eszköz nevét, majd válassza ki **illesztőprogram frissítése**válassza **keressen automatikusan a frissített illesztőprogram**.
4. Ha a Windows nem talál egy új illesztőprogramot, nézzük meg egy, az eszköz gyártójának webhelyén, és kövesse az utasításokat.
5. Indítsa újra a számítógépet, és próbálja meg újra a kapcsolódást.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Hiba: "fájl letöltési hiba történt. nincs megadva a cél URI"

### <a name="cause"></a>Ok

Ennek az az oka egy hibás átjáró típusa van konfigurálva.

### <a name="solution"></a>Megoldás

VPN az Azure VPN gateway típusúnak kell lennie, és a VPN-típust kell **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Alkalmazáscsomag-telepítő VPN nem fejeződik be

### <a name="cause"></a>Ok

Ezt a problémát okozhatja a korábbi VPN-ügyfél telepítését. 

### <a name="solution"></a>Megoldás

Törölje a régi VPN-ügyfél konfigurációs fájljainak a **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId >** , és futtassa újra a VPN-ügyfél telepítőjét. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>A VPN-ügyfelet szeretné vagy alvó egy kis idő múlva

### <a name="solution"></a>Megoldás

Ellenőrizze az alvó és hibernált állapotba lépni a beállítások a számítógép, amelyen fut a VPN-ügyfél.
