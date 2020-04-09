---
title: 'VPN-átjáró: VPN-ügyfél OpenVPN protokoll P2S-kapcsolatokhoz: Azure AD-hitelesítés'
description: A P2S VPN használatával azure AD-hitelesítéssel csatlakozhat a virtuális hálózathoz
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879445"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>VPN-ügyfél konfigurálása P2S OpenVPN protokollkapcsolatokhoz: Azure AD-hitelesítés

Ez a cikk segít konfigurálni a VPN-ügyfél csatlakozni egy virtuális hálózathoz a Point-to-Site VPN és az Azure Active Directory hitelesítés. Mielőtt csatlakozhatna és hitelesíthetne az Azure AD használatával, először konfigurálnia kell az Azure AD-bérlőt. További információ: [Azure AD-bérlő konfigurálása.](openvpn-azure-ad-tenant.md)

> [!NOTE]
> Az Azure AD-hitelesítés csak OpenVPN® protokollkapcsolatok esetén támogatott.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Az ügyfélprofilok kal való együttműködés

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és minden olyan számítógépen konfigurálnia kell egy VPN-ügyfélprofilt, amely csatlakozni szeretne a virtuális hálózathoz. Létrehozhat egy ügyfélprofilt a számítógépen, exportálhatja, majd importálhatja további számítógépekre.

### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezen a [hivatkozáson](https://go.microsoft.com/fwlink/?linkid=2117554) keresztül letöltheti az Azure VPN-ügyfelet. Győződjön meg arról, hogy az Azure VPN-ügyfél rendelkezik engedéllyel a háttérben való futtatáshoz. Az engedély ellenőrzéséhez/engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Start menüt, majd válassza a Beállítások > az Adatvédelem > a háttéralkalmazások at.
2. A Háttéralkalmazások csoportban győződjön meg arról, hogy **az alkalmazások futtatása a háttérben** be van kapcsolva.
3. A Válassza ki, hogy mely alkalmazások futhatnak a háttérben, kapcsolja be az Azure VPN-ügyfél beállításait **Be beállításra.**

  ![Engedély](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Tanúsítványalapú ügyfélprofil létrehozása

Ha tanúsítványalapú profillal dolgozik, győződjön meg arról, hogy a megfelelő tanúsítványok telepítve vannak az ügyfélszámítógépen. A tanúsítványokról további információt az [Ügyféltanúsítványok telepítése című](point-to-site-how-to-vpn-client-install-azure-cert.md)témakörben talál.

  ![Bizonyos](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS-ügyfélprofil létrehozása

  ![Sugár](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> A kiszolgálótitkos kulcsot a P2S VPN-ügyfélprofilba lehet exportálni.  Az ügyfélprofil exportálására vonatkozó utasítások [itt](about-vpn-profile-download.md)találhatók.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Ügyfélprofil exportálása és terjesztése

Miután rendelkezik egy munkaprofillal, és azt más felhasználók nak kell terjesztenie, az alábbi lépésekkel exportálhatja azt:

1. Jelölje ki az exportálni kívánt VPN-ügyfélprofilt, jelölje ki a **...**, majd az **Exportálás**lehetőséget.

    ![Export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Jelölje ki azt a helyet, ahhoz, ahelyére mentse a profilt, hagyja a fájlnevet úgy, ahogy van, majd az XML-fájl mentéséhez válassza a **Mentés** gombot.

    ![Export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Ügyfélprofil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![Importálása](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Tallózással keresse meg a profil XML-fájlját, és jelölje ki. Ha a fájl ki van jelölve, válassza a **Megnyitás**gombot.

    ![Importálása](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Adja meg a profil nevét, és válassza a **Mentés gombot.**

    ![Importálása](./media/openvpn-azure-ad-client/import/import3.jpg)

4. A VPN-hez való csatlakozáshoz válassza a **Csatlakozás** lehetőséget.

    ![Importálása](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Miután csatlakozott, az ikon zöldre vált, és azt mondja **Connected**.

    ![Importálása](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ügyfélprofil törlése

1. Jelölje ki a törölni kívánt ügyfélprofil melletti három pontot. Ezután válassza **az Eltávolítás lehetőséget.**

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. A törléshez válassza az **Eltávolítás** gombot.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Kapcsolat létrehozása

1. A lapon válassza **+** a lehetőséget, majd **a + Add**.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Töltse ki a kapcsolat adatait. Ha nem biztos az értékekben, forduljon a rendszergazdához. Az értékek kitöltése után válassza a **Mentés gombot.**

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create2.jpg)

3. A VPN-hez való csatlakozáshoz válassza a **Csatlakozás** lehetőséget.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Válassza ki a megfelelő hitelesítő adatokat, majd kattintson a **Folytatás gombra.**

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Miután sikeresen csatlakozott, az ikon zöldre vált, és azt **mondja, Connected**.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatikus csatlakozás

Ezekkel a lépésekkel beállíthatja, hogy a kapcsolat automatikusan csatlakozzon a Mindig bekapcsolva beállításhoz.

1. A VPN-ügyfél kezdőlapján válassza a **VPN-beállítások lehetőséget.**

    ![Auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Válassza az **Igen** lehetőséget az Alkalmazások párbeszédablakának váltása párbeszédpanelen.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Győződjön meg arról, hogy a beállítani kívánt kapcsolat még nincs csatlakoztatva, majd jelölje ki a profilt, és jelölje be az **Automatikus csatlakozás** jelölőnégyzetet.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. A VPN-kapcsolat elindításához válassza a **Csatlakozás** lehetőséget.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Csatlakozási problémák diagnosztizálása

1. A csatlakozási problémák diagnosztizálásához használja a **Diagnosztizálás** eszközt. Válassza ki a **...** mellett a VPN-kapcsolat, hogy meg szeretné diagnosztizálni, hogy felfedje a menüt. Ezután válassza **a Diagnosztizálás lehetőséget.**

    ![Diagnosztizálni](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. A **Kapcsolat tulajdonságai** lapon válassza a **Diagnosztika futtatása**lehetőséget.

    ![Diagnosztizálni](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![Diagnosztizálni](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Tekintse meg a diagnosztikai eredményeket.

    ![Diagnosztizálni](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>GYIK

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hogyan vehetek hozzá DNS-utótagokat a VPN-ügyfélhez?

Módosíthatja a letöltött profil XML-fájlját, és ** \<hozzáadhatja a dnssuffixes \<>a dnssufix> \</dnssufix>\</dnssuffixes>** címkéket

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hogyan vehetek fel egyéni DNS-kiszolgálókat a VPN-ügyfélhez?

Módosíthatja a letöltött profil XML-fájlját, és ** \<hozzáadhatja a dnsservers \<>a /dnsserver> \</dnsserver>/dnsservers \<>** címkéket

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Az OpenVPN Azure AD-ügyfél a DNS-névfeloldási házirend-tábla (NRPT) bejegyzéseit használja, ami azt jelenti, hogy a DNS-kiszolgálók nem jelennek meg a kimenetalatt. `ipconfig /all` A használat közbeni DNS-beállítások megerősítéséhez olvassa el a [Get-DnsClientNrptPolicy a](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) PowerShellben.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hogyan adhatok hozzá egyéni útvonalakat a VPN-ügyfélhez?

Módosíthatja a letöltött profil XML-fájlját, és ** \<hozzáadhatja a includeroutes \<>útvonalat>\<célhoz>\<maszkot> \</destination>\</mask>\</route>\</includeroutes>** címkék

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hogyan tudom blokkolni (kizárni) az útvonalakat a VPN-ügyfélből?

Módosíthatja a letöltött profil XML-fájlját, és ** \<hozzáadhatja a kizárási útvonalakat \<>útvonalhoz>cél>\< \<maszk ot> \</destination>\</mask>/route \<>\</excluderoutes>címkéket.**

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Importálhatom a profilt parancssorból?

A profilt parancssorból importálhatja úgy, hogy a letöltött **azurevpnconfig.xml** fájlt a **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** mappába helyezi, és a következő parancsot futtatja:

```
azurevpn -i azurevpnconfig.xml 
```
az import használata a **-f** kapcsolót is


## <a name="next-steps"></a>További lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure AD-hitelesítést használó P2S Open VPN-kapcsolatokhoz című témakörben.](openvpn-azure-ad-tenant.md)
